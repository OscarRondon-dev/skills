# Feature-first

> **Nota:** *Feature-first* = cajas de **capacidad de negocio**, no la carpeta `features/`. Si el repo usa capas (`presentation/`, `domain/`, …) o módulos, mapear **compositor / concern / política / I/O** sin proponer reestructuración masiva.

Organización por **cajas de capacidad de negocio**, no por tipo técnico global.

## Frontend ({{FRONTEND}})

**Dentro de cada feature** (lo que el framework permita): pages, components, services, models, strategies, router, lazy loading, tests, etc.

**Fuera (compartido real):**

- `ui` — design system / primitivas visuales reutilizables
- `core` — infra global (auth, config, HTTP factory, i18n…)
- Otros shared solo si ≥2 features los usan de verdad

```
{{FEATURE_TREE_EXAMPLE}}
```

**DRY de UI (escalera)** — cuando el stack use plantillas declarativas:

1. Un solo host, lista homogénea → array/config + loop en esa vista. No crear componente nuevo.
2. El mismo bloque en 2+ sitios de la **misma caja** → `components/` de la feature.
3. El mismo bloque en 2+ **features** y es presentación tonta → `ui/`.

**DRY no impide extraer un concern heterogéneo usado una sola vez.** La escalera evita duplicar markup homogéneo. Un trabajo de usuario distinto (panel, overlay, formulario, búsqueda, diálogo) con estado propio **sí** se extrae aunque viva en una sola ruta. Ver [Composición de unidades](#composición-de-unidades).

## Backend ({{BACKEND}})

**Forma acordada en setup:** {{RUNTIME_SHAPE}} _(monolito, serverless, BFF, transitorio, etc. — lo que el usuario confirmó; no inferir vendor)_

**Dentro de cada feature/caja:** modelos, schemas, handlers/controllers, middleware propio, acceso a BD del dominio, tests.

**Fuera:** `core` (config, logging, DB connection, auth global) + pocos shared justificados.

- **Entrypoints limpios (anti Fat Entrypoint):** El archivo raíz del servidor (p. ej. `index.ts`, `main.ts`, `app.ts`) debe actuar exclusivamente como orquestador / manifiesto de re-exportación o montaje de rutas (`export * from ...` o `app.use('/ruta', featureRouter)`). Queda prohibido escribir handlers, lógica de negocio o wrappers repetitivos directamente en el archivo de entrada raíz; cada feature empaqueta sus propios endpoints/handlers completos.

```
{{BACKEND_TREE_EXAMPLE}}
```

Si hay capa servidor, el detalle acordado vive en [runtime.md](runtime.md) cuando exista. Para reglas oficiales de un producto concreto → invocación explícita de `extend-domain-standards`.

## Composición de unidades

Smell: *Divergent Change* (Fowler) — un archivo/clase cambia por motivos distintos. SRP de función no basta: aplica al **host** (page, route view, handler, flow, service, component, store, registry).

**Compositor** orquesta la ruta o el caso de uso. **Concern** es un trabajo de usuario nombrable **sin “y”**, con estado o ciclo de vida propio. Extraer un concern **aunque se use una vez**. Un concern extraído **puede volver a ser host** (composición recursiva). Un slot de caja (`services/`, `models/`, `components/`) es una **carpeta de archivos**, no un fichero.

| Capa | Compositor (orquesta) | Concern (un trabajo) |
| --- | --- | --- |
| Frontend | entry de ruta (p. ej. `pages/`, `routes/`, `views/`) — identidad, carga/guardado del agregado, cablea hijos | `components/` de la feature — panel, overlay, formulario, búsqueda, diálogo |
| Backend | handler/controller — auth, parse, delegar | facade del caso de uso (delgada); cada paso / política / I/O es **archivo** en la caja — no un único `*.service` |
| Orquestación | flow / job / pipeline padre | tool / step / módulo reusable acordado en runtime |

El compositor se queda con: identidad (ruta, request, session), load/save del agregado, composición. Cada concern posee su estado efímero y emite comandos hacia arriba (o habla con el store de la caja).

**Extraer** si se cumple **cualquiera**:

- El trabajo se nombra sin “y”.
- Tiene estado efímero propio (abierto/cerrado, query, draft, tab de un subflujo).
- Cambiaría por un ticket distinto al del host.

**No extraer:**

- Lista homogénea → loop en plantilla (escalera DRY).
- Fragmento mínimo sin estado.
- Extraer obligaría a un saco de inputs/outputs para unas pocas líneas.

**Umbral:**

| Situación | Acción |
| --- | --- |
| 1 trabajo + lista homogénea | Queda en el compositor |
| ≥3 clusters de estado independientes en un host | Partir |
| ≥2 overlays / diálogos / paneles con ciclo de vida propio | Cada uno es un concern |
| ≥2 pasos nombrables en service / flow / store / registry | Cada paso no trivial es un concern (archivo) |
| Archivo de aplicación sobre presupuesto (`clean-code.md`) | Partir **antes** de añadir |

### Cómo partir un módulo (orquesta / política / I/O)

No basta con “sacar un service”. Al partir **cualquier** host (incluido un concern que creció):

1. **Orquesta** — facade / compositor: identidad, orden, cableado.
2. **Política** — reglas puras, decisiones, validación de dominio.
3. **I/O** — persistencia, HTTP, LLM, filesystem, **adapters externos → modelo canónico (ACL de datos)**. El copy visible al operador y los mappers de error viven en **presentación/boundary**, no mezclados con adapters de datos.

Bajo presupuesto → un archivo. Al partir → **carpeta del concepto** (`session/session.ts` + `persist.ts` + `prompts.ts`), no otro archivo dios al lado. El spec **sigue el corte** (ver [testing.md](testing.md)).

Código nuevo y alcance tocado (boy scout). No big-bang de hosts legados.

Rule operativa: `.cursor/rules/unit-composition.mdc`.

## Boy scout (obligatorio al tocar código)

1. Si editas un archivo “huérfano” en carpetas técnicas globales, muévelo/acércalo a su feature cuando sea seguro en ese cambio.
2. No crear árboles `features/*` vacíos “por si acaso”.
3. No migrar de framework; solo la forma de organizar *dentro* del stack actual.

## Anti-patrones

- `components/`, `services/`, `models/` gigantes sin dueño de dominio
- Meter todo en `core`/`shared` “por comodidad”
- Copiar estructura de otro framework
- Entrypoint raíz obeso (*Fat Entrypoint*) con lógica o wrappers de transporte duplicados inline
- Host de UI o handler que acumula varios trabajos de usuario con estado propio (*Divergent Change* / *Fat Host*)
- Un `*.service` / use-case / store / `models.ts` / registry que acumula pasos, I/O y política (*Fat Module* / *Fat Service*)
- Composición de un solo salto: extraer un concern y dejarlo crecer sin volver a aplicar el umbral
