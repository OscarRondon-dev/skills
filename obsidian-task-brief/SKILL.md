---
name: obsidian-task-brief
description: Genera fichas de tarea ejecutables con trazabilidad, alcance, mapa de código y criterios de done verificables. Usar en modo standalone (spec para ejecutar, issue, PR o chat) o opcionalmente compatible con Obsidian Backlog. Invocar cuando el usuario pida ficha de tarea, preparar pendiente, spec ejecutable, documentar trabajo sin re-leer el repo, o /obsidian-task-brief.
disable-model-invocation: true
---

# Task Brief (ficha de ejecución)

Genera una **ficha lista para ejecutar**: quien la lea puede hacer la tarea sin re-explorar todo el código.

**Modo por defecto: standalone.** Solo devuelve markdown en el chat. **No** escribe en Obsidian ni en el repo salvo que el usuario lo pida explícitamente.

## Modos

| Modo | Cuándo | Qué hace |
|------|--------|----------|
| **Standalone** (default) | Ficha para ti, issue, PR, otro agente | Devuelve el bloque; fin |
| **Obsidian** (opcional) | Usuario dice "para Obsidian", "Backlog", "Tasks.base" | Misma ficha + al final sugiere handoff a `/obsidian-notes` |

No asumir Obsidian si el usuario no lo menciona.

## Cuándo usar

- Preparar un pendiente con trazabilidad
- Convertir chat, error o issue en spec accionable
- Documentar antes de implementar
- Pasar contexto a otro agente o dev

## Reglas

1. **Explorar el repo** si está disponible: rutas reales en "Dónde tocar".
2. **Sin secretos** (redactar tokens/keys; nombrar solo variables).
3. **Done verificable**: comandos, tests o QA manual explícitos.
4. **Alcance + No incluye** obligatorios.
5. **Preguntas abiertas** si falta info crítica — no inventar.
6. Metadatos de estado en **español** si aplica: `por-hacer` | `en-curso` | `bloqueado` | `hecho`.

## Salida obligatoria

Devolver **solo** este bloque (markdown):

```markdown
# <Título — verbo + objeto>

## Metadatos
- **Proyecto:** <nombre>
- **Prioridad:** <1 alta | 2 media | 3 baja>
- **Estado:** por-hacer
- **Due:** <YYYY-MM-DD o —>
- **Etiquetas:** <dominio, ej. api, azure, qa>

## Objetivo
Una frase: qué queda hecho al cerrar.

## Contexto
3-5 líneas: por qué existe, problema, antecedentes.

## Alcance
### Incluye
- [ ] …

### No incluye
- …

## Dónde tocar
| Área | Ruta / módulo | Qué hacer |
|------|---------------|-----------|
| … | `ruta/real` | … |

## Pasos de implementación
1. …
2. …
3. …

## Criterios de done
- [ ] …
- [ ] Comando/test: `…`
- [ ] QA manual: …

## Riesgos y dependencias
- **Bloquea:** …
- **Depende de:** …
- **Decisiones ya tomadas:** …

## Referencias
- Issue/PR: …
- ADR/doc: …
- Enlaces: …

## Notas para quien ejecute
Comandos, env vars (sin valores), flags, rama sugerida.

## Preguntas abiertas
- … (omitir sección si no aplica)
```

## Calidad mínima

Antes de entregar, verificar:

- [ ] Objetivo en 1 frase
- [ ] ≥1 fila en **Dónde tocar** con ruta real (o justificar por qué no aplica)
- [ ] ≥2 pasos ordenados
- [ ] ≥2 criterios de done verificables
- [ ] Referencias si existen

Si no cumple, completar antes de responder.

## Cómo invocar (standalone)

```
/obsidian-task-brief Prepara ficha para: <descripción breve>
Proyecto: <nombre>. Explora el repo si hace falta.
```

```
/obsidian-task-brief Issue #96 — retirar EmbeddingService. Mapa de callers y done verificable.
```

## Modo Obsidian (solo si el usuario lo pide)

Añadir bloque YAML compatible con `Backlog/tasks/`:

```yaml
---
title: <título>
type: task
status: por-hacer
priority: <1|2|3>
due:
project: <Academia|Komtexia|SAMM365>
tags: [task, <dominio>]
---
```

Al final, sugerir (no ejecutar):

```
Opcional — guardar en Obsidian:
/obsidian-notes <tema> — tarea Backlog, apunta ya: [pegar ficha]
```

Handoff: skill `obsidian-notes` → `obsidian-capture` → `<Tema>/Backlog/tasks/Task — <título>.md`

## Ejemplos

Ver [examples.md](examples.md).
