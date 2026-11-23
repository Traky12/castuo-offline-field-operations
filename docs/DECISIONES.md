# Decisiones de arquitectura — UAV Cork

Continúa la numeración ADR del README (ADR-001…ADR-005 ya están tomadas allí:
offline-first, Evidence Center como fuente de verdad, IA local antes que en nube,
OpenStreetMap, Meshtastic).

Formato: contexto, decisión, consecuencias, y cuándo conviene revisarla.

---

## ADR-006 — La métrica del piloto es un orden de prioridad

**Contexto.** Estimar kilos de corcho exige dos saltos encadenados: de la nube de puntos
a la superficie de pela, y de la superficie al peso, que depende de calibre, espesor y
humedad — variables que ningún sensor aéreo observa. Solo se valida contra báscula, es
decir en la saca, a doce meses vista.

**Decisión.** El sistema estima un **orden de prioridad** por unidad de ordenación.

**Consecuencias.** Elimina el segundo salto y afloja el primero. Permite contrastar contra
el criterio de un corchero experto sin esperar a que se pele un árbol: el momento de la
verdad pasa del mes 12 al mes 6. Un ranking además sobrevive a la pérdida de resolución
del sensor; una estimación de kilos no.

**Revisar.** Si tras G3 la concordancia es alta y aparece demanda de estimación absoluta.

## ADR-007 — El ranking se congela y se sella antes de conocer el orden experto

**Contexto.** Todo el valor del demostrador depende de un contraste ciego. Sin una
garantía técnica, la única respuesta a «¿ajustasteis el modelo después de ver al corchero?»
es la buena fe, que no es una respuesta.

**Decisión.** El ranking se emite, se congela y se sella —hash del contenido, versión del
modelo y hora— **antes** de que el orden del experto pueda entrar en el sistema. Ninguna
versión de ranking se sobrescribe. Una ejecución posterior nace como versión nueva.

**Consecuencias.** La restricción vive en la base de datos, no en el código de aplicación:
un `UPDATE` sobre un ranking congelado se rechaza, y la inserción del orden experto exige
que exista sello previo. Reajustar el modelo sigue siendo legítimo, pero produce una v2 con
fecha posterior. La trampa no se prohíbe: se vuelve visible.

**Revisar.** Nunca mientras el contraste ciego sea el argumento central del proyecto.

## ADR-008 — Sello local y sello certificado son cosas distintas

**Contexto.** El criterio «el experto entra después del sello» se necesita desde el primer
incremento, pero integrar una autoridad de tiempo real no es prioritario hasta que el flujo
local funcione.

**Decisión.** Dos conceptos separados. El **sello local** (hash + versión de modelo + hora
de servidor) es lo que bloquea la carga del experto y existe desde el slice 01. El **sello
certificado** (token RFC 3161) se adjunta después. La exportación reserva el hueco del
token desde el principio.

**Consecuencias.** Añadir el sello certificado más tarde no toca el esquema ni invalida los
paquetes ya exportados. A cambio, hasta que llegue, la hora del sello local depende del
reloj del servidor: sirve internamente, no ante un tercero.

**Revisar.** Al elegir autoridad de sellado (A-03).

## ADR-009 — Cadena de hashes por dispositivo, no global

**Contexto.** Una cadena única exige orden total entre todos los emisores, y el orden total
exige coordinación en línea. En el monte no la hay.

**Decisión.** Cada dispositivo mantiene su propia cadena mediante `prev_hash`. La
correlación entre cadenas se establece por sello, no por secuencia.

**Consecuencias.** Un móvil sin cobertura escribe válidamente durante toda una jornada. A
cambio, no existe un orden estricto entre cuadrillas distintas.

**Revisar.** Si aparece un requisito de secuencia estricta entre equipos.

## ADR-010 — Las correcciones son eventos, no ediciones

**Contexto.** Un asiento auditable y un dato editable son incompatibles.

**Decisión.** Ningún evento se modifica ni se borra. Una corrección es un evento nuevo
(E-11) que referencia al erróneo. El estado de un lote se deriva plegando sus eventos.

**Consecuencias.** El auditor ve el error y la corrección. Un registro sin correcciones no
es un registro limpio: es un registro editado. Como efecto lateral, la sincronización no
tiene conflictos que resolver — no hay dos escritores compitiendo por una fila.

**Revisar.** Nunca.

## ADR-011 — `origen` es obligatorio, inmutable y no admite nulo

**Contexto.** El demostrador de marzo debe enseñar el bucle completo cuando aún no hay saca.
La alternativa honesta a maquillar es que el sistema sepa de dónde viene cada dato.

**Decisión.** Todo evento y todo registro derivado lleva `origen ∈ {real, simulado,
historico}`. No se puede modificar después. La interfaz lo muestra, la exportación lo
arrastra, y cualquier agregado que mezcle orígenes lo declara.

**Consecuencias.** Cuesta una columna y unas comprobaciones. A cambio, permite demostrar el
sistema entero sin que nadie pueda decir después que se le enseñó una saca inexistente.

**Revisar.** Nunca.

## ADR-012 — Las identidades viven fuera del asiento

**Contexto.** Los nombres de los corcheros son datos personales. El asiento está diseñado
para ser inmutable. Las dos cosas chocan.

**Decisión.** En el evento va un `actor` opaco. La tabla de identidades vive aparte, con su
base legal, su retención y su capacidad de borrado.

**Consecuencias.** El asiento se conserva íntegro aunque una identidad se suprima.
Diseñarlo así ahora cuesta poco; añadirlo después obligaría a reescribir la cadena, que es
justo lo que la cadena existe para impedir.

