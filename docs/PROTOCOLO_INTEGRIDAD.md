# Protocolo de Integridad Técnica

**Estándares de inmutabilidad y trazabilidad de registros**

**Documento:** v0.1 · 2026-09-02 · CASTÚO-SYSTEM
**Ámbito:** transversal. Aplica a SUBER UAV (alcornocal y cadena del corcho), a SENDA y a
cualquier otra línea que asiente evidencia sobre el mismo núcleo.
**Depende de:** `CASTUO_CORCHO_ESTADO_MAESTRO.md` (fuente de verdad del estado real) y de
`DECISIONES.md` (ADR-007 a ADR-024 y ADR-034).

---

## 0. Cómo se lee este documento

Cada afirmación del protocolo lleva uno de estos cuatro estados, y el estado forma parte de la
afirmación. Un requisito sin estado no es un requisito: es una intención.

| Estado | Qué significa | Cómo se comprueba |
|---|---|---|
| **Hecho probado** | Está implementado y hay prueba automatizada que falla si se rompe | Ejecutando la batería |
| **Decisión de diseño** | Está decidido y documentado; no hay medida que lo respalde | Leyendo el ADR correspondiente |
| **Pendiente de auditoría** | Implementado o decidido, pero sin verificación de un tercero | Encargando esa verificación |
| **Parcialmente resuelto** | Una parte está construida y otra identificada y abierta | En la tabla de riesgos y decisiones abiertas |

La razón de este encabezado es práctica. Un protocolo de integridad que mezcla lo construido con
lo previsto se vuelve inservible en el momento en que alguien lo comprueba, y lo comprueban
precisamente los interlocutores a los que se dirige.

## 1. Propósito

La gestión de recursos naturales críticos exige pasar de la confianza operativa a la evidencia
técnica verificable. Este protocolo define cómo se registra, se corrige, se sella y se exporta un
dato para que un tercero pueda comprobar por su cuenta que no ha sido alterado desde su origen.

El objetivo no es almacenar información, sino que la historia del dato sea reconstruible, que la
alteración silenciosa sea detectable y que la verificación independiente sea posible sin acceso a
los sistemas que lo produjeron. En el alcornocal esto encaja con metodologías de muestreo y
clasificación que ya existen; en la gestión hídrica, con el rastro documental que exige un
organismo regulador. El núcleo del protocolo es el mismo en ambos casos, y por eso se escribe una
vez.

**Lo que este protocolo ofrece:** resistencia a la alteración silenciosa, trazabilidad de cada
corrección y verificabilidad por terceros. **Lo que no ofrece:** protección frente a quien tenga
acceso administrativo directo al motor de base de datos y decida ejercerlo, ni valor probatorio
ante ninguna instancia, que es una cuestión jurídica y no técnica. Decirlo de entrada es parte de
lo que hace defendible el resto.

## 2. Alcance

**Entra en el protocolo:**

- El asiento de eventos de campo, procesamiento, validación y corrección.
- La ingesta desde dispositivos con conectividad intermitente.
- La emisión, congelación y sellado de resultados de modelo.
- La exportación de paquetes y su verificación fuera del sistema.
- El marcado obligatorio del origen del dato.
- Las identidades de las personas, en tanto que **quedan fuera** del asiento.

**No entra en el protocolo:**

- La calidad del dato de origen. Un dato mal medido y correctamente asentado sigue siendo un dato
  mal medido; la integridad protege la cadena, no la medición.
- La corrección científica de un modelo. El protocolo garantiza que se sabe qué modelo, con qué
  versión y con qué entrada produjo cada salida; no que esa salida sea acertada.
- El valor probatorio ante un tribunal o un organismo. Requiere análisis jurídico, que está
  abierto.
- La disponibilidad y la copia de seguridad, que son requisitos de operación tratados aparte.
- El cifrado en reposo y en tránsito, tratado en el plan de despliegue.

## 3. Modelo append-only

Los registros no se modifican ni se eliminan una vez creados. La corrección no sobrescribe: añade
un evento nuevo que referencia al anterior. **Estado: hecho probado.**

### 3.1 Defensa en el motor, en tres capas

Para que la inmutabilidad sea coercitiva y no una convención de la aplicación, se defiende en el
sitio más profundo que la pueda sostener. Una sola capa no basta, y conviene saber por qué:

