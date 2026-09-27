# Glosario de dominio

Vocabulario compartido entre producto, backend y frontend. **Fuente de verdad** para Lenguaje Ubicuo en UI.

| Término interno / API / código | Término UI (operador) | Evitar | Notas |
| --- | --- | --- | --- |
| {{INTERNAL_TERM}} | {{UI_TERM}} | {{ANTI_PATTERN}} | |

## Reglas

- Nuevo término de negocio → fila aquí **antes** de hardcodear copy en features.
- El copy en catálogos (`*.copy.*` o equivalente del stack) debe usar la columna **Término UI**.
- Identificadores de código pueden permanecer en inglés; la columna UI sigue el idioma del producto acordado en setup.
- Si el producto es multi-idioma: la columna **Término UI** es el locale principal; otros locales viven en el mecanismo i18n acordado, referenciando la misma key estable.

## Relación con otros estándares

- Copy visible: [clean-code.md](clean-code.md#copy-as-code-y-frontera-de-errores-lenguaje-ubicuo)
- Errores al operador: códigos estables + mappers — no usar jerga de la columna "interno" en UI
- Boy scout: al tocar una feature, alinear su copy con este glosario en el mismo cambio
