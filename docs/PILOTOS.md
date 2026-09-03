# Los tres pilotos — SUBER UAV

**Documento:** v0.1 · 2026-09-02

Tres pilotos escalonados. Cada uno cierra con un criterio, no con una fecha, y ninguno empieza
antes de que el anterior haya cerrado. El calendario de fases (F0–F5) del estado maestro sigue
mandando: estos pilotos son el contenido de esas fases vistos desde el sector, no un calendario
paralelo.

---

## Piloto 1 — Un rodal, 50–100 árboles

**Pregunta que responde:** ¿se puede capturar, identificar y volver a encontrar el mismo árbol
en dos momentos distintos?

Es el piloto aburrido y es el que decide el proyecto. Si un árbol no se puede reidentificar
entre campañas, no hay serie temporal, y sin serie temporal el resto del sistema es un
inventario caro.

| | |
|---|---|
| Ámbito | Un rodal, 50–100 alcornoques marcados |
| Entrada | Un vuelo, una pasada de campo |
| Salida | Inventario georreferenciado con marca física y descriptores por pie |
| Fase | F1 |
| Gate | **G1 — captura repetible**: dos vuelos separados producen el mismo conjunto de pies, con la misma identidad, dentro de la tolerancia pactada en F0 |
| Depende de | A-01 (unidad de ordenación), A-02 (tipo de marca física), parcela comprometida |
| Entidades nuevas | `uav_mission`, `forest_asset`, atributos de `tree` |
| Lo que NO demuestra | Nada sanitario, nada de calidad, nada de correlación |

**Cifra de la muestra.** 50–100 árboles es el tamaño acordado con el promotor para el primer
rodal. No procede de un cálculo de potencia estadística: es el tamaño con el que se puede
recorrer el rodal a pie y verificar árbol por árbol. El tamaño de muestra necesario para
sostener una afirmación estadística se calcula en F0, con el protocolo. `[POR CALCULAR EN F0]`

---

## Piloto 2 — UAV → inspección → cala → calidad

**Pregunta que responde:** ¿el indicador derivado del vuelo aporta algo sobre lo que el técnico
ya ve, y guarda alguna relación con lo que dice la cala?

| | |
|---|---|
| Ámbito | Los mismos pies del Piloto 1 |
| Entrada | Vuelo multiespectral, inspección del técnico, calas según protocolo |
| Salida | Orden de prioridad del sistema, sellado, contrastado contra el orden del técnico |
| Fase | F2 |
| Gate | **G2 — concordancia sobre umbral**: la concordancia entre el orden del sistema y el del técnico supera el umbral registrado **antes** de mirar el dato (A-05, ADR-022) |
| Depende de | **A-06 (acuerdo con CICYTEX sobre el protocolo de calas)**, A-05, A-08 |
| Entidades nuevas | `cork_sample`, `sanitary_assessment`, `quality_assessment` |
| Lo que NO demuestra | Que el sistema pueda ocupar el lugar de la cala, ni asignar por su cuenta una clase de calidad |

**El motor ya sabe hacer este piloto en seco.** `scripts/demo_gate_g2.py` recorre el flujo
entero con datos sintéticos: registra el protocolo, emite el orden del sistema, **rechaza** el
orden del experto por no haber sello, sella, admite el orden del experto y calcula el veredicto.
Lo que falta no es software: es la parcela, el técnico, las calas y el umbral pactado.

**Riesgo dominante (H-D2, nivel 4).** El indicador multiespectral puede no aportar nada sobre
el ojo del técnico. Ese resultado es un resultado válido del piloto y hay que poder publicarlo
sin que se lleve por delante el proyecto: por eso el valor del sistema se apoya en la
trazabilidad, que no depende de que el indicador funcione.

---

## Piloto 3 — Árbol → saca → lote → resultado industrial

**Pregunta que responde:** ¿se puede seguir la evidencia sin huecos desde el pie hasta lo que
sale de fábrica?

| | |
|---|---|
| Ámbito | La saca real de la parcela, campaña de junio–agosto de 2027 |
| Entrada | Registro en el tajo, apilado, entrada en fábrica, resultado del proceso |
| Salida | Cadena de custodia completa y verificable por un tercero |
| Fase | F4 y F5 |
| Gate | **G3 — campaña registrada**: cada lote industrial se resuelve hasta los pies que lo componen, y la verificación del paquete exportado no falla |
| Depende de | **A-07 (interlocutor industrial)**, aplicación Android, marca física superviviente |
| Entidades nuevas | `harvest_event` completo, `cork_lot`, `industrial_process`, `industrial_result` |
| Lo que NO demuestra | **La correlación monte–fábrica.** Ver ADR-028 |

**Contradicción que hay que decir en voz alta.** Este piloto necesita la aplicación Android, que
el estado maestro sitúa en «qué no se construye todavía», y un interlocutor industrial que no
existe. **Piloto 3 no es un incremento de la fase actual.** Lo que sí corresponde ahora es el
modelo de datos y el protocolo de custodia, que se pueden escribir y probar con datos
sintéticos sin un solo dispositivo en el tajo.

---

## Lo que cada piloto permite decir

Es la tabla que hay que llevar a una reunión, porque es la que evita prometer el piloto
siguiente.

<!-- lexico:off -->
| Tras cerrar | Se puede afirmar | Se sigue sin poder afirmar |
|---|---|---|
| Piloto 1 | «Identificamos y reencontramos cada pie entre vuelos» | Nada sanitario ni productivo |
| Piloto 2 | «Nuestra ordenación concuerda con la del técnico por encima del umbral pactado de antemano» | Que sustituya a la cala o clasifique corcho |
| Piloto 3 | «La cadena árbol→lote→resultado está registrada y es verificable por un tercero» | Que las variables de monte expliquen el resultado industrial |

Y en ningún caso, tras ninguno de los tres: «el dron detecta enfermedades y determina
automáticamente la calidad del corcho».
<!-- lexico:on -->