| Capa | Qué detiene | Qué no detiene |
|---|---|---|
| **1 · Permisos de rol** | Al rol de aplicación y a cualquier sesión que use sus credenciales | Al propietario de las tablas y al superusuario |
| **2 · Disparadores de fila y de sentencia** | `UPDATE`, `DELETE` y `TRUNCATE`, incluso sobre tablas vacías | A quien pueda desactivar los disparadores |
| **3 · Pruebas de integración** | La regresión: si una capa se cae, la batería falla | Nada en producción; avisa, no impide |

**Capa 1.** El rol de aplicación **nunca recibe** `UPDATE` ni `DELETE`. No basta con revocar lo
concedido: lo correcto es no concederlo, y revocar además como refuerzo. En el sistema
implementado:

```sql
GRANT SELECT, INSERT ON asset, asset_status_event, detection, asset_proposal,
                         review, seal, seal_anchor, trace_event TO castuo_app;

REVOKE UPDATE, DELETE, TRUNCATE ON ALL TABLES IN SCHEMA public FROM castuo_app;

ALTER DEFAULT PRIVILEGES IN SCHEMA public
    REVOKE UPDATE, DELETE, TRUNCATE ON TABLES FROM castuo_app;
```

La cláusula de privilegios por defecto es la que evita el fallo habitual: una tabla creada más
tarde que nace con permisos amplios porque nadie se acordó de revocarlos.

**Capa 2.** Los permisos no alcanzan al propietario de las tablas. Por eso cada tabla de asiento
lleva disparadores que rechazan la operación con independencia de quién la ejecute, y a nivel de
**sentencia** además de a nivel de fila: un disparador de fila no se dispara sobre una tabla
vacía, y `TRUNCATE` no dispara nada a nivel de fila. Sin esa distinción, la invariante se cumple
por casualidad y la prueba que la comprueba pasa sin comprobar nada (ADR-020).

**Capa 3.** La batería intenta de verdad cada operación prohibida contra un motor real, no contra
un doble de prueba. Una invariante que solo existe en el código de aplicación se vería aquí.

> **Nota de despliegue.** Este esquema deja fuera al superusuario y al propietario de las tablas.
> Reducir esa superficie —propietario distinto del rol de mantenimiento, acceso administrativo
> auditado, registro de conexiones— pertenece al plan de despliegue.
> **Estado: parcialmente resuelto.** `[PENDIENTE: endurecimiento operativo]`

### 3.2 Ingesta idempotente

El sistema opera en escenarios *offline-first*, con conectividad intermitente. La ingesta debe
tolerar reintentos sin generar duplicados ni recurrir a la edición posterior:

- **Identificador estable.** Cada evento lleva un UUID generado en el dispositivo de origen.
- **Rechazo por identidad.** El núcleo rechaza toda inserción cuyo identificador ya exista y
  devuelve el estado existente en lugar de crear una segunda fila.
- **Sin edición correctiva.** La robustez de la sincronización no se paga con mutabilidad.

**Estado: hecho probado.**

## 4. Las correcciones son eventos

La edición destruye evidencia. Cualquier error detectado se resuelve con un evento de corrección
que referencia el registro afectado, dejando visibles el error y su subsanación.

### 4.1 Catálogo de eventos

El ciclo de vida del dato se expresa como una cadena de eventos inmutables. El catálogo separa
cuatro capas semánticas —captura, procesamiento, validación y custodia física— para que no se
mezclen procesos distintos en el mismo nivel.

| Código | Descripción | Origen | Referencia previa | Estado |
|---|---|---|---|---|
| E-01 | Cierre de vuelo y sellado del paquete crudo | Estación | Raíz de cadena | Decisión de diseño |
| E-02 | Ingesta del paquete de captura en el concentrador | Estación | Hash E-01 | Decisión de diseño |
| E-03 | Derivación de descriptores por unidad | Estación/Núcleo | Hash E-02 | Decisión de diseño |
| E-04 | Emisión y congelación de una versión de ordenación | Núcleo | Hash E-03 | **Hecho probado** |
| E-05 | Carga del orden del experto, posterior al sello | Núcleo | Hash E-04 | **Hecho probado** |
| E-06 | Apertura de lote por escaneo de marca física | Móvil | Identidad del dispositivo | Decisión de diseño |
| E-07 | Asiento de recurso o pieza en el lote abierto | Móvil | Hash E-06 | Decisión de diseño |
| E-08 | Cierre formal del lote en el tajo | Móvil | Hash E-07 | Decisión de diseño |
| E-09 | Entrada del lote en unidad de pila o patio | Móvil | Hash E-08 | Decisión de diseño |
| E-10 | Generación del paquete de exportación firmado | Núcleo | Raíz de jornada | Parcialmente resuelto |
| E-11 | Registro de incidencia o corrección técnica | Cualquiera | Identificador anterior + hash previo | **Hecho probado** |