**Revisar.** Al definir la política de retención con asesoría legal.

## ADR-013 — La inmutabilidad se defiende en tres capas, no en una

**Contexto.** «Ningún registro se sobrescribe» era una frase. Una restricción `CHECK` o
`UNIQUE` no impide un `UPDATE` ni un `DELETE`.

**Decisión.** Tres capas simultáneas: permisos del rol `castuo_app` (solo `SELECT` e
`INSERT`), triggers que rechazan explícitamente con `CASTUO_APPEND_ONLY`, y pruebas de
integración contra PostgreSQL real que intentan ambas operaciones.

**Consecuencias.** El invariante sobrevive a un cambio accidental de permisos, y el error
es explícito en lugar de un fallo silencioso. Hallazgo de la implementación: **un trigger
de fila no protege una tabla vacía** —`DELETE FROM seal` sobre cero filas no dispara nada
y tiene éxito—, así que se añadieron también triggers de sentencia. Sin ellos, la
invariante habría sido cierta por accidente y la prueba habría pasado por vacuidad.
Coste: las pruebas exigen PostgreSQL real; con SQLite se estaría comprobando otra cosa.

**Revisar.** Nunca mientras la evidencia sea el producto.

## ADR-014 — El token RFC 3161 se ancla aparte; el sello base no se toca

**Contexto.** El criterio 9 exige que añadir el token no cambie el contenido sellado. Pero
si `seal` es append-only, añadir una columna `tsa_token` a posteriori sería un `UPDATE`:
el criterio y la inmutabilidad se contradecían.

**Decisión.** Tabla `seal_anchor`, también append-only, que referencia el sello.

**Consecuencias.** Un paquete exportado antes del anclaje sigue verificando después, y un
sello puede tener varios anclajes (varias autoridades) sin ambigüedad. A cambio, quien
consulte un sello tiene que mirar dos tablas.

**Revisar.** Nunca.

## ADR-015 — El vínculo detección→activo vive en su propia tabla

**Contexto.** Una detección puede proponer un activo que aún no existe, y `asset_id` es
nulo al crearla. Como la detección no se puede actualizar, el vínculo no puede escribirse
encima después.

**Decisión.** Tabla append-only `asset_proposal (detection_id, asset_id)`.

**Consecuencias.** La detección queda intacta y el historial de propuestas es consultable.
A cambio, saber qué activos propuso una detección exige una consulta más.

**Revisar.** Si se decide que toda detección nazca ya con activo.

## ADR-016 — El verificador externo no depende de la aplicación

**Contexto.** Si para comprobar una evidencia hiciera falta el software que la produjo, la
evidencia no valdría ante un tercero.

**Decisión.** `scripts/verify_package.py` no importa nada de `castuo` ni toca la base de
datos: reimplementa la canonicalización en unas líneas y trabaja solo sobre el JSON.

**Consecuencias.** La reimplementación es también una prueba de que el formato está bien
especificado: si no se pudiera reescribir en veinte líneas, no sería verificable por otros.
A cambio, hay dos implementaciones de la canonicalización que deben mantenerse alineadas,
y una prueba lo comprueba ejecutando el verificador como proceso aparte.

**Revisar.** Si el formato del paquete cambia.

## ADR-017 — El activo no tiene ningún campo mutable

**Contexto.** Quedaba un `UPDATE` en el sistema: el de `asset.status`. Un `UPDATE`
controlado por trigger habría funcionado, pero obligaba a mantener el permiso concedido al
rol de aplicación y perdía quién cambió el estado y cuándo.

**Decisión.** El estado deja de ser columna: se registra en `asset_status_event`
(append-only) y el vigente se proyecta en la vista `asset_current`. Las transiciones
declaradas son detectado→validado, detectado→descartado, validado→descartado,
pendiente→detectado y pendiente→descartado; el resto se rechaza, incluido quedarse igual.
El evento debe partir del estado vigente, comprobado bajo bloqueo por activo.

**Consecuencias.** El rol `castuo_app` se queda con `SELECT` e `INSERT` y **ningún
`UPDATE` en ninguna tabla**: la capa de permisos pasa a ser absoluta en lugar de tener una
excepción que alguien acabaría ampliando. Además queda el historial de quién decidió qué,
que en un sistema de evidencia es parte del producto. A cambio, consultar el estado exige
una vista en vez de una columna, y una parcela con muchas transiciones paga una subconsulta.

**Revisar.** Si el volumen hace cara la proyección, se materializa la vista — sin devolver
la mutabilidad.

## ADR-018 — La cadena encadena el evento completo, no solo el payload

**Contexto.** `previous_trace_hash` apuntaba a `payload_hash`. Eso dejaba alterar el actor,
el tipo de evento, la entidad o el origen de un evento sin romper la continuidad: los
metadatos quedaban fuera de la cadena.

**Decisión.** Cada evento calcula `event_hash` sobre su contenido completo —dispositivo,
número de secuencia, tipo, entidad, actor, hora, `payload_hash`, hash anterior, versión de
esquema y origen— y la cadena encadena ese hash. `verify_chain` además recalcula cada
`event_hash`: comprobar solo el encadenado permitiría reescribir un evento y su hash a la vez.

**Consecuencias.** La hora del evento pasa a ser contenido hasheado, así que se fija en la
aplicación y se inserta explícitamente; dejarla al `DEFAULT` del motor produciría un hash
que no corresponde a la fila guardada. Cambiar cualquier campo del evento lo invalida, y
hay una prueba por campo.

**Revisar.** Nunca.

## ADR-019 — Canonicalización versionada con dos implementaciones

