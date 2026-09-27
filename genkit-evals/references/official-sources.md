# Fuentes oficiales y runtime Genkit (evals)

Consultar **en el repo del usuario** antes de implementar. Versiones pueden diferir.

## Documentación Genkit (web)

| Tema | URL |
|------|-----|
| Evaluation (custom evaluators, eval:flow, datasets) | https://genkit.dev/docs/js/evaluation/ |
| Developer UI (Datasets, Evaluations, Prompts) | https://genkit.dev/docs/js/devtools/ |
| Testing | https://genkit.dev/docs/js/testing.md |
| Flows (target de eval) | https://genkit.dev/docs/js/flows/ |
| Dotprompt (judge prompts) | https://genkit.dev/docs/js/dotprompt/ |

**MCP:** `user-genkit` → `read_genkit_docs` paths: `js/evaluation.md`, `js/devtools.md`, `js/testing.md`.

## Paquetes npm (typical)

| Paquete | Rol |
|---------|-----|
| `genkit` | `ai.defineEvaluator`, runtime |
| `genkit-cli` | `genkit eval:flow`, `eval:run`, Dev UI |
| `@genkit-ai/evaluator` | Plugin RAGAS (`genkitEval`, `GenkitMetric.*`) |
| `@genkit-ai/ai` | `ScoreSchema`, `EvalStatusEnum` |
| `@genkit-ai/tools-common` | Parser eval results → Pass % UI |

## Rutas en node_modules (verificar tras instalar)

### ScoreSchema y EvalStatusEnum

```
node_modules/genkit/node_modules/@genkit-ai/ai/lib/evaluator.js
# o
node_modules/@genkit-ai/ai/lib/evaluator.js
```

Buscar: `ScoreSchema`, `EvalStatusEnumSchema`, campos `score`, `status`, `details`.

**Hallazgo clave:** `ScoreSchema` incluye `status` opcional; **no** incluye `pass`.

### Pass % en Dev UI

```
node_modules/@genkit-ai/tools-common/lib/cjs/src/eval/parser.js
```

Buscar: `statusDistribution`, `countBy(items, 'status')`.

La UI agrega Pass % desde `status` (`PASS` / `FAIL` / `UNKNOWN`), no desde `details`.

### Plugin evaluator (patrón status)

```
node_modules/@genkit-ai/evaluator/lib/index.js        # fillScores
node_modules/@genkit-ai/evaluator/lib/metrics/*.js    # faithfulness, maliciousness, …
```

Ejemplo faithfulness: `status: score > 0.5 ? PASS : FAIL`.

## API reference

- GenerateRequest / schemas: https://js.api.genkit.dev/

## Azure OpenAI (LLM-as-judge)

| Tema | URL |
|------|-----|
| Reasoning models — API support, temperature **not supported** | https://learn.microsoft.com/azure/foundry/openai/how-to/reasoning#api--feature-support |
| GPT-5.6 models (incl. terra) | https://learn.microsoft.com/azure/foundry/foundry-models/concepts/models-sold-directly-by-azure#gpt-56 |
| reasoning_effort / verbosity | https://learn.microsoft.com/azure/foundry/openai/how-to/reasoning#reasoning-effort |

## Comandos CLI (doc evaluation.md)

```bash
genkit eval:flow <flow> --input dataset.json --evaluators=a,b -- <start command>
genkit eval:run extracted.json
genkit eval:extractData <flow> --label <label> --output out.json
genkit flow:batchRun <flow> dataset.json --label <label> -- <start command>
```

Formato dataset inferencia:

```json
[{ "input": {}, "reference": {} }]
```

Formato raw (post-extract):

```json
[{ "testCaseId": "", "input": {}, "output": {}, "context": [], "traceIds": [] }]
```
