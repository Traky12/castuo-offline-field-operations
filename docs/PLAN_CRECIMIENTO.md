# Plan de crecimiento — preprototipo y prototipado UAV

**Documento:** v0.1 · 2026-09-02
**Qué es:** el plan de cómo crece SUBER UAV desde el núcleo probado hasta el piloto de campo,
y los conceptos que hacen que ese crecimiento sea comprobable en lugar de opinable.
**Qué no es:** un calendario. El calendario está en el estado maestro y manda él.
**Depende de:** `CASTUO_CORCHO_ESTADO_MAESTRO.md`, `PROTOCOLO_INTEGRIDAD.md`,
`FORTALEZAS.md`, `PILOTOS.md`.

---

## 0. El problema que este plan resuelve

El preprototipo tiene un núcleo probado y una lista larga de cosas que faltan. Esa lista
mezcla tres cosas de naturaleza muy distinta: trabajo que depende solo de nosotros, trabajo
que depende de que haya una parcela y un aparato volando, y trabajo que depende de que un
tercero diga que sí. Tratadas como una sola lista, la primera se retrasa esperando a la
tercera, y el proyecto pasa meses sin producir evidencia mientras espera una reunión.

Este plan separa esas tres cosas, define la unidad con la que se avanza y fija cómo se mide
el avance. Los conceptos que introduce están en el apartado 2; algunos ya existen en el
motor, y se dice cuáles.

## 1. Regla de ordenación

> **Se construye primero lo que sube más el nivel de evidencia por unidad de coste, y
> dentro de eso, primero lo que no depende de nadie externo.**

Consecuencia inmediata y contraintuitiva: el panel de cuatro vistas, que enseña bien, va
después que la raíz de jornada, que no enseña nada. El panel muestra evidencia que ya
existe; la raíz produce evidencia que no existe.

Segunda consecuencia: las gestiones que dependen de terceros **se abren el primer día**,
aunque su resultado no llegue hasta dentro de meses. No son la última tarea del plan: son la
primera, precisamente porque no las controlamos.

## 2. Conceptos que introduce este plan

| Concepto | Qué es | Dónde vive |
|---|---|---|
| **Las tres vías** | Interna, de campo y de acuerdos, avanzando **en paralelo** y nunca en serie | Apartado 3 |
| **UMD — Unidad Mínima Demostrable** | El incremento con el que se crece: atraviesa el sistema de punta a punta y deja evidencia | Apartado 4 |
| **Ensayo en seco obligatorio** | Ningún gate se intenta en campo antes de que su ensayo con datos sintéticos pase | Apartado 5 · `scripts/demo_gate_*.py` |
| **Escalera de carga útil (P0–P3)** | La carga del UAV sube de peldaño solo cuando el anterior ha producido descriptores utilizables | Apartado 6 · tipo `payload_t` en el motor |
| **Escalera de identidad (I0–I3)** | Cómo se identifica un pie, de menos a más compromiso | Apartado 7 · cierra A-02 por peldaños |
| **Presupuesto de evidencia** | Cada gate declara *antes* qué evidencia y de qué nivel EQ tiene que producir | Apartado 8 |
| **Deuda de evidencia** | Registro de afirmaciones por encima de su nivel, con dueño y gate de vencimiento | Apartado 9 |
| **Ventanas del monte** | El calendario se ancla a ventanas biológicas y legales, no a meses de oficina | Apartado 10 |
| **Índice de preparación de gate** | Lista de condiciones que deben estar verdes antes de intentar un gate | Apartado 11 |

Tres de ellos ya son mecanismo y no norma: la escalera de carga útil es un tipo cerrado en
el esquema, el ensayo en seco es un guion ejecutable con código de salida, y el presupuesto
de evidencia se apoya en la escala EQ que el README ya define.

## 3. Las tres vías

Avanzan a la vez. Un plan que las pone en serie es un plan que no empieza.

### Vía A — interna (no depende de nadie)

Trabajo de ingeniería sobre el núcleo y la capa de captura. Es la única vía con cadencia
predecible, y por eso es la que sostiene el proyecto mientras las otras dos maduran.
Contiene UMD-1 a UMD-6.

### Vía B — de campo (depende de un aparato y un sitio)

Banco de sensores, vuelos de ensayo, marca física, autorización. Empieza en cuanto haya
parcela; hasta entonces, su parte simulable se hace en la vía A. **Regla:** todo lo que se
puede ensayar en seco se ensaya en seco antes de gastar una jornada de campo.

### Vía C — de acuerdos (depende de que alguien diga que sí)

Parcela comprometida, técnico participante, acceso al protocolo de muestreo (A-06),
interlocutor industrial (A-07), autoridad de sellado en la UE (A-03), revisión jurídica de
titularidad (A-09). **Se abre el primer día.** El tiempo de respuesta de un tercero no se
comprime trabajando más.

