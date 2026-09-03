# Modelo de datos sectorial — SUBER UAV

**Documento:** v0.1 · 2026-09-02
**Relación con el esquema implementado:** este documento describe el **dominio**; las
migraciones describen el **núcleo de evidencia**. No son lo mismo y no deben leerse como si
lo fueran. Ver ADR-026.

---

## 1. Por qué hay dos modelos

El núcleo construido no sabe qué es un alcornoque. Sus tablas se llaman `asset`, `detection`,
`review`, `seal`, `trace_event`, y esa neutralidad es deliberada: permite que las garantías de
inmutabilidad, encadenamiento y verificación se prueben una vez y valgan para cualquier
dominio. El precio de esa decisión es que **el vocabulario del sector no aparece en el
esquema**, y quien lea las migraciones buscando `cala` o `lote` no las encuentra y concluye,
con razón, que no existen.

Este documento pone las dos cosas una al lado de la otra y dice, entidad por entidad, qué está
construido y qué no. Es la sección que impide presentar un modelo de trece entidades como si
fuera una base de datos que existe.

## 2. Estado del modelo de dominio

| # | Entidad de dominio | Qué es | Soporte hoy en el núcleo | Estado |
|---|---|---|---|---|
| 1 | `forest_asset` | Finca, monte o rodal | Ninguno; `asset.unit_ref` es texto libre | **No existe** |
| 2 | `tree` | Alcornoque individual | `asset` con `asset_type` | **Parcial** |
| 3 | `uav_mission` | Vuelo: plan, aeronave, sensores, condiciones | Ninguno | **No existe** |
| 4 | `uav_observation` | Descriptor por árbol derivado del vuelo | `detection` (tiene `model_version`, `origen`) | **Parcial** |
| 5 | `field_observation` | Observación del técnico en campo | `review` con `review_type` | **Parcial** |
| 6 | `cork_sample` | La cala: ventana abierta y medida en la corteza | Ninguno | **No existe** |
| 7 | `quality_assessment` | Clase asignada: grueso, bueno, flaco, delgado, refugo | Ninguno | **No existe** |
| 8 | `sanitary_assessment` | Valoración sanitaria del pie | Ninguno | **No existe** |
| 9 | `harvest_event` | La saca de un árbol | `asset_status_event` (transición) | **Parcial** |
| 10 | `cork_lot` | Lote o pila que agrupa la saca de varios árboles | Ninguno | **No existe** |
| 11 | `industrial_process` | Proceso en fábrica sobre un lote | Ninguno | **No existe** |
| 12 | `industrial_result` | Resultado de ese proceso | Ninguno | **No existe** |
| 13 | `evidence_event` | Evento de evidencia encadenado y verificable | `trace_event` con `event_hash` | **Existe** |

**Recuento honesto: de trece entidades, una está construida, cuatro tienen soporte parcial y
ocho no existen.** La que está construida es la que sostiene a todas las demás, lo cual es el
orden correcto de construcción, pero no autoriza a decir que el modelo está implementado.

## 3. Detalle por entidad

Para cada una: los datos mínimos y qué hace falta para construirla. La regla común —
identificador propio, referencia espacial cuando aplique, momento, autoría, `origen`, y versión
de método cuando el dato lo produce un modelo— no se repite en cada fila.

### 3.1 `forest_asset` — monte o rodal
Datos mínimos: referencia catastral o equivalente, geometría del perímetro, titularidad
seudonimizada (`owner_ref`), régimen de gestión, turno de descorche declarado.
Bloqueado por: A-09 (titularidad del dato) y la incoherencia 4 (`owner_ref`).

### 3.2 `tree` — alcornoque
Datos mínimos: posición, marca física persistente, perímetro sobre corcho, altura de
descorche, año de la última saca, `forest_asset` al que pertenece.
Soporte actual: `asset` lo admite como `asset_type`, con estado por eventos y `sequence_no`.
Falta: los atributos dendrométricos y la marca física, que dependen de A-02.

### 3.3 `uav_mission` — vuelo
Datos mínimos: plan, aeronave, sensores y su calibración, altura y solape, condiciones de luz,
autorización de vuelo, operador.
Por qué importa: sin la misión, dos observaciones del mismo árbol en fechas distintas no son
comparables. Es la entidad que hace posible la serie temporal, y hoy no existe.

### 3.4 `uav_observation` — descriptor por árbol
Datos mínimos: `tree`, `uav_mission`, descriptores derivados, versión del extractor, calidad
del dato.
Soporte actual: `detection` cubre la parte de salida de modelo. Falta separar la **observación**
(dato derivado del vuelo) de la **detección** (salida de un modelo sobre esa observación); hoy
están mezcladas, lo que impide reprocesar un vuelo con un modelo nuevo sin duplicar el dato
bruto. `[REVISAR ANTES DE F2]`

### 3.5 `field_observation` — observación del técnico
Datos mínimos: `tree`, técnico (referencia seudonimizada), método, momento, contenido.
Soporte actual: `review` con `review_type`, y la regla de que `accepted_by_expert` exige sello
previo. Es el soporte más maduro de los parciales.