**Contexto.** El verificador externo no puede importar la aplicación, así que hay dos
implementaciones de la canonicalización. Sin un contrato escrito, divergen en el primer
caso raro —un acento combinante, un `0.90`, un huso horario— y los paquetes dejan de
verificarse sin que nadie sepa por qué.

**Decisión.** `docs/CANONICALIZACION.md` fija el contrato (CASTUO-CANON-1): UTF-8, claves
ordenadas, sin espacios, NFC, decimales como cadena posicional normalizada, bytes en hex,
UUID en minúsculas, fechas en UTC truncadas a microsegundos y con sufijo Z, y rechazo
explícito de los timestamps sin zona. El número de versión viaja **dentro** de cada
paquete. Catorce vectores congelados en `tests/fixtures/canonical_vectors.json` comprueban
que ambas implementaciones emiten los mismos bytes y el mismo SHA-256.

**Consecuencias.** Cambiar una regla obliga a subir `canon_version`, y los paquetes
antiguos siguen verificándose con la implementación de su versión. El coste es mantener dos
implementaciones sincronizadas; la prueba de vectores es lo que hace ese coste asumible.

**Revisar.** Al añadir un tipo nuevo al contrato.

## ADR-020 — La protección append-only cubre TRUNCATE y las tablas vacías

**Contexto.** Los triggers de fila no se disparan cuando la operación no afecta a ninguna
fila, y TRUNCATE no admite disparo por fila en absoluto. Con solo triggers de fila,
`DELETE FROM seal` sobre una tabla vacía tenía éxito y `TRUNCATE` pasaba siempre.

**Decisión.** Triggers de **sentencia** para UPDATE, DELETE y TRUNCATE en todas las tablas
append-only. Los triggers de fila se conservan únicamente donde nombran el campo concreto
que alguien intentó tocar (`asset`), porque ese detalle vale en una auditoría.

**Consecuencias.** La prohibición es una propiedad de la operación y no del contenido de la
tabla. Las pruebas verifican las tres operaciones, con tablas llenas y vacías, con el rol de
aplicación y con el propietario, y comprueban que la fila sigue intacta tras cada intento
fallido — un rechazo que dejara el dato a medias no serviría de nada.

**Revisar.** Nunca.

## ADR-021 — El orden de los eventos lo fija una secuencia, no la hora

**Contexto.** `asset_current` proyectaba el estado vigente ordenando por `occurred_at` y
desempatando por UUID. En PostgreSQL, `now()` devuelve la hora de la **transacción**: dos
transiciones escritas sin commit intermedio comparten timestamp, y el estado vigente
quedaba decidido por el UUID, es decir al azar. Una prueba lo destapó al fallar de forma
intermitente.

**Decisión.** `asset_status_event` lleva `sequence_no` con `UNIQUE (asset_id,
sequence_no)`, asignado bajo bloqueo por activo; la proyección y el trigger de transición
ordenan por él. El trigger además exige continuidad: sin huecos ni saltos.

**Consecuencias.** El estado vigente de un activo es determinista con independencia del
reloj y de cómo se agrupen las transacciones. Es el mismo patrón que la cadena de traza, lo
que reduce el número de mecanismos distintos que hay que entender. Coste: una consulta más
al calcular la siguiente transición.

**Revisar.** Nunca. La lección general —no ordenar eventos por `now()` dentro de una misma
transacción— vale para cualquier tabla futura.

## ADR-022 — El umbral de aceptación se registra antes que los datos

**Contexto.** El gate G2 compara el orden del sistema con el del corchero experto. Si el
listón se fija después de ver el resultado, no mide nada: es elegir la vara que uno acaba
de saltar, y quien revise el trabajo lo verá.

**Decisión.** Un `contrast_protocol` registra unidad de ordenación, métrica y umbral antes
de que exista ningún orden. `compute_concordance` **copia** métrica y umbral de esa fila;
quien invoca el cálculo no puede pasar ninguno de los dos.

**Consecuencias.** El veredicto es reproducible y auditable: la fila de `concordance`
guarda el valor, el umbral aplicado, la métrica y su versión. A cambio, cambiar de opinión
sobre el umbral exige registrar un protocolo nuevo, que es exactamente la fricción que se
busca.

**Revisar.** Nunca.

## ADR-023 — El muro: el orden del experto exige sello previo, y su rechazo no destruye la transacción

**Contexto.** El contraste ciego era una promesa de método. Un financiador con criterio
pregunta cómo sabe que el modelo no se ajustó después de ver al corchero.

**Decisión.** `expert_ranking_blind_check` rechaza la carga del orden experto si no existe
un sello local del ranking anterior a esa carga. La escritura se hace dentro de un punto de
guardado, porque chocar contra el muro es un resultado esperado del protocolo y no puede
llevarse por delante el trabajo previo del llamante.

**Consecuencias.** La respuesta a la pregunta incómoda deja de ser buena fe y pasa a ser una
propiedad comprobable. Reajustar el modelo sigue permitido: produce un ranking nuevo, con su
propia versión y fecha posterior al sello. Coste: el flujo tiene un orden obligatorio que no
se puede abreviar.

**Revisar.** Nunca mientras el contraste ciego sea el argumento central.

## ADR-024 — La autenticación decide en qué cadena se escribe

**Contexto.** La cadena de traza se particiona por dispositivo. Una API sin identidad de
dispositivo no podría escribir en ninguna cadena concreta.

**Decisión.** Cada dispositivo se enrola con una clave que solo se muestra una vez y se
guarda hasheada. La API autentica por cabecera y usa el `device_id` autenticado como
partición de la cadena y como actor del evento. Un dispositivo se revoca, nunca se borra ni
se reactiva.

