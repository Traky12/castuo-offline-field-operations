# Changelog — UAV Cork

Formato: una entrada por bloque de trabajo. Lo más reciente arriba.

## [No publicado]

### 2026-09-02 — Plan de crecimiento y UMD-1: la capa de captura

**Añadido**
- `docs/PLAN_CRECIMIENTO.md`: cómo crece el proyecto. Tres vías en paralelo, diez unidades
  mínimas demostrables con su vía y su evidencia, ensayo en seco obligatorio, escalera de
  carga útil P0–P3, escalera de identidad I0–I3, presupuesto de evidencia por gate, registro
  de deuda de evidencia, ventanas del monte, índice de preparación de gate, señales de
  crecimiento sano, y la lista de lo que no acelera el proyecto.
- `migrations/0009_uav_mission.sql`: `uav_mission` y `uav_observation`, append-only con el
  mismo triple candado, tipo cerrado `payload_t`, y `detection.observation_id`.
- `src/castuo/uav/`: modelos con la escalera de carga útil, repositorios con ingesta
  idempotente, y `reidentificacion.py` — emparejamiento por vecino mutuo más cercano con
  tolerancia obligatoria y sin valor por defecto.
- `scripts/demo_gate_g1.py`: ensayo en seco del gate G1, semilla fija, código de salida.
- `tests/uav/test_mission_and_reid.py`: 17 pruebas.
- `docs/publicaciones/plan-crecimiento.html`: el plan como página publicada en
  https://claude.ai/code/artifact/7fa32bbe-7aba-4c35-8c92-2efdff347af8.

**Decidido**
- ADR-035 tres vías en paralelo · ADR-036 escalera de carga útil registrada en cada misión ·
  ADR-037 ensayo en seco obligatorio antes de cada gate de campo · ADR-038 la deuda de
  evidencia se registra con dueño y gate de vencimiento.
- Estado maestro v0.8: D-15…D-18, A-10 (forma de la captura de campo), criterios de
  aceptación 13 y 14.

**Corregido**
- **Incoherencia 6 cerrada.** `detection` mezclaba la observación derivada del vuelo con la
  salida del modelo sobre ella, de modo que reprocesar un vuelo obligaba a duplicar el dato
  bruto. Ahora la observación es una entidad propia y la detección la referencia.
- **Hallazgo durante las pruebas:** `TRUNCATE uav_mission` se rechaza por dos motivos y el
  primero no es el nuestro — la clave ajena de `uav_observation` lo impide antes de que se
  dispare el trigger de sentencia. La prueba afirma sobre el rechazo, no sobre qué capa lo
  produjo, igual que se hizo con `CheckViolation`/`RestrictViolation` en el slice 01.

**Pendiente**
- UMD-2: registrar el protocolo de reidentificación antes del dato y sellar el resultado de
  G1. Hoy el ensayo imprime, no asienta.
- UMD-3 raíz de jornada · UMD-4 anclaje contra autoridad real de la UE · UMD-5 credenciales.
- A-10 sin decidir: la forma de la captura de campo bloquea UMD-6 y, con ella, G3.
- Sistema de referencia de las coordenadas: la distancia de reidentificación es euclídea y
  solo vale si están proyectadas en metros. `[POR DECIDIR EN F0]`

**Pruebas**
- 284 contra PostgreSQL 16, todas en verde (267 previas + 17 de la capa de captura).
  `demo_gate_g1.py` sobre 60 pies sintéticos: tasa de reencuentro 0,9500 con 3 pies perdidos
  a propósito y tolerancia de 1,00 m pactada antes de generar el dato.

**Siguiente incremento**
- UMD-2, y en paralelo abrir A-03, A-06, A-01 y A-02 y la búsqueda de parcela. Nada de lo
  interno espera a los acuerdos, y ningún acuerdo espera a lo interno.

### 2026-09-02 — Protocolo de Integridad Técnica, transversal

