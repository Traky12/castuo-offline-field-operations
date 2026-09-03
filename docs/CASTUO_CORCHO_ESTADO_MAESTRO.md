# CASTÚO-SYSTEM™ SUBER UAV — Estado maestro

**Nombre anterior:** UAV Cork / CASTÚO-CORCHO UAV. El nombre de producto cambia; el
identificador de capacidad, los archivos y la numeración ADR no (ADR-025).
**Capacidad de encuadre:** CAP-OFFLINE-FIRST-001 · Offline-First Field Operations
**Encaje:** fase y etapa **exterior** a la arquitectura de CASTÚO-SYSTEM (ADR-031). No es uno
de sus treinta y seis módulos. Entrega su resultado como paquete verificable, no como conexión.
**Estado de la capacidad (README):** N2 – Prepared · objetivo N5 – Pilot
**Estado del demostrador:** pre-prototipo, sin vuelos de ensayo.
Núcleo de evidencia implementado, endurecido y probado con datos sintéticos.
**Documento:** v0.8 · 2026-09-02
**Promotor:** CASTÚO-SYSTEM

Este archivo es la fuente de verdad del proyecto entre sesiones. Los artefactos enlazados al
final son capturas de trabajo; no sustituyen a este documento ni al repositorio.

Documentos que dependen de este: `POSICIONAMIENTO_SUBER.md` (discurso y léxico),
`MODELO_DATOS_SECTORIAL.md` (dominio), `PILOTOS.md` (los tres pilotos),
`INTERLOCUTORES.md` (encuadre por interlocutor), `SOBERANIA_DEL_DATO.md` (las seis propiedades
y su estado), `FORTALEZAS.md` (qué se puede afirmar y con qué nivel), `PROTOCOLO_INTEGRIDAD.md`
(estándar transversal de inmutabilidad y trazabilidad), `PLAN_CRECIMIENTO.md` (cómo crece
el proyecto: tres vías, UMD, escaleras y presupuesto de evidencia), `DECISIONES.md`,
`CHANGELOG.md`.

---

## 1. Qué es y qué no es

SUBER UAV es un **sistema de diagnóstico y trazabilidad digital del alcornocal**. Captura datos
de campo y de vuelo, ordena unidades por prioridad de intervención, registra cada operación de
forma auditable y contrasta lo que predice contra lo que ocurre. El dron es un sensor dentro de
esa infraestructura de evidencia, no el producto.

No forma parte de los treinta y seis módulos de CASTÚO-SYSTEM: es una etapa exterior, con
despliegue, calendario y frontera propios, que existe además para **demostrar en pequeño la
soberanía del dato** que la plataforma declara en grande (ADR-031, ADR-032). No es un producto
genérico de agricultura de precisión. No es un sustituto de la cala ni de la
observación del técnico. No asigna por su cuenta clases de calidad. Y no es una plataforma de corte aéreo: el
descorche lo siguen haciendo los corcheros a mano, y el sistema excluye por diseño cualquier
tecnología de corte energético —láser, plasma, chorro o análoga— sobre el alcornoque.

**Formulación aprobada, de uso obligatorio:** el sistema identifica indicadores de riesgo
sanitario y estima variables de interés productivo mediante datos UAV y observaciones de campo,
con revisión técnica y trazabilidad de la evidencia. El léxico completo, con la formulación
prohibida y su comprobación automática, está en `POSICIONAMIENTO_SUBER.md`, apartado 3.

## 2. Estado real

| Hecho | Estado |
|---|---|
| Prototipo | No existe |
| Núcleo de evidencia (inventario, detección, trazabilidad) | Implementado y endurecido; solo datos sintéticos |
| Ordenación de unidades y contraste ciego (gate G2 en seco) | Implementado; 284 pruebas verdes |
| Capa de captura: misión, observación y reidentificación (G1 en seco) | **Implementada** (UMD-1); solo datos sintéticos |
| API HTTP con autenticación por dispositivo | Implementada; provisional, sin rotación de credenciales |
| Léxico controlado comprobado automáticamente | Implementado (13 patrones, 22 pruebas) |
| Soberanía del dato (S-1…S-6) | 3 propiedades probadas, 2 sin auditar, 1 a medias |
| Catálogo de fortalezas con nivel de evidencia | 10 del preprototipo (8 en EQ3) · 7 comprometidas |
| Protocolo de integridad transversal | Redactado; 11 de 14 criterios con prueba automatizada |
| Catálogo de eventos E-01…E-11 | Semántico, no coercitivo: 3 de 11 implementados |
| Raíz de jornada sobre las cadenas de dispositivo | **No existe**; hace falta antes del anclaje real (UMD-3) |
| Modelo de dominio sectorial (13 entidades) | Documentado; 1 construida, 4 parciales, 8 ausentes |
| Anclaje RFC 3161 contra autoridad real | No probado — solo tokens sintéticos DER |
| Banco de sensores | En montaje |
| Vuelos de ensayo | Ninguno |
| Datos de campo validados | Ninguno |
| Autorización de vuelo | Ninguna |
| Parcela de validación comprometida | Pendiente (F0) |
| Acuerdo con CICYTEX, PTEcor, FUNDECYT o industria | **Ninguno.** Ni conversación formalizada |