### 3.6 `cork_sample` — la cala
Datos mínimos: `tree`, protocolo aplicado y su versión, operador, posición de la ventana,
calibre, espesor, número de capas o años, humedad si se mide, laboratorio si lo hay.
Bloqueado por: A-06 (acuerdo de acceso al protocolo). Es la entidad más importante que falta,
porque es la que conecta el sistema con la metodología aceptada por el sector.
Regla: la cala **se referencia, no se infiere** (ADR-030).

### 3.7 `quality_assessment` — clase de calidad
Datos mínimos: `cork_sample` o `cork_lot` valorado, clase asignada, **escala y versión de la
escala**, **método**, **autoría**, momento.
Regla dura: `method` y `assessed_by` son obligatorios y no admiten un valor que signifique
«el sistema». Un modelo no puede firmar una clase de calidad (ADR-027). Esta restricción es lo
que hace que la frase prohibida del léxico sea imposible de sostener con el dato del sistema.
Bloqueado por: A-08 (qué escala se registra).

### 3.8 `sanitary_assessment` — valoración sanitaria
Datos mínimos: `tree`, indicador o síntoma observado, severidad en escala declarada, método,
autoría.
Regla: un indicador derivado de UAV se registra como **indicador**, con su versión de modelo, y
nunca como diagnóstico. El diagnóstico, si lo hay, lo firma una persona.

### 3.9 `harvest_event` — la saca
Datos mínimos: `tree`, cuadrilla, momento, altura de descorche efectiva, incidencias,
`cork_lot` de destino.
Soporte actual: la transición de estado del activo, ordenada por `sequence_no` y no por hora
(ADR-021). Falta el destino a lote y los datos de la operación.

### 3.10 `cork_lot` — lote o pila
Datos mínimos: conjunto de `harvest_event`, peso, ubicación de apilado, fecha, responsable.
Riesgo conocido (H-T1, D-02): el apilado es el eslabón débil de la cadena física. Un lote que
mezcla la saca de árboles no registrados rompe la trazabilidad aunque el software sea perfecto.

### 3.11 `industrial_process` — proceso en fábrica
Datos mínimos: `cork_lot` de entrada, tipo de proceso, fecha, planta, parámetros.
Bloqueado por: A-07 (interlocutor industrial).

### 3.12 `industrial_result` — resultado
Datos mínimos: `industrial_process`, rendimiento, distribución por clases, mermas, destino.
Bloqueado por: A-07. Es el eslabón que cierra la cadena monte→fábrica, y no llega antes de la
campaña de 2027 en el mejor de los casos.

### 3.13 `evidence_event` — evento de evidencia
Datos mínimos: dispositivo, `sequence_no`, `previous_trace_hash`, `event_hash`, `origen`,
contenido canonicalizado.
Soporte actual: **completo**. Cadena por dispositivo (ADR-009), encadenado del evento entero y
no solo del payload (ADR-018), canonicalización versionada con dos implementaciones (ADR-019),
protección append-only en tres capas incluida `TRUNCATE` (ADR-013, ADR-020), sello local y
anclaje separados (ADR-008, ADR-014), verificador externo independiente de la aplicación
(ADR-016).

## 4. Cómo se construye lo que falta

No todo a la vez, y no en el orden en que se lee la tabla. El orden que respeta las
dependencias reales:

| Orden | Qué | Desbloquea | Depende de |
|---|---|---|---|
| 1 | `uav_mission` + separar `uav_observation` de `detection` | Serie temporal y reproceso | Nada externo |
| 2 | `forest_asset` y atributos de `tree` | Inventario real | A-02, A-09 |
| 3 | `cork_sample` con protocolo versionado | Piloto 2 | **A-06** |
| 4 | `quality_assessment` y `sanitary_assessment` | Contraste sanitario y de calidad | A-06, A-08 |
| 5 | `cork_lot` y `harvest_event` completo | Piloto 3, campaña 2027 | A-02 |
| 6 | `industrial_process` y `industrial_result` | Cadena cerrada | **A-07** |

Los pasos 3 y 6 dependen de acuerdos con terceros que hoy no existen. Construirlos antes de
tener el acuerdo es escribir un esquema contra una metodología imaginada, que es exactamente el
error que este posicionamiento pretende evitar.

## 5. Regla de convivencia entre los dos modelos

La capa de dominio se construye **sobre** el núcleo, no dentro de él:

- Toda entidad de dominio que registre un hecho emite su `evidence_event` correspondiente. El
  núcleo sigue siendo el único sitio donde se define qué es inmutable.
- Ninguna entidad de dominio afloja una garantía del núcleo. Si una entidad necesitara
  `UPDATE`, se convierte en tabla de eventos, como ya ocurrió con el estado del activo
  (ADR-017).
- El vocabulario sectorial no se cuela en el núcleo. `asset` no pasa a llamarse `tree`: la
  neutralidad del núcleo es lo que permite probarlo una vez.
- `origen` es obligatorio también en el dominio. Un dato de cala simulado que pudiera pasar por
  real contaminaría precisamente aquello que da valor al sistema.
