---
name: genkit-evals
description: >-
  Diseña e implementa evaluaciones Genkit (evaluators, datasets golden, Dev UI, CLI)
  en cualquier repo JS/TS con Genkit. Usar SIEMPRE que el usuario mencione evals,
  evaluators, métricas, datasets, eval:flow, Pass % en Dev UI, LLM-as-judge,
  faithfulness, QA de flows/prompts, o quiera montar evaluación colocated en una
  caja AI — aunque no diga "Genkit eval" explícitamente. Complementa genkit-evaluation-js;
  esta skill define layout, contratos de retorno y trampas de la Dev UI.
---

# Genkit Evals (portable)

Skill para **crear o auditar** evaluaciones Genkit en **cualquier repositorio**. No asume layout Komtexia concreto; adapta `<box>` al layout del repo (vertical-slice `ai/<box>/`, feature module, etc.).

## Fuente de verdad (obligatorio antes de codear)

1. **Docs Genkit** — vía MCP `user-genkit`: `search_genkit_docs` → `read_genkit_docs`
   - Mínimo: `js/evaluation.md`, `js/devtools.md`, `js/testing.md`
2. **Versión instalada** — leer `package.json`: `genkit`, `genkit-cli`, `@genkit-ai/evaluator`, `@genkit-ai/ai`
3. **Código del runtime** — verificar en `node_modules` (no confiar en memoria):
   - `ScoreSchema` / `EvalStatusEnum` → `@genkit-ai/ai` (anidado bajo `genkit` o directo según lockfile)
   - Pass % UI → `@genkit-ai/tools-common` → `eval/parser.js` (`statusDistribution`)

Lista completa de URLs y rutas: [`references/official-sources.md`](references/official-sources.md).

---

## Principios (de la doc + runtime real)

| Principio | Por qué |
|-----------|---------|
| **Eval colocated con la caja dueña** | Métricas de dominio viven junto al flow/prompt que evalúan |
| **Datasets golden en git** | Reproducible, CI, equipo |
| **`.genkit/` no es SoT** | Caché local Dev UI; suele estar en `.gitignore` |
| **Heurísticos ≠ LLM judge** | Archivos separados; judge es caro y opcional |
| **`evaluation.status` para Pass %** | La UI no usa `pass: true` ni `details.valid` |
| **Consultar doc del proveedor LLM** | Azure GPT-5 reasoning: `temperature` solo default `1` |

---

## Layout recomendado (parametrizado)

Adaptar `<box>` al convención del repo (`dossier-deep`, `qa`, `my-feature`, …):

```text
<genkit-root>/src/ai/<box>/
  eval/
    register.ts                 # side-effect: registra evaluators
    <box>.eval.pure.ts          # lógica determinística (Vitest)
    <box>.eval.pure.spec.ts
    <box>.eval-status.ts        # score → EvalStatusEnum (reutilizable)
    <box>.evaluators.ts         # ai.defineEvaluator (heurísticos)
    <box>.eval-judge.ts         # opcional: LLM-as-judge
    <box>.eval-judge.pure.ts    # parseo/normalización judge
    <box>.eval.schema.ts        # schemas Zod del judge si hace falta
    datasets.manifest.ts        # IDs, targetFlow, paths, evaluators CSV
    datasets/
      <scenario>.json           # golden fixtures (git)

<promptDir>/<box>/
  eval-judge.prompt             # opcional: instrucciones del juez
```

**Registro:** importar `./ai/<box>/eval/register.js` en el **entry Genkit** (`main.ts`, `genkit.config.ts`, …) **después** de registrar los flows de esa caja.

Plantillas copy-paste: [`references/templates.md`](references/templates.md).

---

## Contrato del evaluator (crítico)

