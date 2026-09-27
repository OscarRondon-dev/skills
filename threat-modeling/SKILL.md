---
name: threat-modeling
description: >-
  Runs STRIDE (and LINDDUN if PII) threat modeling for a software project:
  inception, delta on architecture/auth/data changes, or pre-release review.
  Writes a living model to docs/security/ in the current repo. Use when the
  user invokes /threat-modeling or asks for modelado de amenazas, STRIDE,
  LINDDUN, attack trees, trust boundaries, or secure design analysis.
disable-model-invocation: true
---

# Threat modeling

Ejercicio de **diseño seguro** sobre el repo actual. Identifica amenazas,
prioriza y deja un artefacto vivo en git. **No es** un pentest, un scan OSV
ni la review de un plan (`plan-security`).

Delegar el análisis profundo al subagente `threat-modeler`. El agente
principal explora, confirma alcance, escribe disco y propone backlog.

## Objetivo

Dejar en el repo:

| Artefacto | Rol |
|-----------|-----|
| `docs/security/threat-model.md` | Modelo vivo (única fuente de verdad) |
| `docs/security/README.md` | Índice y convenciones (crear si no existe) |

**No crear** enciclopedias de metodología en el repo. El *cómo* vive en esta
skill; el *qué amenaza a este sistema* vive en `docs/security/`.

## No hace

- Overwrite de `docs/standards/security.md` (eso es `setup-repo-standards`)
- Scan de dependencias / CVE → `osv-vulnerability-scan`
- Review de un plan de implementación → `plan-security` / `plan-council`
- Exploits, PoCs, payloads, ni recetas de ataque
- Tickets/GitHub salvo que el usuario lo pida
- Secretos reales, connection strings, ni datos de `.env` en el modelo

## Idioma

Detectar README / `docs/` / `AGENTS.md`. Si hay docs, usar ese idioma. Si no
hay, **español**. Términos técnicos (STRIDE, DFD, trust boundary) en inglés.

## Modos (elegir uno)

| Modo | Cuándo | Profundidad |
|------|--------|-------------|
| **inception** | Repo nuevo, kickoff, primera arquitectura | DFD completo + STRIDE per element |
| **delta** | Feature o cambio que toca auth, datos, límites o APIs | Solo lo nuevo + impacto en el modelo existente |
| **review** | Pre-release, fin de hito, sistema ya en prod | Verificar mitigaciones; residual; tests de abuso |

Si el usuario no dice el modo: inferir (¿existe `docs/security/threat-model.md`?
¿el cambio es acotado?). Decirlo en el paso 1. No mezclar los tres de golpe.

**Metodología default:** STRIDE + bug bar (Critical/High/Medium/Low).
**LINDDUN** si hay PII, cuentas, RGPD, salud, o el usuario lo pide.
**Árboles de ataque** solo para 1–2 objetivos de alto valor, no para inventariar el sistema.
**PASTA / OCTAVE / TRIKE / DREAD** solo si el usuario los pide.

## Process

```
Task Progress:
- [ ] 1. Alcance (modo, idioma, docs existentes)
- [ ] 2. Explorar arquitectura (solo lectura)
- [ ] 3. Confirmar DFD / límites si inception o hay duda
- [ ] 4. Delegar threat-modeler
- [ ] 5. Resumen + mitigaciones propuestas
- [ ] 6. Escribir docs/security/ (solo con OK)
- [ ] 7. Backlog (proponer; no crear tickets salvo pedido)
```

### 1. Alcance

Leer, no asumir:

- `docs/security/threat-model.md` (¿ya hay modelo?)
- `docs/standards/security.md` (principios de código; no es el threat model)
- `AGENTS.md` / `README.md` / `BACKEND.md` / `DATABASE.md` / `docs/stack.md`
- Auth, APIs, persistencia, actores, exposición (público vs interno)

Anotar: modo, idioma, PII sí/no, y si esto es creación o actualización.

Si no hay repo / workspace de proyecto: parar. El modelo se escribe **en el
repo del producto**, no en home ni en esta skill.

### 2. Explorar

Mapear evidencia real (código, rutas, configs), no un diagrama inventado:

- Actores (usuario, admin, jobs, terceros)
- Procesos (apps, APIs, workers)
- Almacenes (BD, blobs, caches, colas)
- Flujos de datos y **límites de confianza**
- Secretos / identidad (dónde vive la sesión; nunca copiar valores)

No hace falta leer todo el código: arquitectura + boundaries + datos sensibles.

### 3. Confirmar (inception o duda)

Mostrar un DFD corto (Mermaid) y la lista de límites. **Stop** si el usuario
debe corregir actores o almacenes. En **delta** con modelo previo coherente,
seguir.

### 4. Delegar `threat-modeler`

Lanzar el subagente `threat-modeler` (readonly) con:

- Modo, idioma, PII sí/no
- DFD / componentes / límites
- Extracto o ruta del modelo existente si hay
- Instrucción: devolver **solo** la plantilla de [templates/threat-model.md](templates/threat-model.md)

Cargar [reference-stride.md](reference-stride.md). Si PII: también
[reference-linddun.md](reference-linddun.md).

### 5. Resumen al usuario

Antes de escribir disco:

- Recuento por severidad
- Top 5 amenazas
- Mitigaciones que cambian diseño vs las que son control/test
- Amenazas que propones **aceptar** (residual) — el humano decide

### 6. Escribir

Solo tras OK explícito.

- Crear `docs/security/` si no existe.
- **inception:** escribir `threat-model.md` desde la plantilla + `README.md` si falta.
- **delta / review:** actualizar el modelo existente; añadir entrada en `## Changelog`. No crear un archivo por feature.
- No borrar amenazas mitigadas: marcar estado `Mitigated` y el control.

Plantilla: [templates/threat-model.md](templates/threat-model.md).
Índice: [templates/README.md](templates/README.md).

### 7. Backlog

Listar mitigaciones High+ como candidatos a historias (título + criterio de
aceptación). No abrir issues ni tocar `feature_list.json` salvo pedido.

## Convivencia

| Cosa | Dónde |
|------|--------|
| Cómo escribir código seguro | `docs/standards/security.md` |
| Qué amenaza a **este** sistema | `docs/security/threat-model.md` |
| CVEs de paquetes | OSV / `osv-vulnerability-scan` |
| ¿El plan contempla seguridad? | `plan-security` |

## Seguridad del ejercicio

- Amenazas a nivel de diseño (categoría STRIDE + impacto + mitigación).
- Prohibido: exploits, payloads, pasos de ataque reproducibles.
- Si aparece un secreto en el código durante la exploración: **no** copiarlo al modelo; avisar aparte.
