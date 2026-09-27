---
name: genkit-frameworks-js
description: Builds or audits JavaScript and TypeScript Genkit integrations with frontend and backend frameworks. Use for Angular, Astro, Flutter, Next.js, Nuxt, React, Remix, SvelteKit, TanStack Start, Express, Fastify, Hono, or NestJS.
---

# Genkit Frameworks Js

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
`js/app-frameworks/angular.md`, `js/app-frameworks/astro.md`, `js/app-frameworks/flutter.md`, `js/app-frameworks/nextjs.md`, `js/app-frameworks/nuxt.md`, `js/app-frameworks/overview.md`, `js/app-frameworks/react.md`, `js/app-frameworks/remix.md`, `js/app-frameworks/sveltekit.md`, `js/app-frameworks/tanstack-start.md`, `js/backend-frameworks/express.md`, `js/backend-frameworks/fastify.md`, `js/backend-frameworks/hono.md`, `js/backend-frameworks/nestjs.md`, and `js/backend-frameworks/overview.md`.

Audit runtime boundaries, server-only imports, streaming protocol, request auth, environment handling, and framework deployment constraints.