Punto de encuentro: la vía C desbloquea gates que la vía A ya sabe ejecutar en seco. Cuando
llega el «sí», el software no debería tener que construirse: debería tener que conectarse.

## 4. Unidades mínimas demostrables

Una UMD atraviesa el sistema de punta a punta, por fina que sea, y deja evidencia
comprobable. Una capa horizontal completa —«todas las migraciones»— no es una UMD.

| # | Qué recorre | Vía | Depende de | Evidencia que deja | Estado |
|---|---|---|---|---|---|
| **UMD-1** | Misión → observación → reidentificación → veredicto G1 en seco | A | — | EQ3: pruebas de misión, observación y métrica; ensayo ejecutable | **Hecha** |
| UMD-2 | Protocolo de reidentificación registrado antes del dato → resultado sellado → paquete verificable | A | UMD-1 | EQ3 sobre G1 completo, con la tolerancia congelada antes de mirar | Siguiente |
| UMD-3 | Raíz de jornada sobre las cadenas de dispositivo | A | — | EQ3; cierra el criterio 13 del protocolo | Siguiente |
| UMD-4 | Anclaje RFC 3161 contra autoridad real de la UE | A + C | A-03, UMD-3 | EQ2 y camino a EQ5; cierra el criterio 12 y S-5 | Tras UMD-3 |
| UMD-5 | Credenciales: rotación, caducidad y revocación auditada | A | — | EQ3; cierra el criterio 14 | Tras UMD-4 |
| UMD-6 | Captura de campo mínima, offline, con bandeja de salida | A | **decisión abierta** | EQ3 de ingesta desde dispositivo | A-10 |
| UMD-7 | `forest_asset` + atributos de `tree` + identidad I1 | A + B | A-01, A-02 | EQ3, y prerrequisito de G1 real | Tras UMD-2 |
| UMD-8 | `cork_sample` con protocolo versionado | A + C | **A-06** | Prerrequisito de G2 | Bloqueada |
| UMD-9 | `harvest_event` completo + `cork_lot` | A + B | A-02, UMD-6 | Prerrequisito de G3 | Tras UMD-6 |
| UMD-10 | `industrial_process` + `industrial_result` | A + C | **A-07** | Cierra la cadena de custodia | Bloqueada |

Las dos bloqueadas lo están por acuerdos, no por ingeniería. Adelantarlas construyendo un
esquema contra una metodología imaginada es el error que más caro sale: se descubre roto
justo cuando llega el dato real.

### UMD-1, en detalle (hecha en este bloque)

Separa la observación de la detección —la incoherencia 6, abierta desde el reposicionamiento—
y añade la entidad sin la cual no hay serie temporal:

- `uav_mission`: aeronave, carga útil, altura, solape, operador opaco, versión de extractor,
  y **referencia de autorización de vuelo obligatoria cuando `origen = 'real'`**, comprobada
  por el motor. Un vuelo real registrado sin autorización sería una evidencia que no se
  puede enseñar a nadie.
- `uav_observation`: el descriptor por unidad, con `asset_id` **anulable** — vincular la
  observación a un árbol conocido es precisamente lo que G1 tiene que demostrar, y forzarlo
  en la inserción daría por supuesto el resultado.
- `detection.observation_id`: la detección pasa a poder apuntar a la observación de la que
  salió, de modo que reprocesar un vuelo con un modelo nuevo no duplica el dato bruto.
- `reidentificacion.py`: emparejamiento por **vecino mutuo más cercano** dentro de una
  tolerancia obligatoria y sin valor por defecto. Mutuo y no «el más cercano de A en B»,
  porque el segundo criterio no es simétrico y una métrica que cambia al invertir los
  argumentos no vale para un gate.
- `scripts/demo_gate_g1.py`: el ensayo en seco, con semilla fija.

Lo que UMD-1 **no** demuestra: que un vuelo real produzca esos descriptores. Solo que el
sistema sabe registrar dos misiones, compararlas y emitir un veredicto reproducible.

## 5. Ensayo en seco obligatorio

> Ningún gate se intenta en campo antes de que su ensayo en seco pase con datos sintéticos.

No es prudencia: es aritmética. Una jornada de campo cuesta desplazamiento, aparato, permiso
y, en el caso de G2, el tiempo de un técnico. Descubrir en esa jornada que el sistema no sabe
registrar dos misiones del mismo rodal es gastar todo eso para aprender algo que un guion de
cien líneas dice gratis.

