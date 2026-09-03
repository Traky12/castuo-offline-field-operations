# Soberanía del dato — por qué SUBER UAV la refuerza siendo exterior

**Documento:** v0.1 · 2026-09-02
**Depende de:** `CASTUO_CORCHO_ESTADO_MAESTRO.md` (fuente de verdad) y ADR-031, ADR-032.

---

## 1. El argumento en una frase

CASTÚO-SYSTEM declara la soberanía del dato como principio fundacional. Un principio declarado
en la arquitectura de una plataforma de cinco núcleos y treinta y seis módulos es difícil de
demostrar: hay demasiadas piezas y demasiadas dependencias como para que nadie pueda recorrer la
cadena entera. **SUBER UAV es lo bastante pequeño como para recorrerla entera**, y por eso sirve
de banco de prueba de esa soberanía en lugar de ser una afirmación más dentro de ella.

Ser exterior no es una concesión: es la condición que hace verificable el argumento.

## 2. Qué significa soberanía aquí, desmontada en piezas comprobables

«Soberanía del dato» es una palabra grande y, dicha sin desglosar, no compromete a nada. Estas
son las seis propiedades concretas en que se descompone para este sistema, cada una con su
estado real.

| # | Propiedad | Qué exige | Estado hoy |
|---|---|---|---|
| S-1 | **El dato nace y se usa donde se genera** | Que la operación de campo funcione sin conectividad y sin nube | Decisión de diseño; sin medir |
| S-2 | **La inferencia no depende de un proveedor externo** | Que la ordenación se calcule con código propio y determinista | **Hecho**: ordenación determinista, sin llamada a ningún servicio |
| S-3 | **La residencia es europea y conocida** | Infraestructura y, cuando haya modelo generativo, proveedor en la UE | Decisión de diseño (Hetzner, Mistral); sin auditar |
| S-4 | **La verificación no exige confiar en el sistema** | Que un tercero compruebe la evidencia con el fichero y un script | **Hecho**: verificador externo independiente de la aplicación |
| S-5 | **No hay dependencia de una red pública ni de jurisdicción ajena** | Sello temporal por autoridad, no por cadena de bloques | Decisión tomada (ADR-005, D-05); autoridad concreta sin elegir (A-03) |
| S-6 | **Las personas no quedan atrapadas en el asiento** | Identidades fuera de las tablas append-only, borrables | Parcial: actor opaco sí; `owner_ref` sin cerrar |

Tres de las seis son hechos comprobables hoy con la batería de pruebas. Dos son decisiones de
diseño sin medir. Una está a medias. Ese reparto es el estado real, y es el que hay que enseñar.

## 3. Por qué la exterioridad refuerza cada una

**S-1 · Autonomía.** Un módulo dentro de una plataforma hereda sus supuestos de despliegue: base
de datos compartida, autenticación central, cola común. Cada uno de esos supuestos es una
conexión que en el monte no existe. Al ser exterior, SUBER UAV **tiene que** funcionar solo, y
lo que tiene que funcionar solo se prueba solo.

**S-2 · Inferencia propia.** La ordenación por prioridad se calcula con pesos declarados y
código determinista: la misma entrada produce el mismo orden y el mismo hash. No hay llamada a
ningún servicio de terceros en el camino crítico. Esta es la propiedad más fácil de perder en
cuanto entra la primera dependencia cómoda, y es más fácil no perderla en un sistema pequeño con
frontera propia.

**S-3 · Residencia.** Un despliegue independiente tiene un inventario de infraestructura que cabe
en media página. Auditar la residencia de una plataforma de treinta y seis módulos es un
proyecto; auditar la de SUBER UAV es una tarde. Cuando esa auditoría se haga —no está hecha—,
será la primera evidencia de residencia real que tenga la casa.