Ninguna capacidad descrita en este documento está validada. Todo lo que aquí se afirma es
decisión de diseño o hipótesis, salvo donde se indique lo contrario de forma explícita.

## 3. Decisiones ya tomadas

| # | Decisión | Consecuencia principal |
|---|---|---|
| D-01 | Métrica del piloto: **orden de prioridad**, no kilos ni valor | El contraste se resuelve en el mes 6 y no en el 12 |
| D-02 | Vínculo físico: **árbol marcado → lote → pila** | Cero fricción en el tajo; el eslabón débil es el apilado |
| D-03 | Captura: **móvil Android offline + estación Pi 5** | Dos códigos que mantener; refleja el trabajo real |
| D-04 | Demostrador de marzo: **bucle completo con datos mixtos** | Exige el campo `origen` desde la primera migración |
| D-05 | Verificación por terceros mediante **sello RFC 3161**, no cadena propia | Verificable hoy; el anclaje en cadena queda como opción posterior |
| D-06 | Horizonte: **saca de junio–agosto de 2027** | El calendario del monte ordena las fases, no al revés |
| D-07 | Posicionamiento: **capa de diagnóstico y trazabilidad del alcornocal**, alineada con el Plan de Calas | El dron deja de ser el argumento; el activo es la serie y el vínculo |
| D-08 | El modelo sectorial es **capa de dominio**, no rediseño del núcleo (ADR-026) | Dos vocabularios que mantener; el núcleo probado no se toca |
| D-09 | La clase de calidad **se registra con método y autoría**, no se infiere (ADR-027) | La frase prohibida deja de ser sostenible con el propio dato |
| D-10 | **Tres pilotos escalonados**, cada uno con su gate (`PILOTOS.md`) | Cada reunión promete solo el piloto que está en curso |
| D-11 | **Fase exterior** a la arquitectura de CASTÚO-SYSTEM (ADR-031) | Aislamiento de fallo, cadencia propia, contrato explícito; cierra la incoherencia 3 |
| D-12 | La soberanía del dato se declara **desglosada en seis propiedades** (ADR-032) | Obliga a decir que tres de seis no están demostradas |
| D-13 | Ninguna fortaleza se enuncia por encima de su **nivel de evidencia** (ADR-033) | El discurso puede ser enérgico porque cada línea es comprobable |
| D-14 | **Protocolo de integridad transversal**; ante discrepancia manda el motor (ADR-034) | Reutilizable por SENDA y otras líneas; `origen` conserva sus tres valores |
| D-15 | **Tres vías en paralelo** —interna, de campo, de acuerdos— (ADR-035) | Las gestiones con terceros se abren el primer día, no las últimas |
| D-16 | **Escalera de carga útil P0–P3**, registrada en cada misión (ADR-036) | No se compra sensor caro antes de cerrar el gate del peldaño anterior |
| D-17 | **Ensayo en seco obligatorio** antes de cada gate de campo (ADR-037) | Perder una ventana del monte cuesta un año; el ensayo cuesta un guion |
| D-18 | **Deuda de evidencia** registrada con dueño y gate de vencimiento (ADR-038) | Se puede hablar del proyecto sin sobreafirmar y sin quedarse mudo |

## 4. Arquitectura actual

Cuatro capas. El detalle está en `POSICIONAMIENTO_SUBER.md`, apartado 5.

1. **Captura** — UAV (RGB, multiespectral, LiDAR), técnico en campo, cala, báscula, fábrica.
   Toda observación nace con `origen ∈ {real, simulado, historico}`, obligatorio e inmutable.
