# Contexto de continuidad — CASTÚO-SYSTEM™ SUBER UAV

Bloque para pegar al inicio de una sesión de trabajo. Es un resumen operativo: la fuente de
verdad es `docs/CASTUO_CORCHO_ESTADO_MAESTRO.md`, y ante discrepancia manda ese archivo.

---

Trabajamos sobre **CASTÚO-SYSTEM™ SUBER UAV** (antes UAV Cork / CASTÚO-CORCHO UAV), sistema de
diagnóstico y trazabilidad digital del alcornocal, dentro de la capacidad CAP-OFFLINE-FIRST-001.
El nombre de producto cambió; los archivos, el identificador de capacidad y la numeración ADR
no (ADR-025). No es una plataforma de corte aéreo.

**Encaje — importante**
SUBER UAV es una **fase y etapa exterior** a la arquitectura de CASTÚO-SYSTEM (ADR-031). No forma
parte de sus treinta y seis módulos. Despliegue, calendario y frontera propios. Entrega su
resultado como **paquete verificable con formato declarado**, nunca como escritura en la base de
datos de nadie: ninguno de los dos lados escribe en el otro. Esa exterioridad no es una
concesión, es lo que hace auditable el argumento de soberanía del dato.

**Soberanía del dato — desglosada, nunca como principio**
Seis propiedades con estado individual: S-1 el dato nace y se usa donde se genera · S-2 la
inferencia no depende de proveedor externo · S-3 residencia europea conocida · S-4 verificación
sin confiar en el sistema · S-5 sin red pública ni jurisdicción ajena · S-6 personas fuera del
asiento. **Tres probadas, dos sin auditar, una a medias.** Nunca se afirma la soberanía como un
todo. Detalle en `docs/SOBERANIA_DEL_DATO.md`.

**Fortalezas — regla de redacción**
Ninguna fortaleza se enuncia por encima de su nivel de evidencia EQ1–EQ6 del README; lo que no
tiene nivel es decisión de diseño y se dice así. Preprototipo en presente, prototipado con su
gate. Once fortalezas y siete compromisos en `docs/FORTALEZAS.md`, que es de donde se copian las
frases al escribir una memoria. Ninguna fortaleza se usa como respuesta a una pregunta sobre un
hueco.

**La frase que ordena el proyecto**
El dron no es el producto: es un sensor dentro de una infraestructura de evidencia para el
alcornocal, capaz de complementar la metodología experta, no de sustituirla.

**Léxico — obligatorio**
Formulación aprobada: «el sistema identifica indicadores de riesgo sanitario y estima variables
de interés productivo mediante datos UAV y observaciones de campo, con revisión técnica y
trazabilidad de la evidencia». La formulación prohibida y las ocho reglas están en
`docs/POSICIONAMIENTO_SUBER.md`, apartado 3, y las comprueba `scripts/check_lexico.py`, que está
en la batería de pruebas. Si escribes un documento y el comprobador falla, el documento está
mal, no el comprobador.

**Estado real**
Pre-prototipo. Núcleo de evidencia construido, endurecido y probado con datos sintéticos: 284
pruebas contra PostgreSQL 16 real. Banco de sensores en montaje. Sin vuelos de ensayo, sin datos
de campo validados, sin autorización de vuelo. **Sin ningún acuerdo con CICYTEX, PTEcor,
FUNDECYT ni industria.**

**Dos modelos, no uno**
El núcleo (`asset`, `detection`, `review`, `trace_event`) es agnóstico del dominio a propósito.
El modelo sectorial (13 entidades: `tree`, `cork_sample`, `cork_lot`, `industrial_result`…) es
una capa encima (ADR-026). De esas 13: **una construida, cuatro parciales, ocho ausentes**. No
presentes el modelo de dominio como si fuera el esquema.

**Métrica del piloto**
Orden de prioridad por unidad de ordenación. El ranking del sistema se emite, se congela y se
sella antes de introducir el orden del corchero. Ningún ranking se sobrescribe. La restricción
vive en la base de datos, no en el código de aplicación.

