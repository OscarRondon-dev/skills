---
name: project-memory-resume
description: Orients the agent at session start from Obsidian project-memory/ sandbox — reads activeContext and latest session handoff only, presents a trust summary, and asks the user to confirm before acting. Use at session start, /project-memory-resume, retomar proyecto, or when resuming work on a known project.
disable-model-invocation: true
---

# Project Memory — Resume

**Leer poco → resumir → pedir confirmación → no asumir que lo escrito sigue vigente.**

## Invocation

- User-invoked only
- **No writes**
- **No bulk-read** of the vault

## Sandbox (única zona automática)

```text
C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>\project-memory\
```

| Ruta | Rol |
|------|-----|
| `project-memory/` | Leer aquí |
| Resto de `<Tema>/` | PKM humano — **no leer** salvo petición explícita |
| Repo `.agent/` | Config — **no** es memoria |

Si `project-memory/` no existe → sugerir `setup-project-memory`; **stop**.

## Process

```
Task Progress:
- [ ] 1. Resolve <Tema>
- [ ] 2. Read (max 2 files)
- [ ] 3. Trust check
- [ ] 4. Present summary + ask user
- [ ] 5. WAIT
```

### 1. Resolve `<Tema>`

1. User explicit name
2. Git repo folder / remote → match vault folder
3. Ask if ambiguous

### 2. Read (max 2 files)

| # | File | If missing |
|---|------|------------|
| 1 | `project-memory/activeContext.md` | Report gap; suggest setup |
| 2 | Latest `project-memory/20 Sessions/*.md` | Skip; note no handoff |

Glob only: `target_directory: ...\<Tema>\project-memory`

**Do not read:** README, index, all decisions/gotchas, notes outside `project-memory/`.

### 3. Trust check

| Señal | Acción |
|-------|--------|
| `last_verified` o `updated` > 14 días | ⚠️ posiblemente obsoleto |
| `epistemic: hypothesis` | Tratar como no confirmado |
| `status: superseded` / `archived` | Ignorar como mandato |
| Checkboxes `[x]` | No asumir — preguntar |
| Bloqueadores / "No tocar" | Preguntar si siguen |
| Memoria vs repo | **Gana repo**; reportar drift |

Cross-check ligero (si workspace es el repo): `git status` — no auditar código.

### 4. Present summary

```markdown
## Resume — <Tema>

**Fuente:** project-memory/activeContext (+ handoff si existe)
**Confianza:** alta | media | baja

### Lo que dice la memoria
- Objetivo: …
- En progreso: …
- Bloqueadores: …
- Próximo paso: …

### ⚠️ Verificar contigo
- [ ] …

### Drift detectado (si hay)
- …

### ¿Sigo con esta memoria?
Confirma, corrige, o di "ignora memoria" para esta sesión.
```

### 5. WAIT

**Prohibido** implementar, editar código, o escribir en Obsidian hasta respuesta.

Correcciones del usuario = verdad de sesión (no escribir hasta wrap-up salvo petición).

## Anti-patrones

- Leer Pendientes, Resources, varios, o todo el vault
- Confundir repo `.agent/` con `project-memory/`
- Actuar con confianza baja sin preguntar
- Escribir notas en resume

## Related

- Bootstrap: `setup-project-memory`
- Cierre: `project-memory-wrap-up`