2. **CASTÚO Evidence Core** — FastAPI (Python 3.11) + PostgreSQL 16 en Hetzner CX22.
   Append-only en tres capas, cadena de hashes por dispositivo, canonicalización versionada,
   sello local y anclaje certificado separados, verificador externo independiente. **Es lo que
   está construido.** Es agnóstico del dominio a propósito.
3. **SABIONDA (análisis)** — indicadores, ordenación por prioridad, concordancia con el orden
   del técnico. Existe la ordenación determinista y las métricas; no existe ningún modelo
   entrenado con datos reales.
4. **Industria** — lote, proceso, resultado. **No existe.** Depende de A-07.

**Frontera con CASTÚO-SYSTEM:** un paquete verificable con formato declarado, no una conexión.
Ninguno de los dos lados escribe en el otro; los paquetes emitidos siguen verificándose aunque
cualquiera de los dos desaparezca. Es lo que impide que el dato de monte quede cautivo de la
plataforma que lo consume.

Borde: Raspberry Pi 5 como autoridad local de campo. Tajo: aplicación Android offline con
bandeja de salida (no construida). Entre vuelo y borde viajan descriptores por árbol, nunca
nubes de puntos densas. Trazalia: integración externa, fuera del camino crítico.

## 5. Alcance real del demostrador

Debe enseñar el bucle completo: **captura → ordenación → registro → contraste**. El vuelo y el
ranking deben ser reales cuando sea posible. Los datos históricos o simulados van etiquetados
visiblemente y no pueden presentarse como datos de saca real.

### Dos conceptos de sello — no confundirlos

| | Sello local | Sello certificado |
|---|---|---|
| Qué es | Hash del ranking + versión de modelo + hora de servidor | Token RFC 3161 de una autoridad de tiempo |
| Para qué | Bloquea la carga del orden experto | Permite verificación por un tercero |
| Cuándo | Slice 01 | Cuando el flujo local funcione |
| Formato | Campo propio en la exportación | Campo reservado desde el slice 01, relleno después |

### Dos objetivos que suenan igual — tampoco confundirlos

| | Trazabilidad | Correlación |
|---|---|---|
| Qué es | La cadena registrada y verificable | Que el monte explique el resultado de fábrica |
| Cuándo | Campaña 2027, gate G3 | Varias campañas `[POR MEDIR]` |

Ver ADR-028. Presentar la correlación como resultado del primer piloto es la sobreafirmación
más probable de este proyecto después de la frase prohibida del léxico.

## 6. Decisiones abiertas

| # | Decisión | Cierra en | Bloquea a |
|---|---|---|---|
| A-01 | Unidad de ordenación: árbol, rodal o unidad de pila | F0 | Diseño del móvil y viabilidad del dato real |
| A-02 | Tipo de marca física: chapa, QR sobre soporte o pintada | F0 | Piloto 1, supervivencia entre campañas |
| A-03 | Autoridad concreta de sellado RFC 3161, **obligatoriamente en la UE** (ADR-032) | Antes del sello real | S-5 de la soberanía |
| A-04 | Interfaz con Trazalia | F0, depende de tercero | Nada del camino crítico |
| A-05 | Protocolo de concordancia y umbral de aceptación | F0, pactado con el sector | El criterio de G2 |
| A-06 | Acuerdo de acceso al protocolo de calas y a series históricas | F0, depende de tercero | **Piloto 2**, `cork_sample` |
| A-07 | Interlocutor industrial para el resultado de fábrica | F3 | **Piloto 3**, cadena cerrada |
| A-08 | Escala de clases de calidad que se registra y su versión | F1 | `quality_assessment` |
| A-09 | Titularidad y licencia del dato de monte | F0, jurídico | Pitch a industria, `forest_asset` |
| A-10 | Forma de la captura de campo: aplicación nativa o web instalable, ambas offline | F1 | UMD-6, y con ella G3 |

A-05 se fija **antes** de mirar ningún dato. Fijarlo después es elegir el listón que uno acaba
de saltar.

## 7. Riesgos principales