**Consecuencias.** La autenticación no es solo control de acceso: determina la cadena. Una
filtración del volcado no entrega credenciales utilizables. El error de credencial es el
mismo para clave ausente, inválida o revocada, para no regalar información a quien pruebe
claves. **Limitación reconocida:** no hay rotación, caducidad ni revocación auditada; es
suficiente para el prototipo y no para producción. `[PENDIENTE: credenciales de producción]`

**Revisar.** Antes de cualquier despliegue fuera del entorno de pruebas.

## ADR-025 — El nombre de producto cambia; el identificador de capacidad y los archivos no

**Contexto.** El proyecto se reposiciona como CASTÚO-SYSTEM™ SUBER UAV, sistema de diagnóstico
y trazabilidad digital del alcornocal, en lugar de «demostrador aero-forestal». La tentación
inmediata es renombrar el repositorio, los archivos, las tablas y la serie de decisiones para
que todo cuadre con el nombre nuevo.

**Decisión.** Cambia el nombre de producto y el discurso. **No** cambian: el identificador de
capacidad `CAP-OFFLINE-FIRST-001`, los nombres de archivo del repositorio, la numeración ADR,
ni los nombres de tabla del núcleo. Los documentos nuevos declaran la equivalencia; los
antiguos se conservan como están.

**Consecuencias.** Conviven dos vocabularios —«UAV Cork» en los archivos, «SUBER UAV» en el
discurso—, lo que es deuda de nomenclatura y hay que anotarlo como tal. A cambio, el historial
de git, las referencias cruzadas entre ADR y los enlaces ya repartidos siguen resolviendo. Un
renombrado masivo habría producido un cambio enorme, sin valor funcional, sobre un núcleo que
acaba de pasar 258 pruebas.

**Revisar.** Si el demostrador sale del repositorio de la capacidad (incoherencia 3), el
renombrado se hace entonces, de una vez y con la mudanza.

## ADR-026 — El modelo sectorial es una capa de dominio sobre el núcleo, no un rediseño

**Contexto.** El posicionamiento introduce trece entidades de dominio —`tree`, `cork_sample`,
`cork_lot`, `industrial_result`…— mientras el esquema implementado nombra `asset`, `detection`,
`review`, `trace_event`. Leídos juntos parecen dos diseños incompatibles del mismo sistema.

**Decisión.** Son dos capas. El núcleo de evidencia es agnóstico del dominio a propósito: no
sabe qué es un alcornoque, y esa neutralidad es lo que permite probar sus garantías una vez.
La capa sectorial se construye **encima**, registra sus hechos como `evidence_event`, y no
afloja ninguna garantía del núcleo. `asset` no pasa a llamarse `tree`.

**Consecuencias.** El vocabulario del sector no aparece en las migraciones, y quien las lea
buscando `cala` no la encuentra: por eso `MODELO_DATOS_SECTORIAL.md` declara entidad por
entidad qué existe y qué no —una construida, cuatro parciales, ocho ausentes— en lugar de
presentar el modelo como si fuera el esquema. El coste es mantener dos vocabularios y una
tabla de correspondencia que hay que actualizar cada vez que se construye una entidad.

**Revisar.** Nunca la separación. Sí la tabla de correspondencia, en cada incremento.

## ADR-027 — Una clase de calidad del corcho no la firma un modelo

**Contexto.** La afirmación más peligrosa del proyecto es que el sistema «determine
automáticamente la calidad del corcho». La clase —grueso, bueno, flaco, delgado, refugo— se
establece por metodología de campo y laboratorio. Prohibirlo en un documento no impide que
mañana un endpoint escriba una clase con `model_version` y sin persona detrás.

**Decisión.** `quality_assessment` llevará `method` y `assessed_by` obligatorios, sin ningún
valor admitido que signifique «el sistema», y sin camino de inserción que acepte solo una
versión de modelo. Lo mismo para el diagnóstico en `sanitary_assessment`: un indicador
derivado de UAV se registra como indicador, con su versión; el diagnóstico lo firma una
persona.

**Consecuencias.** El sistema queda estructuralmente incapaz de sostener la frase prohibida
con su propio dato, que es más fuerte que prohibirla por escrito. A cambio, ninguna
automatización futura de la clase será posible sin revisar esta decisión de forma explícita,
que es exactamente la fricción que se busca.

**Revisar.** Solo si el sector adopta una escala automatizable y validada, cosa que hoy no
existe.

## ADR-028 — Trazabilidad y correlación son objetivos distintos con calendarios distintos

**Contexto.** La cadena monte→fábrica se puede leer de dos maneras: que esté registrada sin
huecos, o que las variables de monte expliquen el resultado industrial. Suenan igual en una
presentación y no lo son.

**Decisión.** Se separan y se fechan por separado. **Trazabilidad**: la cadena registrada y
verificable; objetivo de la campaña de 2027, gate G3. **Correlación**: el modelo que relaciona
monte y fábrica; requiere varias campañas y varianza suficiente, y no es resultado del primer
piloto. Ningún documento del proyecto presenta la segunda como consecuencia de la primera.

**Consecuencias.** El piloto de 2027 promete menos y puede cumplirlo. La correlación queda
como objetivo plurianual declarado, que es además un argumento mejor ante un centro de
investigación que una promesa a un año. El riesgo H-I4 —que el orden no concuerde con lo que
sale en la saca— se lee en el mes 12 y no invalida G3.

**Revisar.** Tras la segunda campaña con dato industrial.

## ADR-029 — El léxico se comprueba con una prueba, no con una advertencia

