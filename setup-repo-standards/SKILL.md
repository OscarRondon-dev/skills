---
name: setup-repo-standards
description: >-
  Bootstrap coding standards and a restrictive local safety environment in any
  repo: CODING_STANDARDS.md, docs/standards/* (incl. security, unit composition,
  module budget / anti Fat Module, Ubiquitous Language / Copy-as-Code / operator error boundary),
  Cursor rules, lint/typecheck/test scripts, and verify belt(s). Optional Section L:
  UI identity / a11y (doc + rule; design/a11y belts separate from verify v1; omit if no UI).
  Optional domain glossary and i18n-ready copy keys (Section A). Explore, present options
  with a recommendation, confirm, then write. Optional diff-scoped verify:touched belt
  for agents. Security and UI inventories in Explore (read-only). Legacy docs are not
  assumed correct — hierarchy in Section K. Does not audit or fix PRs. Agnostic to cloud
  vendor — stack-specific depth via extend-domain-standards only when the user invokes it
  explicitly.
disable-model-invocation: true
---

# Setup Repo Standards

Prompt-driven bootstrap (como `setup-matt-pocock-skills`). **Explorar → opciones + recomendación → confirmar → escribir.** No es un script ciego.

## Objetivo

Montar un entorno que obligue a buenas prácticas aunque la IA falle:

| Capa | Artefacto |
|------|-----------|
| Docs | `CODING_STANDARDS.md` + `docs/standards/*` (incl. copy operador; glosario opcional) |
| Agent map | Sección `## Coding standards` en `AGENTS.md` o `CLAUDE.md` |
| Rules | `.cursor/rules/*.mdc` (6 core; +2–6 opcionales según SPA, backend, auth, seguridad y, si Section L, UI/a11y) |
| Tooling | lint + typecheck + test + script(s) `verify` acordados |

**No hace:** auditar PRs, parchear código de seguridad, migrar frameworks, crear carpetas feature vacías, CI remoto, ni `docs/agents/*` (Matt). **No hace** estándares vendor-specific profundos — eso es [`extend-domain-standards`](../extend-domain-standards/SKILL.md) con invocación explícita del usuario.

## Principios fijos

1. **Contexto primero** — detectar stack; ejemplos y configs solo de ese stack.
2. **El repo sube al estándar** — no bajar el listón porque el legacy es flojo; tooling se decide con el usuario (opciones + recomendación). ESLint estricto en legacy puede producir muchos findings — es esperado; arreglar código, no relajar reglas.
3. **Jerarquía de verdad** — `docs/standards/*` + `.cursor/rules/*` mandan sobre docs legacy (`DATABASE.md`, `BACKEND.md`, `progress/*`, etc.). Legacy = referencia hasta alinearse (boy scout). Detalle en Section K.
4. **Feature-first por cajas** — dentro del feature: pages/components/services/models/strategies/router/lazy/etc. Fuera: `ui`, `core`, y poco más. **Compositor vs concern:** page/handler/flow orquesta; trabajo con estado propio → extraer aunque se use una vez (*Divergent Change*). Backend: caja con modelos/schemas/middleware/BD; fuera `core` (+ shared justificado). Boy scout al tocar código; no scaffold de carpetas vacías.
5. **Clean Code (Gómez, agnóstico)** — nombres declarativos, SRP (función **y** host), early return, un nivel de abstracción, tests como especificación. Detalle en [reference-clean-code.md](reference-clean-code.md).
6. **Lenguaje Ubicuo y frontera de errores** — UI y mensajes de operador en vocabulario de negocio; catálogos tipados por feature (*Copy-as-Code*); errores traducidos por **códigos estables** y mappers puros (no `error.message` crudo). Compatible con seguridad (sin stack/infra al cliente); los **fallos técnicos** exponen un `traceId` opaco para soporte (los errores de negocio, no). Migrate-on-touch. i18n: keys estables si multi-idioma previsto. Glosario opcional. Detalle en plantilla `clean-code.md` y [reference-clean-code.md](reference-clean-code.md) § Copy-as-Code.
7. **Seguridad agnóstica** — principios en `docs/standards/security.md`; rules `security-boundary` (servidor) y `client-security` (SPA/anti-MVP); profundidad vendor/stack → `extend-domain-standards`. Detalle en [reference-security.md](reference-security.md).
8. **JS/TS: Vitest** (no Jest) como target. Si hay Jest, preguntar (sustituir / paralelo / solo documentar) con recomendación.
9. **Cinturón local** — mínimo `lint` → `typecheck` → `test`. **Default de agentes:** `verify:touched` (diff-scoped: lint en archivos tocados → typecheck completo → tests scoped o full según layout detectado). **Pre-merge/release:** `verify` / `verify:standards` completo. En legacy con `verify` productivo existente, proponer belt en dos velocidades (`verify:standards` + `verify` intacto). Sin format-check en v1. Sin CI remoto. Sin deploy/smoke en v1. `max-lines` warn (presupuesto de módulo) solo si se acordó en Section C — no es un gate nuevo silencioso. Salida de verify **sin truncar** (errores antes del resumen).
10. **Convivencia con Matt** — nosotros: `docs/standards/*` + `## Coding standards`. Matt: `docs/agents/*` + `## Agent skills`. No pisar.
11. **Idioma** — detectar docs existentes; si no hay, default español. Preguntar si hay duda. Distinto del **idioma del copy operador** (Section A): docs vs UI pueden diferir; código/APIs suelen permanecer en inglés.
12. **Agnóstico de vendor** — plantillas y defaults **nunca** nombran un cloud, runtime, SDK, framework CSS, motor a11y ni CLI de marca concretos. El usuario describe su target; la skill documenta lo acordado. Guía oficial → `extend-domain-standards` cuando el usuario lo pida.
13. **Presupuesto de módulo (anti Fat Module)** — un slot de caja (`services/`, `models/`, `components/`, `strategies/`) no es un archivo. Host **recursivo**: page, handler, flow, **service, component, store, registry**. Corte al partir: orquesta / política / I/O. Umbral y lint se **preguntan** en Section C (no escribirlos por defecto sin OK). Detalle en las plantillas `feature-first` y `clean-code`. No es una rule extra en Section F.
14. **Identidad visual / a11y (opcional, Section L)** — no siempre-on. Si hay UI: una fuente de marca acordada manda sobre “sé creativo”. Receta en `docs/standards/ui.md` del **stack detectado**. A11y operativa mínima (agnóstica). Checkers de tokens/spec y a11y automatizada = **belts separados**, mismo patrón que `verify:security`. Sin UI → omitir. Profundidad de un design system o tooling WCAG → `extend-domain-standards`. Detalle interno: [reference-ui.md](reference-ui.md).

## Process

```
Task Progress:
- [ ] 1. Explore (+ security inventory, + UI inventory)
- [ ] 2. Present findings
- [ ] 3. Decide sections (one at a time)
- [ ] 4. Draft
- [ ] 5. Write
- [ ] 6. Done
```

### 1. Explore

Leer, no asumir:

- `package.json` / lockfiles (`pnpm-lock.yaml`, `package-lock.json`, `yarn.lock`) → package manager
- Framework FE/BE (deps + carpetas típicas: `angular.json`, `next.config.*`, `nest-cli.json`, etc.)
- **Raíces de código** — ¿una o varias? (`src/`, `api/`, `server/`, `packages/*`, `apps/*`…). Anotar rutas reales; no inferir vendor.
- **Backend / runtime** — ¿existe capa servidor? ¿Archivos de config de deploy local? (cualquier nombre: `host.json`, `serverless.yml`, `Dockerfile`…). Solo anotar presencia; **no asumir** qué producto es.
- Lenguaje/runtime (`typescript`, `engines.node`, `.nvmrc`)
- Tooling: ESLint, Biome, Prettier, Jest, Vitest, tsconfig strictness, `tsconfig.*` múltiples
- `vitest.config.*`, `test-setup.*`, patrones `**/*.{spec,test}.*`
- `AGENTS.md` / `CLAUDE.md` — ¿existe? ¿ya hay `## Coding standards` o `## Agent skills`?
- `CODING_STANDARDS.md`, `docs/standards/`
- `.cursor/rules/`
- **Hosts de composición** — ¿hay `pages/`, rutas/vistas de entrada, handlers/controllers, flows/orquestadores? (señal para Section F)
- **Módulos grandes (solo lectura)** — listar archivos de aplicación (no generated, no lockfiles) por encima de ~300 líneas o con muchos métodos/tipos públicos. Anotar rutas y tamaños; **no** partir ni auditar en Explore. Señal para el presupuesto de módulo en Section C.
- Monorepo: `pnpm-workspace.yaml`, `workspaces` en package.json
- **Git** — ¿repo git? (necesario para `verify:touched`). Anotar si hay submodules o worktrees habituales.
- **Patrones modulares** — rutas repetibles de cajas/módulos (`features/`, `packages/*/`, `apps/*/`, `modules/*/`…) para tests scoped en `verify:touched` (opcional; no exigir layout feature-first).
- Idioma de README/docs existentes
- **Secrets / config local** — `.env*`, `*.local.json`, `.gitignore` relevante (solo inventariar)
- **Docs legacy candidatos** — `DATABASE.md`, `BACKEND.md`, `DESIGN.md`, `SECURITY.md`, `progress/*` (solo inventariar; **no asumir que están correctos**)
- **UI / superficies** — ¿hay app con pantallas, web, nativo, o solo API/jobs/CLI? Anotar raíces de presentación. Si no hay UI, marcar Section L como omitible.
- **Fuente de marca (solo inventario)** — archivos tipo `DESIGN.md`, `brand.md`, `tokens.*`, `theme.*`, carpetas `design/` / `brand/` — **no asumir que son contrato** (van a K y L).
- **Enfoque de estilo (detectar, no recomendar otro)** — utility CSS, hojas por componente, CSS-in-JS, archivo de tokens, widgets nativos, mix. Anotar lo que hay.
- **Tooling visual / a11y ya presente** — scripts o configs de lint de spec/tokens, tests de a11y. Solo nombres y rutas; **no** instalar ni elegir librería.
- **Superficies públicas detectables** — rutas/pantallas de entrada si el router/nav las nombra. Lista corta para L; **no** proponer barrido de toda la app.

