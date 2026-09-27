# Runtime / backend

Convenciones acordadas en el setup para la capa de servidor de este repo. **No sustituye** la documentación oficial del runtime elegido — para profundizar en una tecnología concreta, usar `extend-domain-standards` con invocación explícita del usuario.

## Target acordado

- **Forma:** {{RUNTIME_SHAPE}} _(ej. monolito in-process, serverless, BFF, handlers transitorios, solo frontend…)_
- **Ubicación en repo:** {{RUNTIME_PATHS}} _(rutas reales, no inventadas)_
- **Estado:** {{RUNTIME_MATURITY}} _(operativo / en transición / planificado)_

## Organización (feature-first)

Dentro de cada caja de negocio: handlers/controllers, schemas, middleware local, acceso a datos del dominio, tests.

Fuera: `core` (config, logging, auth global, conexiones compartidas) + shared justificado.

```
{{RUNTIME_TREE_EXAMPLE}}
```

## Límites de responsabilidad (agnóstico)

- **Boundary del handler:** validar entrada → delegar a dominio → serializar respuesta. Sin reglas de negocio profundas en el adapter.
- **I/O async:** preferir flujo async/await (o equivalente idiomático del lenguaje) con errores manejados en el boundary — no fire-and-forget silencioso.
- **Config y secrets:** nunca en código; ver [local-config.md](local-config.md) si existe.

## Tooling local acordado

| Script / artefacto | Rol |
|--------------------|-----|
| {{RUNTIME_DEV_SCRIPT}} | Desarrollo local del runtime (si aplica) |
| {{RUNTIME_BUILD_SCRIPT}} | Compilación / empaquetado previo al deploy (si aplica) |
| {{RUNTIME_TSCONFIG}} | Typecheck / compilación del backend (si aplica) |

`verify` cubre lint + typecheck + tests unitarios acordados. **No implica** que el runtime desplegable esté listo — validación de deploy queda fuera de v1.

## Transición (si aplica)

Si el backend está **en transición** (p. ej. handlers testables hoy, runtime definitivo mañana):

1. Mantener handlers testables por feature en la ruta acordada.
2. No mezclar dos modelos de registro/ejecución del mismo runtime sin acuerdo explícito.
3. Cuando el target definitivo esté claro → invocar `extend-domain-standards` para ese runtime.

## Profundizar en el stack concreto

Este archivo captura **decisiones del equipo**. Para reglas vendor-specific (SDK, CLI, layout oficial, versión del programming model):

→ `/extend-domain-standards` + nombre exacto de la tecnología que el usuario indique.
