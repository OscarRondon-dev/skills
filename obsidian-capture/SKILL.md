---
name: obsidian-capture
description: Capture conversation context into the DEV-OBSIDIAN vault under the correct topic folder, proposing a logical note plan and getting user agreement before writing. Use when the user asks to take notes, save this, document a decision, capture an idea, or turn chat into Obsidian notes.
disable-model-invocation: true
---

# Obsidian Capture

Orden antes que velocidad. Vault fijo + carpeta de tema + **acuerdo previo** + escritura.

Siempre: `obsidian-vault-structure` (dónde) → acuerdo → `obsidian-markdown` (cómo escribir).

## Vault y carpeta

- Vault: `C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN` (ruta fija — no buscar en disco)
- Ruta completa de nota: `C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>\<subcarpeta>\<nota>.md`
- Carpeta: tema/proyecto explícito en la **raíz del vault** (ej. `Komtexia/`, `SAMM365/`), hermana de `.obsidian/`
- **Nunca** guardar notas dentro de `.obsidian/` (eso es solo configuración de Obsidian)
- Si falta el nombre del tema → preguntar

## Capture modes (dentro del tema)

| Mode | When | Type | Default bajo `<Tema>/` |
|------|------|------|-------------------------|
| Fleeting | Idea incompleta | `fleeting` | `00 Inbox/` |
| Literature | Artículo/vídeo/doc | `literature` | `00 Inbox/` o `30 Resources/` si ya procesada |
| Permanent | Claim claro en palabras propias | `permanent` | `30 Resources/` |
| Decision | Elección y consecuencias | `project` / decision | `10 Decisions/` |
| Session | Qué pasó en la sesión/reunión | `type: daily` (o `project` + tag `session`) | `20 Sessions/` |
| MOC / index | Navegación del tema | `moc` | `<Tema> — index.md` |

## Decision tree

1. ¿Qué **tema/carpeta**? → si no está claro, pregunta.
2. ¿Es **decisión**, **sesión**, **idea permanente** o **captura cruda**?
3. ¿Ya existe nota parecida en esa carpeta? → planificar update, no duplicar.
4. Armar **plan de árbol** → pedir acuerdo → escribir.

## Workflow (obligatorio)

```text
Capture progress:
- [ ] Vault = DEV-OBSIDIAN
- [ ] Tema/carpeta identificado
- [ ] Inspeccionar carpeta existente
- [ ] Proponer árbol de notas + enlaces (sin escribir aún)
- [ ] Esperar confirmación del usuario
- [ ] Escribir/actualizar solo lo acordado
- [ ] Enlazar al index/MOC del tema
- [ ] Reportar rutas finales
```

## Anti-caos

- Prohibido un dump único tipo `notas.md` en la raíz del vault
- Prohibido mezclar temas
- Preferir varias notas atómicas enlazadas desde el index del tema
- Si el volumen es grande: proponer fases (ej. “fase 1: index + decisiones; fase 2: resources”)

## Anti-duplication

1. Buscar títulos/notas similares **dentro de la carpeta del tema**
2. Si existe cerca → proponer **actualizar**
3. Crear archivo nuevo solo si la idea es distinta

## From chat → plan → notes

Al capturar desde la conversación:

1. Extraer hechos, decisiones, preguntas abiertas, next actions (sin relleno)
2. Agrupar en el plan por tipo (decisión / sesión / resource)
3. Mostrar el plan al usuario
4. Tras “ok”, escribir OFM con frontmatter y `[[wikilinks]]` al index

## Decision note template

```markdown
---
title: Decision — <short name>
type: project
status: active
created: YYYY-MM-DD
updated: YYYY-MM-DD
project: <Tema>
---

# Decision — <short name>

## Context
...

## Options
- …

## Decision
...

## Consequences
...

## Related
- [[<Tema> — index]]
```

## Quality bar

- [ ] Acuerdo previo (o bypass explícito)
- [ ] Carpeta de tema correcta bajo DEV-OBSIDIAN
- [ ] Frontmatter válido
- [ ] Título/filename legible
- [ ] Enlace al index u otra nota del tema
- [ ] Sin secretos (redactar tokens/passwords salvo petición explícita)
