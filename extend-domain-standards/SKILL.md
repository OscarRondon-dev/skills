---
name: extend-domain-standards
description: >-
  Researches official docs for one user-named technology, framework, or library,
  contrasts with the current repo, and proposes domain-specific standards (rules,
  docs). Use only when the user explicitly invokes extend-domain-standards or
  asks to add stack-specific coding standards after generic bootstrap. Does not
  auto-run. Does not write files without explicit user approval.
disable-model-invocation: true
---

# Extend Domain Standards

Complemento **invocable solo por el usuario** de [`setup-repo-standards`](../setup-repo-standards/SKILL.md). **Un dominio por invocación** (el que el usuario nombre: cualquier framework, SDK, servicio, patrón de stack).

## Invocación y autorización (obligatorio)

- **Solo el usuario invoca** esta skill (comando, adjunto, mención explícita). El agente **no** la aplica por inferencia ni porque detecte una librería en el repo.
- **`disable-model-invocation: true`** — no cargar ni ejecutar en segundo plano.
- **Prohibido escribir** en el repo (archivos, rules, commits, issues) **sin autorización explícita del usuario en el mensaje actual**.
- Flujo: **explorar → investigar → contrastar → proponer → esperar OK → escribir (si aprueba)**. Si el usuario no aprueba, **no se crea nada**.

## Cuándo usar / cuándo no

| Usar | No usar |
| --- | --- |
| El usuario pide estándares de **una** tecnología concreta | Bootstrap genérico del repo → `setup-repo-standards` |
| Investigar guía oficial **actual** y contrastar con el repo | Auditar o refactorizar código sin pedido de estándares |
| Proponer rule + doc satélite **tras aprobación** | Escribir artefactos “porque encaja” sin OK |
| Cualquier stack: backend, frontend, IA, BD, infra, CLI… | Varios dominios en una pasada sin acuerdo |

## Principios de contraste

1. **Buena práctica manda** — documentación oficial vigente y patrones reconocidos del ecosistema son la referencia principal.
2. **El repo se evalúa, no se asume correcto** — ADR, código legacy o “así lo hacemos” solo se **mantienen** si al contrastar siguen siendo buena práctica para la versión en uso.
3. **Desalineación explícita** — si repo ≠ oficial: explicar riesgo, recomendar adoptar oficial, migrar gradualmente, o documentar excepción **solo con OK del usuario** (y motivo).
4. **Oficial > blog** — vendor docs, MCP de docs del producto si existe, changelog. Blogs/foros solo como pista, verificados contra oficial.
5. **Artefactos mínimos** — tras OK: satélite `docs/standards/<slug>.md`, rule `.cursor/rules/<slug>.mdc`, punteros en hub; ADR nuevo solo si el usuario lo pide o la decisión lo exige.
6. **No ensuciar setup genérico** — nada de tecnologías concretas en plantillas de `setup-repo-standards`. El bootstrap genérico solo captura lo que el **usuario acordó** (p. ej. `runtime.md` con su descripción); la profundidad vendor-specific vive aquí.
7. **Idioma** — el del repo (`CODING_STANDARDS.md` / README); si vacío, preguntar.

## Process

```
Task Progress:
- [ ] 0. Confirm scope with user (technology name)
- [ ] 1. Explore repo (read-only)
- [ ] 2. Research official (read-only)
- [ ] 3. Contrast & recommend (present to user)
- [ ] 4. WAIT — explicit approval required
- [ ] 5. Draft artifacts (show in chat, no write yet)
- [ ] 6. WAIT — approval to write
- [ ] 7. Write (only if approved)
- [ ] 8. Done
```

### 0. Confirm scope

Preguntar si falta algo: nombre exacto de la tecnología, versión objetivo, alcance (solo docs vs rule vs ADR), rutas preferidas del repo.

**No avanzar** sin respuesta clara del usuario sobre el dominio.

### 1. Explore repo (solo lectura)

Buscar lo que exista para **esa** tecnología:

- ADRs / decision records
- Rules (`.cursor/rules/`, `.claude/rules/`)
- `CODING_STANDARDS.md`, `docs/standards/`
- Dependencias (`package.json`, `pyproject.toml`, `go.mod`, etc.)
- Código que la use (patrones repetidos, anti-patrones)

Anotar hallazgos; **no modificar**.

### 2. Research official (solo lectura)

1. Versión instalada / declarada en el repo
2. MCP de documentación del producto **si el entorno lo expone** (descubrir con `GetMcpTools`, no asumir cuál existe)
3. Documentación oficial y changelog reciente (`WebFetch` / búsqueda acotada)
4. Extraer 3–5 reglas **accionables** citables

Registrar URLs y versión consultada.

### 3. Contrast & recommend

Presentar **en el chat** (tabla obligatoria):

| Tema | Repo hoy | Oficial / vigente | Recomendación |
| --- | --- | --- | --- |
| … | … | … | mantener / adoptar / corregir / excepción documentada / fuera de alcance |

Cerrar con:

- **Recomendación resumida**
- **Artefactos propuestos** (rutas y nombres)
- **Qué no se tocará**

Terminar con: **“¿Apruebas que redacte el borrador?”** — **no escribir aún**.

### 4–6. Draft y write (solo tras OK)

- Tras OK al borrador en chat → **“¿Apruebas que escriba en el repo?”**
- Solo entonces crear/editar archivos acordados.
- Si el usuario pide cambios → ajustar borrador; nueva aprobación antes de escribir.

### 8. Done

Listar lo creado (si hubo write) o entregar informe (si solo research). Si quedan dominios en cola, recordar **una invocación por dominio** y nueva autorización.

## Ejemplos de invocación (ilustrativos)

El usuario nombra el dominio; la skill **no** está ligada a ninguno:

> `/extend-domain-standards` + “ORM que usamos en el backend”

> `/extend-domain-standards` + “cliente HTTP / API externa X”

> `/extend-domain-standards` + “patrón de estado en el frontend”

Los nombres concretos los aporta **siempre el usuario** en la invocación.

## Anti-patrones

- Hardcodear tecnologías en la skill o asumir stack del último chat
- Escribir rules/docs sin **dos OK** (propuesta + write), salvo que el usuario unifique en un solo “apruebo todo”
- “El repo gana” cuando contradice buena práctica oficial
- Copiar docs oficiales enteras al repo
- Big-bang multi-dominio en un diff
- Auto-invocar la skill al ver una dependencia en `package.json`

## Recursos

- Plantillas de artefactos: [reference-artifacts.md](reference-artifacts.md)
- Research y contraste: [reference-research.md](reference-research.md)
- Bootstrap genérico: [setup-repo-standards](../setup-repo-standards/SKILL.md)