Patrón oficial: [`defineEvaluator`](https://genkit.dev/docs/js/evaluation/) + `BaseEvalDataPoint` desde `genkit/evaluator`.

### Retorno correcto

```typescript
import { EvalStatusEnum, type BaseEvalDataPoint } from "genkit/evaluator";

return {
  testCaseId: datapoint.testCaseId,
  evaluation: {
    score: 0.85,                              // number 0..1 típico
    status: EvalStatusEnum.PASS,              // ← Dev UI Pass % usa ESTO
    details: { reasoning: "...", ... },       // texto libre; no afecta Pass %
  },
};
```

### Mitos frecuentes (investigados en runtime)

| Creencia | Realidad |
|----------|----------|
| `pass: true` en evaluation | **No existe** en `ScoreSchema` |
| `details.valid: true` | **Ignorado** por Pass % |
| Score 1.00 ⇒ Pass 100% | **Falso** sin `status: PASS` |
| Doc custom solo muestra `{ score }` | Plugin `@genkit-ai/evaluator` sí rellena `status`; custom debe hacerlo |

### Umbrales `status` (alinear con plugin oficial)

| Tipo métrica | Regla típica | Referencia |
|--------------|--------------|------------|
| Binaria (schema OK) | `score === 1` → PASS | — |
| Continua | `score > 0.5` → PASS | faithfulness en `@genkit-ai/evaluator` |
| Judge normalizado 0..1 | `score >= 0.8` → PASS | criterio de producto; documentar en manifest |

Implementación reusable en `<box>.eval-status.ts` — ver [`references/templates.md`](references/templates.md).

---

## Tipos de evaluación (Genkit)

1. **Inference-based** (la más común): dataset `{ input, reference? }` → corre flow → evaluators puntúan `output`
2. **Raw** (`eval:run`): dataset ya trae `input`, `output`, `context`; sin inferencia

Doc: [Evaluation — Core concepts](https://genkit.dev/docs/js/evaluation/).

---

## Datasets

### Formato golden (git)

```json
[
  {
    "input": { "...": "..." }
  },
  {
    "input": { "...": "..." },
    "reference": { "...": "..." }
  }
]
```

- `reference` **no** afecta inferencia; lo reciben evaluators (judge, comparación)
- Validación UI opcional si el dataset está ligado a un flow con schemas

### `.genkit/datasets/`

- Generado por Dev UI al crear datasets localmente
- **No versionar** como fuente de verdad (normalmente gitignored)
- Para equipo/CI: mantener JSON en `eval/datasets/` + `datasets.manifest.ts`

### Manifiesto (recomendado)

```typescript
export const EVAL_DATASETS = {
  smoke: {
    datasetId: "<box>/smoke",
    targetFlow: "myFlowName",
    file: "src/ai/<box>/eval/datasets/smoke.json",
    evaluators: "box/outputValid,box/claimsQuality",
  },
} as const;
```

---

## Dev UI — flujo QA humano

1. Arrancar: `genkit start -- <entry>` (ej. `tsx src/genkit.config.ts`)
2. **Datasets** → crear/abrir dataset tipo **Flow** → target = flow a evaluar
3. Añadir filas (solo `input` obligatorio)
4. **Run new evaluation** → Flow + Dataset + **métricas**
5. Revisar en **Evaluations** (Pass %, scores, traces)

### Trampas UI

| Pantalla | Uso |
|----------|-----|
| **Datasets → Run evaluation** | ✅ Camino principal (flow + métricas) |
| **Evaluators → Run** con `{ dataset: [], evalRunId: "" }` | Raw eval; **no** primer smoke |
| **Prompts** | Iterar texto; input `any` → formulario vacío es normal |
| **Flows** | Camino producción (tools, Exa, flags) |
| **Context** en eval | Solo auth/tenant/runtime; no sustituye `input` |

Doc: [Developer Tools](https://genkit.dev/docs/js/devtools/).

---

## CLI (CI / reproducible)

```bash
genkit eval:flow <flowName> \
  --input path/to/dataset.json \
  --evaluators=box/metricA,box/metricB \
  -- tsx src/genkit.config.ts
```

- Resultados: `http://localhost:4000/evaluate`
- `--evaluators` omitido → corre **todos** los registrados
- `eval:extractData` + `eval:run` → raw eval desde traces

Añadir script npm en el repo que apunte al manifest (paths relativos a `functions/` o cwd del entry).

---

## Heurísticos vs LLM-as-judge

| | Heurísticos (`*.eval.pure.ts`) | Judge (`*.eval-judge.ts`) |
|---|-------------------------------|---------------------------|
| LLM | No | Sí |
| Coste | Gratis | Tokens × casos |
| Tests | Vitest puro | Mock `runJudgePrompt` |
| Cuándo | CI smoke, schema, reglas | Fidelidad, alucinaciones |
| Doc pattern | `defineEvaluator` + reglas | `defineEvaluator` + `ai.prompt` / `ai.generate` |

Judge separado en Dotprompt `<promptDir>/<box>/eval-judge.prompt` — permite probar instrucciones en pestaña Prompts.

Plugin RAGAS (`genkitEval`, `GenkitMetric.FAITHFULNESS`, …): cablear en **composición** `genkit({ plugins: [...] })` — métricas genéricas; métricas de dominio siguen en `eval/`.

---

## LLM / Azure (GPT-5 reasoning)

**No asumir** que un deployment distinto (ej. alias `terra`) admite `temperature: 0.2`.

- Familia **GPT-5 reasoning** (`gpt-5-mini`, `gpt-5.6-terra`, `gpt-5.2`, …): Azure documenta **`temperature` no soportado** (solo default `1`)
- Doc: [Azure OpenAI reasoning models](https://learn.microsoft.com/azure/foundry/openai/how-to/reasoning#api--feature-support)
- Para judge más estable: `reasoning_effort: 'none' | 'low'`, `verbosity: 'low'` — **no** bajar temperature
- Deployments fuera del catálogo plugin Genkit: resolver con helper tipo `resolveNamedChatModelCall(alias)` → `model` + `config.version`

Detalle: [`references/pitfalls.md`](references/pitfalls.md).

---

## Workflow del agente

### Crear evals nuevos

1. Leer docs MCP + versiones package
2. Identificar `<box>`, flow target, entry Genkit
3. Crear layout `eval/` (pure → status → evaluators → register)
4. Añadir 3–10 casos golden representativos + edge cases
5. Registrar en entry; script npm `eval:<box>:<scenario>`
6. Vitest sobre `*.eval.pure.ts` (sin LLM)
7. Smoke Dev UI: Datasets → Run evaluation
8. Opcional: judge en archivo aparte tras heurísticos verdes

### Auditar evals existentes

1. ¿Retorno incluye `evaluation.status`?
2. ¿Datasets en git vs solo `.genkit`?
3. ¿Judge mezclado con heurísticos en un monolito legacy?
4. ¿Temperature válida para el modelo desplegado?
5. ¿Namespace evaluators consistente (`<box>/<metric>`)?

---

## Anti-patrones

- Carpeta global `src/evals/` sin dueño de caja
- Solo datasets en Dev UI sin JSON versionado
- `details.valid` esperando Pass %
- `pass: true` (campo inexistente)
- Judge inline gigante sin tests ni Dotprompt
- `temperature: 0.2` en GPT-5 reasoning Azure
- Evaluar solo pestaña **Evaluators** con dataset vacío en el primer smoke

---

## Verificación

| Check | Comando / acción |
|-------|------------------|
| Lógica pura | `vitest run **/eval/**/*.spec.ts` |
| Typecheck | `tsc --noEmit` |
| Smoke inferencia | Dev UI o `genkit eval:flow ...` |
| Pass % coherente | Tras eval: barra superior + pills por métrica |
| Traces | Prompt renderizado, modelo, errores API |

---

## Referencias bundled

- [`references/official-sources.md`](references/official-sources.md) — URLs + rutas `node_modules`
- [`references/templates.md`](references/templates.md) — plantillas TypeScript/JSON/prompt
- [`references/pitfalls.md`](references/pitfalls.md) — Pass %, temperature, UI

## Skills relacionadas

- `genkit-evaluation-js` — auditoría contra doc oficial Genkit
- `genkit-index` — routing a skills Genkit
- `genkit-prompts-js` — Dotprompt del judge
