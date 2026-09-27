---
name: genkit-index
description: Routes JavaScript/TypeScript and Python Genkit implementation or audit tasks to the appropriate official-documentation skill. Use first for any Genkit request.
---

# Genkit Index

## Mandatory routing
1. Identify the language: JavaScript/TypeScript or Python.
2. Identify the task mode: build or audit.
3. Load every matching domain skill below; cross-cutting work can require multiple skills.
4. Before using a time-sensitive API, package, CLI flag, provider capability, or deployment behavior, query the `user-genkit` MCP: `search_genkit_docs`, then `read_genkit_docs` using only returned paths.
5. Treat the canonical paths below as complete coverage for the MCP index captured on 2026-07-21. Ignore duplicate export paths shaped as `js/js/*` or `python/python/*`.

## Skill router
- Foundation, setup, API, context, errors: `genkit-core-js` or `genkit-core-python`.
- Flows, streaming, interruptions: `genkit-flows-js` or `genkit-flows-python`.
- Dotprompt, models, structured outputs: `genkit-prompts-js` or `genkit-prompts-python`.
- Tools, retrieval, vector stores: `genkit-tools-rag-js` or `genkit-tools-rag-python`.
- Full-stack agents: `genkit-agents-js`; agentic pattern selection: `genkit-architecture-js`.
- Tests, evaluations, feedback: `genkit-evaluation-js` or `genkit-evaluation-python`.
- Dev UI, traces, telemetry: `genkit-observability-js` or `genkit-observability-python`.
- Provider, cloud, database, or Auth0 plugin: `genkit-integrations-js` or `genkit-integrations-python`.
- App or backend framework: `genkit-frameworks-js` or `genkit-frameworks-python`.
- Production serving and hosting: `genkit-deployment-js` or `genkit-deployment-python`.
- MCP or plugin authoring: `genkit-extensibility-js` or `genkit-extensibility-python`.
- Official tutorial patterns: `genkit-tutorials-js`.

## Canonical coverage manifest
### JavaScript/TypeScript
- Core: `js/get-started.md`, `js/overview.md`, `js/api-references.md`, `js/api-stability.md`, `js/error-types.md`, `js/client.md`, `js/context.md`, `js/develop-with-ai.md`.
- Flows/prompts: `js/flows.md`, `js/durable-streaming.md`, `js/interrupts.md`, `js/dotprompt.md`, `js/models.md`.
- Architecture: `js/agentic-patterns.md`, `js/chat.md`, `js/multi-agent.md`, `js/middleware.md`.
- Agents: every document under `js/agents/`.
- Tooling/quality: `js/tool-calling.md`, `js/rag.md`, `js/testing.md`, `js/evaluation.md`, `js/feedback.md`.
- Observability: `js/devtools.md`, `js/local-observability.md`, every document under `js/observability/`.
- Frameworks: every document under `js/app-frameworks/` and `js/backend-frameworks/`.
- Deployment: every document under `js/deployment/`.
- Extensibility: `js/model-context-protocol.md`, `js/mcp-server.md`, `js/plugin-authoring/overview.md`.
- Integrations: every document under `js/integrations/` (provider documents use `genkit-integrations-js`; vector/database documents use `genkit-tools-rag-js`).
- Tutorials: every document under `js/tutorials/`.

### Python
- Core: `python/get-started.md`, `python/overview.md`, `python/api-references.md`, `python/api-stability.md`, `python/error-types.md`, `python/client.md`.
- Flows/prompts: `python/flows.md`, `python/interrupts.md`, `python/dotprompt.md`, `python/models.md`.
- Tooling/quality: `python/tool-calling.md`, `python/rag.md`, `python/evaluation.md`, `python/feedback.md`.
- Observability: `python/devtools.md`, `python/local-observability.md`, `python/observability/telemetry-collection.md`.
- Frameworks: every document under `python/app-frameworks/` and `python/backend-frameworks/`.
- Deployment: every document under `python/deployment/`.
- Extensibility: `python/middleware.md`, `python/mcp-server.md`, `python/plugin-authoring/overview.md`.
- Integrations: every document under `python/integrations/`.

If a newly listed MCP document has no obvious owner, use this index plus the closest domain skill, then add it to the relevant coverage manifest.
