# CASTÚO-SYSTEM™ SUBER UAV — Posicionamiento sectorial

**Documento:** v0.1 · 2026-09-02
**Sustituye a:** el encuadre genérico «demostrador aero-forestal» de v0.1–v0.4 del estado maestro.
**No sustituye a:** `CASTUO_CORCHO_ESTADO_MAESTRO.md`, que sigue siendo la fuente de verdad
del estado real y del alcance.
**Encaje:** SUBER UAV es una **fase exterior** a la arquitectura de CASTÚO-SYSTEM, no uno
de sus módulos (ADR-031). Ver apartado 12 y `SOBERANIA_DEL_DATO.md`.

Este documento fija **qué es el sistema para el sector del corcho** y, sobre todo, **qué no
puede decirse de él**. El estado maestro dice dónde está el proyecto; este dice cómo se
nombra y ante quién.

---

## 1. La frase que define el sistema

> El dron no es el producto. Es un sensor dentro de una infraestructura de evidencia para el
> alcornocal, capaz de complementar la metodología experta, no de sustituirla.

De ahí se deriva todo lo demás. Presentar un dron con cámara multiespectral es competir en un
mercado de agricultura de precisión ya poblado, con un argumento —«más resolución, más
hectáreas por hora»— que cualquiera puede igualar comprando el mismo sensor. Presentar una
capa de diagnóstico y trazabilidad del alcornocal es otra cosa: el activo no es el vuelo, es
la serie temporal georreferenciada, el vínculo árbol→lote→resultado industrial y la evidencia
verificable de que ese vínculo no se ha tocado.

Y hay una segunda razón, menos evidente y probablemente más importante a medio plazo. CASTÚO-SYSTEM declara la soberanía del dato como principio fundacional; declararla en una plataforma de cinco núcleos y treinta y seis módulos es fácil, y demostrarla es otra cosa. SUBER UAV es lo bastante pequeño como para recorrer la cadena entera —captura, inferencia, sello, verificación— y enseñarla. **Es una fase exterior a esa arquitectura, y lo es a propósito:** un sistema con frontera propia se puede auditar en una tarde, y lo que se puede auditar en una tarde deja de ser una declaración para convertirse en una propiedad.

**Nombre corto:** SUBER UAV.
**Descripción de una línea:** sistema de diagnóstico y trazabilidad digital del alcornocal.

## 2. Qué es y qué no es

**Es** una capa de captura, ordenación, registro auditable y contraste sobre el alcornocal,
que produce evidencia reutilizable por el propietario forestal, el técnico, el investigador y
la industria transformadora.

**No forma parte de los treinta y seis módulos de CASTÚO-SYSTEM**: es una etapa exterior con despliegue, calendario y
frontera propios, que entrega su resultado como paquete verificable y no como escritura en la
base de datos de nadie. Tampoco es un producto genérico de agricultura de precisión, ni un
sustituto de la cala, ni un clasificador automático de calidad del corcho, ni una plataforma
de corte aéreo. El descorche
lo siguen haciendo los corcheros a mano; el sistema excluye por diseño cualquier tecnología de
corte energético sobre el alcornoque.

## 3. Léxico controlado

Esta sección es normativa. Toda comunicación del proyecto —memorias, presentaciones, textos de
solicitud, respuestas a un tercero— se somete a ella.

<!-- lexico:off -->
### 3.1 Formulación prohibida

> «El dron detecta enfermedades y determina automáticamente la calidad del corcho.»

Es falsa en dos puntos y cara en un tercero. Falsa porque un sensor aéreo no diagnostica una
enfermedad: registra reflectancia, de la que se derivan indicadores. Falsa porque la clase de
calidad del corcho —grueso, bueno, flaco, delgado, refugo— se establece por metodología de
campo y laboratorio, no por teledetección. Y cara porque, dicha una vez ante un organismo de
investigación, invalida el resto del discurso: quien conoce el sector sabe que no es cierto y
deja de escuchar.
<!-- lexico:on -->

### 3.2 Formulación aprobada

> «El sistema identifica indicadores de riesgo sanitario y estima variables de interés
> productivo mediante datos UAV y observaciones de campo, con revisión técnica y trazabilidad
> de la evidencia.»