**Contexto.** El posicionamiento define una formulación prohibida y una aprobada. Escrito solo
en un documento, lo respeta quien lo ha leído y se acuerda; y la frase prohibida es
precisamente la que más fácil sale cuando hay que resumir el proyecto en una línea.

**Decisión.** `scripts/check_lexico.py` recorre los documentos publicables buscando ocho
patrones prohibidos (LEX-01…LEX-08) y termina con código 1 si encuentra alguno. Está en la
batería (`tests/docs/test_lexico.py`). Para citar una formulación prohibida —hay que citarla
para prohibirla— se envuelve el bloque entre `<!-- lexico:off -->` y `<!-- lexico:on -->`; un
bloque sin cerrar es también un hallazgo.

**Consecuencias.** La norma baja un nivel: de documento a prueba automatizada. No llega al
motor porque una frase en una memoria no pasa por la base de datos; la sustancia sí está en el
motor, vía ADR-027 y la regla del sello. **Limitación reconocida:** el comprobador detecta
formulaciones literales, no equivalentes semánticos; un texto puede sobreafirmar sin disparar
ningún patrón. Es un cinturón, no un airbag.

**Revisar.** Cada vez que aparezca una sobreafirmación que el comprobador no vio: se añade el
patrón.

## ADR-030 — La cala es dato de un tercero: se referencia, nunca se infiere

**Contexto.** `cork_sample` es la entidad que conecta el sistema con la metodología aceptada
por el sector. Es también la más tentadora de rellenar: con suficientes descriptores UAV se
puede producir un número que se parezca a un calibre.

**Decisión.** La cala se registra con su protocolo y versión, su operador y su método, o no se
registra. No hay camino que produzca una `cork_sample` a partir de datos UAV. Una estimación
derivada del vuelo es una `uav_observation` con versión de modelo, y se llama así.

**Consecuencias.** El Piloto 2 queda bloqueado por un acuerdo con un tercero (A-06) en lugar
de poder simularse hacia adelante, lo cual es lento y correcto: un esquema escrito contra una
metodología imaginada se descubre roto justo cuando llega el dato real. Los datos sintéticos de
cala son posibles para probar el flujo, pero nacen con `origen = 'simulado'` e inmutable.

**Revisar.** Cuando exista acuerdo sobre el protocolo (A-06).

## ADR-031 — SUBER UAV es una fase exterior a la arquitectura de CASTÚO-SYSTEM

**Contexto.** El demostrador vive en el repositorio de la capacidad `CAP-OFFLINE-FIRST-001` y la
lectura por defecto era que acabaría siendo un componente más de la plataforma. La incoherencia 3
del estado maestro llevaba abierta desde el primer día. Con el reposicionamiento sectorial la
tensión dejó de ser de nomenclatura: el discurso ya no es «capacidad de operación en campo» sino
«sistema sectorial con interlocutores propios».

**Decisión.** SUBER UAV es una **fase y etapa exterior** a la arquitectura de CASTÚO-SYSTEM. No
forma parte de sus treinta y seis módulos. Tiene despliegue propio, calendario propio —el del
monte— y frontera propia. Su resultado entra en la plataforma como **paquete verificable con
formato declarado**, nunca como escritura en una base de datos compartida ni como dependencia de
esquema. Ninguno de los dos lados escribe en el otro.

**Consecuencias.** Aislamiento de fallo en ambos sentidos: un demostrador que no ha volado no
puede desestabilizar producción, y un cambio interno de la plataforma no obliga a rehacer nada
aquí. La interfaz **tiene que** declararse, cosa que un módulo interno nunca llega a hacer, y esa
declaración es lo que hace la evidencia portátil y auditable. La reversibilidad del piloto queda
acotada. **Lo que cuesta:** dos vocabularios, dos despliegues, riesgo de deriva, y la tentación
de reimplementar lo que la plataforma ya tiene. Se contiene con una regla: SUBER UAV solo
construye lo que necesita para la evidencia de campo, y el contrato es el paquete, no el esquema.

**Cierra:** la incoherencia 3 del estado maestro, abierta desde v0.1.

**Revisar.** Si tras el piloto de 2027 el sistema pasa a operación continua, procede decidir si
la fase exterior se mantiene o se integra. Antes, no.

## ADR-032 — La soberanía del dato se declara desglosada, no como principio

**Contexto.** «Soberanía del dato» es un principio fundacional de la casa y, dicho sin desglosar,
no compromete a nada ni se puede comprobar. Un interlocutor técnico lo detecta de inmediato, y la
palabra pasa de activo a pasivo.

**Decisión.** Para SUBER UAV la soberanía se descompone en seis propiedades con estado
individual: S-1 el dato nace y se usa donde se genera · S-2 la inferencia no depende de un
proveedor externo · S-3 la residencia es europea y conocida · S-4 la verificación no exige
confiar en el sistema · S-5 no hay dependencia de red pública ni de jurisdicción ajena · S-6 las
personas no quedan atrapadas en el asiento. Ningún documento afirma la soberanía como un todo;
cada propiedad se afirma con su estado. Hoy: tres hechos probados, dos decisiones de diseño sin
auditar, una a medias.

**Consecuencias.** El argumento se vuelve comprobable y, por eso mismo, más difícil de escribir:
obliga a decir que tres de seis no están demostradas. A cambio, la exterioridad (ADR-031) hace
que auditarlas sea abordable, y esa auditoría sería la primera evidencia de residencia real de la
casa. **Efecto sobre A-03:** la autoridad de sellado RFC 3161 deja de ser una elección libre y
pasa a llevar una condición: debe estar en la Unión Europea, o S-5 se pierde por la puerta de
atrás. **Limitación reconocida:** S-3 y S-6 dependen de una auditoría y de una revisión jurídica
que no están hechas.