| Cód. | Riesgo | Nivel | Se lee |
|---|---|---|---|
| H-I3 | El orden del sistema no concuerda con el del corchero experto | 3 | mes 6 |
| H-I4 | Ese orden no concuerda con lo que sale en la saca | 4 | mes 12 |
| H-D2 | El indicador multiespectral no aporta sobre el ojo del técnico | 4 | mes 10 |
| H-T2 | Trazalia no expone interfaz utilizable | 4 | mes 2–4 |
| H-T1 | El asiento por plancha estorba a la cuadrilla | 3 | mes 4 simulado / mes 11 real |
| H-S1 | No se consigue acuerdo sobre el protocolo de calas (A-06) | 4 | F0 |
| H-S2 | El discurso se sobreafirma en una reunión y se pierde credibilidad | 3 | en cualquier momento |

H-D2 merece una nota: que el indicador no aporte es un resultado válido del piloto y debe poder
publicarse. Por eso el valor del sistema se apoya en la trazabilidad, que no depende de que el
indicador funcione.

Riesgos de ingeniería, no de hipótesis: el Pi 5 se estrangula por temperatura en un vehículo en
julio; la nube de puntos de una jornada ocupa gigabytes y no sube durante la campaña; la pérdida
del Pi sin copia diaria cifrada perdería una campaña entera.

## 8. Calendario

| Fase | Cuándo | Cierre | Piloto |
|---|---|---|---|
| F0 Definición y protocolo | sep–oct 2026 | Protocolo firmado, parcela comprometida, respuesta de Trazalia, acuerdo A-06 | — |
| F1 Banco de sensores | nov 2026–ene 2027 | **G1** captura repetible | Piloto 1 |
| F2 Vuelo de contraste | feb–mar 2027 | **G2** concordancia sobre umbral | Piloto 2 |
| F3 Captura del «antes» | abr–may 2027 | Dato pre-saca verificado | — |
| F4 Campaña real | jun–ago 2027 | **G3** campaña registrada | Piloto 3 |
| F5 Análisis y contraste sectorial | sep–oct 2027 | Decisión sobre 2028 | Piloto 3 |

## 9. Criterios de aceptación vigentes

Un incremento no está terminado porque la demo funcione:

1. El ranking no se puede editar — restricción de base de datos, no norma de equipo.
2. El orden del experto solo entra después del sello local.
3. El modelo que produjo cada ranking queda identificado por versión.
4. Los datos simulados aparecen marcados como simulados en salida y exportación.
5. Un cambio posterior sobre el ranking congelado rompe la verificación del hash.
6. Los reintentos de ingesta no duplican eventos (idempotencia por UUID).
7. Las correcciones no eliminan el dato original (evento E-11).
8. La exportación se puede verificar fuera de la aplicación.
9. Las pruebas automatizadas reproducen todo el flujo — nivel EQ3 del README.
10. Ningún documento publicable contiene una formulación prohibida del léxico, y el
    comprobador falla si alguien la introduce.
11. Ninguna fortaleza aparece enunciada por encima de su nivel de evidencia EQ1–EQ6, y las del
    prototipado van con su gate, no en presente.
12. Los catorce criterios del protocolo de integridad se cumplen o están marcados como
    `[POR PROBAR]` / `[POR CONSTRUIR]`; ninguno se da por bueno sin prueba.
13. Ningún gate se intenta en campo sin que su ensayo en seco pase con datos sintéticos.
14. Una misión con `origen = 'real'` sin referencia de autorización de vuelo es rechazada
    por el motor.

## 10. Qué no se construye todavía

Decisión automática · corte físico · láser de potencia · nodo blockchain propio · facturación ·
multiusuario avanzado · cualquier cifra de exactitud, ahorro o rendimiento no validada ·
integración con Trazalia · aplicación Android · las ocho entidades de dominio ausentes, salvo
las que el incremento en curso necesite · cualquier modelo de correlación monte–fábrica.

## 11. Incremento en curso

**UMD-1 · capa de captura — COMPLETADA.** Misión de vuelo, observación separada de la
detección, reidentificación entre misiones y ensayo en seco del gate G1. Cierra la
incoherencia 6. ADR-035…ADR-038 y `PLAN_CRECIMIENTO.md`. 284 pruebas verdes.

**Protocolo de integridad transversal — COMPLETADO.** Estándar único de inmutabilidad y
trazabilidad, con cuatro estados por afirmación, bloque de alcance, tabla de riesgos, tabla de
evidencia software/campo y catorce criterios de aceptación. Reutilizable por SENDA. ADR-034.

**Encaje exterior y catálogo de fortalezas — COMPLETADO.** SUBER UAV queda declarado fase
exterior a la arquitectura de CASTÚO-SYSTEM, con la soberanía del dato desglosada en seis
propiedades de estado individual y un catálogo de fortalezas donde ninguna se enuncia por encima
de su nivel de evidencia. ADR-031…ADR-033.

