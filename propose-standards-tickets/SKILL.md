---
name: propose-standards-tickets
description: >-
  After setup-repo-standards, analyze the repo against CODING_STANDARDS.md,
  docs/standards/*, .cursor/rules/*, and the verify belt; propose risk-ordered
  remediation phases, then tracer-bullet TDD tickets for one chosen phase.
  Writes .scratch issues first; GitHub only on explicit ask. Use when the user
  invokes /propose-standards-tickets, wants standards-gap tickets, or wants safe
  incremental repo fixes after standards setup.
disable-model-invocation: true
---

# Propose Standards Tickets

Tras `setup-repo-standards`, convertir gaps vs estándares en **fases** y luego
**tickets** verticales (estilo `to-tickets`), seguros y TDD. **No edita código
de producto.**

## Objetivo

1. Leer la verdad del repo (`docs/standards/*` + `.cursor/rules/*` + cinturón).
2. Detectar fallos/gaps en solo lectura.
3. Proponer **fases** priorizadas por riesgo (quiz).
4. El usuario elige **una** fase → generar tickets solo de esa fase.
5. Publicar a `.scratch/.../issues/` tras aprobación; GitHub solo si lo pide.

## Límites fijos

- No tocar código de producto, no relajar lint/typecheck, no “arreglar en silencio”.
- No inventar estándares: solo lo acordado en setup (+ jerarquía de verdad).
- No saturar contexto: **nunca** el backlog completo de tickets de golpe.
- Idioma: **español** (títulos, cuerpos, quiz y plantilla `.scratch`).
- Convive con Matt (`docs/agents/*`): no pisar; usar vocabulario de triage si existe.
- Análisis: **no escribir** bajo el repo (ni `.scratch` hasta el paso 6 aprobado, ni caches de lint si se pueden evitar; si aparece cache nuevo, no borrarlo sin OK del usuario).

## Prerrequisitos

Antes de analizar, comprobar:

| Artefacto | Esperado |
|-----------|----------|
| Hub | `CODING_STANDARDS.md` |
| Satélites | `docs/standards/*` (al menos testing + tooling + clean-code o feature-first) |
| Rules | `.cursor/rules/*.mdc` |
| Cinturón | script `verify` o `verify:standards` (lint → typecheck → test) por paquete relevante |

Si faltan: **parar** y pedir `/setup-repo-standards` (o completar lo que falte). No improvisar reglas.

Jerarquía de verdad: `docs/standards/*` + `.cursor/rules/*` > config ejecutable (eslint/tsconfig/scripts) > docs legacy.  
Si un satélite **contradice** la config viva: gana config + hub; ticket de alinear el doc (cinturón o deuda menor) — no bajar severidad.

## Process

```
Task Progress:
- [ ] 1. Precondiciones
- [ ] 2. Gap analysis (read-only)
- [ ] 3. Proponer FASES → quiz
- [ ] 4. Usuario elige 1 fase
- [ ] 5. Tickets de esa fase → quiz
- [ ] 6. Escribir .scratch
- [ ] 7. (Opcional) GitHub
- [ ] 8. Otra fase → volver a 4
```

### 1. Precondiciones

Confirmar tabla de arriba. Detectar **mono vs multi-paquete** (`functions/`, `apps/*`, `packages/*`, `verify:all`, `npm run … --prefix`). Anotar el verify de **cada** raíz de código y si existe `verify:security` aparte.

### 2. Gap analysis (solo lectura)

Recoger evidencia, sin patch. Escalera de tooling (parar cuando baste):

1. Leer `docs/standards/tooling.md` + scripts/CI documentados.
2. Lint de un paquete si es barato (capturar salida; en Windows evitar pipes que traguen `stdio: inherit`).
3. Typecheck si lint no explica el rojo.
4. Tests solo si el cinturón/docs dicen que fallan o hace falta para la fase.

Si verify es **rojo intencional** documentado o tarda demasiado: usar **baseline documentado + conteos/muestreo por carpeta**; no bloquear el diseño de fases esperando un verde completo.

Recoger también:

- Gaps vs docs/rules: testing, clean-code, feature-first, naming; seguridad (ver abajo).
- Módulos grandes / fat hosts / Fat Entrypoint (señales; **no** meter extracción de entrypoint en fase cinturón).
- Prefactors obvios y blast radius ancho → expand–contract.
- Vocabulario de dominio / ADRs / `CONTEXT.md` si existen.

**Seguridad sin satélite:** si no hay `docs/standards/security*` ni rules `*security*`, o bien (a) omitir fase seguridad, o (b) tratar secciones Auth/Secrets/Rules del **hub** como SoT — elegir una y decirlo en el quiz. No inventar política nueva.

Resumir hallazgos en pocas viñetas **antes** de las fases.

### 3. Proponer FASES (no tickets aún)

Presentar fases numeradas. Plantilla por fase:

- **Nombre**
- **Por qué ahora** (riesgo / seguridad del cambio)
- **Qué entra** / **Qué queda fuera**
- **Paquete(s)** afectados (si multi-root)
- **Señales de done** (comando verify concreto: root, `--prefix X`, o `verify:all`)

Orden candidata (ajustar al repo; **omitir fases vacías**):

1. **Cinturón verde** — fallos lint/typecheck/test en lotes que no exigen redesign. Deuda de tipos/`any` con listón intencional **sí** entra aquí (no “aceptada para siempre”).
2. **Seguridad fail-closed** — gaps vs security docs/rules o hub §Auth/Secrets (si se eligió b).
3. **Prefactors / expand–contract** — Fat Entrypoint, renames de blast radius; expand → migrate por lotes → contract.
4. **Módulos / clean-code / feature-first** — slices verticales TDD (partir services, migrar legacy on-touch).
5. **Deuda menor** — OnPush, control flow templates, nits de naming/docs.

Quiz: ¿orden ok? ¿fusionar/omitir? ¿empezar por cuál?  
**Stop** hasta que elijan una fase.

### 4–5. Tickets de la fase elegida

Reglas `to-tickets` + `tdd`:

- Slice **vertical** = una caja de dominio, una familia de callables, o un lote de lint acotado a un árbol — no “toda una capa”.
- Tamaño: un context window fresco. Deuda masiva de lint → **varios tickets por caja**, no un mega-ticket.
- Prefactors / expand–contract cuando el blast radius lo exija.
- Cada ticket declara **Blocked by**.
- Cada ticket nombra el **seam** público (o “acordar seam”); AC con red → green; sin acoplar a internals.
- No horizontal slicing de “todos los tests primero”.

Por cada ticket en el quiz:

- **Title** / **Blocked by** / **What it delivers** / **Seam TDD**

Preguntar granularidad y edges; iterar hasta OK. Solo entonces escribir.

### 6. Escribir `.scratch`

Slug: `standards-<fase>` (p. ej. `standards-cinturon-verde`).  
**No** reutilizar ni sobrescribir carpetas `.scratch/` de otros features.

Ruta: `.scratch/<feature-slug>/issues/<NN>-<slug>.md`  
Numerar desde `01` en orden de dependencias (blockers primero).

```markdown
# <NN> — <Título del ticket>

**Qué construir:** comportamiento E2E verificable (usuario/verify), no lista por capas.

**Bloqueado por:** números/títulos que gatean, o "Ninguno — puede empezar ya".

**Seam TDD:** interfaz pública bajo test; rojo → verde en ese seam.

**Estado:** ready-for-agent

- [ ] Criterio de aceptación observable
- [ ] Test falla primero en el seam acordado; luego pasa con el cambio mínimo
- [ ] Cinturón relevante verde para el alcance del ticket (no relajar reglas)
```

Evitar paths/snippets frágiles salvo decisión de prototipo — solo lo decisional.  
No cerrar ni modificar issues padre en el tracker.

### 7. GitHub (solo si lo piden)

Tras `.scratch` aprobado: publicar en dependency order; edges nativos o “Bloqueado por”; label `ready-for-agent` si Matt lo define.

### 8. Siguiente fase

Ofrecer otra fase; no generar tickets de fases no elegidas.

## Prioridad / seguridad del cambio

Preferir lotes que dejen el cinturón más verde sin apagar reglas. Seguridad fail-closed no se diluye en tidy-ups. Wide refactors → expand–contract. Fat Entrypoint / hosts gordos → fase 3–4, no fase 1.

## Relación con otras skills

| Skill | Relación |
|-------|----------|
| `setup-repo-standards` | Prerrequisito |
| `to-tickets` | Forma, blockers, quiz |
| `tdd` | Seams, red→green |
| `setup-matt-pocock-skills` | Tracker/labels; no pisar `docs/agents/*` |
| `extend-domain-standards` | Solo vendor-depth con invocación explícita |

## Done

- Fases acordadas (al menos la elegida).
- Tickets de **una** fase en `.scratch/.../issues/` tras quiz.
- Frontier claro (sin blockers pendientes).
- Código de producto intacto.
