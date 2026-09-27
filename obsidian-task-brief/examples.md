# Ejemplos — obsidian-task-brief

## Standalone — prompt

```
/obsidian-task-brief Komtexia: ficha para verificar que search use Azure en expansión y vectores, no emulator.
Explora el repo y dame rutas reales.
```

El agente devuelve la ficha en el chat. **No** menciona Obsidian salvo que lo pidas.

---

## Standalone — salida (recorte)

```markdown
# Verificar search Azure (expansión + vectores)

## Metadatos
- **Proyecto:** Komtexia
- **Prioridad:** 2 media
- **Estado:** por-hacer
- **Due:** —
- **Etiquetas:** search, azure, embeddings

## Objetivo
El flujo de búsqueda usa Azure para expansión y vectores; el emulator no interviene en preview/prod.

## Dónde tocar
| Área | Ruta / módulo | Qué hacer |
|------|---------------|-----------|
| Search flow | `functions/src/.../search` | Confirmar seam Azure en embed + query |
| Emulator guard | `.../emulator` | Asegurar que no se activa fuera de local |

## Criterios de done
- [ ] Log sin `[Emulator] Simulando búsqueda` en preview
- [ ] Test o script: `npm test -- search`
- [ ] QA manual: query "extremo" devuelve resultados reales

## Referencias
- [[Pendientes]] (si luego va a Obsidian)
- Issue #96 (scope acotado)
```

---

## Con Obsidian — prompt

```
/obsidian-task-brief Academia — ficha Backlog para fix a11y sidebar. Formato Obsidian.
```

Respuesta = ficha + YAML frontmatter + sugerencia de `/obsidian-notes`.

---

## Handoff opcional (solo si quieres guardar)

```
/obsidian-notes Academia — tarea Backlog, apunta ya:

[pegar ficha]
```
