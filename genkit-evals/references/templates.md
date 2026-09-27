# Plantillas Genkit eval (copiar/adaptar)

Reemplazar `<box>`, `<Box>`, `myFlow`, paths según el repo.

## eval-status.ts

```typescript
import { EvalStatusEnum, type BaseEvalDataPoint } from "genkit/evaluator";

export type PassRule =
  | { kind: "exact"; value: number }
  | { kind: "gt"; value: number }
  | { kind: "gte"; value: number };

export function evalStatusFromScore(score: number, rule: PassRule): EvalStatusEnum {
  switch (rule.kind) {
    case "exact":
      return score === rule.value ? EvalStatusEnum.PASS : EvalStatusEnum.FAIL;
    case "gt":
      return score > rule.value ? EvalStatusEnum.PASS : EvalStatusEnum.FAIL;
    case "gte":
      return score >= rule.value ? EvalStatusEnum.PASS : EvalStatusEnum.FAIL;
  }
}

export function toEvaluation(
  datapoint: BaseEvalDataPoint,
  scored: { score: number; details: Record<string, unknown> },
  passRule: PassRule
) {
  return {
    testCaseId: datapoint.testCaseId,
    evaluation: {
      score: scored.score,
      status: evalStatusFromScore(scored.score, passRule),
      details: scored.details,
    },
  };
}
```

## evaluators.ts (heurístico)

```typescript
import type { BaseEvalDataPoint } from "genkit/evaluator";
import { ai } from "<path-to-genkit-instance>";
import { toEvaluation } from "./<box>.eval-status.js";
import { scoreOutputValid } from "./<box>.eval.pure.js";

export const outputValidEvaluator = ai.defineEvaluator(
  {
    name: "<box>/outputValid",
    displayName: "<Box> — output válido",
    definition: "Descripción breve para Dev UI.",
  },
  async (datapoint: BaseEvalDataPoint) =>
    toEvaluation(
      datapoint,
      scoreOutputValid(datapoint.output),
      { kind: "exact", value: 1 }
    )
);
```

## eval-judge.ts (LLM-as-judge)

```typescript
import type { BaseEvalDataPoint } from "genkit/evaluator";
import { ai } from "<path-to-genkit-instance>";
// import { resolveNamedChatModelCall } from "<provider-seam>"; // si Azure alias

const JUDGE_CONFIG = {
  temperature: 1, // GPT-5 reasoning Azure: solo 1
  custom: { max_completion_tokens: 1024 },
};

export const faithfulnessEvaluator = ai.defineEvaluator(
  {
    name: "<box>/faithfulness",
    displayName: "<Box> — fidelidad (LLM judge)",
    definition: "Juez LLM: output fiel al input/context.",
  },
  async (datapoint: BaseEvalDataPoint) => {
    // 1. Validar output / input
    // 2. const result = await ai.prompt("<box>/eval-judge")({ tabPack, flowOutput }, {
    //      model: call.model,
    //      config: { ...(call.version ? { version: call.version } : {}), ...JUDGE_CONFIG },
    //    });
    // 3. return toEvaluation(datapoint, parseJudge(result.output), { kind: "gte", value: 0.8 });
  }
);
```

## register.ts

```typescript
import "./<box>.evaluators.js";
import "./<box>.eval-judge.js"; // opcional
```

## Entry Genkit (main / genkit.config side-effect)

```typescript
import "./ai/<box>/<box>.flow.js";
import "./ai/<box>/eval/register.js"; // después de flows
```

## datasets/smoke.json

```json
[
  {
    "input": {
      "fieldA": "value",
      "fieldB": []
    }
  }
]
```

## datasets.manifest.ts

```typescript
export const EVAL_DATASETS = {
  smoke: {
    datasetId: "<box>/smoke",
    targetFlow: "myFlow",
    file: "src/ai/<box>/eval/datasets/smoke.json",
    evaluators: "<box>/outputValid,<box>/faithfulness",
  },
} as const;
```

## package.json script

```json
"eval:<box>:smoke": "genkit eval:flow myFlow --input src/ai/<box>/eval/datasets/smoke.json --evaluators=<box>/outputValid -- tsx src/genkit.config.ts"
```

## eval-judge.prompt (Dotprompt)

```yaml
---
config:
  temperature: 1
  custom:
    max_completion_tokens: 1024
input:
  schema:
    context: any
    flowOutput: any
output:
  schema: <Box>EvalJudgeOutputSchema
---

{{role "system"}}
Eres un auditor. Evalúa fidelidad del flowOutput al context. Escala 1-10 en score.

{{role "user"}}
CONTEXTO:
{{json context}}

OUTPUT:
{{json flowOutput}}
```

Registrar schema en instancia Genkit: `ai.defineSchema("<Box>EvalJudgeOutputSchema", ...)`.

## Vitest (pure, sin LLM)

```typescript
import { describe, expect, it } from "vitest";
import { evalStatusFromScore } from "./<box>.eval-status.js";
import { EvalStatusEnum } from "genkit/evaluator";

it("status PASS cuando score cumple umbral", () => {
  expect(evalStatusFromScore(1, { kind: "exact", value: 1 })).toBe(EvalStatusEnum.PASS);
});
```