**Revisar.** Cuando exista la auditoría de residencia o se cierre `owner_ref`.

## ADR-033 — Ninguna fortaleza se enuncia por encima de su nivel de evidencia

**Contexto.** El proyecto necesita destacar lo que ya vale —un preprototipo que no sabe enseñar
sus fortalezas no consigue financiación— y a la vez tiene una regla de prudencia que prohíbe
sobreafirmar. Las dos cosas parecen tirar en direcciones opuestas, y la salida fácil es
suavizarlo todo hasta que no diga nada.

**Decisión.** Cada fortaleza se enuncia acompañada de su nivel de evidencia en la escala EQ1–EQ6
del README, sin inventar niveles nuevos; lo que no tiene nivel se enuncia como decisión de
diseño. Las fortalezas del preprototipo se escriben en presente; los compromisos del prototipado,
con su nivel de destino y su gate. Ninguna fortaleza se ofrece como respuesta a una pregunta
sobre un hueco. El catálogo vive en `FORTALEZAS.md` y es de donde se copian las frases al
escribir una memoria.

**Consecuencias.** El discurso puede ser enérgico sin ser falso, porque cada línea es
comprobable por quien la lea. **Lo que cuesta:** el catálogo hay que mantenerlo, y un nivel de
evidencia que sube exige actualizar el documento; un catálogo desactualizado sería peor que no
tenerlo, porque afirmaría en presente lo que aún no se cumple. **Limitación reconocida:** la
regla no impide que alguien enuncie una fortaleza inexistente; para eso está el comprobador de
léxico, que tampoco cubre los equivalentes semánticos.

**Revisar.** En cada cierre de gate, que es cuando cambian los niveles.

## ADR-034 — El protocolo de integridad es transversal, y el vocabulario del motor manda

**Contexto.** El protocolo de integridad se redactó apoyándose en el trabajo de SUBER UAV pero
con la intención de servir también a SENDA y a líneas futuras. Al contrastarlo con el sistema
implementado aparecieron dos discrepancias de vocabulario y una de alcance: el borrador declaraba
`origen ∈ {campo, sintetico}` frente al `origen_t ∈ {real, simulado, historico}` del motor;
revocaba permisos a roles (`tecnico`, `analista`) que no existen, en lugar de no concederlos al
rol que sí existe; y presentaba como implementados un catálogo de once eventos y una raíz de
jornada que no lo están.

**Decisión.** El protocolo se escribe una vez, es transversal y vive en
`docs/PROTOCOLO_INTEGRIDAD.md`. Ante discrepancia entre el protocolo y el motor, **manda el
motor**: `origen` conserva sus tres valores, y la nomenclatura de dos queda como sinónimo de
trabajo con equivalencia declarada. Los permisos se documentan como se implementan —no conceder,
y revocar además, incluidos los privilegios por defecto—, no como una revocación a roles
inexistentes. Cada afirmación del protocolo lleva uno de cuatro estados —hecho probado, decisión
de diseño, pendiente de auditoría, parcialmente resuelto— y el estado forma parte de la
afirmación.

**Consecuencias.** El tercer valor de `origen` se conserva porque hace un trabajo que los otros
dos no pueden hacer: un dato de campaña anterior fue real y no es dato de la campaña en curso, y
confundirlos falsearía cualquier contraste. El protocolo queda utilizable por otra línea sin
reescribirlo, a cambio de que cada línea declare su catálogo de eventos y su lectura de `origen`.
**Lo que cuesta:** dos documentos que mantener sincronizados —el protocolo y el estado maestro— y
la disciplina de actualizar el estado de cada afirmación cuando sube de nivel. **Limitación
reconocida:** ninguna capa del sistema impide hoy escribir un evento fuera del catálogo E-01…E-11;
el catálogo es semántico, no coercitivo.

**Revisar.** Cuando una segunda línea adopte el protocolo, que es cuando se sabrá si es
transversal de verdad o solo está escrito como si lo fuera.

## ADR-035 — El trabajo avanza por tres vías en paralelo, no por una lista en serie

**Contexto.** La lista de lo que falta mezcla trabajo que depende solo de nosotros, trabajo
que necesita un aparato volando sobre una parcela, y trabajo que depende de que un tercero
diga que sí. Tratada como una sola lista ordenada, la primera se retrasa esperando a la
tercera y el proyecto pasa meses sin producir evidencia mientras espera una reunión.

**Decisión.** Tres vías simultáneas: **A interna** (no depende de nadie), **B de campo**
(depende de aparato y sitio), **C de acuerdos** (depende de un tercero). La vía C se abre el
primer día aunque su resultado tarde meses. Regla de ordenación dentro de cada vía: primero
lo que más sube el nivel de evidencia por unidad de coste.

**Consecuencias.** El panel de cuatro vistas, que enseña bien, queda detrás de la raíz de
jornada, que no enseña nada: el panel muestra evidencia que ya existe y la raíz produce
evidencia que no existe. Cuando llegue un «sí» de la vía C, el software no debería tener que
construirse sino conectarse. **Lo que cuesta:** tres frentes abiertos exigen más disciplina
de registro, y la vía C consume tiempo de gestión que no produce commits.

**Revisar.** Si una vía se queda sin trabajo desbloqueado durante más de un mes.

## ADR-036 — La carga útil del UAV sube por peldaños, y el peldaño se registra