#### Security inventory (solo lectura)

Inventario de señales — **no auditar ni puntuar**. Tabla para Section J:

| Señal | Qué buscar |
|-------|------------|
| Secrets gitignored | `.env`, `*.local.json`, patrones en `.gitignore` |
| Plantillas example | `*.example`, `*.sample` |
| Auth en boundary | guards, middleware, interceptors, `@Authorize`, etc. |
| Validación HTTP | Zod, class-validator, Joi, Pydantic, etc. |
| Lockfiles | `package-lock.json`, `pnpm-lock.yaml`, etc. |
| Advisories tooling | `npm audit` script, Dependabot, OSV config |
| Headers / CORS hints | config de servidor o proxy |
| Token en storage JS | `localStorage`, `sessionStorage`, patrones auth en FE |
| SPA / auth cliente | guards, interceptors, MSAL/OAuth SDK, login flows |

**No invocar MCP ni WebFetch de un vendor** en Explore salvo que el usuario ya haya nombrado una tecnología en el mensaje. Para research oficial → `extend-domain-standards`.

#### UI inventory (solo lectura)

Inventario — **no rediseñar ni auditar WCAG**. Tabla para Section L:

| Señal | Qué buscar |
|-------|------------|
| Hay UI | App, site, nativo, o solo backend |
| Brand source | `DESIGN.md` u otros; ¿existe? |
| Tokens / theme | archivo o tema del stack |
| Enfoque CSS / estilo | utility / modules / CSS-in-JS / nativo |
| Checker de spec | script o CLI ya en el repo |
| Runner a11y | tests o config ya en el repo |
| Superficies nombradas | pocas rutas/pantallas de entrada |