**S-4 · Verificación sin confianza.** El paquete exportado se comprueba con un script autónomo,
sin la aplicación, sin la base de datos y sin credenciales. Un tercero —un centro de
investigación, un auditor, un comprador— verifica sin pedir acceso a nada. Esto solo es
demostrable si el formato de salida no depende del interior de una plataforma: la exterioridad
lo garantiza por construcción.

**S-5 · Sin red pública.** No hay nodo de cadena de bloques, ni comisiones, ni un tercero fuera
de la UE decidiendo la disponibilidad del registro. La marca de tiempo la pone una autoridad
RFC 3161. Queda por elegir cuál, y ahora la decisión lleva una condición añadida: **la autoridad
debe estar en la Unión Europea** (A-03), porque una autoridad fuera de ella reintroduciría por
la puerta de atrás lo que se evitó por la de delante.

**S-6 · Personas fuera del asiento.** En el evento viaja un actor opaco; la identidad vive en
otro sitio y se puede borrar sin romper la cadena de hashes. Es la única manera de que un
registro inmutable conviva con el derecho de supresión. Falta cerrar qué puede referenciar
`owner_ref` y con qué base legal, y falta decidir de quién es el dato del propietario forestal
(A-09).

## 4. Lo que la exterioridad cuesta

Un documento que solo cuenta las ventajas de una decisión de arquitectura no sirve para
decidir nada.

| Coste | Por qué aparece | Cómo se contiene |
|---|---|---|
| Dos vocabularios | El núcleo dice `asset`, el dominio dice `tree` | Tabla de correspondencia mantenida en `MODELO_DATOS_SECTORIAL.md` |
| Dos despliegues | Núcleo propio, plataforma aparte | El demostrador cabe en una máquina; el coste es real y pequeño |
| Deriva | Dos sistemas evolucionan por separado | El contrato es el paquete exportado, no el esquema; si el paquete verifica, no hay deriva que importe |
| Tentación de reimplementar | Lo que la plataforma ya tiene se vuelve a escribir | Regla: SUBER UAV solo construye lo que necesita para la evidencia de campo |
| Integración diferida | El día que haya que unir, hay trabajo | Es trabajo conocido y acotado, y llega con el dominio ya probado |

## 5. Cómo entra el dato en CASTÚO-SYSTEM

Por el paquete verificable, no por la base de datos. La frontera es un **artefacto**, no una
conexión: un fichero con su canonicalización versionada, su cadena de hashes, su sello local y,
cuando exista, su anclaje temporal.

```
SUBER UAV  ──── paquete verificable ────>  CASTÚO-SYSTEM
 (exterior)      formato declarado           (plataforma)
                 verificable sin
                 acceder a ninguno
                 de los dos lados
```

Consecuencias de que la frontera sea un artefacto y no una conexión:

- Ninguno de los dos lados puede modificar el dato del otro. No hay escritura cruzada.
- La integración se prueba con un fichero, no levantando dos sistemas.
- Si SUBER UAV desaparece, los paquetes que ya emitió siguen siendo verificables.
- Si CASTÚO-SYSTEM cambia por dentro, SUBER UAV no se entera.

Esa última propiedad es la que convierte una decisión de encaje en una decisión de soberanía: el
dato de monte no queda cautivo de la plataforma que lo consume.

## 6. Lo que no se puede decir todavía

Ni en una memoria, ni en una reunión, ni en un pie de página:

- Que la soberanía del dato esté garantizada. Tres propiedades de seis son hechos probados; el
  resto son decisiones de diseño sin auditar.
- Que el sistema cumpla el RGPD. No hay revisión jurídica cerrada; `owner_ref` y la titularidad
  del dato de monte siguen abiertos.
- Que la residencia europea esté verificada. Está elegida, no auditada.
- Que exista sello temporal certificado. El anclaje RFC 3161 nunca se ha probado contra una
  autoridad real; hoy solo hay tokens sintéticos.

El comprobador de léxico vigila las tres primeras (LEX-09, LEX-10, LEX-11).
