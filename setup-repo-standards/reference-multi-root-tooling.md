# Multi-root tooling (referencia interna para el agente)

Patrones **agnósticos** cuando el repo tiene más de una raíz de código (p. ej. `src/` + `api/`, `apps/` + `packages/`, frontend + backend en el mismo package).

Usar solo lo que el usuario confirmó en el draft. No asumir rutas concretas.

## Typecheck

- Un `tsconfig` por raíz lógica si las opciones del compilador difieren (module resolution, target, types).
- Script `typecheck` encadena los proyectos acordados: `tsc -p <project> --noEmit && …`
- Documentar en `docs/standards/tooling.md` qué proyectos entran.

## ESLint (flat config)

Cuando lint es type-aware y hay varias raíces:

1. Crear `tsconfig.eslint.json` que **incluya** todas las raíces acordadas para lint (puede diferir del tsconfig de build).
2. Bloques separados en `eslint.config.*` por glob si las reglas difieren (p. ej. templates HTML vs TS puro del backend).
3. Alinear major del plugin de framework con el major del framework (regla genérica — consultar compatibilidad, no hardcodear versiones en la skill).

Si `projectService` / type-aware falla con “file not found by the project service” → revisar inclusión en `tsconfig.eslint.json` antes de relajar reglas.

## Vitest

Opciones a proponer al usuario (Section H):

| Layout | Cuándo recomendar |
|--------|-------------------|
| **Config única** | Una raíz de tests o globs simples en un solo `vitest.config.*` |
| **`test.projects`** | Varias raíces con distinto entorno (p. ej. jsdom vs node), distintos `setupFiles` |
| **Solo documentar** | Legacy con otro runner paralelo temporalmente |

Convenciones agnósticas:

- `setupFiles` por contexto (compilador/framework en FE; mocks de request en BE — ejemplos del stack detectado, no de un vendor).
- `include` explícito por raíz acordada.
- Relajar reglas de lint en `*.spec.ts` solo si el usuario lo acepta en el draft.

## Scripts opcionales (fuera de `verify` v1)

Proponer **solo si el usuario los pide** en Section G/E:

- `dev:<runtime>` — arranque local del backend
- `build:<runtime>` — compilación previa al deploy
- `verify:<scope>` — subconjunto (p. ej. solo backend)

No añadir smoke/e2e/deploy a `verify` en v1.

## Anti-patrones

- Copiar layout de un cloud vendor en plantillas genéricas
- Un solo tsconfig strict que mezcla targets incompatibles sin acuerdo
- Asumir carpeta `api/` = serverless