Es más larga y menos vendible. También es defendible delante de un técnico de CICYTEX, que es
el único público cuya opinión cambia el proyecto.

### 3.3 Reglas de escritura

<!-- lexico:off -->
| No escribir | Escribir |
|---|---|
| detecta enfermedades | identifica indicadores de riesgo sanitario |
| determina la calidad | estima variables de interés productivo |
| clasifica el corcho | registra la clase asignada por el técnico, con método y autoría |
| sustituye la cala | complementa y digitaliza la cala |
| automáticamente / sin intervención | con revisión técnica |
| precisión del NN % | `[POR MEDIR EN F2]` |
| validado por | `[PENDIENTE: no existe acuerdo]` |
<!-- lexico:on -->

### 3.4 Mecanismo

Una norma de redacción en un documento la respeta quien la ha leído y se acuerda. Por eso el
léxico se comprueba: `scripts/check_lexico.py` recorre `docs/`, `README.md` y los textos
publicables, y termina con código 1 si encuentra una formulación prohibida. Está en la batería
de pruebas (`tests/docs/test_lexico.py`). Ver ADR-029.

El mecanismo cubre la redacción. La sustancia la cubre el motor: una revisión no puede
registrarse como `accepted_by_expert` sin sello previo del orden del sistema, y una clase de
calidad no entra sin método y autoría declarados (ADR-027). El sistema no puede afirmar por su
cuenta lo que la frase prohibida afirma, aunque alguien lo escriba en un folleto.

## 4. Alineación con el Plan de Calas

> **Origen del dato de esta sección:** descripción aportada por el promotor, no verificada
> contra fuente documental de CICYTEX. `[POR CONFIRMAR CON FUENTE]`

La cala es el procedimiento por el que se abre una ventana en la corteza y se mide el corcho
antes de decidir la saca. Según la descripción disponible, el Plan de Calas de CICYTEX recoge
del orden de 75 muestras por explotación, con datos sanitarios y selvícolas, georreferenciadas,
y persigue una serie histórica que permita modelizar en 2026 el corcho de ocho años.

**La posición del sistema respecto a ese plan es de extensión, no de sustitución.** En
concreto:

| El Plan de Calas aporta | SUBER UAV aporta |
|---|---|
| Verdad de campo medida en el árbol | Cobertura continua entre calas |
| Metodología aceptada por el sector | Registro inalterable y verificable de cada observación |
| Serie histórica en construcción | Georreferenciación, versionado de modelo y contraste ciego |
| Clase de calidad asignada por técnico | El vínculo de esa clase con el lote y con el resultado industrial |

La cala sigue siendo la referencia. El sistema la digitaliza, la sitúa en el tiempo y en el
espacio, y la conecta con lo que ocurre después. Cuando el sistema y el técnico discrepan, el
dato del técnico es el que manda y la discrepancia se registra: ese registro es precisamente el
material que permite mejorar el modelo, y es también la razón por la que el orden del sistema se
sella antes de conocer el del experto (ADR-007).

**Lo que el sistema no hace:** no propone clases de calidad, no reduce el número de calas, no
reemplaza la observación sanitaria del técnico. Cualquier afirmación en ese sentido está fuera
del léxico aprobado.

## 5. Arquitectura funcional en cuatro capas

```
  Capa 1 — Captura                Capa 2 — Núcleo de evidencia
  UAV (RGB, multiespectral,       CASTÚO Evidence Core
  LiDAR) · técnico en campo       append-only · hash por dispositivo
  · cala · báscula · fábrica      sello local + RFC 3161 · origen obligatorio
          │                                    │
          └────────── descriptores ────────────┘
                                               │
  Capa 4 — Industria              Capa 3 — Análisis
  lote · proceso · resultado      SABIONDA
  retroalimenta la capa 3         indicadores · ordenación · concordancia
```

**Capa 1 — UAV y campo.** Fuentes de dato heterogéneas con una propiedad común: cada
observación nace con `origen ∈ {real, simulado, historico}`, inmutable desde la primera
migración (ADR-011). Entre el vuelo y el borde viajan descriptores por árbol, no nubes de
puntos densas.

