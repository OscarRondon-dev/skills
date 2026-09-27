---
name: project-plan-master
description: Crea la plantilla maestra plan-[slug].md reutilizable para planificar cualquier feature con TDD. Consume BOOTSTRAP_BRIEF del harness-architect. Ejecutar tras project-harness-init. Usar con @project-plan-master cuando falte el plan maestro del repo.
disable-model-invocation: true
---

# Project Plan Master

Crea **una vez por repo** el archivo `plan-[slug].md` — plantilla maestra para planificar features (sustituir `[FEATURE_ID]` en cada sesión). **No** genera un plan por feature; eso es uso futuro de `@plan-[slug].md`.

## Entrada

- **`BOOTSTRAP_BRIEF`** (YAML) — mismo contrato que `project-harness-init`.
- Campo clave: `plan_template` (`filename`, `include_stride`, `include_feature_first_layout`, `proportional_depth_note`).

Si no hay brief en el chat → pedir al usuario que complete `harness-architect` o pegue el YAML.

## Salida

| Archivo | Condición |
|---------|-----------|
| `plan-{project_slug}.md` | Siempre (salvo que ya exista → preguntar sobrescribir) |

Nombre exacto: `plan_template.filename` del brief, o por defecto `plan-{project_slug}.md`.

## Flujo

```
Task Progress:
- [ ] Fase 0 — Validar brief y conflictos
- [ ] Fase 1 — Generar plan maestro desde plantilla
- [ ] Fase 2 — Verificar enlaces con el arnés
- [ ] Fase 3 — Confirmar al usuario
```

### Fase 0 — Validar

1. Parsear `BOOTSTRAP_BRIEF`.
2. Si existe `plan-{slug}.md` → parar; preguntar sobrescribir / cancelar.
3. Leer [templates/plan-master.md.template](templates/plan-master.md.template) y [reference.md](reference.md).

### Fase 1 — Generar

Sustituir placeholders del brief en la plantilla:

| Placeholder | Origen brief |
|-------------|--------------|
| `{{project_name}}`, `{{project_slug}}` | raíz |
| `{{objective}}`, `{{mvp_summary}}` | raíz |
| `{{verify_command}}` | `stack.verify_command` |
| `{{stack_*}}` | `stack` |
| `{{architecture_*}}` | `architecture` |
| `{{domain_concepts_table}}` | `domain.concepts` |
| `{{security_invariants_list}}` | `domain.security_invariants` |
| `{{reference_docs_list}}` | `agent_docs.reference_docs` |
| `{{dependency_rules_block}}` | `architecture.dependency_rules` |
| `{{frontend_conventions_block}}` | Generar desde `stack.frontend` — ver reference.md |
| `{{backend_conventions_block}}` | Generar desde `stack.backend` |
| `{{persistence_conventions_block}}` | Generar desde `stack.database` |
| `{{frontend_test_conventions}}` | Genérico + `testing_policy` |
| `{{e2e_notes_block}}` | Genérico |
| `{{local_dev_commands_block}}` | `verify_command` + AGENTS.md |
| `{{stride_section_content}}` | `plan_template.include_stride` |
| `{{proportional_depth_paragraph}}` | `plan_template.proportional_depth_note` |
| `{{plan_filename}}` | `plan_template.filename` |

**Arquitectura:** los **layouts** (`frontend_layout`, `backend_layout`) vienen del brief. El **árbol de decisión** y la **tabla de capas** son doctrina Feature-First genérica en la plantilla. Si `include_feature_first_layout: false`, omitir árbol de decisión, tabla de capas y ejemplos legacy al generar.

**Convenciones de stack:** rellenar `{{*_conventions_block}}` desde el brief — ver [reference.md](reference.md). No hardcodear rutas de un repo concreto.

- `include_stride: true` → incluir §2 STRIDE completo.
- `include_stride: false` → §2 acortado: «STRIDE proporcional; profundizar solo si la feature toca auth, datos sensibles o fronteras de confianza».
- `include_feature_first_layout: true` → incluir layouts y reglas de dependencia del brief.
- `include_feature_first_layout: false` → § arquitectura con `architecture.pattern` y layouts del brief sin árbol Feature-First extendido.
- `proportional_depth_note: true` → incluir párrafo de profundidad proporcional al `acceptance`.

Idioma del archivo = `doc_language` del brief.

### Fase 2 — Enlaces con el arnés

El plan generado debe referenciar (ajustar si no existen aún):

- `feature_list.json`, `progress/current.md`, `CHECKPOINTS.md`, `AGENTS.md`
- `BACKEND.md`, `DATABASE.md` si el stack aplica
- Patrón `progress/{dominio}-frontend.md` y `-backend.md`
- Índices `progress/feature-frontend.md`, `progress/feature-backend.md`

### Fase 3 — Confirmar

Mensaje al usuario:

> Plantilla maestra creada: `plan-{slug}.md`. Para planificar una feature: `@plan-{slug}.md` + ID numérico. Modo planificación: no implementar hasta aprobación.

No implementar features.

## Qué NO hacer

- No generar el plan TDD de una feature concreta (solo la plantilla maestra).
- No modificar `feature_list.json` ni mocks del bootstrap.
- No copiar literal un `plan-academy.md` de otro repo — parametrizar desde el brief.

## Recursos

- Plantilla: [templates/plan-master.md.template](templates/plan-master.md.template)
- Placeholders y secciones condicionales: [reference.md](reference.md)
