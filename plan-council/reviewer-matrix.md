# Matriz de revisores Tier 2 — plan-council

El orquestador convoca **siempre** los 5 revisores Tier 1 y evalúa este documento para decidir qué revisores Tier 2 activar.

## Reglas de activación

Un revisor Tier 2 se convoca si **al menos una** condición de su fila se cumple en el borrador del plan (título, pasos, archivos, stack, dependencias).

| ID | Subagente | Activar cuando el plan mencione o implique |
|----|-----------|---------------------------------------------|
| security | `plan-security` | auth, login, token, JWT, Bearer, permisos, roles, RBAC, secretos, API keys, CORS, validación de entrada, datos personales, PII, GDPR, cifrado, HTTPS, inyección, XSS, CSRF |
| data | `plan-data` | schema, modelo, migración, índice, MongoDB, Cosmos, Mongoose, SQL, base de datos, agregación, `$lookup`, `$slice`, colección, persistencia, seed, transacción |
| ux-a11y | `plan-ux-a11y` | UI, UX, formulario, modal, navegación, sidebar, botón, accesibilidad, WCAG, ARIA, contraste, foco, teclado, responsive, empty state, skeleton, diseño |
| ops | `plan-ops` | deploy, CI/CD, pipeline, Azure Functions, Static Web App, SWA, entorno, staging, producción, rollback, observabilidad, logs, Application Insights, infraestructura, Bicep, Terraform |
| angular | `plan-angular` | Angular, componente, signal, `resource()`, routing, lazy load, OnPush, reactive form, TestBed, `inject()`, template, feature module, `ng test`, frontend SPA |
| cloud-platform | `plan-cloud-platform` | Azure, Microsoft, GCP, Google Cloud, Firebase, Cosmos, Blob Storage, Key Vault, MSAL, B2C, Cloud Functions, App Service, Cloud Run, servicio cloud nombrado |
| performance | `plan-performance` | video, chat, mapa, chart, apexcharts, three.js, librería pesada, bundle, code splitting, lazy loading, LCP, FCP, carga, hidratación, `@defer`, consulta masiva, paginación, N+1, render browser |

## Heurísticas adicionales

### cloud-platform — detección de proveedor

| Señal en el plan | Proveedor | Acción del revisor |
|------------------|-----------|-------------------|
| Azure, Functions, SWA, Cosmos, MSAL, B2C | Microsoft Azure | Consultar MCP `microsoft-learn` o Azure MCP si está disponible |
| GCP, Firebase, Cloud Run, Firestore | Google Cloud | Buscar documentación oficial de Google |
| Ambos proveedores | Multi-cloud | Revisar cada servicio contra su doc oficial |
| Ninguna señal cloud | — | **No convocar** este revisor |

### performance — umbral de complejidad

Convocar si el plan incluye **cualquiera** de:

- Integración de media (video/audio streaming, reproductor)
- Componentes interactivos pesados (chat en tiempo real, mapas, gráficos)
- Nueva dependencia npm con impacto en bundle (> ~50 kB gzip estimado o librería conocida como pesada)
- Consultas sin límite o sin paginación
- Ausencia de estrategia de carga cuando hay componentes pesados

### angular — umbral

Convocar si el plan toca `src/`, componentes Angular, o frontend de una app SPA Angular. **No convocar** para planes puramente backend/CLI.

## Convocatoria mínima esperada por tipo de plan

| Tipo de plan | Tier 1 | Tier 2 típico |
|--------------|--------|---------------|
| Fix backend pequeño | 5 | data (si aplica) |
| Feature Angular UI | 5 | angular, ux-a11y, performance (si charts/media) |
| Feature full-stack Azure | 5 | angular, data, security, ops, cloud-platform, performance (según señales) |
| Refactor sin UI | 5 | architecture implícito en Tier 1; data/ops según señales |

## Formato de registro

Al convocar Tier 2, el orquestador debe listar en el informe:

```markdown
### Revisores convocados
- Tier 1 (siempre): craftsmanship, architecture, scope, risk, testability
- Tier 2 (activados): [lista con motivo breve por cada uno]
- Tier 2 (omitidos): [lista con motivo de omisión]
```