**Añadido**
- `docs/PROTOCOLO_INTEGRIDAD.md`: estándar único de inmutabilidad y trazabilidad, aplicable a
  SUBER UAV y a SENDA. Doce apartados con cuatro estados por afirmación —hecho probado, decisión
  de diseño, pendiente de auditoría, parcialmente resuelto—, bloque de alcance (qué entra y qué
  no), catálogo de eventos E-01…E-11 con su estado real, tabla de evidencia software frente a
  campo, tabla de riesgos R-01…R-09, catorce criterios de aceptación y encaje por línea de
  proyecto.
- `docs/publicaciones/protocolo-integridad.html`: el protocolo como página publicada en
  https://claude.ai/code/artifact/39d460dd-e146-49a7-90bf-07ab4aaefd13, para compartir con auditores e interlocutores sin acceso al repositorio.
<!-- lexico:off -->
- `scripts/check_lexico.py`: LEX-13, retórica absoluta —«inexpugnable», «escudo legal»,
  «imposible de alterar», «prueba matemática de integridad»—, con dos pruebas: una que la detecta
  y otra que confirma que el vocabulario técnico sobrio no se marca.
<!-- lexico:on -->

**Decidido**
- ADR-034: el protocolo es transversal y, ante discrepancia con el motor, manda el motor.
- Estado maestro v0.7: D-14, criterio de aceptación 12, incoherencias 9, 10 y 11.

**Corregido**
- **`origen`.** El borrador del protocolo declaraba dos valores; el motor tiene tres. Se conserva
  el del motor: un dato de campaña anterior fue real y no es dato de la campaña en curso, y
  confundirlos falsearía cualquier contraste. La nomenclatura de dos queda como sinónimo con
  equivalencia declarada.
- **Permisos.** El borrador revocaba a roles inexistentes (`tecnico`, `analista`). Lo
  implementado es no conceder `UPDATE`/`DELETE` al rol que sí existe, revocar además, e incluir
  los privilegios por defecto para que una tabla creada más tarde no nazca abierta.
<!-- lexico:off -->
- **Retórica.** Bajada la intensidad donde afirmaba de más: «prueba matemática de integridad»,
  «escudo legal», «inexpugnable», «impide cualquier reclamación». Sustituidas por «evidencia
  verificable» y «resistencia a la alteración silenciosa», y añadido de forma explícita lo que el
  protocolo **no** ofrece.
<!-- lexico:on -->
- **Afirmaciones absolutas.** S-5 pasa de garantía a objetivo de diseño del formato verificable.

**Pendiente**
- **Raíz de jornada: no existe.** El motor encadena por dispositivo (ADR-009) y la raíz que
  agrupa las cadenas para anclarlas una sola vez es trabajo nuevo, necesario antes del primer
  anclaje real.
- El catálogo E-01…E-11 es semántico: nada en el motor rechaza un evento fuera de él.
- Criterios 12, 13 y 14 del protocolo sin prueba: anclaje real, raíz de jornada y revocación
  auditada de credenciales.
- Acceso administrativo directo al motor: fuera del alcance del esquema, dentro del plan de
  despliegue.

**Pruebas**
- 267 contra PostgreSQL 16, todas en verde (265 previas + 2 del léxico). El comprobador cubre
  13 patrones y 22 pruebas.

**Siguiente incremento**
- **Raíz de jornada + anclaje RFC 3161 contra autoridad real de la UE**, en ese orden: la raíz es
  lo que hace que el anclaje tenga un único valor que sellar, y juntos cierran los criterios 12 y
  13 del protocolo y la propiedad S-5 de la soberanía.

### 2026-09-02 — Encaje exterior, soberanía del dato y catálogo de fortalezas

**Añadido**
- `docs/SOBERANIA_DEL_DATO.md`: la soberanía desglosada en seis propiedades comprobables
  (S-1…S-6) con su estado individual —tres probadas, dos sin auditar, una a medias—, por qué la
  exterioridad refuerza cada una, lo que la exterioridad cuesta, y la lista de lo que todavía no
  se puede afirmar.