**Evolución documental del posicionamiento — COMPLETADA.** El corpus queda alineado con el
encuadre sectorial: posicionamiento y léxico, modelo de dominio con su estado real entidad por
entidad, tres pilotos con sus gates, encuadre por interlocutor, y el léxico bajado de norma
escrita a prueba automatizada. ADR-025…ADR-030. 265 pruebas verdes.

**Sigue fuera:** Android, sincronización móvil→Pi, Trazalia, nodo blockchain, sello certificado
real, panel web, autonomía del dron, y las entidades de dominio ausentes.

**Siguiente incremento recomendado:** el **anclaje RFC 3161 contra una autoridad real**. Es
barato, cierra la única pieza del núcleo que sigue sin probarse contra el mundo, y convierte
«verificable por un tercero» en un hecho comprobable en lugar de una decisión de diseño. El
panel de cuatro vistas puede esperar: enseña lo que ya existe, no cierra ningún riesgo.

**Alternativa si el calendario del monte aprieta:** `uav_mission` y la separación de
`uav_observation` respecto a `detection`, que es el paso 1 del orden de construcción del
modelo sectorial y no depende de ningún tercero.

## 12. Historial de cambios

| Fecha | Cambio |
|---|---|
| 2026-09-01 | Creación del estado maestro. Recogidas D-01…D-06 y A-01…A-05. Definido el Vertical Slice 01. |
| 2026-09-01 | Slice 01 implementado y probado. ADR-013…ADR-016. Abierta la incoherencia 4 (owner_ref). |
| 2026-09-01 | Endurecimiento: TRUNCATE, estado por eventos, event_hash, canonicalización versionada. ADR-017…ADR-020. 205 pruebas. |
| 2026-09-02 | Contraste ciego y API HTTP. ADR-021…ADR-024. 245 pruebas. |
| 2026-09-02 | Reposicionamiento sectorial: SUBER UAV, léxico comprobado, modelo de dominio, tres pilotos, interlocutores. ADR-025…ADR-030. D-07…D-10, A-06…A-09. 258 pruebas. |
| 2026-09-02 | Encaje como fase exterior y soberanía del dato desglosada. Catálogo de fortalezas por nivel de evidencia. ADR-031…ADR-033. D-11…D-13. Incoherencia 3 resuelta. |
| 2026-09-02 | Protocolo de integridad transversal (SUBER UAV + SENDA). ADR-034, D-14, criterio 12. Incoherencias 9–11. |
| 2026-09-02 | Plan de crecimiento y UMD-1: misión, observación, reidentificación, G1 en seco. ADR-035…ADR-038. D-15…D-18, A-10, criterios 13–14. Incoherencia 6 cerrada. |

## 13. Artefactos de referencia

Capturas de trabajo, no fuente de verdad. Los tres primeros son **anteriores al
reposicionamiento** y describen el proyecto con el encuadre antiguo:

- Documento base del sistema — https://claude.ai/code/artifact/7963d913-8c59-4296-b675-c3be2e4e39c3 `[DESACTUALIZADO]`
- Plan de validación «Ruta a la saca 2027» — https://claude.ai/code/artifact/464c4ff3-9793-4130-97b6-38aa6720e3a5 `[PARCIALMENTE VIGENTE: el calendario sí, el encuadre no]`
- Diseño de sistema del demostrador — https://claude.ai/code/artifact/b99f948a-1004-48fc-869b-98db3d07ce3f `[PARCIALMENTE VIGENTE]`
- **Documento de posicionamiento SUBER UAV** (propuesta de valor · arquitectura funcional ·
  piloto mínimo viable) — https://claude.ai/code/artifact/5e4ca2a4-af64-4dcc-8c5c-1268d0914a71 `[VIGENTE]`
  Fuente en el repositorio: `docs/publicaciones/suber-uav-dossier.html`, cubierta por el
  comprobador de léxico.
- **Protocolo de Integridad Técnica** (estándar transversal SUBER UAV + SENDA) — https://claude.ai/code/artifact/39d460dd-e146-49a7-90bf-07ab4aaefd13 `[VIGENTE]`
  Fuente: `docs/PROTOCOLO_INTEGRIDAD.md` y `docs/publicaciones/protocolo-integridad.html`.
