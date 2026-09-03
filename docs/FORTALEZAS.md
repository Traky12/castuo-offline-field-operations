# Fortalezas por fase — preprototipo y prototipado

**Documento:** v0.1 · 2026-09-02

Este documento existe para poder **destacar** lo que el sistema tiene de fuerte sin que
destacarlo se convierta en sobreafirmar. La técnica es una sola y se aplica sin excepción:

> **Ninguna fortaleza se enuncia por encima de su nivel de evidencia.**

Los niveles son los del README de la capacidad, y no se inventa ninguno nuevo:

| Nivel | Qué es |
|---|---|
| EQ1 | Captura de pantalla |
| EQ2 | Registros de ejecución |
| EQ3 | Prueba automatizada |
| EQ4 | Medida de KPI |
| EQ5 | Validación independiente |
| EQ6 | Monitorización continua |
| — | Sin evidencia: es una **decisión de diseño**, y se dice así |

Una fortaleza con EQ3 se afirma en presente y sin condicionales. Una fortaleza sin nivel se
enuncia como propiedad buscada, no como capacidad. Esa distinción, sostenida en todos los
documentos, es lo que permite que el discurso sea enérgico sin ser falso: quien lo lea puede
comprobar cada línea, y a quien pueda comprobarlo no hay que convencerlo dos veces.

---

## Parte I — Fortalezas del preprototipo

Lo que ya es verdad hoy, antes de que el dron haya volado una sola vez. Todas menos dos están
sostenidas por la batería de 284 pruebas contra PostgreSQL real. Son once desde que la capa
de captura existe (UMD-1).

### F-01 · La evidencia no se puede alterar, y no por norma sino por construcción
**Nivel:** EQ3 · **Lo sostiene:** permisos de rol, disparadores de fila y de sentencia
—incluido `TRUNCATE` y las tablas vacías—, y pruebas de integración que lo intentan de verdad.
**Por qué es fuerte:** casi todos los sistemas que prometen inmutabilidad la defienden en el
código de aplicación, donde un script de mantenimiento la atraviesa sin resistencia. Aquí la
rechaza el motor.

### F-02 · Un tercero verifica sin acceso al sistema y sin confiar en él
**Nivel:** EQ3 · **Lo sostiene:** `scripts/verify_package.py`, autónomo, ocho comprobaciones,
código de salida 0/1, sin dependencia de la aplicación ni de la base de datos.
**Por qué es fuerte:** convierte «confíe en nosotros» en «compruébelo usted». Es el argumento que
funciona ante un auditor, un centro de investigación y un comprador industrial, y es el mismo
argumento para los tres.

### F-03 · El mismo dato produce siempre el mismo hash
**Nivel:** EQ3 · **Lo sostiene:** canonicalización versionada `CASTUO-CANON-1`, dos
implementaciones independientes y catorce vectores congelados que ambas deben reproducir.
**Por qué es fuerte:** sin determinismo, la verificación de un tercero falla por motivos que
nadie sabe explicar, y la confianza se pierde en la primera discrepancia.

### F-04 · El sistema se compromete antes de saber el resultado
**Nivel:** EQ3 · **Lo sostiene:** el orden del sistema se congela y se sella; el motor **rechaza**
el orden del experto si no hay sello previo; el umbral de aceptación se registra antes que los
datos.
**Por qué es fuerte:** es diseño experimental, no demostración comercial. Elimina de raíz la
sospecha de haber ajustado el listón después del salto, que es la primera pregunta de cualquiera
que sepa del asunto.

### F-05 · Un dato simulado no puede pasar por real
**Nivel:** EQ3 · **Lo sostiene:** `origen ∈ {real, simulado, historico}`, obligatorio, sin nulo e
inmutable desde la primera migración, y presente en salida y exportación.
**Por qué es fuerte:** permite enseñar el bucle completo con datos mixtos sin contaminar nada de
lo que se construya encima.

### F-06 · El orden de los hechos no depende del reloj
**Nivel:** EQ3 · **Lo sostiene:** secuencia por activo y por dispositivo, continuidad verificada
en el motor, asignación bajo bloqueo consultivo, y prueba de regresión con dos escritores
concurrentes.
**Por qué es fuerte:** nació de un defecto real encontrado y corregido en este proyecto: dos
transiciones en la misma transacción compartían hora y el estado vigente lo decidía un UUID.
Encontrarlo antes del campo es exactamente para lo que sirve un preprototipo.

