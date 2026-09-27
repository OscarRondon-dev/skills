---
name: plan-council
description: Orquesta revisión multi-disciplina de planes de implementación antes de entregarlos. Usar cuando el usuario invoque /plan-council o pida explícitamente calificar o revisar un plan con el consejo.
disable-model-invocation: true
---

# Plan Council — Orquestador de revisión de planes

Coordina subagentes especializados para **calificar y mejorar** un borrador de plan antes de entregarlo al usuario. Todo el informe final debe estar en **español**.

## Cuándo usar

- El usuario escribe `/plan-council`
- El usuario pide explícitamente: "revisa el plan con el consejo", "califica el plan", "somete el plan al council"

**No** invocar automáticamente en tareas triviales o sin petición explícita.

## Entrada

Acepta cualquiera de:

- Markdown del plan en el chat
- Archivo en `.cursor/plans/*.md`
- Borrador recién generado en Plan mode

Si no hay plan, pedir al usuario que pegue o indique el borrador.

## Subagentes disponibles

### Tier 1 — siempre convocar (en paralelo)

| Subagente | Archivo |
|-----------|---------|
| `plan-craftsmanship` | `~/.cursor/agents/plan-craftsmanship.md` |
| `plan-architecture` | `~/.cursor/agents/plan-architecture.md` |
| `plan-scope` | `~/.cursor/agents/plan-scope.md` |
| `plan-risk` | `~/.cursor/agents/plan-risk.md` |
| `plan-testability` | `~/.cursor/agents/plan-testability.md` |

### Tier 2 — condicional

Leer [reviewer-matrix.md](reviewer-matrix.md) y convocar solo los aplicables:

| Subagente | Cuándo |
|-----------|--------|
| `plan-security` | auth, permisos, datos sensibles, APIs |
| `plan-data` | schemas, migraciones, BD |
| `plan-ux-a11y` | UI, formularios, accesibilidad |
| `plan-ops` | deploy, CI/CD, Azure Functions, SWA |
| `plan-angular` | frontend Angular |
| `plan-cloud-platform` | Azure o Google Cloud |
| `plan-performance` | video, chat, mapas, charts, bundles, consultas masivas |

## Flujo de ejecución

```
1. Recibir borrador del plan
2. Evaluar reviewer-matrix.md → lista Tier 2 activados/omitidos
3. Lanzar Tier 1 + Tier 2 aplicables EN PARALELO (subagentes readonly)
4. Cada subagente devuelve su plantilla (puntuación, veredicto, concerns)
5. Consolidar informe
6. Si hay Rechazado o cambios obligatorios → revisar el plan
7. Entregar al usuario: plan revisado + sección Consejo de revisión
```

### Instrucción para delegar subagentes

Para cada revisor, invoca el subagente con este prompt base:

```
Revisa el siguiente plan de implementación según tu disciplina.
Responde SOLO con tu plantilla de salida en español.

--- PLAN ---
[contenido del borrador]
--- FIN PLAN ---
```

Ejecutar revisores en **paralelo** cuando la herramienta lo permita.

## Umbrales de decisión

| Condición | Acción del orquestador |
|-----------|------------------------|
| Cualquier veredicto **Rechazado** | Revisar plan antes de entregar; unificar cambios obligatorios; marcar bloqueantes |
| Media de puntuaciones **< 7** | Entregar con aviso: "requiere refinamiento" |
| Todos **≥ 7** sin Rechazado | Entregar plan + resumen breve del consejo |

**Bloqueante:** veredicto Rechazado o cambio obligatorio que impide implementación segura.

## Plantilla de informe consolidado

Entregar al usuario esta estructura:

```markdown
## Consejo de revisión

### Revisores convocados
- **Tier 1 (siempre):** craftsmanship, architecture, scope, risk, testability
- **Tier 2 (activados):** [lista con motivo breve]
- **Tier 2 (omitidos):** [lista con motivo]

### Tabla de calificaciones

| Revisor | Nota | Veredicto | Bloqueante |
|---------|------|-----------|------------|
| Craftsmanship | X/10 | ... | Sí/No |
| Arquitectura | X/10 | ... | Sí/No |
| ... | ... | ... | ... |

**Media:** X.X/10
**Estado global:** Aprobado | Requiere refinamiento | Bloqueado

### Top 3 riesgos
1.
2.
3.

### Cambios obligatorios unificados
-

### Preguntas abiertas al humano
-

---

## Plan revisado

[Plan completo incorporando cambios no ambiguos derivados del consejo]
```

## Reglas del orquestador

1. **No inventar** críticas: basarse solo en lo que devuelven los subagentes.
2. **Deduplicar** cambios obligatorios similares de varios revisores.
3. **No aplicar** cambios ambiguos sin marcarlos como pregunta al humano.
4. **Registrar** por qué se omitió cada revisor Tier 2.
5. Si `plan-cloud-platform` aplica, asegurar que el subagente consulte documentación oficial vía MCP cuando esté disponible.
6. Mantener el plan **proporcional**: no expandir scope más allá de lo que los revisores exigen.

## Ejemplo de invocación

```
/plan-council

Borrador:
## Objetivo
Añadir gráficos apexcharts en course-detail con datos desde Azure Functions...
```

## Referencias

- Matriz Tier 2: [reviewer-matrix.md](reviewer-matrix.md)
- Subagentes: `~/.cursor/agents/plan-*.md`