**Capa 2 — CASTÚO Evidence Core.** Es lo que está construido hoy. Eventos append-only
defendidos en tres capas —permisos, disparadores de fila y de sentencia, pruebas de integración
contra PostgreSQL real (ADR-013, ADR-020)—, cadena de hashes por dispositivo (ADR-009, ADR-018),
canonicalización versionada con dos implementaciones independientes (ADR-019), sello local y
anclaje certificado separados (ADR-008, ADR-014). Esta capa es agnóstica del dominio a
propósito: no sabe qué es un alcornoque.

**Capa 3 — SABIONDA (análisis).** Calcula indicadores, ordena unidades por prioridad de
intervención y mide la concordancia con el orden del técnico. Hoy existe la ordenación
determinista y las métricas de concordancia (Spearman, Kendall tau-b, con versión de método
declarada); no existe ningún modelo entrenado con datos reales.

**Capa 4 — Industria.** Lote, proceso y resultado. **No existe.** Es el objeto del Piloto 3 y
depende de un interlocutor industrial que todavía no hay (A-07).

## 6. La cadena de correlación monte → fábrica

```
árbol → observación UAV → cala → clase de calidad → saca → lote → proceso → resultado
```

Es la razón de ser del sistema y también su afirmación más delicada. Hay que separar dos
objetivos que suenan igual y no lo son:

| | Trazabilidad | Correlación |
|---|---|---|
| Qué es | Que la cadena esté registrada y sea verificable | Que las variables de monte expliquen el resultado industrial |
| Qué exige | Un eslabón por evento, sin huecos | Varias campañas y varianza suficiente |
| Cuándo se puede demostrar | Campaña 2027 | No antes de varias campañas `[POR MEDIR]` |
| Estado | Núcleo construido; eslabones de lote e industria no | Ninguno |

**El piloto de 2027 cierra la trazabilidad, no la correlación.** Presentar la correlación como
resultado del primer piloto es la segunda forma de sobreafirmación más probable de este
proyecto, después de la frase del apartado 3.1. Ver ADR-028.

## 7. Capacidades de análisis

Ordenadas por lo que hace falta para tenerlas. Ninguna está validada.

| # | Capacidad | Requiere | Estado |
|---|---|---|---|
| CA-01 | Inventario georreferenciado de pies | Vuelo + extracción de descriptores | Esquema listo, sin dato real |
| CA-02 | Estimación de variables dendrométricas | LiDAR + calibración con campo | `[POR MEDIR EN F1]` |
| CA-03 | Indicadores de riesgo sanitario | Multiespectral + observación del técnico | `[POR MEDIR EN F2]` |
| CA-04 | Ordenación por prioridad de intervención | CA-01 + CA-02 | Implementada con pesos provisionales |
| CA-05 | Concordancia con el orden del técnico | CA-04 + protocolo pactado | Implementada; umbral abierto (A-05) |
| CA-06 | Serie temporal por árbol entre campañas | Marca física persistente (A-02) | No |
| CA-07 | Vínculo cala ↔ árbol ↔ observación UAV | Acuerdo de acceso al protocolo (A-06) | No |
| CA-08 | Vínculo árbol → lote → resultado industrial | Interlocutor industrial (A-07) | No |
| CA-09 | Explicabilidad de la salida del modelo | CA-03, CA-04 | Parcial: versión y pesos declarados |
| CA-10 | Verificación por un tercero sin acceso al sistema | Anclaje RFC 3161 real | Verificador construido; anclaje sin probar |

## 8. Requisitos

### 8.1 Funcionales

| # | Requisito | Estado |
|---|---|---|
| RF-01 | Registrar cada observación con origen, autor y momento | Hecho |
| RF-02 | Impedir la modificación y el borrado de lo registrado | Hecho (tres capas) |
| RF-03 | Emitir una ordenación de unidades con modelo identificado por versión | Hecho |
| RF-04 | Congelar y sellar la ordenación antes de conocer el criterio experto | Hecho |
| RF-05 | Registrar el criterio experto y medir la concordancia con umbral previo | Hecho |
| RF-06 | Exportar un paquete verificable fuera de la aplicación | Hecho |
| RF-07 | Ingerir evidencia desde dispositivo autenticado, de forma idempotente | Hecho, provisional |
| RF-08 | Registrar la cala con su protocolo, método y autoría | **No** |
| RF-09 | Registrar la clase de calidad asignada, nunca inferirla | **No** |
| RF-10 | Agrupar árboles en lote y seguir el lote hasta la industria | **No** |
| RF-11 | Operar sin cobertura y sincronizar después sin duplicar | Parcial: idempotencia sí, app no |
| RF-12 | Anclar la evidencia en una autoridad de tiempo externa | Construido, sin probar |