### F-07 · El discurso está vigilado por la batería de pruebas
**Nivel:** EQ3 · **Lo sostiene:** `scripts/check_lexico.py`, trece patrones prohibidos, con
exención explícita para citar lo que se prohíbe, y veintidós pruebas.
**Por qué es fuerte:** es raro, y es útil. Detectó dos infracciones en la primera redacción de
los propios documentos de posicionamiento, escritos por quien acababa de redactar la norma.

### F-08 · El sistema se levanta desde cero y da el mismo resultado
**Nivel:** EQ3 · **Lo sostiene:** migraciones numeradas reproducibles, esquema recreado en cada
prueba, y dos guiones de demostración de extremo a extremo.
**Por qué es fuerte:** un preprototipo que solo funciona en la máquina de quien lo escribió no es
un preprototipo.

### F-09 · El núcleo no depende del corcho
**Nivel:** — (decisión de diseño, ADR-026) · **Lo sostiene:** el esquema no nombra ninguna
entidad del sector.
**Por qué es fuerte:** las garantías se prueban una vez y valen para todo lo que se apoye encima;
y el dominio puede cambiar sin tocar lo que ya está probado. **Lo que cuesta:** dos vocabularios
que mantener.

### F-10b · Una misión de vuelo real no se registra sin autorización
**Nivel:** EQ3 · **Lo sostiene:** un `CHECK` del esquema: `origen = 'real'` exige referencia
de autorización de vuelo, y la prueba lo intenta.
**Por qué es fuerte:** un vuelo real asentado sin autorización sería una evidencia que no se
puede enseñar a nadie. Es la clase de requisito que suele vivir en una lista de comprobación
y aquí vive en el motor.

### F-10 · La ordenación no llama a ningún servicio de terceros
**Nivel:** — (decisión de diseño) · **Lo sostiene:** pesos declarados y código determinista en el
camino crítico.
**Por qué es fuerte:** es la pieza S-2 de la soberanía del dato, y es la que más fácil se pierde
en cuanto entra la primera dependencia cómoda.

---

## Parte II — Fortalezas que el prototipado tiene que convertir en evidencia

Aquí no hay logros: hay compromisos con su nivel de evidencia de destino y la fase en que se
cobran. Enunciarlos como capacidades actuales sería exactamente el error que este documento
existe para impedir.

| # | Fortaleza comprometida | Hoy | Destino | Fase | Depende de |
|---|---|---|---|---|---|
| F-11 | Capturar y reencontrar el mismo pie entre vuelos | EQ3 en seco | EQ4 | F1 · G1 | A-01, A-02, parcela |
| F-12 | Concordancia con el criterio del técnico sobre umbral previo | EQ3 en seco | EQ4 → EQ5 | F2 · G2 | **A-06**, A-05, A-08 |
| F-13 | Anclaje temporal por autoridad real, en la UE | tokens sintéticos | EQ2 → EQ5 | F1–F2 | A-03 |
| F-14 | Cadena de custodia del pie hasta el lote industrial | — | EQ4 | F4 · G3 | **A-07**, app de campo |
| F-15 | Operación completa sin cobertura, medida | decisión de diseño | EQ4 | F1 | banco de sensores |
| F-16 | Residencia europea e independencia de proveedor, auditadas | decisión de diseño | EQ5 | F2–F3 | auditoría externa |
| F-17 | Indicador de riesgo sanitario que aporte sobre el ojo del técnico | — | EQ4 | F2 | **puede no cumplirse** |

**F-17 merece una nota.** Es el único compromiso de esta tabla que puede resolverse en negativo,
y el proyecto está construido para sobrevivir a ese resultado: el valor se apoya en F-01 a F-04 y
en F-14, ninguna de las cuales depende de que el indicador funcione. Decirlo de antemano es lo
que permite publicar el resultado sea cual sea.

---

## Parte III — Cómo se usa esto al escribir

Tres reglas, y ninguna admite excepción por prisa:

1. **Se cita el nivel o no se cita la fortaleza.** «Verificable por un tercero (EQ3)» se puede
   escribir; «verificable por un tercero» a secas, en un contexto donde el lector entienda que
   está validado, no.
2. **Las fortalezas del preprototipo van en presente; las del prototipado, en futuro con su
   gate.** «El sistema rechaza el orden del experto sin sello previo» es presente. «La captura
   será repetible» no se escribe: se escribe «G1 cierra cuando dos vuelos separados produzcan el
   mismo conjunto de pies».
3. **Ninguna fortaleza compensa un hueco.** Si alguien pregunta por algo que no existe, la
   respuesta es que no existe, y después —si viene a cuento— qué sí existe. Nunca al revés.

Once fortalezas probadas y siete compromisos fechados es un preprototipo respetable. Presentar
dieciocho capacidades sería un folleto, y duraría hasta la primera pregunta.
