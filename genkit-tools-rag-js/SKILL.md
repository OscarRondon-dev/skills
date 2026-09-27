---
name: genkit-tools-rag-js
description: Builds or audits JavaScript and TypeScript Genkit tools, tool calling, RAG, retrievers, embeddings, and vector-store integrations. Use for function tools, retrieval, documents, embeddings, or vector search.
---

# Genkit Tools Rag Js

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
`js/tool-calling.md`, `js/rag.md`, `js/integrations/astra-db.md`, `js/integrations/chroma.md`, `js/integrations/cloud-firestore.md`, `js/integrations/cloud-sql-postgresql.md`, `js/integrations/dev-local-vectorstore.md`, `js/integrations/lancedb.md`, `js/integrations/neo4j.md`, `js/integrations/pgvector.md`, `js/integrations/pinecone.md`, `js/integrations/vectorsearch-bigquery.md`, and `js/integrations/vectorsearch-firestore.md`.

Review tool schemas and authorization, retrieval quality, document metadata, embedding compatibility, indexes, latency, and grounding behavior.