| Gate | Ensayo en seco | Estado |
|---|---|---|
| G1 captura repetible | `scripts/demo_gate_g1.py` | **Existe** |
| G2 concordancia | `scripts/demo_gate_g2.py` | **Existe** |
| G3 campaña registrada | `scripts/demo_gate_g3.py` | No existe · UMD-9 |

El ensayo en seco tiene además un efecto secundario valioso: obliga a fijar el método antes
de tener el dato, que es la misma disciplina que sostiene el contraste ciego.

## 6. Escalera de carga útil

El UAV no empieza con el sensor más caro. Cada peldaño se justifica con lo que produjo el
anterior.

| Peldaño | Carga | Qué tiene que demostrar para justificar el siguiente |
|---|---|---|
| **P0** | RGB | Que se extraen posiciones y descriptores por pie, y que G1 pasa |
| **P1** | RGB + posicionamiento preciso | Que la tolerancia de reidentificación baja lo suficiente para que la serie temporal sea útil |
| **P2** | Multiespectral | Que el indicador aporta sobre la observación del técnico (es F-17, y puede salir en negativo) |
| **P3** | LiDAR | Que las variables dendrométricas estimadas mejoran de forma medible lo que ya daba P0–P2 |

Está en el motor como tipo cerrado (`payload_t`) y viaja en cada misión. Subir un peldaño es
una decisión registrada, no un ajuste de configuración que nadie recuerda haber hecho.

**Lo que esta escalera evita:** comprar LiDAR antes de haber demostrado que se puede
reencontrar un árbol. Es el gasto que más fácil se justifica en una reunión y el que más
probablemente se queda sin usar.

## 7. Escalera de identidad del árbol

A-02 —tipo de marca física— está planteada como una decisión única. Es mejor una escalera:
cada peldaño es utilizable por sí solo y el siguiente solo se paga si el anterior se queda
corto.

| Peldaño | Cómo se identifica un pie | Qué cuesta | Qué falla |
|---|---|---|---|
| **I0** | Solo posición | Nada | Se rompe con la deriva del posicionamiento y con pies juntos |
| **I1** | Posición + marca pasiva duradera | Barato, una campaña de marcado | Hay que leerla a mano |
| **I2** | Marca legible por el móvil en el tajo | Medio | Depende de la aplicación de campo (UMD-6) |
| **I3** | Marca legible desde el aire | Alto y no resuelto | Puede no sobrevivir a la saca |

**Criterio de subida:** se sube de peldaño cuando el ensayo de G1 con el peldaño actual no
alcanza la tolerancia pactada, no antes. Y el peldaño que se elija tiene que sobrevivir al
descorche: una marca que se va con la corteza rompe la continuidad justo en el momento que
más importa (riesgo R-08).

## 8. Presupuesto de evidencia

Cada gate declara **antes de empezar** qué evidencia debe producir y de qué nivel, con la
escala EQ1–EQ6 del README. Sin esto, «el gate ha ido bien» es una opinión.

| Gate | Evidencia que debe producir | Nivel de destino |
|---|---|---|
| **G1** | Dos misiones registradas, reidentificación por encima de tolerancia pactada, paquete verificable de ambas | EQ4 |
| **G2** | Orden del sistema sellado antes del orden del técnico, concordancia sobre umbral registrado antes del dato | EQ4, con vista a EQ5 |
| **G3** | Cada lote industrial resuelto hasta sus pies, verificación del paquete sin fallo | EQ4 |

Y una regla que evita el autoengaño: **un gate no se da por superado con evidencia de nivel
inferior al declarado.** Si G1 solo produce capturas de pantalla (EQ1), G1 no está superado
aunque todo el mundo haya visto que funciona.

## 9. Deuda de evidencia

El equivalente de la deuda técnica, para afirmaciones. Toda frase publicada por encima de su
nivel de evidencia es deuda: se registra, tiene dueño y vence en un gate.

| # | Afirmación | Nivel real | Nivel al que se enuncia | Vence en |
|---|---|---|---|---|
| DE-01 | Los pesos de la ordenación (0,6 / 0,4) | decisión de diseño | se citan como si estuvieran calibrados | G2 |
| DE-02 | La operación funciona sin cobertura | decisión de diseño | se presenta como capacidad | G1 |
| DE-03 | Residencia europea | sin auditar | se presenta como propiedad | F2–F3 |
| DE-04 | Catálogo E-01…E-11 | 3 de 11 implementados | se lee como taxonomía vigente | F1 |

La deuda de evidencia no es mala por existir: un preprototipo la tiene por definición. Es
mala cuando no está registrada, porque entonces nadie sabe cuánta hay ni cuándo vence. El
comprobador de léxico atrapa las formulaciones más peligrosas, pero no distingue matices de
nivel: para eso está esta tabla.

## 10. Ventanas del monte