- **Plan de crecimiento SUBER UAV** (tres vías, UMD, escaleras, presupuesto de evidencia)
  — https://claude.ai/code/artifact/7fa32bbe-7aba-4c35-8c92-2efdff347af8 `[VIGENTE]`
  Fuente: `docs/PLAN_CRECIMIENTO.md` y `docs/publicaciones/plan-crecimiento.html`.

## 14. Incoherencias detectadas y pendientes de resolver

1. **Escala N.** El README declara `Status: N2 – Prepared` y sitúa N3 en «Implementation
   Complete». Cualquier instrucción de «no ampliar hacia N2/N3» prohibiría el propio slice. La
   intención correcta es **no saltar a N4 (validación de integración) ni a N5 (piloto de
   campo)**. `[POR CONFIRMAR]`
2. **Idioma.** El README está en inglés; estos documentos, en español. Conviene decidir uno
   para el repositorio. `[POR DECIDIR]`
3. **Encaje. `[RESUELTA en v0.6]`** SUBER UAV es una fase **exterior** a la arquitectura de
   CASTÚO-SYSTEM (ADR-031, D-11): despliegue, calendario y frontera propios, y entrega por
   paquete verificable. Sigue viviendo en el repositorio de la capacidad por comodidad, no por
   encaje; la mudanza a repositorio propio, con el renombrado que ADR-025 aplaza, se hace cuando
   convenga y ya no bloquea nada. `[MUDANZA PENDIENTE, SIN BLOQUEO]`
4. **`owner_ref` y protección de datos.** Tipado como `uuid` para forzar una referencia
   seudonimizada, pero nada impide hoy que apunte a una tabla con datos personales. El análisis
   jurídico de qué puede referenciar, con qué base legal y con qué retención sigue abierto, y
   ahora se cruza con A-09 (titularidad del dato de monte).
   `[PENDIENTE: revisión jurídica]`
5. **Dos vocabularios.** «UAV Cork» en archivos y ADR antiguos, «SUBER UAV» en el discurso;
   `asset`/`detection` en el esquema, `tree`/`cork_sample` en el dominio. Es deuda de
   nomenclatura aceptada a conciencia (ADR-025, ADR-026), no contradicción. Se paga en la
   mudanza de la incoherencia 3. `[ACEPTADO]`
6. **`detection` mezclaba observación y salida de modelo. `[RESUELTA en v0.8]`** Existen
   `uav_mission` y `uav_observation`, y `detection.observation_id` apunta a la observación de
   la que salió, de modo que reprocesar un vuelo con un modelo nuevo no duplica el dato bruto.
   Las detecciones anteriores conservan el vínculo por `evidence_id`; migrarlas no procede
   mientras solo existan datos sintéticos. `[SIN BLOQUEO]`
7. **El Piloto 3 nombra pasos que están excluidos del alcance.** Necesita la aplicación Android
   y un interlocutor industrial. No es un incremento de la fase actual: lo que corresponde ahora
   es el modelo de datos y el protocolo de custodia, probables con datos sintéticos.
   `[RESUELTO en PILOTOS.md; anotado aquí para que no se redescubra]`
8. **La cifra 50–100 árboles no procede de un cálculo de potencia.** Es el tamaño con el que se
   puede verificar el rodal a pie. El tamaño de muestra necesario para sostener una afirmación
   estadística se calcula en F0 con el protocolo. `[POR CALCULAR EN F0]`
9. **Raíz de jornada declarada, cadena por dispositivo implementada.** El protocolo describe una
   raíz que agrupa todas las cadenas de la jornada para anclarla una sola vez; el motor encadena
   por dispositivo y no computa esa raíz. Son compatibles, pero la raíz es trabajo nuevo y hace
   falta **antes** del primer anclaje real. `[POR CONSTRUIR ANTES DE F2]`
10. **El catálogo E-01…E-11 es semántico, no coercitivo.** Nada en el motor rechaza un evento que
   no encaje en el catálogo. Bajarlo de nivel exigiría un campo de tipo de evento con conjunto
   cerrado; conviene decidirlo antes de que existan eventos reales que reclasificar.
   `[POR DECIDIR ANTES DE F1]`
11. **El protocolo deja fuera al propietario de las tablas y al superusuario.** Las capas 1 y 2 no
   alcanzan a quien tenga acceso administrativo directo. Reducir esa superficie es plan de
   despliegue, no de esquema. `[PENDIENTE: endurecimiento operativo]`
