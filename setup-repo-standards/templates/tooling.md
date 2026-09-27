# Tooling

Cinturón de seguridad local. El entorno debe fallar en ruido de mala calidad.

## Scripts de contrato

| Script | Rol |
|--------|-----|
| `lint` | Reglas estáticas restrictivas |
| `lint:touched` _(si se acordó verify:touched)_ | Lint solo en archivos del diff |
| `typecheck` | Tipos / análisis estático del compilador |
| `test` | Suite de tests unitarios |
| `verify:touched` _(recomendado)_ | Belt diff-scoped: lint touched → typecheck → tests (scoped o full según config) |
| `verify` o `verify:standards` | `lint` → `typecheck` → `test` (cinturón completo de código) |
| `verify:security` _(opcional)_ | Audit de dependencias + secret scan — belt separado |
| `{{DESIGN_CHECK_COMMAND}}` _(opcional; quitar fila si Section L no lo acordó)_ | Lint/validación de spec o tokens — belt **separado** |
| `{{A11Y_CHECK_COMMAND}}` _(opcional; quitar fila si Section L no lo acordó)_ | A11y automatizada en {{A11Y_NAMED_SURFACES}} — belt **separado** |
| `verify` _(producto, si aplica)_ | Gates de producto existentes — sin cambiar en legacy hasta fase 2 |

Comando principal de agentes (cambios medios/grandes): `{{VERIFY_TOUCHED_COMMAND}}`

Comando completo pre-merge / release: `{{VERIFY_COMMAND}}`

## Configuración acordada en el setup

- **Linter:** {{LINTER}}
- **Typecheck:** {{TYPECHECK}}
- **Test runner:** {{TEST_RUNNER}}
- **Presupuesto de archivo:** {{FILE_BUDGET_LINT}} _(p. ej. `max-lines` warn a {{FILE_BUDGET_LINES}}; o “omitido” si Section C lo rechazó)_

### Varias raíces de código (si aplica)

| Artefacto | Rol |
|-----------|-----|
| {{TSCONFIG_PROJECTS}} | Typecheck por raíz acordada |
| `tsconfig.eslint.json` | Inclusión unificada para lint type-aware (si se acordó) |
| {{ESLINT_CONFIG_FILE}} | Flat config con bloques por glob si las reglas difieren |

Scripts opcionales **fuera de `verify` v1** (solo si el usuario los pidió): {{OPTIONAL_RUNTIME_SCRIPTS}}

## verify:touched

Orquestador: `scripts/verify-touched.mjs` + `scripts/verify-touched.config.mjs` (valores desde Explore + Section C/E).

- Base ref por defecto: `HEAD` (working tree + staged); acepta SHA explícito.
- Lint: solo archivos tocados bajo las raíces acordadas.
- Typecheck: suite completa (tipos cruzan módulos).
- Tests: scoped por módulo/caja si Explore detectó patrón repetible; si no, `full` o `skip` según Section E.

## Uso

- Cambios medios/grandes (agentes): correr `{{VERIFY_TOUCHED_COMMAND}}` antes de dar por cerrado el trabajo.
- Pre-merge / release: correr `{{VERIFY_COMMAND}}` cuando el repo lo define.
- Belts de diseño/a11y: correrlos cuando el cambio toca UI **si Section L los acordó**. No están dentro de `{{VERIFY_COMMAND}}`. Si fallan → arreglar UI/a11y, no relajar el belt.
- No usar `--no-verify` / saltarse hooks para esconder fallos.
- No relajar reglas del linter para acomodar código nuevo; arreglar el código.

## Relación con agentes

Las rules en `.cursor/rules/` empujan el mismo estándar. Si lint/typecheck/test fallan, el agente debe corregir el código, no el cinturón.