- `docs/FORTALEZAS.md`: diez fortalezas del preprototipo con su nivel de evidencia EQ1–EQ6 (ocho
  en EQ3) y siete compromisos del prototipado con su nivel de destino y su gate. Incluye las tres
  reglas de redacción con las que se copian frases a una memoria.
<!-- lexico:off -->
- `scripts/check_lexico.py`: cuatro patrones nuevos —LEX-09 soberanía garantizada, LEX-10
  conformidad con el RGPD, LEX-11 certificaciones inexistentes, LEX-12 SUBER UAV como módulo de
  la plataforma—, con prueba cada uno y una prueba que exige que ningún patrón quede sin
  ejercitar.
<!-- lexico:on -->
- `POSICIONAMIENTO_SUBER.md`: apartados 12 (encaje exterior y soberanía) y 13 (cómo se enuncian
  las fortalezas), más el encuadre exterior en la cabecera y en los apartados 1, 2 y 11.

**Decidido**
- ADR-031 fase exterior a la arquitectura de CASTÚO-SYSTEM · ADR-032 la soberanía se declara
  desglosada, no como principio · ADR-033 ninguna fortaleza por encima de su nivel de evidencia.
- Estado maestro v0.6: D-11…D-13, criterio de aceptación 11, frontera por paquete verificable en
  el apartado de arquitectura.
- **A-03 cambia de forma:** la autoridad de sellado RFC 3161 deja de ser elección libre y debe
  estar en la Unión Europea; si no, S-5 se pierde por la puerta de atrás.

**Corregido**
- **Incoherencia 3 (encaje), abierta desde v0.1: resuelta.** SUBER UAV no es un componente futuro
  de la plataforma sino una fase exterior con frontera propia. La mudanza a repositorio propio
  deja de bloquear nada y pasa a ser una comodidad.
<!-- lexico:off -->
- El comprobador volvió a hacer su trabajo sobre su propio autor: LEX-12 marcó la frase «no es un
  módulo de CASTÚO-SYSTEM» en el apartado 2 del posicionamiento, escrita mientras se redactaba el
  patrón. Reformulada.
<!-- lexico:on -->

**Pendiente**
- S-3 (residencia europea) y S-6 (personas fuera del asiento) siguen sin auditoría ni revisión
  jurídica. S-5 espera la elección de autoridad en la UE (A-03).
- Anclaje RFC 3161 contra autoridad real: **no probado**.
- A-06 y A-07 siguen bloqueando los pilotos 2 y 3.
- El catálogo de fortalezas hay que mantenerlo en cada cierre de gate: desactualizado sería peor
  que no tenerlo.

**Pruebas**
- 265 contra PostgreSQL 16, todas en verde (258 previas + 7 nuevas del léxico). El comprobador
  cubre 12 patrones y 20 pruebas, y una de ellas falla si se declara un patrón sin ejercitarlo.

**Siguiente incremento**
- Sin cambios: anclaje RFC 3161 contra una autoridad real **de la UE**, que ahora cierra a la vez
  la última pieza sin probar del núcleo y la propiedad S-5 de la soberanía.

### 2026-09-02 — Reposicionamiento sectorial: SUBER UAV

**Añadido**
- `docs/POSICIONAMIENTO_SUBER.md`: el encuadre sectorial completo — la frase que define el
  sistema, el léxico controlado con formulación prohibida y aprobada, la alineación con el Plan
  de Calas, la arquitectura en cuatro capas, la cadena monte→fábrica, diez capacidades de
  análisis, doce requisitos funcionales, diez no funcionales, diez cualidades y los cinco
  bloques de credibilidad.
- `docs/MODELO_DATOS_SECTORIAL.md`: las trece entidades de dominio con su estado real —una
  construida, cuatro parciales, ocho ausentes— y el orden de construcción con sus dependencias
  externas.
