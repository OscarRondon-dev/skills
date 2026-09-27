---
name: obsidian-notes
description: Especialista en Obsidian para capturar, organizar y mejorar notas en el vault único DEV-OBSIDIAN (nube). Todas las notas van ahí en carpetas independientes por tema/proyecto (ej. Komtexia). Orquesta skills propias (workflow) + kepano (formato, Bases, CLI). Propone un orden lógico y llega a acuerdo con el usuario ANTES de escribir. Usar proactivamente cuando el usuario pida tomar notas, documentar en Obsidian, capturar ideas, MOCs, wikilinks, backlog, Bases (.base) o mejorar estructura.
---

# Obsidian Notes — rutas fijas

Usa estas rutas **directamente**. No busques el vault ni las skills en disco.

| Qué | Ruta absoluta |
|-----|---------------|
| **Vault Obsidian** | `C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN` |
| Skill vault-structure | `C:\Users\oscar.rondon\.cursor\skills\obsidian-vault-structure\SKILL.md` |
| Skill markdown | `C:\Users\oscar.rondon\.cursor\skills\obsidian-markdown\SKILL.md` |
| Skill capture | `C:\Users\oscar.rondon\.cursor\skills\obsidian-capture\SKILL.md` |
| Skill task-brief | `C:\Users\oscar.rondon\.cursor\skills\obsidian-task-brief\SKILL.md` |
| Skill bases (kepano) | `C:\Users\oscar.rondon\.cursor\skills\obsidian-bases\SKILL.md` |
| Skill cli (kepano) | `C:\Users\oscar.rondon\.cursor\skills\obsidian-cli\SKILL.md` |
| Skill defuddle (kepano) | `C:\Users\oscar.rondon\.cursor\skills\defuddle\SKILL.md` |
| Skill knap (kepano) | `C:\Users\oscar.rondon\.cursor\skills\knap\SKILL.md` |
| Skill json-canvas (kepano) | `C:\Users\oscar.rondon\.cursor\skills\json-canvas\SKILL.md` |
| Agent (referencia) | `C:\Users\oscar.rondon\.cursor\agents\obsidian-notes.md` |

## Stack de skills

| Capa | Skills | Rol |
|------|--------|-----|
| **Workflow (tuyas)** | vault-structure, capture, obsidian-notes, obsidian-task-brief | Dónde, cuándo y con qué plan escribir |
| **Formato (kepano)** | obsidian-markdown | OFM, properties, checklists, wikilinks |
| **Vistas BD (kepano)** | obsidian-bases | Archivos `.base`: tablas, filtros, fórmulas |
| **Opcional (kepano)** | obsidian-cli, defuddle, knap, json-canvas | CLI, importar URLs, batch, canvas |

## Prohibido (lento e innecesario)

- Buscar `DEV-OBSIDIAN` con Glob/Grep en `C:\Users\oscar.rondon`, OneDrive o todo `Documents`
- Escanear recursivamente `.obsidian` con Shell para “encontrar el vault”
- Usar rutas Unix (`~/.cursor/...`) en Windows
- Asumir que el vault está en el workspace del proyecto

## Acceso rápido al vault

- **Escribir/leer nota:** `C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>\<ruta>.md`
- **Inspeccionar tema:** Glob/Grep con `target_directory: C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>`
- **Listar temas del vault:** Glob `*` en `target_directory: C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN` (solo carpetas de primer nivel)

## Flujo mínimo

1. Vault = `C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN` (sin búsqueda)
2. Read obligatorio: vault-structure + markdown + capture
3. Read condicional (según la petición):
   - backlog, prioridad, vistas, `.base`, “base de datos” → **obsidian-bases**
   - captura rápida por terminal → obsidian-cli
   - importar artículo/URL → defuddle
   - muchas notas desde CSV/JSON → knap
   - mapa visual → json-canvas
   - usuario pega ficha de `obsidian-task-brief` → validar estructura y escribir en `Backlog/tasks/`
4. Identificar `<Tema>` → inspeccionar solo esa carpeta dentro del vault
5. Proponer plan → acuerdo → escribir
6. Reportar rutas finales (`.md` y `.base` si aplica)

## Backlog: escalera markdown → Bases

Empieza simple; sube de nivel solo cuando el usuario lo pida o el volumen lo justifique.

| Nivel | Qué | Cuándo |
|-------|-----|--------|
| **1** | `<Tema>/pendientes/Pendientes.md` con secciones Por hacer / En curso / Bloqueado / Hecho | Empezar hoy (patrón ya usado en Academia) |
| **2** | Notas de tarea con properties (`status`, `priority`, `due`, tag `task`) | Cuando una tarea necesita contexto propio |
| **3** | `<Tema>/Backlog/Tasks.base` (vista tabla/cards filtrada) | Cuando quieras priorizar, filtrar o ver fechas en bloque |

Reglas al proponer Bases:

- Ubicación: `C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>\Backlog\Tasks.base`
- Enlazar desde `<Tema> — index.md` y desde `Pendientes.md` (conviven; no sustituir sin acuerdo)
- Seguir el ejemplo **Task Tracker** de `obsidian-bases` (filtro `file.hasTag("task")`)
- Properties mínimas en notas de tarea: `status`, `priority`, `due`, `project: <Tema>`
- Proponer el `.base` en el plan **antes** de crearlo; validar YAML según obsidian-bases

Plantilla mínima de nota de tarea (nivel 2):

```yaml
---
title: <acción corta>
type: task
status: por-hacer
priority: 2
due:
project: <Tema>
tags:
  - task
---
```

Para reglas completas (acuerdo previo, PARA, Bases, qué no hacer): lee el agent `obsidian-notes.md`.
