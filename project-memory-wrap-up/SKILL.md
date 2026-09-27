---
name: project-memory-wrap-up
description: Closes an agent session by drafting updates to Obsidian project-memory/ sandbox — activeContext and session handoff — shows draft in chat, and writes only after user confirmation. Use at session end, /project-memory-wrap-up, or when the user asks to save session memory to Obsidian.
disable-model-invocation: true
---

# Project Memory — Wrap-up

**Resumir sesión → borrador → preguntar → escribir solo en `project-memory/`.**

## Invocation

- User-invoked only
- **No write** until user approves draft

## Sandbox (única zona de escritura)

```text
C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>\project-memory\
```

| Ruta | Rol |
|------|-----|
| `project-memory/` | Escribir aquí |
| Resto de `<Tema>/` | **No escribir** salvo petición explícita |
| Repo `.agent/` | **No escribir** memoria aquí |

Si `project-memory/` no existe → sugerir `setup-project-memory`; **stop**.

## Process

```
Task Progress:
- [ ] 1. Resolve <Tema>
- [ ] 2. Gather session facts
- [ ] 3. Draft updates
- [ ] 4. Show draft + ask
- [ ] 5. Write (only after OK)
- [ ] 6. Report paths
```

### 1. Resolve `<Tema>`

Igual que `project-memory-resume`.

### 2. Gather session facts

From conversation (+ optional `git status` / `git diff --stat`):

- Qué se hizo
- Decisiones importantes
- Gotchas descubiertos
- Tareas abiertas / bloqueadores
- Próximo paso

**No copiar:** docs oficiales, rutas de archivos, versiones de deps.

### 3. Draft updates

Preparar borrador de:

| Archivo | Acción |
|---------|--------|
| `activeContext.md` | Actualizar (mantener ~80 líneas) |
| `20 Sessions/YYYY-MM-DD — <título>.md` | Crear handoff |
| `10 Decisions/Decision — <nombre>.md` | Solo si hubo decisión importante |
| `30 Gotchas/Gotcha — <nombre>.md` | Solo si hubo gotcha verificado |

Plantillas: [templates/](templates/) (mismas que `setup-project-memory/templates/`).

Frontmatter obligatorio:

```yaml
epistemic: hypothesis | decision | verified
status: active | superseded | archived
last_verified: YYYY-MM-DD
updated: YYYY-MM-DD
```

### 4. Show draft + ask

Mostrar **contenido completo** del borrador en chat.

Pregunta obligatoria:

```markdown
### ¿Escribo esto en project-memory/?
- [ ] Sí, todo
- [ ] Sí, pero ajusta: …
- [ ] No escribir
```

**WAIT** hasta respuesta.

### 5. Write (only after OK)

- Escribir **solo** archivos aprobados dentro de `project-memory/`
- Actualizar `updated` y `last_verified` en frontmatter
- Decisiones: append-only; superseder viejas con `status: superseded` + link, no borrar
- **No** tocar Pendientes, varios, Resources, index humano, `.obsidian/`, repo `.agent/`

### 6. Report paths

Listar rutas absolutas escritas y recordar `/project-memory-resume` para la próxima sesión.

## Anti-patrones

- Escribir sin borrador aprobado
- Escribir fuera de `project-memory/`
- Duplicar Pendientes.md
- Documentar rutas src o versiones
- Auto-wrap-up al terminar conversación

## Templates

Copiar estructura desde `setup-project-memory/templates/`:

- [session-handoff.md](../setup-project-memory/templates/session-handoff.md)
- [decision.md](../setup-project-memory/templates/decision.md)
- [gotcha.md](../setup-project-memory/templates/gotcha.md)
- [activeContext.md](../setup-project-memory/templates/activeContext.md)

## Related

- Bootstrap: `setup-project-memory`
- Inicio: `project-memory-resume`