- `docs/PILOTOS.md`: los tres pilotos con su gate, sus dependencias y, sobre todo, lo que cada
  uno **no** permite afirmar al cerrarse.
- `docs/INTERLOCUTORES.md`: encuadre diferenciado para CICYTEX, PTEcor, FUNDECYT-PCTEX e
  industria, con la advertencia de que no existe acuerdo con ninguno.
- `scripts/check_lexico.py`: comprobador del léxico controlado, ocho patrones prohibidos,
  exención por bloque `<!-- lexico:off -->` para poder citar lo que se prohíbe.
- `tests/docs/test_lexico.py`: 13 pruebas. No requieren PostgreSQL.
- `docs/publicaciones/suber-uav-dossier.html`: documento de posicionamiento en tres
  partes —propuesta de valor, arquitectura funcional, piloto mínimo viable—, publicado
  en https://claude.ai/code/artifact/5e4ca2a4-af64-4dcc-8c5c-1268d0914a71. La fuente vive en el repositorio y pasa por el comprobador de léxico.

**Decidido**
- ADR-025 el nombre cambia, los archivos no · ADR-026 el modelo sectorial es capa de dominio ·
  ADR-027 una clase de calidad no la firma un modelo · ADR-028 trazabilidad y correlación son
  objetivos distintos · ADR-029 el léxico se comprueba con una prueba · ADR-030 la cala se
  referencia, nunca se infiere.
- Estado maestro v0.5: D-07…D-10 añadidas, A-06…A-09 abiertas, riesgos H-S1 y H-S2 añadidos,
  criterio de aceptación 10 añadido, incoherencias 5–8 registradas.

**Corregido**
- El propio comprobador de léxico detectó una formulación prohibida en la primera redacción del
  estado maestro, en la línea que negaba la asignación automática de clases de calidad. Corregida antes de cerrar el bloque. Es la
  justificación empírica de ADR-029: la norma escrita no habría bastado ni con el autor de la
  norma escribiendo el documento.
- Los tres artefactos publicados quedan marcados como desactualizados o parcialmente vigentes en
  el apartado 13 del estado maestro. Describen el encuadre anterior.

**Pendiente**
- Anclaje RFC 3161 contra autoridad real: **no probado**.
- A-06 y A-07 bloquean los Pilotos 2 y 3, y son acuerdos con terceros, no trabajo de ingeniería.
- Separar `uav_observation` de `detection` antes de que existan vuelos reales (incoherencia 6).
- `owner_ref` y titularidad del dato de monte: revisión jurídica sin resolver.
- Panel de cuatro vistas, PostGIS, Android, sincronización real, operación del dron.

**Pruebas**
- 258 contra PostgreSQL 16, todas en verde (245 previas + 13 del léxico). Migraciones
  reproducibles desde cero. `scripts/check_lexico.py` termina en 0 sobre el repositorio y en 1
  sobre un documento con una formulación prohibida.

**Siguiente incremento**
- Anclaje RFC 3161 contra una autoridad real. Cierra la única pieza del núcleo sin probar contra
  el mundo. Alternativa sin dependencia de terceros: `uav_mission` y la separación de
  `uav_observation`.

### 2026-09-02 — Contraste ciego y API HTTP

**Añadido**
- `migrations/0007_contrast.sql`: `contrast_protocol`, `ranking`, `ranking_item`,
  `expert_ranking`, `expert_ranking_item`, `concordance`, y el trigger del muro.
- `migrations/0008_devices.sql`: enrolado y revocación de dispositivos.
- `src/castuo/ranking/`: ordenación determinista de unidades y persistencia del contraste.
- `src/castuo/contrast/concordance.py`: Spearman y Kendall tau-b con empates, sin
  dependencias externas y con versión de método declarada.
- `src/castuo/api/`: API HTTP (FastAPI) con autenticación por dispositivo y el verificador
  externo servido como endpoint.
