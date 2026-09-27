# Reference — artefactos por dominio

Convenciones **agnósticas**. Sustituir `<slug>` por nombre corto acordado con el usuario (kebab-case, derivado de la tecnología que pidió).

## Naming

| Artefacto | Patrón | Ejemplo ilustrativo (no prescriptivo) |
| --- | --- | --- |
| Doc satélite | `docs/standards/<slug>.md` | `docs/standards/orm.md` |
| Rule Cursor | `.cursor/rules/<slug>.mdc` | `.cursor/rules/http-client.mdc` |
| Globs | Rutas donde aplica **esa** tecnología en **este** repo | Detectar en explore; no fijar en la skill |

Si ya existe ADR o doc dueño → el satélite **condensa operativo** y enlaza; no reemplaza la decisión arquitectónica.

## Plantilla — `docs/standards/<slug>.md`

```markdown
# <Nombre tecnología / dominio>

Estándares operativos en este repo. Decisión arquitectónica: [ADR-…](…) (si aplica).

**Fuentes consultadas:** <URL oficial>, versión <X.Y>, fecha research.

## Reglas

1. …
2. …

## ✅ / ❌

\`\`\`<lang del stack>
// ❌ …
// ✅ …
\`\`\`

## Referencia en repo (si aplica)

- Ruta: `…` — qué patrón observar (sin pegar archivos enteros).

## Enlaces

- [Documentación oficial](https://…)
```

## Plantilla — `.cursor/rules/<slug>.mdc`

```markdown
---
description: <Tecnología> — reglas operativas al escribir código que la use.
globs: "<detectado en explore>"
alwaysApply: false
---

# <Tecnología>

Fuente: `docs/standards/<slug>.md`.

- Bullets operativos (≤10)
- Puntero al satélite para ✅/❌
```

## Hub (`CODING_STANDARDS.md` o equivalente)

Solo **con aprobación explícita**:

- 1–2 bullets en la sección de stack que corresponda
- 1 fila en tabla de fuentes / mapa de docs

No volcar el satélite ni el ADR en el hub.

## Cuándo **proponer** ADR nuevo (no crear sin OK)

- Decisión irreversible (proveedor, layout, contrato entre equipos)
- Conflicto repo vs oficial que requiere acuerdo explícito
- El “por qué” no cabe en el satélite operativo

Siempre: proponer en chat → esperar autorización.
