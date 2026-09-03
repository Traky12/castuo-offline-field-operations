# Núcleo de evidencia — estado del preprototipo

Estado: **implementado y verificado en entorno sintético**. 245 pruebas contra
PostgreSQL 16 real, migraciones reproducibles desde cero. Ningún dato de campo: todo va
marcado `simulado` desde el esquema y el verificador lo dice en pantalla.

## Qué está cerrado

| Etapa | Estado |
|---|---|
| Inventario con identificador estable e ingesta idempotente | Cerrada |
| Detección append-only con hash reproducible | Cerrada |
| Propuesta de activo sin modificar la detección | Cerrada |
| Estado del activo por eventos, con transiciones declaradas | Cerrada |
| Cadena de trazabilidad por dispositivo sobre `event_hash` | Cerrada |
| Concurrencia sobre la cadena | Cerrada |
| Sello local y anclaje RFC 3161 separados | Cerrada |
| Canonicalización versionada con dos implementaciones | Cerrada |
| Exportación y verificador externo | Cerrada |
| **Ordenación de unidades y contraste ciego** | **Cerrada** |
| **API HTTP con autenticación por dispositivo** | **Cerrada** |
| Anclaje contra autoridad de sellado real | **Pendiente** |
| Panel de cuatro vistas | Pendiente |
| Aplicación Android y sincronización real | Fuera de alcance |

## El contraste ciego, que es el corazón del preprototipo

El gate G2 pregunta si el orden que produce el sistema se parece al del corchero experto.
Para que esa respuesta valga ante un tercero, el orden del sistema tiene que existir antes
que el del experto, y el listón tiene que estar puesto antes que los datos. Ambas cosas las
impone el motor:

1. **El protocolo se registra primero** (`contrast_protocol`): unidad de ordenación,
   métrica y umbral. Se guarda antes de que exista ningún orden.
2. **El sistema ordena** y el resultado se congela con su `content_hash`.
3. **Se sella** ese hash.
4. **Solo entonces** puede entrar el orden del experto. Sin sello previo, la inserción se
   rechaza con `CASTUO_BLIND`.
5. **La concordancia** lee métrica y umbral del protocolo, no de quien la invoca.

Ensayo reproducible: `python3 scripts/demo_gate_g2.py`.

## Cómo se reproduce todo

```bash
docker compose -f docker-compose.test.yml up -d
for f in migrations/*.sql; do psql "$CASTUO_DSN" -v ON_ERROR_STOP=1 -f "$f"; done
python3 -m pytest                                   # 245 pruebas
python3 scripts/demo_gate_g2.py                     # ensayo del gate G2
python3 scripts/demo_flujo_sintetico.py paquete.json
python3 scripts/verify_package.py paquete.json      # 0 íntegro, 1 alterado
uvicorn castuo.api.app:app --app-dir src            # API HTTP
```

La primera comprobación de cualquier sesión es
`pytest tests/integration/test_database_invariants.py`. Si falla, hay que parar.

## Invariantes y dónde viven

| Invariante | Dónde se defiende |
|---|---|
| No hay UPDATE, DELETE ni TRUNCATE en las tablas append-only | Permisos + triggers de sentencia |
| El activo no cambia en ningún campo | Trigger de fila + triggers de sentencia |
| El estado sigue solo transiciones declaradas, en orden | Trigger + `UNIQUE (asset_id, sequence_no)` |
| `origen` obligatorio e inmutable | `NOT NULL` + triggers |
| Ingesta y propuesta idempotentes | `UNIQUE` + `ON CONFLICT` |
| La cadena encadena eventos completos | `event_hash` + trigger de continuidad |
| **El orden experto exige sello previo** | **Trigger `expert_ranking_blind_check`** |
| **El umbral procede del protocolo** | **Se copia en `compute_concordance`** |
| La aceptación experta exige sello local | `CHECK` + trigger |
| Un dispositivo revocado no se reactiva | Trigger `device_guard` |

El rol `castuo_app` tiene `SELECT` e `INSERT` en todo, y un único `UPDATE (revoked_at)`
sobre `device` — necesario para poder revocar una credencial.

## API HTTP

`/health` · `POST /v1/evidence` (idempotente) · `POST /v1/detections` ·
`POST /v1/protocols` · `POST /v1/rankings` · `POST /v1/rankings/{id}/seal` ·
`POST /v1/rankings/{id}/expert-order` (409 antes del sello) ·
`POST /v1/rankings/{id}/concordance` · `GET /v1/rankings/{id}` · `POST /v1/verify`.

Autenticación por clave de dispositivo en cabecera `X-CASTUO-Key`, guardada solo como
hash. La escritura se registra en la cadena del dispositivo autenticado: la autenticación
no es solo control de acceso, decide en qué cadena se escribe.

## Límites — lo que este resultado NO es

No es producción. Siguen sin existir: Android, sincronización real de campo, datos reales,
operación del dron, PostGIS (geometría como WKT en texto, sin validación espacial ni
índices), panel web, despliegue.

Tres límites que conviene tener presentes:

1. **El anclaje RFC 3161 se ha probado solo con tokens sintéticos DER.** El extractor de
   `messageImprint` no se ha contrastado nunca contra una autoridad real. Condición de
   salida antes de usarlo fuera del entorno sintético.
2. **La autenticación es provisional.** Clave por dispositivo, sin rotación, sin caducidad
   y sin revocación auditada. Sirve para distinguir dispositivos en el prototipo; no es un
   esquema de producción.
3. **`owner_ref` está tipado `uuid`** para forzar una referencia seudonimizada, pero nada
   impide que apunte a una tabla con datos personales. Falta el análisis jurídico.

Los pesos del modelo de ordenación (`PESOS_POR_DEFECTO`) son una decisión de diseño
provisional, no un resultado medido: el reparto real se ajusta con datos de campo en F2.

## Coste del enfoque

Cortar en vertical obliga a volver sobre migraciones y repositorios en cada incremento: ya
se han reescrito tres veces. A cambio, cada vuelta ha destapado un defecto real mientras
todavía era barato — los triggers de sentencia, el encadenado sobre `payload_hash`, y el
orden de estado que dependía de `now()` y por tanto del azar.