- `scripts/demo_gate_g2.py`: ensayo en seco del gate G2 completo.
- Pruebas: contraste ciego (15), concordancia (8), API HTTP (16).

**Decidido**
- ADR-021 orden por secuencia y no por hora · ADR-022 umbral registrado antes que los datos
  · ADR-023 el muro y su punto de guardado · ADR-024 la autenticación decide la cadena.

**Corregido**
- **Defecto real:** `asset_current` ordenaba por `occurred_at`, y `now()` es la hora de la
  transacción; dos transiciones en la misma transacción dejaban el estado vigente decidido
  por el UUID. Corregido con `sequence_no` por activo. Hay prueba de regresión.
- El rechazo del muro abortaba la transacción del llamante y se perdía el trabajo previo.
  La escritura pasa a ir en su propio punto de guardado.

**Pendiente**
- Anclaje RFC 3161 contra autoridad real: **no probado**.
- Autenticación sin rotación, caducidad ni revocación auditada.
- `owner_ref`: revisión jurídica sin resolver.
- Panel de cuatro vistas, PostGIS, Android, sincronización real, operación del dron.

**Pruebas**
- 245 contra PostgreSQL 16, todas en verde. Migraciones reproducibles desde cero.

**Siguiente incremento**
- Panel de cuatro vistas sobre la API, o anclaje contra una autoridad de sellado real.
  Lo segundo cierra una condición de salida; lo primero es lo que se enseña.

### 2026-09-01 (tarde) — Endurecimiento del Slice 01

**Añadido**
- Protección contra `TRUNCATE` en todas las tablas append-only, mediante triggers de
  sentencia; cobertura también sobre tablas vacías.
- `asset_status_event` + vista `asset_current`: el estado del activo pasa a ser evento.
- `event_hash` en `trace_event`; la cadena encadena el evento completo.
- `docs/CANONICALIZACION.md` (CASTUO-CANON-1), `src/castuo/common/canonical.py` y 14
  vectores congelados verificados contra las dos implementaciones.
- `src/castuo/export/anchor.py`: extracción del `messageImprint` RFC 3161 y contrato de
  cuatro estados (sin anclaje / válido / de otro hash / ilegible).
- Verificador externo reescrito con ocho comprobaciones y salida `--json`.
- Pruebas de concurrencia con dos escritores reales sobre el mismo dispositivo.
- `docs/SLICE01.md`.

**Decidido**
- ADR-017 activo sin campos mutables · ADR-018 cadena sobre el evento completo ·
  ADR-019 canonicalización versionada con dos implementaciones · ADR-020 append-only
  incluye TRUNCATE y tablas vacías.

**Corregido**
- Migraciones consolidadas en un juego coherente de 0001 a 0006 (nada estaba desplegado);
  desaparece el antiguo `0006_asset_proposal.sql`, plegado en `0001_schema.sql`.
- `asset.status` ya no existe como columna mutable; `InventoryRepository.promote()` se
  sustituye por `AssetStatusRepository.transition()`.
- La cadena encadenaba `payload_hash`, lo que permitía alterar actor, tipo, entidad u
  origen sin romper la continuidad.
- `propose_asset()` era idempotente solo por la restricción; ahora lo es también en la API
  y devuelve `(proposal_id, creada)`.
- El anclaje sintético era una cadena inventada; ahora es DER real con `messageImprint`.

**Pendiente**
- El extractor de `messageImprint` **no se ha probado contra una autoridad de sellado
  real**: condición para usarlo fuera del entorno sintético.
- `owner_ref`: revisión jurídica sin resolver.
- PostGIS fuera del slice; la geometría sigue siendo WKT en texto.
- A-01 a A-05 abiertas.

**Pruebas**
- 205 pruebas contra PostgreSQL 16, todas en verde. Migraciones reproducibles desde cero.