**Contexto.** El sensor caro es el gasto más fácil de justificar en una reunión y el que más
probablemente acaba sin usarse. Comprar LiDAR antes de haber demostrado que se puede
reencontrar un árbol entre dos vuelos es gastar en resolución un problema que no es de
resolución.

**Decisión.** Escalera cerrada de cuatro peldaños —P0 RGB, P1 RGB con posicionamiento
preciso, P2 multiespectral, P3 LiDAR— implementada como tipo `payload_t` en el motor y
registrada en cada misión. Un peldaño solo se justifica con lo que produjo el anterior, y el
criterio de subida se declara antes: P0→P1 cuando la tolerancia de reidentificación no baste;
P1→P2 cuando la serie temporal sea estable; P2→P3 cuando el indicador haya cerrado su gate.

**Consecuencias.** La decisión de carga útil queda registrada en cada evidencia, de modo que
un resultado obtenido con P0 no se puede presentar después como si fuera de P2. **Lo que
cuesta:** el conjunto cerrado obliga a una migración para añadir un peldaño no previsto, que
es exactamente la fricción que se busca. **Limitación reconocida:** el motor registra el
peldaño, no comprueba que el anterior cerrara su gate; eso es disciplina de proyecto.

**Revisar.** Al cierre de cada gate.

## ADR-037 — Ningún gate se intenta en campo sin ensayo en seco previo

**Contexto.** Una jornada de campo cuesta desplazamiento, aparato, permiso y —en G2— el
tiempo de un técnico. Descubrir en esa jornada que el sistema no sabe registrar dos misiones
del mismo rodal es gastar todo eso para aprender algo que un guion dice gratis. Y perder una
ventana del monte cuesta un año, no una semana.

**Decisión.** Cada gate tiene un ensayo en seco ejecutable, con semilla fija y código de
salida, que recorre el flujo entero con datos sintéticos marcados como tales. El gate no se
intenta en campo mientras su ensayo no pase. G1 y G2 ya lo tienen; G3 lo tendrá con UMD-9.

**Consecuencias.** Obliga a fijar el método —tolerancia, umbral, métrica— antes de tener el
dato, que es la misma disciplina del contraste ciego y aquí sale gratis. **Lo que cuesta:**
mantener los ensayos al día; un ensayo que no se ejecuta desde hace tres meses no protege
nada. Van en la batería para que dejen de pasar sin que nadie se entere.

**Revisar.** Nunca la regla. Sí los ensayos, en cada cambio de esquema.

## ADR-038 — Cada afirmación por encima de su nivel de evidencia es deuda registrada

**Contexto.** ADR-033 obliga a enunciar cada fortaleza con su nivel. En la práctica hay
afirmaciones que se usan un escalón por encima del que les toca —pesos de ordenación citados
como si estuvieran calibrados, residencia europea presentada como propiedad— y suprimirlas
del todo dejaría el discurso sin nada que decir.

**Decisión.** Esas afirmaciones no se prohíben: se registran como **deuda de evidencia**, con
su nivel real, el nivel al que se enuncia y el gate en el que vence. La tabla vive en
`PLAN_CRECIMIENTO.md`, apartado 9. Una deuda sin gate de vencimiento no se admite.

**Consecuencias.** Un preprototipo tiene deuda de evidencia por definición; el problema no es
tenerla sino no saber cuánta hay ni cuándo vence. **Limitación reconocida:** el comprobador
de léxico atrapa las formulaciones más peligrosas pero no distingue matices de nivel, así que
esta tabla se mantiene a mano y puede quedarse desactualizada.

**Revisar.** En cada cierre de gate, junto con `FORTALEZAS.md`.

## ADR-039 — El repositorio canónico de la capacidad offline-first es `castuo-agro-edge`; este repositorio es extracción experimental

**Contexto.** ADR-031 declara SUBER UAV fase exterior a la arquitectura de CASTÚO-SYSTEM, pero
la incoherencia 3 del estado maestro dejaba el código «en el repositorio de la capacidad por
comodidad» y daba por pendiente una mudanza sin fecha. Entre tanto, la decisión de
consolidación del ecosistema del 2026-09-21 (`castuo-system`, `docs/architecture/
ECOSYSTEM-CONSOLIDATION-DECISIONS-2026-09-21.md`, apartado 2, ítems 1 y 2; registrada en
`docs/ECOSYSTEM.md`) estableció que el canónico del dominio «offline field operations» es
`castuo-agro-edge` y que este repositorio pasa a **extracción experimental**, con el commit
`b5f23f4` (95 archivos, 10.958 líneas añadidas) archivado en `archive/suber-uav-snapshot` y
**no fusionado a `main`**. El estado maestro v0.8 (2026-09-02) es anterior y no la recoge.
Verificado el 2026-11-23: `HEAD` del snapshot = `origin/archive/suber-uav-snapshot` =
`b5f23f4`; `origin/main` (`7ffbc20`) no contiene `docs/DECISIONES.md`, `src/castuo/` ni
`migrations/`.

**Decisión.** (1) `castuo-agro-edge` rige como repositorio canónico del dominio. (2) Este
repositorio conserva el código de SUBER UAV solo en la rama de archivo, con estado
`experiment`. (3) ADR-031 **no cambia**: la exterioridad es respecto de la arquitectura de
CASTÚO-SYSTEM, no respecto de qué repositorio aloja el trabajo; este ADR aclara el hogar, no
el encaje. (4) Se registra como decisión **abierta**, y no se resuelve aquí, dónde vive y se
valida el código de SUBER UAV (A-11).