**Advertencia sobre este catálogo.** El motor implementado encadena y protege eventos, pero **no
impone hoy esta taxonomía de códigos**: nada rechaza un evento que no encaje en E-01…E-11. Lo que
sí está construido es el principio —encadenamiento por dispositivo, corrección como evento nuevo,
sello previo a la carga del criterio experto— y las tres filas marcadas como hecho probado.
Presentar el catálogo completo como implementado sería exactamente el error que este documento
existe para evitar. **Estado global del catálogo: parcialmente resuelto.**

### 4.2 Protocolo de corrección (E-11)

El evento de corrección no sustituye ni oculta el registro anterior. Añade una entrada que apunta
explícitamente al registro afectado, y el estado vigente se obtiene plegando la secuencia de
eventos, no leyendo una columna que alguien pudo sobrescribir.

Dos consecuencias prácticas:

- Un historial sin correcciones no es necesariamente mejor que uno con ellas. Un registro que
  muestra su propia historia es más fácil de defender que uno que aparece impecable.
- El orden de los eventos **no lo fija la hora**. Dentro de una misma transacción, la hora del
  servidor es idéntica para todas las filas; el orden lo fija una secuencia explícita por
  dispositivo y por entidad, verificada en el motor (ADR-021). Este punto nació de un defecto real
  encontrado y corregido en el sistema. **Estado: hecho probado.**

## 5. Sellado temporal y firma

La sincronización temporal evita el postdatado y las reordenaciones posteriores. La arquitectura
separa dos cosas que suelen confundirse.

### 5.1 Sello local — inmediato

Contiene, como mínimo: hash del contenido, versión del algoritmo de canonicalización, versión del
modelo cuando el contenido es una salida de modelo, hora del servidor, identificador del
dispositivo o sesión y versión del protocolo.

Su función es doble: da integridad byte a byte del fichero exportado y, sobre todo, **bloquea la
carga del criterio experto antes de que el sistema se haya comprometido**. Esa segunda función es
la que convierte el sello en diseño experimental y no en un adorno criptográfico.
**Estado: hecho probado.**

### 5.2 Anclaje externo — RFC 3161

El sello local no demuestra *cuándo* existió el contenido ante un tercero. Para eso se ancla en
una autoridad de sellado de tiempo mediante el estándar RFC 3161. Dos condiciones:

- El token se guarda en una **tabla de anclaje aparte**, append-only, de modo que añadirlo no
  modifica el sello base ni obliga a reexportar el histórico (ADR-014).
- La autoridad debe estar **en la Unión Europea**. Una autoridad fuera de ella reintroduciría la
  dependencia de jurisdicción ajena que el diseño evita por otras vías.

**Estado: parcialmente resuelto.** El formato, la tabla de anclaje, la extracción del
`messageImprint` y el verificador están construidos y probados con tokens sintéticos. **El
anclaje contra una autoridad real nunca se ha ejecutado.** Es la única pieza del núcleo que sigue
sin contrastarse con el mundo, y así debe declararse mientras lo sea.

### 5.3 Raíces de jornada

Para agrupar las cadenas de todos los dispositivos en un único punto de anclaje se computará una
raíz por jornada o por lote, que es lo que se sella. Reduce el coste del anclaje y da un único
valor que comprobar.

**Estado: no existe.** El sistema encadena hoy por dispositivo —decisión deliberada, porque en
campo no hay un orden total entre dispositivos (ADR-009)— y la raíz de jornada es trabajo nuevo,
compatible con lo construido pero no construido. `[POR CONSTRUIR ANTES DEL ANCLAJE REAL]`

### 5.4 Hueco reservado para la firma cualificada

El esquema y el formato de exportación reservan el espacio de una firma o certificado cualificado
futuro, de modo que integrarlo no obligue a rehacer el esquema ni a reinterpretar el histórico.
**Estado: decisión de diseño**, con el hueco ya presente en el formato.

## 6. Auditoría externa

La validez del protocolo depende de poder comprobarse fuera de la infraestructura que origina los
datos.

### 6.1 Marcado de origen

Queda prohibida la mezcla de datos reales y sintéticos sin distinción técnica inequívoca. Cada
registro lleva una columna `origen`, no nula, inmutable y restringida a un conjunto cerrado de
valores. En el sistema implementado ese conjunto tiene **tres** valores y no dos:

| Valor | Qué es | Equivalencia con la nomenclatura de trabajo |
|---|---|---|
| `real` | Dato medido en campo en la campaña en curso | `campo` |
| `simulado` | Dato generado para demostración o prueba | `sintetico` |
| `historico` | Dato de campaña anterior o de fuente documental | *sin equivalente* |

El tercer valor no es un lujo: un dato de campaña anterior no es sintético —fue real— pero
tampoco es dato de la campaña en curso, y confundirlos falsearía cualquier contraste. La
nomenclatura de dos valores queda como sinónimo de trabajo; **la del motor es la que manda**
(ADR-034).

Toda exportación que contenga registros no reales lo declara en la cabecera de metadatos. Esto
importa especialmente en demostración y transferencia tecnológica, donde el dato sintético es
útil pero no debe confundirse con evidencia operativa. **Estado: hecho probado.**

### 6.2 Recomputación

Un verificador independiente debe poder, sin la aplicación y sin credenciales:

1. Extraer contenido y metadatos del paquete.
2. Recomputar el hash byte a byte con el algoritmo y la versión declarados.
3. Comprobar la coherencia del sello local.
4. Validar el anclaje temporal externo cuando exista, y decir que no existe cuando no.
5. Rechazar el paquete completo ante cualquier discrepancia.

Un solo byte alterado invalida la exportación. El verificador es un guion autónomo que no depende
de la aplicación ni de la base de datos, y devuelve un código de salida utilizable en una cadena
de integración. **Estado: hecho probado**, salvo el paso 4 contra autoridad real.

## 7. Estado de la evidencia

Qué está probado en software y qué no existe todavía en campo. Es la tabla que evita que este
protocolo se lea como una descripción de un sistema en operación.

| Propiedad | En software | En campo |
|---|---|---|
| Inmutabilidad en el motor, tres capas | **Probada** | Sin desplegar en operación real |
| Ingesta idempotente | **Probada** | Sin dispositivos reales |
| Corrección como evento nuevo | **Probada** | Sin uso real |
| Orden por secuencia, no por hora | **Probada**, con regresión | — |
| Determinismo del hash | **Probada**, con vectores congelados | — |
| Sello local previo al criterio experto | **Probada** | Sin técnico real |
| Verificación por un tercero | **Probada** | Sin auditor real |
| Marcado de origen | **Probada** | — |
| Catálogo E-01…E-11 completo | Parcial (3 de 11) | No |
| Raíz de jornada | **No existe** | No |
| Anclaje RFC 3161 real | Solo tokens sintéticos | No |
| Credenciales rotables y revocación auditada | **No existe** | No |

## 8. Soberanía del dato

El protocolo es también el instrumento con el que se comprueba la soberanía del dato, que se
declara desglosada y nunca como un todo (ADR-032).

| # | Propiedad | Estado | Qué falta para subirlo |
|---|---|---|---|
| S-1 | El dato nace y se usa donde se genera | Decisión de diseño | Medir la operación completa sin cobertura |
| S-2 | La inferencia no depende de un proveedor externo | **Hecho probado** | — |
| S-3 | La residencia es europea y conocida | Pendiente de auditoría | Auditoría de residencia |
| S-4 | La verificación no exige confiar en el sistema | **Hecho probado** | Contraste con un verificador ajeno |
| S-5 | Sin dependencia de red pública ni de jurisdicción ajena para verificar | Decisión de diseño | Elegir autoridad de sellado en la UE |
| S-6 | Las personas no quedan atrapadas en el asiento | Parcialmente resuelto | Cerrar `owner_ref` y su base legal |

S-5 se enuncia como **objetivo de diseño del formato verificable**, no como garantía: el paquete
está construido para poder comprobarse sin recurrir a una red pública, y esa propiedad se
sostiene mientras el formato y el verificador se conserven.

## 9. Riesgos

Un protocolo sin tabla de riesgos se lee como una promesa. Estos son los que condicionan su
cumplimiento, y ninguno se resuelve escribiendo más código.

