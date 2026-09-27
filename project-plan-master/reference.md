# Referencia — project-plan-master

## Campos del brief usados

| Campo | Uso en plan maestro |
|-------|---------------------|
| `project_name`, `project_slug` | Título y nombre de archivo |
| `objective`, `mvp_summary` | Bloque introductorio |
| `doc_language` | Idioma del markdown |
| `stack.*` | Contexto, convenciones, §6 |
| `architecture.*` | Layouts, reglas, patrón |
| `domain.concepts` | Tabla dominio |
| `domain.security_invariants` | Invariantes + STRIDE |
| `agent_docs.reference_docs` | Lista docs de entrada |
| `plan_template.*` | Salida y secciones condicionales |

## Placeholders — listas y tablas

### `{{domain_concepts_table}}`

Una fila por `domain.concepts[]`. Si vacío: una fila `_Definir en BOOTSTRAP_BRIEF_`.

### `{{reference_docs_list}}`

Bullets `- **path** — purpose`. Si vacío: solo `BACKEND.md` / `DATABASE.md` según stack.

### `{{security_invariants_list}}`

Bullets desde `domain.security_invariants[]`. Si vacío: bullets genéricos desde `architecture.testing_policy` o «validar inputs en boundary HTTP».

### `{{dependency_rules_block}}`

Una regla por línea desde `architecture.dependency_rules[]`. Si vacío, la plantilla ya incluye reglas Feature-First por defecto.

### `{{proportional_depth_paragraph}}`

Si `plan_template.proportional_depth_note: true`:

> La profundidad del plan debe ser **proporcional** al `acceptance`. No imponer STRIDE exhaustivo, migración arquitectónica completa ni E2E en fixes puntuales.

Si `false`: cadena vacía.

## Placeholders — convenciones de stack (generar desde brief, no hardcodear)

Rellenar al generar el plan. **No** copiar rutas de un repo concreto (Academy, Azure, Angular) salvo que el brief las indique.

### `{{frontend_conventions_block}}`

Construir desde `stack.frontend` + `project_type`:

```markdown
**Stack UI:** {stack.frontend}

| Regla | Detalle |
|-------|---------|
| Estructura | features/[dominio]/{pages, components, services, lib, models} |
| Estado | Patrón reactivo del framework (signals, hooks, etc.) |
| Routing | Lazy load por dominio si el stack lo soporta |
| Tests | Unitarios junto al componente; ver AGENTS.md |
```

Si `project_type` es `api_only` o `cli_library`: bloque mínimo «N/A — sin frontend».

### `{{backend_conventions_block}}`

Desde `stack.backend` + `stack.database`:

```markdown
**Stack servidor:** {stack.backend}

| Regla | Detalle |
|-------|---------|
| Entrypoint | Ver BACKEND.md del repo |
| Validación | Esquema en boundary HTTP antes del servicio |
| Persistencia | {stack.database o N/A} |
```

### `{{persistence_conventions_block}}`

Solo si `stack.database` no es null:

```markdown
**Motor:** {stack.database}

- Singleton de conexión documentado en BACKEND.md / DATABASE.md
- Índices: revisar plan de ejecución antes de crear nuevos
- Multi-tenant: filtro en servidor si aplica al dominio
```

### `{{frontend_test_conventions}}`

Genérico:

```markdown
- Ejecutar suite de tests frontend según AGENTS.md
- Arrange → Act → Assert; esperar estabilidad async si aplica
- Mockear HTTP; no mockear auth en tests Hardening
```

Añadir detalles del brief (`architecture.testing_policy`) si los hay.

### `{{e2e_notes_block}}`

Genérico:

```markdown
**Herramientas:** suite E2E del repo si existe (ver AGENTS.md).
**Credenciales:** no hardcodear en código ni docs versionados; usar env local de ejemplo.
**Servidores:** no levantar duplicados si el usuario ya tiene dev activo.
```

### `{{local_dev_commands_block}}`

Desde `stack` del brief — **ejemplo genérico**:

```markdown
- Verificación: `{verify_command}`
- Dev frontend/backend: documentar en AGENTS.md cuando existan scripts
```

No inventar puertos ni paths (`localhost:7072`, `ng serve`) salvo que el brief o AGENTS.md los definan.

## Sección STRIDE (`{{stride_section_content}}`)

### Si `include_stride: true`

```markdown
Vectores: **S**poofing · **T**ampering · **R**epudiation · **I**nformation Disclosure · **D**oS · **E**levation of Privilege.

Para cada vector aplicable: Vector → Superficie → Mitigación → Archivo/capa → Test Hardening (`it('...')`).

**Superficies habituales** (adaptar al dominio del proyecto):

| Superficie | Vectores típicos |
|------------|------------------|
| Auth / sesión | S, E |
| Multi-tenant / aislamiento de datos | I, E |
| APIs con roles distintos | S, E, I |
| Entrada de usuario / uploads | T, I |
| (_añadir filas desde domain.concepts_) | |

**Mitigaciones alineadas al proyecto:**

- Validación exportada + parse seguro en boundary HTTP
- Auth y RBAC antes de lógica de negocio
- Filtro de tenant en queries si aplica
- Errores genéricos al cliente; detalle en logs
- Secretos en variables de entorno, nunca en repo

**Entregable:** matriz STRIDE + lista de `it(...)` Hardening nombrados.

Referencia: `progress/*-threat-model.md` si existen.
```

### Si `include_stride: false`

Usar versión acotada (proporcional; matriz breve o N/A en features triviales).

## Arquitectura Feature-First

La plantilla incluye **árbol de decisión** y **tabla de capas** como doctrina genérica del patrón — no son hardcode de un repo.

Los **árboles de carpetas** concretos vienen **solo** de:

- `architecture.frontend_layout`
- `architecture.backend_layout`

definidos en el `BOOTSTRAP_BRIEF` (entrevista harness-architect).

Si el brief trae layouts vacíos o «por definir», el agente debe proponer layouts Feature-First acordes a `project_type` y pedir confirmación antes de escribir el archivo final.

## Uso posterior (no es esta skill)

```text
@plan-{project_slug}.md

Planifica la feature ID [FEATURE_ID].
```
