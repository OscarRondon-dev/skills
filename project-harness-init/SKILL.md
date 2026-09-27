---
name: project-harness-init
description: Bootstrap del arnés de trabajo en repos nuevos. Orquesta harness-architect si falta BOOTSTRAP_BRIEF; crea feature_list.json, progress/, CHECKPOINTS.md y AGENTS.md desde el brief. Punto de entrada para iniciar un proyecto con memoria en disco. Usar con @project-harness-init al empezar un repo sin arnés.
disable-model-invocation: true
---

# Project Harness Init

Skill de **punto de entrada** para montar el arnés de trabajo. Consume un `BOOTSTRAP_BRIEF` (YAML) producido por el subagente `harness-architect`.

## Qué crea

| Archivo | Rol |
|---------|-----|
| `feature_list.json` | Backlog completo desde `mock_features` del brief (no el array vacío de la plantilla) |
| `progress/current.md` | Sesión activa con estado mock |
| `progress/history.md` | Bitácora con entrada de bootstrap |
| `progress/feature-backend.md` | Índice rolling backend (stub) |
| `progress/feature-frontend.md` | Índice rolling frontend (stub) |
| `progress/{dominio}-backend.md` | Un stub por entrada en `progress_domains` (o uno derivado del mock) |
| `progress/{dominio}-frontend.md` | Par del anterior |
| `BACKEND.md` | Spec backend (stub) si hay backend en el stack |
| `DATABASE.md` | Spec datos (stub) si hay persistencia |
| `CHECKPOINTS.md` | Criterios mínimos de cierre |
| `AGENTS.md` | Mapa para agentes |
| `CLAUDE.md` | Solo si `agent_docs.generate_claude_md: true` |

**No crea** `plan-[slug].md` — eso es `project-plan-master` (siguiente paso).

## Flujo (seguir en orden)

```
Task Progress:
- [ ] Fase 0 — Obtener BOOTSTRAP_BRIEF
- [ ] Fase 1 — Comprobar conflictos
- [ ] Fase 2 — Generar archivos
- [ ] Fase 3 — Actualizar memoria post-bootstrap
- [ ] Fase 4 — Handoff a project-plan-master
```

### Fase 0 — Obtener BOOTSTRAP_BRIEF

**Si ya hay un bloque `# BOOTSTRAP_BRIEF` en el chat** → parsearlo y continuar.

**Si no hay brief** → lanzar el subagente `harness-architect` (Task tool o invocación explícita). Esperar el YAML confirmado por el usuario. No crear archivos hasta tenerlo.

Esquema completo del brief: [reference.md](reference.md).

### Fase 1 — Comprobar conflictos

Antes de escribir, comprobar si existen:

- `feature_list.json`
- `progress/current.md`
- `AGENTS.md`

Si **alguno existe** → parar y preguntar al usuario: sobrescribir / fusionar / cancelar. No sobrescribir sin confirmación explícita.

### Fase 2 — Generar archivos

Leer las plantillas en `templates/` y generar contenido **sustituyendo** valores del brief. No copiar plantillas sin adaptar.

| Plantilla | Destino |
|-----------|---------|
| `templates/feature_list.json.template` | Estructura de referencia → `feature_list.json` |
| `templates/current.md.template` | `progress/current.md` |
| `templates/history.md.template` | `progress/history.md` |
| `templates/progress-features-index-backend.md.template` | `progress/feature-backend.md` |
| `templates/progress-features-index-frontend.md.template` | `progress/feature-frontend.md` |
| `templates/progress-domain-backend.md.template` | `progress/{slug}-backend.md` (por dominio) |
| `templates/progress-domain-frontend.md.template` | `progress/{slug}-frontend.md` (por dominio) |
| `templates/BACKEND.md.template` | `BACKEND.md` (si aplica) |
| `templates/DATABASE.md.template` | `DATABASE.md` (si aplica) |
| `templates/CHECKPOINTS.md.template` | `CHECKPOINTS.md` |
| `templates/AGENTS.md.template` | `AGENTS.md` |
| `templates/CLAUDE.md.template` | `CLAUDE.md` (solo si aplica) |

**Reglas al generar:**

1. **`feature_list.json`** — `features` = **todos** los `mock_features` del brief. Campo raíz `description` = resumen corto (stack + ids mock pendientes). `rules` estándar; `postponed_meaning` solo si está en el brief. Omitir `depends_on` en features sin dependencias. Ver [reference.md](reference.md).
2. **`progress/current.md`** — sin `in_progress`; sugerir siguiente feature mock con deps satisfechas.
3. **`progress/history.md`** — entrada de bootstrap con fecha de hoy.
4. **Docs de dominio** — por cada `progress_domains[]` del brief; si falta, derivar un dominio del primer mock con `id >= 2` (ver reference.md). Crear par `{slug}-frontend.md` + `{slug}-backend.md`.
5. **`progress/feature-*.md`** — índices rolling con enlace al dominio mock creado.
6. **`BACKEND.md` / `DATABASE.md`** — stubs si el stack aplica; no crear si `api_only` sin DB o `frontend_only`.
7. **`CHECKPOINTS.md`** — `verify_command` + `testing_policy`; omitir secciones irrelevantes al `project_type`.
8. **`AGENTS.md`** — stack, layouts, jerarquía con `BACKEND.md`, `DATABASE.md` y patrón `progress/{dominio}-*.md`.
9. Idioma = `doc_language` del brief.
10. Tras crear archivos: marcar `harness_bootstrap` `done` si los acceptance del harness se cumplen.

### Fase 3 — Actualizar memoria post-bootstrap

Si el bootstrap se completó con éxito:

1. En `feature_list.json`, cambiar `harness_bootstrap` a `status: done` (si todos los acceptance del harness se cumplen con los archivos creados).
2. Añadir en `progress/history.md` línea: bootstrap del arnés completado vía `project-harness-init`.
3. Dejar `progress/current.md` en «sin tarea activa» o con siguiente feature sugerida.

### Fase 4 — Handoff

Al terminar, decir al usuario:

> Arnés listo. Siguiente paso: invocar **`project-plan-master`** con el mismo `BOOTSTRAP_BRIEF` para crear `plan-[slug].md`. Si el brief sigue en el chat, continúa automáticamente leyendo esa skill.

No implementar features de producto todavía.

## Adaptación por `project_type`

| Tipo | Ajustes |
|------|---------|
| `web_fullstack` | AGENTS y CHECKPOINTS con frontend + backend |
| `api_only` | Sin secciones UI en CHECKPOINTS/AGENTS |
| `frontend_only` | Sin sección backend |
| `mobile` | Sustituir «frontend web» por stack mobile |
| `cli_library` | Comandos de build/test del runtime; sin hosting web |
| `other` | Seguir `constraints` y stack del brief |

## Qué NO hacer

- No crear `plan-[slug].md` (otra skill)
- No implementar features del mock backlog
- No explorar el repo más allá de comprobar conflictos de archivos
- No inventar campos del brief que no existan en el YAML

## Recursos

- Esquema del brief y mapeo campos → archivos: [reference.md](reference.md)
- Plantillas: carpeta [templates/](templates/)