| Cód. | Riesgo | Efecto sobre el protocolo | Se resuelve |
|---|---|---|---|
| R-01 | No hay autorización de vuelo ni parcela comprometida | Sin eventos E-01…E-03 reales | F0 |
| R-02 | Sin acuerdo de acceso al protocolo de muestreo de un tercero | Falta el eslabón de verdad de campo | F0, depende de tercero |
| R-03 | Sin interlocutor industrial | La cadena de custodia no llega al final | F3, depende de tercero |
| R-04 | El indicador derivado del sensor puede no aportar | No afecta a la integridad, sí al valor del contraste | F2 |
| R-05 | Residencia europea sin auditar | S-3 no puede afirmarse | F2–F3 |
| R-06 | Autoridad de sellado sin elegir ni probar | El anclaje externo sigue sin existir | Antes del sello real |
| R-07 | Acceso administrativo directo al motor | Elude las capas 1 y 2 | Plan de despliegue |
| R-08 | Marca física que no sobrevive a la campaña | Rompe la continuidad de la cadena entre campañas | F1 |
| R-09 | `owner_ref` sin base legal cerrada | Conflicto entre append-only y derecho de supresión | Revisión jurídica |

## 10. Criterios de aceptación

El protocolo se considera cumplido solo si el sistema **falla** en todos estos casos. Están
escritos como propiedades comprobables, y todos salvo los tres últimos tienen hoy prueba
automatizada.

1. Falla si una sentencia `UPDATE`, `DELETE` o `TRUNCATE` es aceptada sobre una tabla de asiento
   por el rol de aplicación previsto.
2. Falla si un disparador de sentencia no rechaza esa misma operación sobre una tabla vacía.
3. Falla si se puede insertar un registro con `origen` nulo o con un valor fuera del conjunto
   cerrado.
4. Falla si `origen` puede modificarse después de la inserción.
5. Falla si un identificador duplicado genera una segunda fila en lugar de tratarse como
   reintento idempotente.
6. Falla si una corrección elimina, oculta o hace inconsultable el registro original.
7. Falla si el criterio experto puede cargarse antes de que exista sello del resultado del
   sistema.
8. Falla si dos eventos escritos en la misma transacción quedan sin orden determinista.
9. Falla si un paquete exportado con un byte alterado supera la recomputación del sello.
10. Falla si un paquete con registros no reales no lo declara en su cabecera de metadatos.
11. Falla si la verificación exige la aplicación, la base de datos o credenciales.
12. `[POR PROBAR]` Falla si el anclaje contra una autoridad de sellado real no se valida.
13. `[POR CONSTRUIR]` Falla si la raíz de jornada no agrupa todas las cadenas de dispositivo de
    esa jornada.
14. `[POR CONSTRUIR]` Falla si una credencial de dispositivo revocada sigue admitiéndose, o si su
    revocación no queda registrada.

## 11. Encaje por línea de proyecto

El núcleo de este protocolo es neutral respecto al dominio, y esa neutralidad es lo que permite
escribirlo una vez y aplicarlo en varias líneas. Lo que cambia por proyecto es el catálogo de
eventos, el interlocutor que audita y la naturaleza de la verdad de campo.

**SUBER UAV — alcornocal y cadena del corcho.** La verdad de campo la aporta el muestreo del
técnico, y el protocolo la digitaliza y la conecta con lo que ocurre después: saca, lote y
resultado industrial. La clase de calidad se **registra con método y autoría**, nunca se infiere
(ADR-027), y el muestreo se referencia, nunca se deduce del sensor (ADR-030). El interés sectorial
en digitalización y coordinación de la cadena de valor es el que da sentido a que la evidencia sea
portátil entre monte, laboratorio, industria y auditoría.

**SENDA — recursos hídricos.** La verdad de campo la aportan medidas instrumentales y el
interlocutor que audita es un organismo regulador, con exigencias de rastro documental y de
plazos. Cambian los eventos de captura y el destinatario del paquete; no cambian el asiento, la
corrección por evento, el sello ni la verificación.

**Líneas futuras.** El requisito para incorporarse es el mismo: declarar su catálogo de eventos,
declarar qué significa `origen` en su dominio y aceptar los catorce criterios de aceptación. Lo
que no se admite es aflojar una garantía del núcleo para acomodar un dominio: si un dominio
necesita mutabilidad, lo que necesita es una tabla de eventos.

## 12. Cierre

Este protocolo define una práctica de registro cuyo resultado puede ser comprobado por quien no
participó en producirlo. Su utilidad no está en la solidez de sus afirmaciones, sino en que cada
una de ellas lleva escrito su estado y puede ser contrastada: lo probado con la batería de
pruebas, lo decidido con el registro de decisiones, lo pendiente con la lista de riesgos.

En un ámbito donde la veracidad de los datos sobre recursos naturales interesa a reguladores,
centros de investigación y socios industriales, esa es la propiedad que hace transferible el
trabajo. El resto —la retórica de la inviolabilidad— se agota en la primera comprobación.