No invocar MCP/CLI de un design system ni instalar dependencias de a11y en Explore.

Patrones multi-root (referencia interna del agente): [reference-multi-root-tooling.md](reference-multi-root-tooling.md). UI/a11y (referencia interna): [reference-ui.md](reference-ui.md).

### 2. Present findings

Resumen corto: presente / ausente / stack detectado / raíces de código / backend (sí-no-transitorio) / **tabla security inventory** / **tabla UI inventory** / **docs legacy inventariados**. Luego secciones **una a una**.

Orden sugerido: **A → B → C → D → H → E → G → I → J → K → L → F**

(Section K antes de L y F: no canonizar un brand source sin jerarquía. Section L omitida si no hay UI. Section F hereda rules opcionales de G/J/L.)

### 3. Decide (una pregunta por mensaje)

En **cada** sección: opciones → **recomendación en negrita** → esperar respuesta. No escribir aún.

**Section A — Idioma de artefactos y copy operador**  
Detectado inglés/español en docs → proponerlo. Si vacío → **español** para artefactos de estándares.

En la **misma** pregunta (no asumir):

| Tema | Opciones | Recomendación |
|------|----------|---------------|
| Idioma del **copy operador** (UI, toasts, errores) | Igual que docs / otro idioma acordado | Igual que docs salvo producto explícitamente bilingüe |
| **Mono-idioma vs i18n** | Solo catálogo tipado / multi-idioma actual o previsto | Mono-idioma por defecto; si i18n previsto → **keys estables** desde día 1 |
| **Glosario de dominio** | Omitir / generar `domain-glossary.md` | Generar si hay UI de producto con vocabulario de negocio rico |
| **Guard tests anti-jerga** | Omitir / lista inicial acordada | Proponer lista según stack detectado si hay frontend de producto |
| **Correlación de errores (`traceId`)** | Por defecto / Omitir | **Por defecto** — el fallo técnico expone un id opaco; la UI lo pinta como `(ref: …)` solo en fallos técnicos |

### A.3b — i18n ya presente (si Explore detectó señales)

Señales: carpetas `locales/`, `i18n/`, `translations.*`, scripts `i18n:*`, middleware de traducción en API.

| Opción | Acción |
|--------|--------|
| **Integrar** (default si hay señales) | Copy-as-Code alimenta el pipeline existente; keys estables; no instalar otra librería por defecto |
| Mono-idioma | Catálogo tipado; sin i18n |
| Reemplazo explícito | Solo si el usuario lo pide → `extend-domain-standards` |

**Integrar significa:** nuevas cadenas → catálogo tipado + pipeline actual. **No** crear un segundo sistema paralelo. **No** borrar locales ni selector de idioma.

No instalar librería i18n por defecto. Profundidad i18n del stack → `extend-domain-standards` si el usuario lo pide.

**Section B — AGENTS.md vs CLAUDE.md**  
- Si existe uno → editar ese.  
- Si existen ambos → editar el que ya use el equipo (preguntar).  
- Si ninguno → preguntar cuál crear (**recomendar AGENTS.md**).  
Nunca crear el otro si uno ya existe solo para este setup. Añadir `## Coding standards` con jerarquía (Section K) sin tocar `## Agent skills`.

**Section C — Tooling lint**  
Según lo encontrado: mantener y endurecer / migrar a Biome / introducir ESLint estricto. Recomendar según stack (p. ej. Angular → ESLint ecosistema; greenfield JS → Biome o ESLint).

Si hay **varias raíces TS**, proponer en el draft (no escribir aún): `tsconfig.eslint.json` + bloques flat config por glob. Ver [reference-multi-root-tooling.md](reference-multi-root-tooling.md). Alinear major del plugin linter con el major del framework **del repo detectado**.

Legacy con muchos findings de lint → esperado; no proponer relajar reglas como default.

**Presupuesto de módulo (misma Section C, no una sección nueva).** Incluir en *esta* pregunta de linter — no preguntar aparte ni escribir sin OK:

| Opción | Qué proponer en el draft |
|--------|--------------------------|
| Omitir | Umbral solo cualitativo (SRP / host recursivo) |
| Solo doc | Umbral numérico en `clean-code.md` + `clean-code.mdc`; sin lint |
| **Doc + lint warn** | + `max-lines` (opcional `max-lines-per-function`) en **warn**; exclude generated + vendor |

**Recomendar doc + lint warn** si Explore vio archivos >300 líneas o hay backend/SPA con services/hosts. Default **300** líneas; el usuario puede cambiar el número al responder. Warn-only en v1 (no `error`) para no tumbar legacy. Si el usuario no menciona presupuesto al elegir linter, **preguntar en la misma respuesta** antes de Draft — no asumir el default y escribir.

**Reglas ESLint locales opcionales** _(solo si Section C eligió ESLint; omitir con Biome u otro linter sin plugin local)_ — incluir en la **misma** pregunta de linter:

