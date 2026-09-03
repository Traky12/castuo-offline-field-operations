# Interlocutores y encuadre de cada conversación

**Documento:** v0.1 · 2026-09-02

> **Advertencia previa, aplicable a todo el documento.** No existe hoy ningún acuerdo, convenio,
> carta de apoyo ni conversación formalizada con ninguna de las organizaciones citadas. Este
> documento describe **cómo se plantearía** cada conversación, no un estado de relación.
> Cualquier uso de estos textos que sugiera respaldo institucional es una afirmación falsa.
> `[PENDIENTE: no existe acuerdo con ninguno]`

Cuatro interlocutores, cuatro conversaciones distintas. El error caro es llevar el mismo
argumentario a los cuatro: lo que a un centro de investigación le parece riguroso, a una fábrica
le parece irrelevante, y al revés.

---

## 1. CICYTEX — centro de investigación

**Qué le interesa.** El dato. La serie histórica. Que el método aguante una revisión.

**Cómo se plantea.** Como extensión instrumental del Plan de Calas, no como alternativa. El
sistema no propone reducir calas ni sustituir la observación del técnico: propone georreferenciar
lo que ya se mide, conservarlo de forma inalterable y añadir cobertura continua entre calas.

**El argumento que sí funciona con este interlocutor:** el contraste ciego. El orden del sistema
se congela y se sella antes de conocer el del técnico, y el umbral de aceptación se registra
antes de mirar el dato. Es un diseño experimental, no una demostración comercial, y es la única
parte del proyecto que ya está construida y probada.

**Lo que hay que decir aunque reste:** ningún modelo está entrenado con datos reales; los pesos
de la ordenación son provisionales; el indicador multiespectral puede no aportar sobre el ojo
del técnico, y ese resultado se publicaría igual.

**Lo que se pide.** Acceso al protocolo de calas y, si procede, a series históricas, con las
condiciones que el centro establezca (A-06). Es la decisión abierta que bloquea el Piloto 2.

**Lo que no se pide.** Financiación ni respaldo público en esta fase.

---

## 2. PTEcor — plataforma tecnológica sectorial

**Qué le interesa.** Que el sector se digitalice sin perder lo que sabe. Interoperabilidad.
Que no aparezca otra herramienta aislada más.

**Cómo se plantea.** Como infraestructura de evidencia compartible, no como producto de una
empresa. El núcleo es neutral respecto al dominio y el paquete exportado se verifica **sin
acceso al sistema y sin confiar en él**: cualquiera puede comprobar que un registro no se ha
tocado con un script de 200 líneas y el fichero.

**El argumento que sí funciona:** el vínculo árbol→lote→resultado industrial es un problema del
sector entero, no de una explotación. Quien lo resuelva primero fija el formato.

**Lo que hay que decir aunque reste:** de las trece entidades del modelo sectorial, hoy hay una
construida, cuatro parciales y ocho sin empezar. La interoperabilidad con Trazalia está fuera
del camino crítico y sin respuesta (A-04, H-T2).

**Lo que se pide.** Contraste del modelo de datos con otros actores antes de congelarlo.

---

## 3. FUNDECYT-PCTEX — instrumento de apoyo a la innovación

**Qué le interesa.** Que el proyecto sea ejecutable, medible y no se evapore tras la subvención.
Hitos con criterio de cierre.

**Cómo se plantea.** Con el calendario y los gates tal como están: F0 protocolo, F1/G1 captura
repetible, F2/G2 concordancia sobre umbral, F3 captura del «antes», F4/G3 campaña registrada,
F5 análisis. Una fase no se supera por calendario: se supera por criterio cumplido.

**El argumento que sí funciona:** el proyecto ya tiene núcleo construido, reproducible desde
cero y con batería de pruebas automatizadas contra base de datos real. Lo que se pide financiar
no es una idea: es el trabajo de campo que convierte un núcleo probado en un resultado medido.

**Lo que hay que decir aunque reste:** no hay vuelos de ensayo, ni autorización de vuelo, ni
parcela comprometida, ni dato de campo validado, ni acuerdo con ningún tercero. Y el riesgo de
nivel 4 —que el indicador no aporte— se lee en el mes 10.

**Lo que se pide.** Financiación de las fases F0–F2, que son las que producen el primer
resultado contrastable.

---

## 4. Industria transformadora — interlocutor citado por el promotor: Hidrocork

> No se dispone de información verificada sobre esta empresa ni sobre su interés en el proyecto.
> `[POR CONFIRMAR]`

**Qué le interesa.** El rendimiento del lote y la merma. La procedencia. Poder responder a un
cliente que pregunta de dónde viene una plancha.

**Cómo se plantea.** Por el final de la cadena, no por el principio. A la industria no le
interesa el dron: le interesa que un lote que entra en fábrica se resuelva hasta los pies que lo
componen, con evidencia que un auditor pueda verificar sin entrar en el sistema.

**El argumento que sí funciona:** trazabilidad verificable por un tercero, que es exactamente lo
que el núcleo ya hace. No hace falta creer en el modelo para que esto valga.

**Lo que hay que decir aunque reste:** hoy no existen `cork_lot`, `industrial_process` ni
`industrial_result`. La cadena está construida en su parte de garantías y vacía en su parte
industrial. Y la correlación entre variables de monte y resultado de fábrica **no** es un
resultado del piloto de 2027.

**Lo que se pide.** Un interlocutor técnico y un lote piloto (A-07).

---

## 5. El argumento de soberanía, por interlocutor

Es el mismo hecho contado desde cuatro sitios. Y en los cuatro se cuenta **desglosado**: tres
propiedades probadas, dos sin auditar, una a medias. Afirmar la soberanía como un todo la
convierte en palabrería delante de cualquiera de los cuatro.

| Interlocutor | La propiedad que le importa | Cómo se dice |
|---|---|---|
| CICYTEX | S-4 verificación sin confianza | «Puede comprobar nuestros datos sin pedirnos acceso a nada» |
| PTEcor | S-4 y S-5 | «El formato de evidencia no ata a nadie a un proveedor ni a una red pública» |
| FUNDECYT-PCTEX | S-3 residencia, S-2 inferencia propia | «Es un sistema pequeño, y por eso su residencia se puede auditar de verdad» |
| Industria | S-1 y S-6 | «El dato del monte no queda cautivo de la plataforma que lo consume» |

Y una frase que sirve para los cuatro: SUBER UAV es una **fase exterior** a la arquitectura de
CASTÚO-SYSTEM, no uno de sus módulos, y esa exterioridad es lo que hace el argumento comprobable
en lugar de declarado.

## 6. Lo común a las cuatro conversaciones

Se dice siempre, ante todos:

- El dron es un sensor dentro de una infraestructura de evidencia, no el producto.
- El sistema complementa la metodología experta; no la sustituye.
- Ninguna capacidad está validada. Los huecos se enseñan marcados.
- La soberanía del dato se cuenta desglosada, nunca como principio.
- Ninguna fortaleza se enuncia por encima de su nivel de evidencia.
- La frase prohibida del léxico no se dice ni se insinúa, ni siquiera cuando el interlocutor la
  ofrece él mismo como resumen amable de lo que hacemos.

Ese último punto es el que más cuesta: cuando alguien resume tu proyecto mejor de lo que es, la
tentación de asentir es fuerte. Corregirlo en esa reunión cuesta un minuto incómodo; no
corregirlo cuesta el proyecto en la siguiente.
