---
name: setup-project-memory
description: Bootstraps Obsidian project memory in DEV-OBSIDIAN by creating a project-memory/ sandbox folder with minimal templates and optional repo rule. Explores vault and repo, asks section-by-section, shows draft before writing. Use for /setup-project-memory or when the user wants agent memory for a project without touching human PKM notes.
disable-model-invocation: true
---

# Setup Project Memory

Prompt-driven bootstrap (como `setup-repo-standards`). **Explorar → opciones + recomendación → confirmar → borrador → escribir.** No es un script ciego.

## Objetivo

Crear la sandbox `project-memory/` dentro de un tema del vault. **No tocar** el resto del tema (Pendientes, Resources, varios…).

| Capa | Artefacto |
|------|-----------|
| Sandbox | `DEV-OBSIDIAN/<Tema>/project-memory/` |
| Tablero | `project-memory/activeContext.md` (~80 líneas max) |
| Navegación | `project-memory/project-memory — index.md` |
| Apunte | `project-memory/README.md` (no confundir con repo `.agent/`) |
| Runtime | `project-memory-resume` + `project-memory-wrap-up` |
| Repo (opcional) | `.cursor/rules/project-memory.mdc` |

**No hace:** mirror docs, ingest masiva, reorganizar PKM humano, escribir en `.obsidian/` ni en repo `.agent/`.

## Vault fijo

```text
C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>\project-memory\
```

**No confundir:**

| Ruta | Qué es |
|------|--------|
| `DEV-OBSIDIAN/<Tema>/project-memory/` | Memoria Obsidian del agente |
| `<repo>/.agent/` | Config Cursor del repo — **no escribir memoria aquí** |
| Resto de `<Tema>/` | PKM humano — **no tocar** en setup |

## Jerarquía de verdad (README + index)

| Prioridad | Fuente |
|-----------|--------|
| 1 | Repo (código, package.json, CI) |
| 2 | MCP / docs oficiales |
| 3 | `project-memory/activeContext.md` (hipótesis) |
| 4 | `10 Decisions/`, `30 Gotchas/` |
| 5 | `20 Sessions/` (evidencia, no mandato) |

No documentar rutas de archivos ni versiones en `project-memory/`.

## Process

```
Task Progress:
- [ ] 1. Explore (vault + repo)
- [ ] 2. Present findings
- [ ] 3. Decide sections (one at a time)
- [ ] 4. Draft
- [ ] 5. Write
- [ ] 6. Done
```

### 1. Explore (read-only)

**Vault** — `target_directory: C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>`

| Señal | Qué buscar |
|-------|------------|
| Carpeta tema | ¿Existe? |
| `project-memory/` | ¿Ya existe? ¿Parcial? |
| PKM humano | Pendientes, Resources, varios — **solo inventariar, no tocar** |
| Index humano | `<Tema> — index.md` |

**Repo** (si hay workspace):

| Señal | Qué buscar |
|-------|------------|
| `.agent/` | Existe — recordar en README anti-confusión |
| `.cursor/rules/project-memory.mdc` | ¿Ya existe? |

**No asumir** el tema: inferir del repo y **confirmar** en Section A.

### 2. Present findings

Tabla corta. Orden: **A → B → C → D → E → Draft → Write**

### 3. Decide (una pregunta por mensaje)

**Section A — Tema**

| Opción | Acción |
|--------|--------|
| Tema existente | `DEV-OBSIDIAN/<Tema>/` |
| Tema nuevo | Crear carpeta tema + `project-memory/` |
| Match repo | ej. `komtexia` → `Komtexia/` |

**Section B — Sandbox**

| Opción | Acción |
|--------|--------|
| **Crear `project-memory/`** | Layout estándar (recomendado) |
| Ya existe parcial | Completar faltantes; no borrar |

Layout estándar:

```text
<Tema>/project-memory/
├── README.md
├── project-memory — index.md
├── activeContext.md
├── 10 Decisions/
├── 20 Sessions/
└── 30 Gotchas/
```

**Section C — Semilla activeContext**

| Opción | Qué |
|--------|-----|
| Plantilla vacía | Solo placeholders |
| **+ objetivo actual** | Preguntar objetivo y próximo paso |

**Section D — Enlace en index humano**

| Opción | Acción |
|--------|--------|
| Omitir | No tocar `<Tema> — index.md` |
| **1 línea** | `## Memoria agente` → `[[project-memory/project-memory — index]]` |

**Recomendar omitir** si el usuario quiere probar solo la sandbox.

**Section E — Rule en repo**

| Opción | Acción |
|--------|--------|
| Omitir | Solo vault |
| **Rule** | `.cursor/rules/project-memory.mdc` desde [templates/project-memory.mdc](templates/project-memory.mdc) |

### 4. Draft

Mostrar **sin escribir**:

1. Árbol `project-memory/`
2. Contenido README, index, activeContext
3. Cambio en index humano (si D ≠ omitir)
4. Rule (si E ≠ omitir)
5. **No haré:** tocar Pendientes, varios, Resources, `.agent/`, `.obsidian/`

Esperar OK explícito.

### 5. Write

Solo tras OK. Plantillas: [templates/](templates/).

- Crear **solo** dentro de `project-memory/`
- Index humano: **solo** la línea acordada en D
- Rule en repo si E acordó

### 6. Done

Informar rutas y próximos pasos:

1. `/project-memory-resume` al iniciar
2. `/project-memory-wrap-up` al cerrar

## Anti-patrones

- Escribir sin borrador aprobado
- Tocar notas humanas fuera de `project-memory/`
- Escribir memoria en repo `.agent/`
- Reorganizar el vault del tema
- Auto-ejecutar resume/wrap-up

## Templates

- [README.md](templates/README.md)
- [project-memory-index.md](templates/project-memory-index.md)
- [activeContext.md](templates/activeContext.md)
- [session-handoff.md](templates/session-handoff.md)
- [decision.md](templates/decision.md)
- [gotcha.md](templates/gotcha.md)
- [project-memory.mdc](templates/project-memory.mdc)