| Opción | Qué proponer |
|--------|----------------|
| Omitir | Solo `max-lines` global acordado |
| **Soft compositor + boundary import** | `eslint-local-rules.mjs` + plugin `local/`: warn soft budget en hosts compositor (pages/handlers/flows — globs desde Explore); warn imports entre cajas/modulos (regex desde Explore). Hard `max-lines` sigue en config global. |

**Recomendar soft + boundary** si Explore detectó hosts compositor, cajas/modulos con imports cruzados, o SPA+backend multi-caja. Patrones y mensajes **desde Explore** — nunca copiar regex de otro repo. Si el usuario rechaza, no escribir `eslint-local-rules.mjs`.

**Section D — Tests**  
JS/TS: target Vitest. Si hay Jest: opciones sustituir / Vitest en paralelo / solo documentar target. **Recomendar** según riesgo (repo nuevo → sustituir; legacy con muchos tests Jest → paralelo o documentar).

**Section H — Layout Vitest** _(solo JS/TS; omitir si Section D no elige Vitest)_  
| Opción | Descripción |
|--------|-------------|
| Config única | Un `vitest.config.*` con globs para todas las raíces acordadas |
| `test.projects` | Proyectos separados (distinto entorno/setup por raíz) |
| Solo documentar | Runner acordado pero sin cambiar configs aún |

**Recomendar** según número de raíces y entornos (jsdom vs node). Incluir `setupFiles` acordados en el draft.

**Section E — Alcance de `verify`**  
- Repo casi vacío → montar cinturón completo (`verify` = lint → typecheck → test).  
- Legacy con configs → proponer cambios concretos y pedir OK.  
Nunca borrar Jest/ESLint sin confirmación explícita.

| Opción | Descripción |
|--------|-------------|
| Belt único | `verify` = lint → typecheck → test |
| **Belt en dos velocidades** | `verify:standards` = lint → typecheck → test; `verify` productivo existente **sin cambios** |
| Solo documentar | Scripts nuevos pero sin encadenar aún |

**Recomendar** belt en dos velocidades cuando ya exista `verify` con gates de producto. Documentar fase 2: unificar cuando `verify:standards` sea estable.

Incluir en el draft, si aplica multi-root: tsconfigs de typecheck por raíz, `tsconfig.eslint.json`, scripts opcionales `dev:*` / `build:*` **solo si el usuario los pide**.

**`verify:touched`** _(misma Section E; omitir si no hay git)_:

| Opción | Descripción |
|--------|-------------|
| Omitir | Solo belt completo `verify` / `verify:standards` |
| **Diff-scoped (recomendado)** | `scripts/verify-touched.mjs` + config; scripts `verify:touched` y `lint:touched` |

Si se acordó diff-scoped, preguntar en la **misma** respuesta (sub-opciones):

| Sub-tema | Opciones | Recomendación |
|----------|----------|---------------|
| Comando lint | El acordado en C (`eslint` / `biome check` / …) | Mismo que `lint` |
| Tests sin módulo en diff | `full` / `skip` | `full` en repos pequeños; `skip` si suite lenta y typecheck cubre tipos |
| Tests scoped | Solo si Explore encontró patrón modular repetible | Regex + args del runner detectado (Vitest, pytest path, …) |
| Base ref default | `HEAD` / otro | `HEAD` |

`verify:touched` **no requiere** carpetas `features/*`; lint+typecheck siempre; scoped tests solo cuando el layout lo permita.

**Section G — Backend / runtime** _(omitir si el repo es solo frontend o el usuario dice “sin backend”)_  
Preguntar **una** cosa: ¿cómo describís la capa servidor de este repo?

Opciones (el usuario elige o describe libremente):

| Opción | Qué documentar |
|--------|----------------|
| **Sin backend** | No crear `runtime.md` ni rule `runtime-boundary` |
| **Transitorio** | Handlers/código en rutas actuales; target definitivo pendiente → `runtime.md` con sección Transición |
| **Target nombrado por el usuario** | Forma acordada (monolito, serverless, BFF, microservicio in-repo…) en `runtime.md` — **sin** copiar guías del vendor |
| **Profundizar después** | Mínimo en `runtime.md` + sugerir `/extend-domain-standards` cuando el usuario quiera reglas oficiales |

**Nunca** proponer Azure/GCP/AWS por defecto. Si el usuario nombra uno, documentar solo lo acordado y mencionar `extend-domain-standards` para el detalle oficial. **No** canonizar docs legacy como verdad.

**Section I — Config local y secrets** _(omitir si no hay backend ni config local detectable; o si el usuario declina)_  
¿Dónde viven settings/secrets locales y qué va gitignored?  
Opciones: plantilla acordada (`*.example`), solo `.gitignore` / doc en `local-config.md`, omitir por ahora.

**Section J — Postura de seguridad**  
Presentar tabla del security inventory. Opciones:

| Opción | Qué crear |
|--------|-----------|
| Omitir | Sin backend, sin auth, sin datos sensibles detectables |
| Solo doc | `docs/standards/security.md` (incl. sección Trampas MVP) |
| **Doc + rules servidor** | + `security-boundary.mdc` |
| Doc + rules servidor + cliente | + `client-security.mdc` (SPA con auth — anti localStorage/XSS) |
| Doc + rules + belt | + script `verify:security` (audit + secret scan; **belt separado**, no dentro de `verify:standards` v1) |

**Recomendar doc + `security-boundary`** cuando haya backend o API. **Añadir `client-security.mdc`** cuando haya SPA/frontend con login, interceptors o tokens en storage JS — es el antídoto típico a MVP inseguro de agentes (token en `localStorage`, `innerHTML`, CSP relajado).

