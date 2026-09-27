# Trampas conocidas (evals Genkit)

Basado en doc oficial + inspección de `@genkit-ai/tools-common` y `@genkit-ai/ai` + QA real.

## 1. Pass 0% con score 1.00 en pills

**Síntoma:** Barra superior `Pass 0%`; fila muestra `1.00` en métrica.

**Causa:** Falta `evaluation.status`. La UI cuenta `PASS`/`FAIL` vía `statusDistribution`, no via `score`.

**Fix:** Añadir `status: EvalStatusEnum.PASS | FAIL` al mismo nivel que `score`.

**No funciona:** `pass: true`, `details.valid: true`.

---

## 2. Evaluators tab con dataset vacío

**Síntoma:** Input `{ "dataset": [], "evalRunId": "" }`.

**Causa:** Modo **raw eval** (`eval:run`), no inferencia.

**Fix:** Usar **Datasets → Run new evaluation** (flow + dataset + métricas).

---

## 3. `.genkit/datasets/` como backup

**Síntoma:** Compañero clona repo sin datasets.

**Causa:** `.genkit/` gitignored; solo local.

**Fix:** Golden JSON en `eval/datasets/*.json` + manifest.

---

## 4. temperature 0.2 en Azure GPT-5

**Síntoma:** `400 Unsupported value: 'temperature' does not support 0.2... Only the default (1)`.

**Causa:** Modelos **reasoning** (gpt-5-mini, gpt-5.6-terra, gpt-5.2, …) no soportan `temperature` custom.

**Fix:** `temperature: 1` (u omitir). Estabilidad vía `reasoning_effort: 'none'|'low'`.

**Mito:** "terra acepta 0.2 pero mini no" — **ambos** son reasoning GPT-5 en Azure doc.

---

## 5. Alias deployment fuera del catálogo plugin

**Síntoma:** Modelo custom (ej. `gpt-5.6-terra`) no aparece como `azure-openai/gpt-5.6-terra` en Genkit.

**Patrón:** Host catalog (`gpt-5.2`) + `config.version` = nombre deployment Azure.

Verificar helper del repo o implementar `resolveNamedChatModelCall`.

---

## 6. Prompts UI "vacíos"

**Síntoma:** Input `{}` sin ayuda de campos.

**Causa:** Dotprompt `input.schema` con `any`.

**Nota:** Normal. El prompt **sí** incluye system instructions; la UI solo muestra variables. Ver **Traces** tras Run.

---

## 7. Flow vs Prompt confusión

| Usar | Para |
|------|------|
| Flow eval | Producción, tools, flags, datasets oficiales |
| Prompt run | Iterar texto system/user del `.prompt` |

Mismo Dotprompt puede ejecutarse por ambos caminos.

---

## 8. genkitEval plugin no cargado

**Síntoma:** "You have no evaluation metrics" para métricas RAGAS; solo custom si registrados.

**Causa:** `@genkit-ai/evaluator` instalado pero `genkitEval({...})` no en `genkit({ plugins })`.

**Fix:** Plugin en composición AI **o** solo evaluators custom `defineEvaluator`.

---

## 9. Context en Run evaluation

**Síntoma:** Usuario pregunta qué poner en Context.

**Regla:** Solo metadatos runtime (auth, tenant). Datos de negocio van en `input` del dataset.

---

## 10. Mismatch genkit vs genkit-cli

**Síntoma:** Comportamiento UI distinto a docs.

**Check:** Alinear versiones `genkit` y `genkit-cli` en package.json; inspeccionar `@genkit-ai/ai` anidado bajo `genkit`.