### 8.2 No funcionales

| # | Requisito | Estado |
|---|---|---|
| RNF-01 | Reproducibilidad: el esquema se levanta desde cero con las migraciones | Hecho |
| RNF-02 | Determinismo: la misma entrada produce el mismo hash | Hecho, 14 vectores congelados |
| RNF-03 | Las invariantes viven en el motor, no en la aplicación | Hecho |
| RNF-04 | Los datos simulados son distinguibles en salida y exportación | Hecho |
| RNF-05 | Soberanía del dato: infraestructura y modelos en la UE | Decisión de diseño |
| RNF-06 | Protección de datos: sin identidades personales en tablas append-only | Parcial (`owner_ref`, incoherencia 4) |
| RNF-07 | Operación en monte sin cobertura, con autonomía local | Decisión de diseño |
| RNF-08 | Coste de despliegue compatible con explotación forestal media | `[POR MEDIR]` |
| RNF-09 | Credenciales de dispositivo rotables, caducables y revocables con auditoría | **No** |
| RNF-10 | Ningún dato de un tercero se publica sin su titularidad resuelta (A-09) | **No** |

### 8.3 Datos mínimos por entidad

Ver `MODELO_DATOS_SECTORIAL.md`. Regla común a todas: identificador propio, referencia
espacial, momento, autoría, `origen`, y versión de método cuando el dato lo produce un modelo.

## 9. Cualidades que debe conservar el sistema

1. **Complementariedad.** Complementa la metodología experta; no la sustituye.
2. **Reversibilidad de la lectura.** Todo dato derivado remite al dato bruto que lo originó.
3. **Inmutabilidad.** Lo registrado no se edita; se corrige con un evento nuevo.
4. **Explicabilidad.** Toda salida de modelo lleva versión, pesos y entrada identificables.
5. **Verificabilidad por terceros.** Sin acceso al sistema y sin confiar en él.
6. **Honestidad del origen.** Real, simulado o histórico, marcado desde el esquema.
7. **Prudencia declarativa.** Los huecos se marcan; no se rellenan con cifras plausibles.
8. **Autonomía en campo.** Funciona sin cobertura y sin nube.
9. **Soberanía.** Dato e inferencia dentro de la UE.
10. **Neutralidad de dominio del núcleo.** El núcleo de evidencia no depende del corcho, lo que
    permite reutilizarlo y, a la vez, obliga a documentar aparte la capa sectorial.

## 10. Los cinco bloques que sostienen la credibilidad

**Validación científica.** El contraste ciego, con umbral registrado antes de mirar el dato
(ADR-022), y el orden del sistema sellado antes de conocer el del experto (ADR-023). Es la
diferencia entre un piloto y una demostración.

**Modelo de correlación monte–fábrica.** Documentado como objetivo plurianual y separado de la
trazabilidad, que es lo que sí cierra 2027 (ADR-028).

**Explicabilidad de la IA.** Versión de modelo obligatoria, pesos declarados, entradas
identificables, salida no editable. Hoy los pesos de la ordenación son una decisión de diseño
provisional (`perimetro_rel` 0,6 · `altura_rel` 0,4) `[POR MEDIR EN F2]`.

**Trazabilidad jurídica y técnica.** Append-only defendido en el motor, cadena por dispositivo,
sello local y anclaje certificado separados. Pendiente: `owner_ref` y su base legal, y la
titularidad del dato de monte (A-09).

**Métricas de desempeño.** Precisión, sensibilidad, falsos positivos, repetibilidad y
concordancia con el técnico experto. Ninguna medida todavía; el protocolo y los umbrales se
pactan en F0 y se registran antes de la primera medida.

## 11. Qué cambia y qué no respecto al corpus anterior