Preguntar en J si `client-security.mdc` usa `alwaysApply: true` (recomendado en repos SPA con auth activa) o solo globs FE.

`verify:security` como fase 2 (warn-only al inicio).

Profundidad (OAuth, CSP por framework, inyección BD, auth cloud) → **`extend-domain-standards`**, no en este bootstrap.

**Section K — Confianza en docs legacy**  
¿Qué documentos **no** deben tratarse como fuente de verdad?

| Opción | Qué documentar |
|--------|----------------|
| Lista explícita del usuario | p.ej. `DATABASE.md`, `BACKEND.md`, `progress/*` |
| **Jerarquía estándar** | standards > código/configs > todo lo demás |
| Mantener jerarquía actual del repo | Solo si el usuario insiste |

**Recomendación: jerarquía estándar** en `CODING_STANDARDS.md` y bloque `## Coding standards` de AGENTS. Si un doc legacy contradice standards, gana standards; corregir legacy al tocar (boy scout).

Si Explore detectó **corpus normativo extenso** (p. ej. carpeta de reglas, ADRs, contributing largo):

**Recomendación:** bootstrap **mínimo** — jerarquía (standards mandan; legacy referencia) + satélites solo para **huecos** (copy operador, rules Cursor, verify belt). **No** duplicar security/testing/architecture ya cubiertos en legacy.

**Section L — Identidad visual, tokens y a11y** _(omitir si no hay UI o el usuario dice “sin UI”)_

Presentar la tabla UI inventory. Recalcar: un `DESIGN.md` (u otro) inventariado **no** es contrato hasta que K + L lo digan.

| Opción | Qué crear |
|--------|-----------|
| Omitir | Sin UI, o el usuario declina. No `ui.md`, no `ui.mdc`, no belts. |
| Solo doc | `docs/standards/ui.md`: fuente de marca + mínimo a11y + ✅/❌ del stack detectado. Brand source legacy **no** se promociona solo. |
| **Doc + rule** | + `.cursor/rules/ui.mdc` (globs de superficies UI). Contrato: no inventar look; boy scout en lo tocado. |
| Doc + rule + belts | + `{{DESIGN_CHECK_COMMAND}}` y/o `{{A11Y_CHECK_COMMAND}}` **fuera** de `verify` / `verify:standards` v1, **solo si el repo ya tiene** checker de spec/tokens y/o runner a11y. No instalar librería por defecto. |

**Recomendar omitir** sin UI. **Recomendar doc + rule** con UI. **Añadir belts** solo si Explore vio checker/runner; no proponer instalar un motor a11y ni un linter de tokens “porque es maduro”. **No** recomendar “solo doc” si hay SPA/app con pantallas: los agentes ignoran un markdown de marca sin rule.

En la **misma** pregunta (no asumir):

- Fuente de marca (`{{BRAND_SOURCE}}`): promover a **contrato** / dejar **legacy-referencia** / “aún no hay — agentes no inventan look; usan lo ya pintado”.
- Superficies a11y: lista explícita del usuario o las pocas detectadas (`{{A11Y_NAMED_SURFACES}}`). Default de fallo: hallazgos **graves/críticos** (`{{A11Y_FAIL_SEVERITY}}`). Ampliar **una** superficie cuando esté limpia — nunca dump de toda la app.
- `ui.mdc`: `alwaysApply: true` en repos con UI habitual, o solo `{{UI_SURFACE_GLOBS}}` si el usuario quiere acotar.

Profundidad (tokens pipeline, design system de un vendor, nivel WCAG de producto, CLI de marca) → **`extend-domain-standards`**, no este bootstrap.

**Section F — Rules**  
Confirmar las **6 rules core** (recomendado: sí). Globs adaptados al stack.

Opcionales según acuerdos previos:

| # | File | Cuándo |
|---|------|--------|
| 1–6 | core (ver tabla abajo) | Siempre |
| 7 | `unit-composition.mdc` | FE con pages/rutas/vistas, BE con handlers, u orquestación (flows/jobs). **Recomendado: sí** si Explore detectó hosts |
| 8 | `html-template-dry.mdc` | SPA con plantillas declarativas (Angular, Vue, Svelte, JSX…). Opcional; **recomendado junto a 7** |
| 9 | `runtime-boundary.mdc` | Section G ≠ “sin backend” y el usuario acepta |
| 10 | `security-boundary.mdc` | Section J ≥ doc + rule servidor |
| 11 | `client-security.mdc` | Section J acordó SPA/auth cliente |
| 12 | `ui.mdc` | Section L ≥ doc + rule. **No** fusionar con `html-template-dry` (DRY ≠ marca/a11y) ni con `client-security`. |

Si Explore detectó pages/rutas/handlers/flows **o** módulos grandes / services gordos → proponer **7 (`unit-composition`)** antes que runtime/security. El usuario puede rechazarla. **No** añadir una rule `fat-module.mdc`: el presupuesto vive en `clean-code` + unit-composition + lint acordado. `ui.mdc` es opcional **#12 solo** si Section L ≥ doc + rule.

Si Section G acordó backend → proponer **opcionalmente** `runtime-boundary.mdc` (recomendado: sí cuando hay handlers). El usuario puede rechazarla.

Tres rules de seguridad, roles distintos — **no fusionar**:

| Rule | Capa | Enfoque |
|------|------|---------|
| `runtime-boundary` | Servidor | Forma del handler; delegación |
| `security-boundary` | Servidor | Auth, input, secrets, fail closed |
| `client-security` | Cliente | Token storage, XSS, headers, anti-MVP |

Tres rules de UI, roles distintos — **no fusionar**:

| Rule | Enfoque |
|------|---------|
| `html-template-dry` | Markup homogéneo / escalera DRY |
| `unit-composition` | Extraer concerns de UI |
| `ui` | Marca, tokens, a11y mínima, no inventar look |

Saltar secciones ya resueltas por exploración (ej. si no es JS/TS, adaptar D/H/E al ecosistema real: pytest, ruff, etc., siempre preguntando).

### 4. Draft

Mostrar borrador de:

1. Bloque `## Coding standards` para AGENTS/CLAUDE (con jerarquía Section K)  
2. `CODING_STANDARDS.md` (núcleo corto + puntero a security; + UI si L ≠ omitir)  
3. Lista de satélites a crear (core + condicionales: `runtime.md`, `local-config.md`, `security.md`, `domain-glossary.md` si A acordó glosario, `ui.md` si L ≠ omitir)  
4. Nombres de las `.mdc` (6 core + opcionales 7–12 según acuerdos)  
5. Diff propuesto de scripts (`lint`, `typecheck`, `test`, `verify` / `verify:standards`, opcional `verify:security`, opcionales `{{DESIGN_CHECK_COMMAND}}` / `{{A11Y_CHECK_COMMAND}}` **fuera** de verify) y configs nuevas/cambiadas  
6. Si multi-root: `tsconfig.eslint.json`, tsconfigs por raíz, bloques ESLint/Vitest  
7. Si se acordó en C: umbral `{{FILE_BUDGET_LINES}}` + snippet `max-lines` warn (globs, exclude generated). Si se omitió: no inventar la regla lint.  
8. Si L no omitió: `{{BRAND_SOURCE}}` + estado contrato vs legacy; si no hay fuente: frase “no inventar identidad visual”.  
9. Si L acordó belts: comandos en tooling/testing/verify-before-commit como **aparte** de `{{VERIFY_COMMAND}}`. Si se omitieron: no inventar scripts ni deps.  
10. Lista `{{A11Y_NAMED_SURFACES}}` (o “sin belt a11y”).
11. Si A acordó i18n previsto: nota de keys estables en `clean-code.md`; mecanismo concreto TBD o → `extend-domain-standards`.
12. Si A acordó glosario: filas iniciales propuestas para `domain-glossary.md`. Si A acordó guard tests: lista de términos proscritos inicial. Si A acordó correlación: viñeta `traceId` en `clean-code.md` + `security.md`.
13. Si E acordó `verify:touched`: scripts `verify:touched`, `lint:touched`; diff de `scripts/verify-touched.mjs` + `verify-touched.config.mjs` (valores rellenados, no placeholders).
14. Config scoped tests: regex de módulo, fallback full/skip, comandos de test por módulo (o `null` si no aplica).
15. Si C acordó reglas ESLint locales: diff de `eslint-local-rules.mjs` + bloque `local/` en flat config (globs compositor desde Explore).
16. `verify-before-commit.mdc` con `{{VERIFY_TOUCHED_COMMAND}}` + regla anti-truncado.
17. Filas `verify:touched` / `lint:touched` en `tooling.md` y principio 7 de `coding-standards.md`.

Dejar editar antes de escribir.

### 5. Write

Solo tras OK explícito.

**Docs (core — siempre)**

- `CODING_STANDARDS.md` en raíz — plantilla: [templates/coding-standards.md](templates/coding-standards.md)
- `docs/standards/clean-code.md`
- `docs/standards/feature-first.md`
- `docs/standards/testing.md`
- `docs/standards/tooling.md`

**Docs (condicionales — según acuerdos en G / I / J / L)**

- `docs/standards/runtime.md` — [templates/runtime.md](templates/runtime.md)
- `docs/standards/local-config.md` — [templates/local-config.md](templates/local-config.md)
- `docs/standards/security.md` — [templates/security.md](templates/security.md)
- `docs/standards/ui.md` — [templates/ui.md](templates/ui.md) _(Section L ≠ omitir)_
- `docs/standards/domain-glossary.md` — [templates/domain-glossary.md](templates/domain-glossary.md) _(Section A acordó glosario)_

Rellenar ejemplos **del stack detectado** y **texto acordado con el usuario**. Idioma acordado. Principios: [reference-clean-code.md](reference-clean-code.md), [reference-security.md](reference-security.md), [reference-ui.md](reference-ui.md).

Si Section A omitió glosario: no copies `domain-glossary.md`; quita el principio 4 / enlace en `coding-standards.md`. Si A acordó mono-idioma sin i18n: no dejes placeholders de locales en el glosario.

Si Section A **omitió** correlación de errores (`traceId`): quita las viñetas/frases de correlación en `clean-code.md`, `security.md` y `clean-code.mdc`; no dejes placeholders.

Al copiar plantillas de clean-code / feature-first / tooling / unit-composition: `{{FILE_BUDGET_LINES}}` = número acordado en C (default 300 **solo si el usuario aceptó umbral**). `{{FILE_BUDGET_LINT}}` = `max-lines` warn o “omitido”. No rellenar un lint que el usuario rechazó. Si C eligió **omitir** presupuesto: no dejes placeholders; quita la tabla numérica y la fila de lint — deja solo host recursivo + corte orquesta/política/I/O.

**AGENTS / CLAUDE** — añadir o actualizar in-place:

```markdown
## Coding standards

Estándares de código, feature-first, composición de unidades (anti *Divergent Change*), seguridad y cinturón local. **Contrato:** `CODING_STANDARDS.md` y `docs/standards/`.

| Prioridad | Fuente | Rol |
|-----------|--------|-----|
| 1 | `docs/standards/*`, `.cursor/rules/*` | Estándares — mandan en conflictos |
| 2 | Código + configs + tests que pasan | Hecho verificable |
| 3 | Docs legacy acordados en setup | Referencia — alinear al tocar; no asumir vigencia |

- Composición compositor vs concern: `docs/standards/feature-first.md#composición-de-unidades`
- Lenguaje Ubicuo / copy operador: `docs/standards/clean-code.md` — códigos de error estables, mappers puros, correlación `traceId`, migrate-on-touch
- Presupuesto de módulo (anti Fat Module): `docs/standards/clean-code.md` — umbral/lint solo si se acordó en setup
- Cambios medios/grandes (agentes): `{{VERIFY_TOUCHED_COMMAND}}` — leer salida completa; no truncar
- Pre-merge / release: `{{VERIFY_COMMAND}}` (belt completo)
- Cierre release / producto: `verify` completo si el repo lo define aparte
- Profundidad vendor: `/extend-domain-standards` (invocación explícita)
- Skills Matt (`docs/agents/*`): flujo de issues — no sustituyen coding standards
```

Si Section L ≠ omitir, añadir al bloque (no si se omitió):

```markdown
- Identidad visual: fuente `{{BRAND_SOURCE}}` + `docs/standards/ui.md` — no inventar un look
- Belts diseño/a11y (si se acordaron): `{{DESIGN_CHECK_COMMAND}}` / `{{A11Y_CHECK_COMMAND}}` — **fuera** de `{{VERIFY_COMMAND}}`
```

Si la sección ya existe, actualizarla; no duplicar. Ajustar filas de jerarquía existente en AGENTS si contradice Section K.

**Rules** (`.cursor/rules/`) — crear/actualizar:

| File | alwaysApply / globs | Concern |
|------|---------------------|---------|
| `feature-first.mdc` | alwaysApply: true | Cajas; boy scout; no carpetas vacías |
| `naming-conventions.mdc` | globs del stack | Nombres declarativos |
| `testing-standards.mdc` | `**/*.{spec,test}.*` (+ patrones del stack) | Vitest u runner acordado; tests como spec |
| `clean-code.mdc` | globs del stack | SRP, early return, presupuesto de módulo (si se acordó) |
| `discriminator-dispatch.mdc` | globs del stack | Registry map; handlers no triviales en archivo propio |
| `verify-before-commit.mdc` | alwaysApply: true | Belt acordado en Section E |
| `unit-composition.mdc` _(opcional)_ | globs del stack (+ plantillas FE) | Compositor vs concern; host recursivo; anti Fat Host/Module — [templates/rules/unit-composition.mdc](templates/rules/unit-composition.mdc) |
| `html-template-dry.mdc` _(opcional)_ | globs FE acordados | DRY markup homogéneo; no bloquea composición — [templates/rules/html-template-dry.mdc](templates/rules/html-template-dry.mdc) |
| `runtime-boundary.mdc` _(opcional)_ | globs del backend acordado | Boundary handler; async; delegación — [templates/rules/runtime-boundary.mdc](templates/rules/runtime-boundary.mdc) |
| `security-boundary.mdc` _(opcional)_ | globs del backend + auth boundary | Auth, input, secrets, fail closed — [templates/rules/security-boundary.mdc](templates/rules/security-boundary.mdc) |
| `client-security.mdc` _(opcional)_ | globs FE; `alwaysApply` si SPA+auth | Token storage, XSS, headers, anti-MVP — [templates/rules/client-security.mdc](templates/rules/client-security.mdc) |
| `ui.mdc` _(opcional)_ | globs de superficies UI; `alwaysApply` si L lo acordó | Marca, tokens, a11y mínima, boy scout — [templates/rules/ui.mdc](templates/rules/ui.mdc) |

Cada rule < 50 líneas, ✅/❌ concretos del stack. Semillas: [templates/rules/](templates/rules/).

Al escribir `unit-composition.mdc` / `html-template-dry.mdc`: `SOURCE_GLOBS` y `FE_TEMPLATE_GLOBS` deben incluir extensiones de plantilla del stack (p. ej. `html`, `tsx`, `vue`, `svelte`).

Al escribir `ui.md` / `ui.mdc`: rellenar `{{BRAND_SOURCE}}`, `{{BRAND_SOURCE_STATUS}}`, `{{STYLE_APPROACH}}`, `{{FRONTEND_UI}}`, `{{UI_SURFACE_GLOBS}}`. `{{UI_RULE_ALWAYS_APPLY}}` = `true` o `false` (boolean YAML, sin comillas). Si L eligió **solo doc**, no crear `ui.mdc`. Si un belt es omitido: quita filas/viñetas en `ui.md`, `tooling.md`, `testing.md` y `verify-before-commit.mdc`; no dejes placeholders. Si L **omitió**: no copies `ui.md`, ni `ui.mdc`, ni el principio 8 / frase boy-scout de UI en `coding-standards.md`, ni las filas de belts UI en tooling/testing/verify-before-commit.

**Tooling** — aplicar solo lo confirmado en C–E, J, L (+ multi-root / H si aplica):

- Scripts: `lint`, `typecheck`, `test`, `verify` y/o `verify:standards` según Section E
- Si E acordó diff-scoped: copiar [templates/scripts/verify-touched.mjs](templates/scripts/verify-touched.mjs) → `scripts/verify-touched.mjs`; [templates/scripts/verify-touched.config.mjs](templates/scripts/verify-touched.config.mjs) → `scripts/verify-touched.config.mjs` (rellenar placeholders desde Explore; ver [templates/scripts/verify-touched.types.md](templates/scripts/verify-touched.types.md)); añadir `verify:touched` y `lint:touched` en `package.json` apuntando al orchestrator y al lint acordado
- Si C acordó reglas ESLint locales: [templates/eslint-local-rules.mjs](templates/eslint-local-rules.mjs) en raíz (o ruta acordada); registrar plugin `local/` en flat config con globs compositor/boundary desde Explore
- `verify:security` opcional (Section J): audit + secret scan; belt separado; warn-only hasta acuerdo
- `{{DESIGN_CHECK_COMMAND}}` / `{{A11Y_CHECK_COMMAND}}` opcionales (Section L): **belts separados**; no meterlos en `verify` / `verify:standards` v1; no instalar checker ni runner si el repo no los tenía
- Configs de linter/tsconfig/vitest según acuerdo
- `max-lines` / `max-lines-per-function` **solo** si Section C acordó doc + lint warn (severity warn; exclude generated)
- No añadir CI remoto ni deploy smoke a `verify` v1
- No añadir CI remoto de a11y/design en v1

### 6. Done

Informar qué se creó y los comandos de belt acordados (`{{VERIFY_TOUCHED_COMMAND}}` para agentes; `{{VERIFY_COMMAND}}` pre-merge).

Si `verify:touched` aplicó: recordar que typecheck sigue siendo completo; tests scoped dependen del patrón modular detectado; sin git no aplica.

Mencionar:

- [`setup-matt-pocock-skills`](../setup-matt-pocock-skills/SKILL.md) — sin conflicto de rutas con Matt.
- [`extend-domain-standards`](../extend-domain-standards/SKILL.md) — **solo invocación explícita del usuario** para profundizar en un runtime/SDK/cloud concreto (guía oficial, reglas vendor-specific, MCP de docs), o en un design system / tooling WCAG si Section L lo dejó para después.

Si L aplicó: informar contrato de marca (`{{BRAND_SOURCE}}` contrato vs legacy) y que diseño/a11y **no** están dentro de `{{VERIFY_COMMAND}}`.

Re-ejecutar este skill solo para re-bootstrap o cambio de target de tooling.

## Anti-patrones

- Escribir sin preguntar
- Canonizar docs legacy (`DATABASE.md manda…`) sin Section K
- Auditar o parchear código en el setup (inventario ≠ auditoría)
- Copiar checklist OWASP entera en `security.md`
- Fusionar `runtime-boundary`, `security-boundary` y `client-security` en una rule
- Proponer token en `localStorage` / `sessionStorage` por defecto en SPA
- Documentar trampas MVP solo en `security.md` sin `client-security.mdc` cuando hay SPA con auth
- Ejemplos React en repo Angular (o cualquier mismatch de stack)
- Proponer migración de framework
- Crear `features/` vacías
- Pisar `docs/agents/` o `## Agent skills`
- Meter format-check en `verify` (v1)
- Jest como target en JS/TS nuevos
- Relajar ESLint/typecheck para “hacer pasar” legacy
- **Hardcodear vendor** (Azure, GCP, AWS, Vercel, etc.) en plantillas, defaults o recomendaciones
- **Asumir serverless** porque exista carpeta `api/` o `functions/`
- **Auto-invocar** `extend-domain-standards` o MCP de un cloud sin que el usuario lo pida
- **Investigar documentación oficial de un producto** durante setup genérico — eso es otra skill
- Copiar layouts o snippets de un vendor en `runtime.md` sin contraste y OK del usuario
- Regla DRY de plantillas **sin** `unit-composition` cuando hay SPA con pages/rutas (la escalera DRY empuja a un solo archivo)
- Asumir “componente solo si 2+ usos” — contradice composición por concern de un solo uso
- Tratar un slot de caja (`services/`, `models/`, `components/`, `strategies/`) como **un** archivo (*Fat Module*)
- Composición de un solo salto: extraer un concern y dejarlo crecer sin volver a partir
- Registry con handlers no triviales en el mismo archivo que el mapa
- Spec espejo de un módulo obeso (el test no sigue el corte de producción)
- Escribir `max-lines` o umbral numérico **sin** OK en Section C / Draft
- Añadir una rule `fat-module.mdc` — el presupuesto vive en clean-code + unit-composition + lint acordado (`ui.mdc` no es presupuesto)
- Partir o “arreglar” archivos gordos durante Explore (inventario ≠ refactor)
- Canonizar `DESIGN.md` (u otro brand source) como verdad **sin** Section K + L
- Inventar identidad visual / paleta / tipografía porque un skill de “frontend design” lo pida
- Meter lint de tokens o a11y **dentro** de `verify` / `verify:standards` v1
- Instalar por defecto un linter de spec, un runner a11y o un CLI de marca
- Barrido a11y de toda la app en v1; crecer sin tener limpias las superficies nombradas
- Fusionar `ui.mdc` con `html-template-dry.mdc` o con `client-security.mdc`
- Restyle masivo “para cumplir” la fuente de marca; boy scout solo en lo tocado
- Relajar el belt de diseño/a11y para “hacer pasar” un cambio
- Hardcodear un framework CSS, un motor a11y, un CLI de marca, rutas de login o un package manager en plantillas de esta skill
- Aplicar Section L a un repo sin UI
- Auto-invocar `extend-domain-standards` porque hay `DESIGN.md` o un theme
- Copiar `verify-touched.mjs` / `eslint-local-rules.mjs` de otro repositorio sin rellenar desde Explore
- Exigir layout `features/*` para montar `verify:touched` (lint+typecheck funcionan sin cajas)
- Truncar salida de verify (`tail`, `Select-Object -Last`, etc.) — oculta errores reales
- Acoplar esta skill a flujos de issues/tickets concretos de un producto
