# Especificación de canonicalización — CASTUO-CANON-1

Versión: **1** (el número viaja dentro de cada paquete como `canon_version`)

Dos implementaciones independientes tienen que producir **los mismos bytes** para el mismo
valor lógico: la de la aplicación (`src/castuo/common/canonical.py`) y la del verificador
externo (`scripts/verify_package.py`), que no puede importar la aplicación. Esta
especificación es el contrato entre ambas; los vectores de prueba de
`tests/fixtures/canonical_vectors.json` son su comprobación.

## Reglas

1. **Codificación de salida:** UTF-8, sin BOM.
2. **Formato:** JSON. Claves ordenadas por su representación en texto (`sort_keys`),
   separadores sin espacios (`,` y `:`), sin escapes ASCII (`ensure_ascii=False`).
3. **Cadenas:** se normalizan a **Unicode NFC** antes de serializar. Así «árbol» escrito
   con é precompuesta y con e + acento combinante producen el mismo hash.
4. **Enteros:** se serializan como números JSON, sin sufijo ni separadores.
5. **Floats y Decimal:** **nunca** como número JSON. Se convierten a `Decimal` a partir de
   su representación en texto, se normalizan (`0.90` → `0.9`, `1E+2` → `100`) y se emiten
   como **cadena decimal en notación posicional**. Los flotantes binarios no son estables
   entre plataformas; una cadena decimal sí.
6. **Bytes:** cadena hexadecimal en minúsculas, sin prefijo.
7. **UUID:** cadena en minúsculas con guiones (forma canónica RFC 4122).
8. **Fechas y horas:** se convierten a **UTC**, se truncan a **microsegundos** y se emiten
   en ISO 8601 con sufijo `Z` (`2026-02-15T09:12:00.123456Z`). Un valor sin zona horaria
   se rechaza: no hay una interpretación segura.
9. **null:** se emite como `null`. **Booleanos:** `true` / `false` en minúsculas, y nunca
   se confunden con 0/1.
10. **Listas:** conservan su orden. El orden es contenido, no presentación.
11. **Objetos anidados:** las reglas se aplican recursivamente a cualquier profundidad.
12. **Claves:** se convierten a texto y se normalizan a NFC antes de ordenar.

## Qué queda fuera del contenido hasheado

Los valores generados que cambian entre ejecuciones idénticas: identificadores creados por
la base de datos o por el proceso, y las marcas de tiempo de escritura. Están enumerados en
un solo sitio (`NON_DETERMINISTIC`) para que nadie los añada por descuido. La lista es:

`detection_id`, `asset_id`, `review_id`, `trace_id`, `seal_id`, `anchor_id`,
`proposal_id`, `event_id`, `detected_at`, `created_at`, `reviewed_at`, `sealed_at`,
`anchored_at`, `generated_at`, `package_hash`.

`occurred_at` **sí** entra en el hash del evento de traza: es contenido del evento, no una
marca de escritura, y sin él se podría alterar la hora sin romper la cadena.

## Hash

`SHA-256` sobre los bytes canónicos. Se representa en hexadecimal minúsculas.

## Compatibilidad

Un cambio en cualquiera de estas reglas exige subir `canon_version`. Los paquetes emitidos
con una versión anterior siguen verificándose con la implementación de esa versión: por eso
el número viaja dentro del paquete y no se deduce del código que lo lee.