| | Antes | Ahora |
|---|---|---|
| Nombre de producto | UAV Cork / CASTÚO-CORCHO UAV | CASTÚO-SYSTEM™ SUBER UAV |
| Encuadre | Demostrador aero-forestal | Capa de diagnóstico y trazabilidad del alcornocal |
| Papel del dron | Componente principal | Un sensor entre varios |
| Interlocutor de referencia | Sector genérico | CICYTEX, PTEcor, industria transformadora |
| Identificador de capacidad | `CAP-OFFLINE-FIRST-001` | Sin cambio (ADR-025) |
| Nombres de archivo y series ADR | — | Sin cambio (ADR-025) |
| Núcleo implementado | — | Sin cambio: sigue siendo válido y no se rediseña |
| Encaje | Demostrador dentro de la capacidad | **Fase exterior** a la arquitectura (ADR-031) |
| Frontera con la plataforma | Implícita | Paquete verificable, no conexión |
| Papel respecto a la soberanía del dato | Ninguno declarado | Banco de prueba de las seis propiedades (S-1…S-6) |

El repositorio no se renombra. El posicionamiento cambia el discurso y añade una capa de
dominio; no invalida una línea del núcleo construido.

## 12. Encaje: una fase exterior que refuerza la soberanía del dato

SUBER UAV **no es uno de los treinta y seis módulos** de CASTÚO-SYSTEM. Es una fase y una etapa
exterior a esa arquitectura, con su propio despliegue, su propio calendario —el del monte, no el
de la plataforma— y su propia frontera. Lo que entrega no es una escritura en la base de datos de
la plataforma: es un **paquete verificable** con formato declarado, que se comprueba sin acceso a
ninguno de los dos lados.

Cuatro consecuencias, y las cuatro son de ingeniería antes que de discurso:

1. **Aislamiento de fallo.** Un demostrador que todavía no ha volado no puede desestabilizar una
   plataforma en producción. Y al revés: un cambio interno de la plataforma no obliga a rehacer
   nada aquí.
2. **Cadencia propia.** La saca ocurre entre junio y agosto y no se aplaza. Un componente interno
   habría quedado sujeto al ciclo de la plataforma, que responde a otras prioridades.
3. **Contrato explícito.** Al ser exterior, la interfaz **tiene que** declararse. Un módulo
   interno comparte esquema y nunca llega a escribir el contrato, que es justo lo que hace que la
   evidencia sea portátil y auditable.
4. **Reversibilidad.** Si el piloto no confirma sus hipótesis, no hay nada que deshacer dentro de
   la plataforma. El coste del fracaso queda acotado al propio piloto.

Y una consecuencia que es de soberanía: **el dato de monte no queda cautivo de la plataforma que
lo consume.** Los paquetes emitidos siguen siendo verificables aunque SUBER UAV desaparezca,
aunque la plataforma cambie por dentro, y aunque el propietario forestal decida llevarse su
información a otra parte.

La soberanía del dato se descompone aquí en seis propiedades comprobables —autonomía de campo,
inferencia propia, residencia europea, verificación sin confianza, ausencia de red pública y de
jurisdicción ajena, y personas fuera del asiento—, de las que **tres son hechos probados hoy y
tres no lo son**. El desglose completo, con lo que cuesta la exterioridad y con lo que todavía no
se puede afirmar, está en `SOBERANIA_DEL_DATO.md`.

## 13. Cómo se enuncian las fortalezas

Un preprototipo tiene que poder enseñar lo que ya vale sin prometer lo que aún no. La regla que
lo permite es una y no admite excepciones:

> Ninguna fortaleza se enuncia por encima de su nivel de evidencia, con la escala EQ1–EQ6 del
> README de la capacidad. Lo que no tiene nivel es una decisión de diseño, y se dice así.

Hoy: **once fortalezas del preprototipo**, nueve de ellas sostenidas por prueba automatizada
(EQ3), y **siete compromisos del prototipado** con su nivel de destino y su gate. Uno de esos
siete puede resolverse en negativo, y el sistema está construido para sobrevivir a ese resultado.
El detalle está en `FORTALEZAS.md`, que es el documento del que se copian las frases cuando hay
que escribir una memoria o preparar una reunión.