El calendario no lo fija la oficina. Estas son las ventanas conocidas y las que faltan por
fijar:

| Ventana | Cuándo | Estado |
|---|---|---|
| Saca | junio–agosto | Conocida (D-06) |
| Cala previa a la saca | antes de la saca de ese año | Depende de A-06 |
| Vuelo con condiciones comparables entre campañas | `[POR FIJAR EN F0]` | Sin fijar |
| Ventana fenológica útil para el indicador multiespectral | `[POR FIJAR EN F0, con criterio técnico]` | Sin fijar |

Consecuencia dura: **perder una ventana cuesta un año.** Por eso la vía C se abre el primer
día, y por eso el ensayo en seco es obligatorio: llegar a la ventana con el software sin
probar es perder la ventana.

## 11. Índice de preparación de gate

Antes de intentar un gate en campo, estas condiciones deben estar verdes. Si alguna está en
rojo, el gate se aplaza; intentarlo igual produce una jornada perdida y, peor, un resultado
ambiguo que nadie sabe interpretar después.

**Para cualquier gate:** ensayo en seco pasando · presupuesto de evidencia declarado ·
umbral o tolerancia registrados antes de mirar el dato · `origen` correctamente configurado
· copia de seguridad de la jornada prevista · quién firma cada observación, decidido.

**Añadido para G1:** parcela comprometida · autorización de vuelo · peldaño de identidad
elegido y marcado hecho · dos ventanas de vuelo comparables reservadas.

**Añadido para G2:** técnico comprometido · protocolo de muestreo acordado (A-06) · escala
de clases acordada (A-08) · el orden del sistema sellado **antes** de que el técnico vea
nada.

**Añadido para G3:** interlocutor industrial (A-07) · captura de campo operativa · marca
que sobrevive a la saca · custodia del lote definida de punta a punta.

## 12. Señales de que el crecimiento es sano — y de que no

**Sano:** cada bloque de trabajo sube al menos una afirmación de nivel de evidencia; la
deuda de evidencia baja o al menos no crece sin registrarse; los ensayos en seco se escriben
antes que el trabajo de campo; las decisiones abiertas se cierran o se reasignan, no se
arrastran calladas.

**No sano:** el número de pruebas crece pero ninguna afirmación sube de nivel; se construyen
entidades del dominio bloqueadas por acuerdos que no llegan; aparece una capacidad nueva en
una presentación antes que en el catálogo de fortalezas; se sube un peldaño de carga útil sin
que el anterior haya cerrado su gate; una decisión abierta cambia de fecha por tercera vez sin
que nadie lo registre.

Esa última merece atención: una decisión abierta que se aplaza tres veces no es una decisión
pendiente, es una decisión tomada por omisión.

## 13. Lo que no acelera el proyecto

Escrito aquí para no tener que discutirlo cada dos meses:

- **Más sensores.** El cuello de botella no es la resolución: es que no hay parcela ni
  técnico.
- **Un modelo más grande.** Sin dato de campo etiquetado, un modelo mejor produce mejores
  números sobre datos simulados.
- **Adelantar la integración industrial.** Sin trazabilidad demostrada en campo, la cadena no
  tiene qué transportar.
- **El panel de cuatro vistas.** Enseña lo que ya existe; no cierra ningún riesgo. Va después
  de UMD-3 y UMD-4.
- **Renombrar el repositorio.** Cambio enorme, valor funcional nulo (ADR-025).
- **Blockchain.** Ya decidido: aporta dependencia y no aporta verificabilidad que el sello y
  el verificador externo no den ya (D-05).
- **Una demostración más.** Ya hay dos ensayos en seco. La tercera demostración con datos
  sintéticos no convence a nadie que no estuviera ya convencido.

## 14. Qué se hace exactamente a continuación

En orden, y con la razón de que sea ese orden:

1. **UMD-2** — protocolo de reidentificación registrado antes del dato y resultado de G1
   sellado. Convierte el ensayo en seco en evidencia persistida y reutiliza la maquinaria de
   contraste que ya existe. No depende de nadie.
2. **UMD-3** — raíz de jornada. Es lo que da un único valor que sellar y cierra el criterio
   13 del protocolo.
3. **UMD-4** — anclaje contra autoridad real de la UE. Cierra el criterio 12 y la propiedad
   S-5 de la soberanía, y es la única pieza del núcleo sin contrastar con el mundo.
4. **En paralelo, desde hoy:** abrir A-03 (autoridad de sellado), A-06 (protocolo de
   muestreo), A-01 y A-02 (unidad de ordenación e identidad), y la búsqueda de parcela.

Nada de la lista 1–3 espera a la lista 4, y nada de la lista 4 espera a la 1–3. Ese es todo
el plan.
