# Contrato CASTÚO de operaciones offline · v1 (PLAN)

**Autoridad**: `Traky12/Castuo-system`. El repositorio organiza operaciones humanas sin conexión; no autentica por sí mismo decisiones de liberación o retirada. El candidato `castuo-trace-core` no está instalado ni publicado.

## Fronteras de interoperabilidad

| Componente | Evidencia esperada | Estado |
| --- | --- | --- |
| castuo-agro-edge | org_id, device_id, sequence, buffering y conflictos | PENDING |
| castuo-offline-field-operations | traza, lote, estado offline, decisiones propuestas | PENDING |
| castuo-evidence | fixtures, hashes, sobres, límites de afirmación | PENDING |
| castuo-e3-001 | SHA-256, firmas Ed25519, trust pin independiente, CLI + Action + navegador | PENDING |
| Castuo-system | aislamiento por organización, roles, maker-checker, gates | BLOCKED |
| goldfish | checkpoint, recuperación, reversión y seguridad | PENDING |

## Pruebas negativas y seguridad

Rechazar secuencias duplicadas con contenido diferente, alteración de hash/manifest, conflicto perdido, cruce de organización, firma no confiable o revocada, symlink/ruta fuera de paquete, replay y autoaprobación. Distinguir integridad de bytes, autenticación externa, revisión humana y validación operacional. IA propone; no ejecuta.

## Usabilidad y resiliencia

Registrar estados vacío/carga/error/offline/conflicto/revisión/bloqueo. Exigir claro/oscuro, 360 px, tablet, escritorio, teclado y lector de pantalla, con QA visual y humana. Marcas temporales del equipo no demuestran tiempo confiable; prevenir truncamiento mediante checkpoint independiente.

## ISO, Six Sigma y promoción

ISO 22000/22005/27001, ISO 22301 y 42001: correspondencias de control propuestas, sin conformidad afirmada. Objetivo ≤0,3 DPMO por CTQ y población verificable: NOT_DEMONSTRATED. G1/G2 = BLOCKED. Para integrar: HEAD exacto, pytest/Ruff/Gitleaks/pip-audit/HTTP aplicables, pruebas de interoperabilidad, manifiesto global íntegro, revisor independiente y aprobación de persona distinta. No se autoriza merge, release, despliegue, DNS ni agentes.
