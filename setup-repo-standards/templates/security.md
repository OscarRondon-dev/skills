# Seguridad

Principios de seguridad de este repositorio. Agnóstico de vendor. **No sustituye** guías oficiales de un producto concreto — para profundizar → `/extend-domain-standards`.

## Principios

1. **Secrets** — fuera del repo y del código; ver [local-config.md](local-config.md)
2. **Auth** — verificada en el boundary del servidor; fail closed
3. **Input** — validado antes de lógica de negocio
4. **Errores** — al cliente: sin stack traces, rutas internas, detalle de infraestructura ni datos sensibles. Eso **no impide** mensajes de negocio concretos y accionables para operadores (ver frontera de errores en `clean-code.md`). El `code` de negocio y el `traceId` (id opaco de correlación, solo en fallos técnicos) **son seguros** de exponer. Detalle técnico solo server-side / logs
5. **Logs** — sin tokens, passwords ni PII completa
6. **Dependencias** — advisories high/critical no ignoradas sin justificación

## Trampas MVP (agentes)

Los agentes priorizan “que funcione”. Estas prácticas están **prohibidas por defecto**:

| Trampa | Por qué | Alternativa |
|--------|---------|-------------|
| Token/JWT en `localStorage` / `sessionStorage` | XSS roba sesión | Cookie HttpOnly + Secure + SameSite, o SDK auth oficial |
| API keys en frontend / `environment` commiteado | Expuestas en bundle | Solo backend; env vars en runtime server |
| `innerHTML` / bypass sanitizer | XSS | Binding seguro del framework; sanitizar |
| CSP/headers desactivados “temporalmente” | Superficie de ataque | Configurar en infra; ticket + rollback si hay excepción |
| Auth/autorización solo en UI | Bypass trivial | Guard en servidor + verificación en API |

Rule Cursor: `client-security.mdc` (frontend). Excepciones legacy → documentar y plan de migración (boy scout).

## Boundary

| Capa | Rule / doc |
|------|------------|
| Servidor (handlers) | [runtime.md](runtime.md), `security-boundary.mdc` |
| Cliente (SPA) | `client-security.mdc` |

## Headers HTTP

CSP, HSTS, X-Frame-Options, Referrer-Policy — en infra/proxy/CDN del deploy. No debilitar desde código de aplicación. Detalle por producto → `extend-domain-standards`.

## Cinturón (si aplica)

| Script | Rol |
|--------|-----|
| `verify:security` | Audit de dependencias + secret scan (belt separado; ver [tooling.md](tooling.md)) |

## Profundizar

OAuth, CSP por framework, inyección BD, auth cloud → invocación explícita de `extend-domain-standards`.
