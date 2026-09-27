# Coding standards

Estándares de este repositorio. El detalle vive en satélites para no saturar el contexto del modelo: lee este archivo primero; abre un satélite solo cuando lo necesites.

## Stack

- **Lenguaje / runtime:** {{LANGUAGE_RUNTIME}}
- **Frontend:** {{FRONTEND}}
- **Backend:** {{BACKEND}} _(forma acordada: {{RUNTIME_SHAPE}}; ver [runtime.md](docs/standards/runtime.md) si existe)_
- **Package manager:** {{PACKAGE_MANAGER}}

## Jerarquía

1. `docs/standards/*` + `.cursor/rules/*` — contrato (mandan en conflictos)
2. Código + configs + tests que pasan — hecho verificable
3. Docs legacy acordados en setup — referencia; alinear al tocar (boy scout)

Si el repo ya tenía corpus normativo al bootstrap: este paquete **complementa**, no sustituye. En conflicto, gana `docs/standards/*`; legacy se alinea al tocar (boy scout).

## Principios (resumen)

1. **Feature-first por cajas** — cada capacidad de negocio es una caja con su UI/API, modelos y dependencias internas. Fuera solo `core`, `ui` y shared justificado. Detalle: [docs/standards/feature-first.md](docs/standards/feature-first.md).
2. **Composición de unidades** — page/handler/flow/**service/component/store** = compositor si ya tiene ≥2 trabajos; concern con estado propio → extraer aunque se use una vez (recursivo). Un slot de caja no es un archivo. Detalle: [feature-first § Composición](docs/standards/feature-first.md#composición-de-unidades).
3. **Clean Code** — nombres declarativos, funciones pequeñas, early return, un nivel de abstracción, dispatch por discriminador (registry map, no `switch` creciente), presupuesto de módulo (`{{FILE_BUDGET_LINES}}` líneas / lint acordado). Detalle: [docs/standards/clean-code.md](docs/standards/clean-code.md).
4. **Lenguaje Ubicuo y copy operador** — UI y mensajes al usuario en vocabulario de negocio; catálogos tipados por feature (*Copy-as-Code*); frontera de errores con códigos estables y mappers puros (sin filtrar stack/infra al cliente). Migrate-on-touch. i18n: keys estables si multi-idioma previsto. Glosario opcional: [domain-glossary.md](docs/standards/domain-glossary.md) _(quitar enlace si no se generó)_. Detalle: [clean-code.md § Copy-as-Code](docs/standards/clean-code.md#copy-as-code-y-frontera-de-errores-lenguaje-ubicuo).
5. **Seguridad** — secrets, auth en boundary, input validado, fail closed. Detalle: [docs/standards/security.md](docs/standards/security.md).
6. **Tests como especificación** — runner: {{TEST_RUNNER}}. Detalle: [docs/standards/testing.md](docs/standards/testing.md).
7. **Cinturón local** — agentes: `{{VERIFY_TOUCHED_COMMAND}}` (diff-scoped). Pre-merge/release: `{{VERIFY_COMMAND}}` (completo). Detalle: [docs/standards/tooling.md](docs/standards/tooling.md).
8. **UI / a11y** _(quitar este punto si Section L omitió)_ — fuente {{BRAND_SOURCE}}; receta [docs/standards/ui.md](docs/standards/ui.md). No inventar look. Belts de diseño/a11y, si existen, fuera de `{{VERIFY_COMMAND}}`.

## Boy scout

Al tocar código que no sigue feature-first o clean code, mejóralo en el mismo cambio (sin reescritura masiva ni cambio de framework). Al tocar plantillas/estilos, alinear a `ui.md` en el mismo cambio (sin restyle masivo) si Section L aplicó.

## Para agentes

- Respetar `.cursor/rules/`.
- No inventar patrones de otro framework.
- No debilitar lint/typecheck/tests para “hacer pasar” un cambio.
