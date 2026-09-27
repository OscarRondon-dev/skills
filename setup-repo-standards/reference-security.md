# Security principles (language-agnostic)

Base para `docs/standards/security.md`, `security-boundary.mdc` y `client-security.mdc`. Agnóstico de vendor. Profundidad stack-specific → `extend-domain-standards`.

## Secrets

- Nunca en código, commits ni logs
- Config local gitignored; plantillas `.example` sin valores reales
- Agentes no inventan connection strings ni API keys
- API keys nunca en bundle frontend commiteado

## Authentication & authorization

- Verificar en el boundary del servidor antes de lógica de negocio
- Fail closed: permiso dudoso → denegar
- Frontend no es frontera de seguridad (UI puede ocultar, no proteger)

## Client session storage (anti-MVP)

Los agentes suelen guardar tokens en `localStorage` para cerrar MVP rápido — **XSS = robo de sesión**.

- Preferir: cookie HttpOnly + Secure + SameSite, o SDK auth oficial (MSAL, Auth0, etc.)
- Evitar por defecto: JWT/refresh en `localStorage` / `sessionStorage`
- Si legacy exige storage JS: excepción documentada; plan de migración; minimizar TTL

## XSS & DOM

- No `innerHTML` / binding HTML crudo con input de usuario
- No bypass de sanitización del framework “para que compile”
- Output encoding según contexto (HTML, URL, JS)

## HTTP security headers

- CSP, HSTS, X-Frame-Options, Referrer-Policy — en infra/proxy/CDN, no debilitar desde app
- Detalle por producto (Static Web Apps, nginx, etc.) → `extend-domain-standards`
- Regla para agentes: no proponer `unsafe-inline` / desactivar CSP como default

## Input

- Validar y acotar todo input externo (body, query, params, headers, uploads)
- Queries parametrizadas / ODM — nunca concatenar input en queries

## Errors & logging

- Cliente: sin stack traces, rutas internas, secrets ni detalle de implementación
- **Operadores de negocio:** mensajes de dominio concretos y accionables vía frontera de errores (`clean-code.md`) — distinto de filtrar `error.message` crudo de librerías
- **Correlación (segura de exponer):** `code` de negocio y `traceId` opaco (id de invocación/operación, solo en fallos técnicos) — permiten a soporte localizar el fallo sin filtrar el detalle
- Logs: sin tokens, passwords, connection strings, PII completa

## Dependencies

- Lockfiles versionados
- Advisories high/critical: no ignorar sin justificación documentada

## MVP traps (agentes)

Reglas Cursor cortas ✅/❌ > documentación larga. Cubrir en `client-security.mdc` + sección en `security.md`:

- Token en localStorage
- Secrets en frontend
- innerHTML / sanitizer bypass
- Headers/CSP relajados “temporalmente”
- Auth solo en cliente

## Pointers (en el satélite generado)

- `local-config.md` — archivos locales
- `runtime.md` — forma del handler
- `security-boundary.mdc` — servidor
- `client-security.mdc` — SPA / cliente
- `extend-domain-standards` — profundizar dominio concreto