**Consecuencias.** Hay una sola fuente por dominio en el ecosistema y se evita mantener dos
repositorios con el mismo propósito. **Lo que cuesta:** el estado maestro, los ADR y las 284
pruebas viven en un repositorio que ya no es el canónico y cuyo `main` no los contiene, de modo
que el trabajo de validación no tiene hoy un hogar con integración continua. Además, `main` y
el snapshot divergen desde `47d3af1`; restaurar uno sobre otro exigirá reconciliar 21 commits
de gobernanza y de CI.

**Reabre:** la incoherencia 3 del estado maestro (resuelta en v0.6 como encaje, reabierta en
v0.9 como ubicación).

**Revisar.** Cuando se decida A-11, o antes de intentar cualquier gate de campo, lo que ocurra
primero. También si `castuo-agro-edge` deja de ser el canónico.

## ADR-040 — El código de SUBER UAV se lleva a `castuo-agro-edge` (cierra A-11)

**Estado:** decidida el 2026-11-23, **sin ejecutar**. **Quién decide:** el promotor delegó la
decisión en sesión («Decide A-11 ahora»); la propuesta y su razonamiento son de Claude. El
promotor puede revocarla.

**Contexto.** ADR-039 declara canónico a `castuo-agro-edge` y deja este repositorio como
extracción experimental, con el código solo en `archive/suber-uav-snapshot` (`b5f23f4`). A-11
preguntaba dónde vive y se valida ese código: restaurarlo a `main` de este repositorio,
mantenerlo archivado o llevarlo a otro. Verificado con `gh` el 2026-11-23: `castuo-agro-edge`
es `PUBLIC`, no está archivado, `main` con último push 2026-11-18, y tiene `tests.yml`,
`secret-scan.yml` y `.gitleaks.toml`.

**Decisión.** El código de SUBER UAV (`src/castuo/`, `migrations/`, `tests/`, `scripts/`) se
lleva a `castuo-agro-edge`, en un directorio propio de la capacidad y por PR; este repositorio
conserva su rama de archivo como histórico. Los gates de campo `SUBER-G1…G3` se validan con la
integración continua de ese repositorio. Se descartan: (a) restaurar a `main` aquí, porque
contradice ADR-039 y exige reconciliar 21 commits con un repositorio no canónico; (b) mantenerlo
archivado, porque deja sin hogar con CI justo lo que la validación necesita.

**Condiciones antes de ejecutar** `[PENDIENTE]`
1. El promotor confirma que publicar el código de SUBER UAV es aceptable: `castuo-agro-edge` es
   público. Si no lo es, la alternativa es un repositorio privado, no volver a (a) o (b).
2. Revisión previa de lo que se publicaría: sin datos reales ni referencias a titulares;
   `owner_ref` es `uuid` seudonimizado, pero nada impide hoy que apunte a datos personales
   (incoherencia 4).
3. Las 284 pruebas necesitan PostgreSQL 16; `[POR CONFIRMAR]` que `tests.yml` de
   `castuo-agro-edge` lo provee. Hoy GitHub Actions no arranca por facturación, así que no habría
   CI aunque el flujo esté bien.

**Estado de las condiciones (2026-11-23, actualizado en v0.12)**
1. **Cumplida.** El promotor confirmó en sesión que publicar el código en `castuo-agro-edge`
   (público) es aceptable.
2. **Revisión hecha, con dos matices.** `gitleaks git` sobre `b5f23f4` (3 commits, ~586 KB):
   sin fugas. Búsqueda de patrones de correo, DNI y teléfono en `src`, `tests`, `scripts`,
   `migrations` y `docs/publicaciones`: sin coincidencias. No hay datos reales de campo: los
   *fixtures* son sintéticos (`parcela_sintetica.png`, `vuelo_sintetico.json`).
   Matices: (a) no es una revisión humana del contenido; (b) `docs/publicaciones/` (3 HTML:
   dossier, protocolo, plan de crecimiento) es posicionamiento, no código; `[PENDIENTE]` decidir
   si viaja a un repositorio público o se queda fuera de la mudanza. *Resuelto:* el promotor
   decidió dejar `docs/publicaciones/` (y todo `docs/`) fuera.
3. **Cumplida.** *(Texto original: no cumplida; después, flujo escrito sin ejecutar.)* El `tests.yml` de `castuo-agro-edge` usa Python 3.12, no declara servicio
   de PostgreSQL y solo ejecuta `tests/unit`. Las 284 pruebas de SUBER UAV, que necesitan
   PostgreSQL 16, **no correrían** con él: hace falta un flujo nuevo con un servicio
   `postgres:16`. Y GitHub Actions no arranca por facturación, así que no habría ejecución
   aunque se escriba. Se escribió `suber-uav-tests.yml` con un servicio `postgres:16`, que crea
   el rol `castuo_app` antes de las pruebas. En local las 284 pruebas **pasan** con ese montaje.
   Hallazgo: `tests/conftest.py` usa `castuo_app` antes de que la migración 0006 lo cree, así
   que en un clúster nuevo falla sin ese paso. Mudanza: PR `castuo-agro-edge#51`, **fusionado** (`a7ab478`) con `--admin` y sin
   aprobación de revisión. En CI de GitHub el flujo falló al principio porque `fastapi` y `httpx`
   no estaban declarados; se declararon en `pyproject.toml` y las 284 pruebas pasan.

**Consecuencias.** Buenas: una sola fuente por dominio, y un hogar con CI y escaneo de
secretos para el expediente de los gates. Malas: el trabajo de migración (reubicar rutas y
reejecutar las 284 pruebas, que hoy están solo «recolectadas») y la dependencia de un
repositorio público.

**Revisar.** Al ejecutar la mudanza, si el promotor rechaza la condición 1, o si
`castuo-agro-edge` deja de ser el canónico.
