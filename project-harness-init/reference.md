# BOOTSTRAP_BRIEF — referencia para project-harness-init

Contrato compartido con el subagente `harness-architect`. Versión actual: `1`.

## Campos obligatorios

| Campo | Uso en el arnés |
|-------|-----------------|
| `project_type` | Qué plantillas crear (frontend/backend/docs) |
| `project_slug` | `feature_list.json` → `project`; nombres de archivo |
| `project_name` | Títulos en docs |
| `doc_language` | Idioma de archivos generados |
| `objective` | Contexto en `feature_list.json` → `description` |
| `mvp_summary` | Parte del `description` del JSON |
| `stack.verify_command` | CHECKPOINTS, AGENTS, acceptance |
| `stack.*` | BACKEND, DATABASE, docs de dominio |
| `architecture.*` | Layout en AGENTS, BACKEND, docs dominio |
| `agent_docs.*` | CLAUDE.md; tabla `reference_docs` en AGENTS |
| `mock_features` | **`features[]` completo** en `feature_list.json` |

## Campos opcionales

| Campo | Uso |
|-------|-----|
| `audience`, `constraints` | AGENTS.md |
| `domain.concepts`, `domain.security_invariants` | AGENTS, CHECKPOINTS, docs dominio |
| `rules.postponed_meaning` | Solo si se define en entrevista |
| `progress_domains` | Stubs `progress/{slug}-frontend.md` y `-backend.md` |
| `architecture.initial_domains` | Opcional; dominios acordados en Fase 3 (referencia para AGENTS y progress) |
| `plan_template` | Solo `project-plan-master` |

### `progress_domains` (opcional)

```yaml
progress_domains:
  - slug: ai-tutor
    title: AI Tutor
  - slug: auth
    title: Autenticación
```

- `slug`: kebab-case, sin sufijos `-frontend`/`-backend`.
- Si **falta** en el brief: derivar **un** dominio del primer `mock_features` con `id >= 2` → `slug` = prefijo del `name` antes del primer `_` (ej. `user_auth_mvp` → `user`), `title` = `title` de esa feature.

## `feature_list.json` — reglas de generación

**No** usar el array de ejemplo de la plantilla como destino final. **Sustituir** por `mock_features` del brief.

### Raíz del JSON

```json
{
  "project": "<project_slug>",
  "description": "<resumen legible: stack + estado mock>",
  "rules": { ... },
  "features": [ ...mock_features del brief... ]
}
```

**`description` (campo raíz):** una línea estilo:
`{{stack resumido}}. Pendiente mock: #2 {{title}}, #3 {{title}}.`

No copiar el formato largo de repos maduros con cientos de IDs.

### `rules`

Siempre:

```json
{
  "one_feature_at_a_time": true,
  "require_verify_to_close": true,
  "valid_status": ["pending", "in_progress", "done", "blocked", "aborted", "postponed"]
}
```

Añadir `postponed_meaning` **solo** si viene en el brief.

### Cada feature en `features[]`

| Campo | Obligatorio | Notas |
|-------|-------------|-------|
| `id` | sí | número |
| `name` | sí | snake_case |
| `title` | sí | |
| `description` | sí | |
| `acceptance` | sí | array de strings; incluir verify donde aplique |
| `status` | sí | |
| `depends_on` | no | array de ids; **omitir clave** si no hay dependencias |
| `notes` | no | solo features `postponed` con motivo |

## Archivos `progress/` — convención de nombres

| Patrón | Ejemplo | Rol |
|--------|---------|-----|
| `progress/{dominio}-backend.md` | `ai-tutor-backend.md` | Arquitectura backend del dominio |
| `progress/{dominio}-frontend.md` | `ai-tutor-frontend.md` | Arquitectura frontend del dominio |
| `progress/feature-backend.md` | — | Índice rolling de cambios backend por feature |
| `progress/feature-frontend.md` | — | Índice rolling de cambios frontend por feature |
| `progress/current.md` | — | Sesión activa |
| `progress/history.md` | — | Bitácora append-only |

## Docs raíz (referencia técnica)

| Archivo | Cuándo crear en bootstrap |
|---------|---------------------------|
| `BACKEND.md` | `project_type` tiene backend (`web_fullstack`, `api_only`, …) y `stack.backend` no es null |
| `DATABASE.md` | Hay persistencia (`stack.database` no null) |

## `progress/current.md` — lógica mock

- Sin feature `in_progress` → «Sin tarea activa».
- «Siguiente sugerida»: primera `pending` con `depends_on` satisfechos.

## Detección del brief en el chat

Buscar bloque `# BOOTSTRAP_BRIEF` o YAML con `version: 1`. Parsear como YAML.