**Siguiente incremento**
- Slice 02: ingesta HTTP con FastAPI sobre este núcleo. Sin tocar el modelo de datos.

### 2026-09-01 — Vertical Slice 01: núcleo de evidencia

**Añadido**
- `migrations/0001…0006` — esquema, inmutabilidad, cadena por dispositivo, reglas de
  sello, roles y privilegios, propuestas de activo.
- `src/castuo/` — inventario, detección, revisión, trazabilidad, exportación y sello.
- `scripts/verify_package.py` — verificador externo independiente de la aplicación.
- `scripts/demo_flujo_sintetico.py` — flujo completo reproducible.
- `tests/` — 54 pruebas contra PostgreSQL 16 real, con fixtures sintéticos (JSON + PNG).
- `.gitattributes` — normalización de finales de línea.

**Decidido**
- ADR-013 inmutabilidad en tres capas · ADR-014 anclaje RFC 3161 en tabla aparte ·
  ADR-015 vínculo detección→activo en `asset_proposal` · ADR-016 verificador independiente.

**Corregido**
- Un trigger de fila no protege una tabla vacía: `DELETE FROM seal` sobre cero filas tenía
  éxito. Añadidos triggers de sentencia; sin ellos la invariante era cierta por accidente.
- El criterio 9 («añadir el token no cambia el contenido sellado») era incompatible con un
  `seal` append-only: hacía falta una tabla de anclaje separada.
- Dos pruebas esperaban `CheckViolation` donde el trigger salta antes y lanza
  `RestrictViolation`. Se corrigieron las aserciones, no el esquema: las dos capas
  defienden y lo que importa es que el motor rechace.
- El mensaje de error del sello confundía «sin sello» con «sello inexistente». Separados.

**Pendiente**
- `owner_ref`: revisión jurídica de qué puede referenciar (incoherencia 4).
- A-01 a A-05 siguen abiertas.
- PostGIS fuera del slice: la geometría viaja como WKT en texto.

**Siguiente incremento**
- Slice 02: ingesta HTTP con FastAPI sobre este mismo núcleo, endpoint idempotente y
  verificador como servicio. Sin tocar el modelo de datos.

### 2026-09-01 — Estado maestro y contrato de continuidad

**Añadido**
- `docs/CASTUO_CORCHO_ESTADO_MAESTRO.md` — fuente de verdad del proyecto entre sesiones.
- `docs/DECISIONES.md` — ADR-006 a ADR-012, continuando la numeración del README.
- `docs/CONTEXTO_CONTINUIDAD.md` — bloque de arranque de sesión.
- `docs/CHANGELOG.md` — este archivo.

**Decidido**
- Métrica del piloto: orden de prioridad (ADR-006).
- Congelación y sello del ranking previos al orden experto (ADR-007).
- Separación entre sello local y sello certificado (ADR-008).
- Cadena de hashes por dispositivo (ADR-009).
- Correcciones como eventos, nunca ediciones (ADR-010).
- `origen` obligatorio e inmutable (ADR-011).
- Identidades fuera del asiento (ADR-012).

**Corregido en el contrato de continuidad**
- El slice 01 no incluye sincronización: sin Android no hay nada que sincronizar. Lo que
  sí se prueba es la ingesta idempotente.
- «El experto entra después del sello» exigía distinguir sello local de sello certificado.
- «Ningún ranking se sobrescribe» pasa a ser restricción de base de datos, no norma.
- «No ampliar hacia N2/N3» contradecía el estado N2 declarado en el README; la intención
  es no saltar a N4/N5.

**Pendiente**
- Vertical Slice 01 sin implementar.
- A-01 a A-05 abiertas.
- Incoherencias 1 a 3 del estado maestro sin resolver.

**Siguiente incremento**
- Vertical Slice 01: contrato de datos y migraciones, eventos append-only con firma
  Ed25519, ranking versionado y congelado, carga posterior del experto, comparación y
  exportación verificable. Todo con `origen = simulado`.
