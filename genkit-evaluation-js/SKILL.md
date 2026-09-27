---
name: genkit-evaluation-js
description: Builds or audits JavaScript and TypeScript Genkit tests, evaluations, metrics, datasets, and feedback loops. Use for quality measurement, testing, evaluation, or feedback.
---

# Genkit Evaluation Js

## Source of truth
Before proposing, auditing, or editing Genkit code, call `search_genkit_docs` and then `read_genkit_docs` for the relevant canonical `js/...` document. Do not infer package names, signatures, configuration keys, or provider support from memory.

## Audit workflow
1. Identify the runtime, Genkit version, plugin versions, environment variables, and execution entry point.
2. Compare each implementation detail against the documents listed below.
3. Report findings by severity with evidence, affected code, fix, and a verification command or test.
4. Do not report style preferences as defects.

## Build workflow
1. Choose the smallest documented primitive that satisfies the requirement.
2. Define schemas and error behavior before the implementation.
3. Use the current documented import and initialization pattern.
4. Add an executable verification path using the Dev UI, a test, evaluation, or trace as applicable.

## Covered official documents
`js/testing.md`, `js/evaluation.md`, and `js/feedback.md`.

Require reproducible datasets, explicit metrics and thresholds, representative cases, regression coverage, and feedback privacy controls.