**Dos parejas que no hay que confundir**
Sello local (bloquea la carga del orden experto) vs sello certificado RFC 3161 (permite
verificación por un tercero). Y trazabilidad (cadena registrada y verificable: objetivo 2027) vs
correlación monte–fábrica (modelo explicativo: varias campañas, ADR-028).

**Arquitectura**
Cuatro capas: captura (UAV + campo + cala + fábrica) → CASTÚO Evidence Core (FastAPI +
PostgreSQL 16, Hetzner CX22) → SABIONDA (análisis) → industria. Borde: Raspberry Pi 5. Tajo:
Android offline, no construido. Entre vuelo y borde viajan descriptores por árbol, no nubes de
puntos densas.

**Asiento**
Eventos append-only defendidos en tres capas. Cadena de hashes por dispositivo con `sequence_no`,
no cadena global. Ingesta idempotente por UUID. Correcciones mediante evento nuevo, nunca
borrado. `origen ∈ {real, simulado, historico}` obligatorio e inmutable. Identidades fuera del
asiento. Una clase de calidad no la firma un modelo (ADR-027); la cala se referencia, nunca se
infiere (ADR-030).

**No construir todavía**
Decisión automática. Corte físico. Láser de potencia. Nodo blockchain propio. Facturación.
Multiusuario avanzado. Android. Trazalia. Cifras de exactitud, ahorro o rendimiento no validadas.
Modelos de correlación monte–fábrica. Las entidades de dominio ausentes que el incremento en
curso no necesite.

**Alcance de madurez**
El trabajo actual completa N3 (Implementation Complete) sobre la capacidad, que el README declara
en N2. No saltar a N4 (validación de integración) ni a N5 (piloto de campo).

**Decisiones abiertas**
A-01 unidad de ordenación · A-02 marca física · A-03 autoridad de sellado **en la UE** · A-04 Trazalia ·
A-05 protocolo de concordancia y umbral, que se pacta antes de mirar ningún dato · **A-06
acuerdo sobre el protocolo de calas (bloquea el Piloto 2)** · **A-07 interlocutor industrial
(bloquea el Piloto 3)** · A-08 escala de clases de calidad · A-09 titularidad del dato de monte.

**Regla de prudencia**
No inventar cifras, validaciones, apoyos institucionales ni capacidades. Separar siempre: hecho
validado · decisión de diseño · hipótesis · dato histórico · dato simulado · pendiente de probar.

**Protocolo de integridad**
`docs/PROTOCOLO_INTEGRIDAD.md` es el estándar transversal (SUBER UAV y SENDA). Cada afirmación
lleva uno de cuatro estados: hecho probado · decisión de diseño · pendiente de auditoría ·
parcialmente resuelto. Ante discrepancia entre el protocolo y el motor, **manda el motor**
(ADR-034): `origen` tiene tres valores, no dos. Catorce criterios de aceptación, once con
prueba. **La raíz de jornada no existe**: el motor encadena por dispositivo, y esa raíz hace
falta antes del primer anclaje real.

**Cómo crece el proyecto**
`docs/PLAN_CRECIMIENTO.md`. Tres vías en paralelo: **A interna** (no depende de nadie),
**B de campo**, **C de acuerdos** — y la C se abre el primer día. Se avanza por **UMD**
(unidad mínima demostrable): atraviesa el sistema de punta a punta y deja evidencia.
UMD-1 hecha (misión, observación, reidentificación, G1 en seco); UMD-2 es la siguiente.
Reglas duras: ningún gate se intenta en campo sin ensayo en seco que pase (ADR-037); la
carga útil sube por peldaños P0–P3 y el peldaño se registra en cada misión (ADR-036); toda
afirmación por encima de su nivel de evidencia se registra como deuda con gate de
vencimiento (ADR-038).

**Tarea de esta sesión**
Antes de escribir código, leer `docs/CASTUO_CORCHO_ESTADO_MAESTRO.md`, contrastarlo con el
repositorio, señalar las contradicciones que encuentres y proponer el plan de archivos. No
ampliar el alcance más allá del incremento en curso.
