# Clean Code principles (language-agnostic)

Base: ideas de *Clean JavaScript* (Miguel A. Gómez), aplicables a cualquier lenguaje. Usar al generar `docs/standards/clean-code.md` y la rule `clean-code.mdc`. Ejemplos siempre en el **stack del repo**.

## Naming

- Nombres **declarativos**: revelan intención (`isExpired`, `calculateTotal`, no `flag`, `data`, `temp`).
- Funciones = verbos/frases verbales; booleanos = `is`/`has`/`can`/`should`.
- Evitar abreviaturas opacas y nombres genéricos (`handle`, `process`, `util` sin contexto).

## Functions

- **Una responsabilidad** (SRP). Si la descripción necesita “y”, partir.
- **Un nivel de abstracción** por función.
- Preferir **early return** a anidación profunda.
- Evitar flags booleanos que cambian el comportamiento de punta a punta → funciones separadas.
- Pocos parámetros; agrupar en objeto/tipo con nombre si crecen.

## Structure

- Código legible de arriba abajo; side-effects explícitos.
- No comentar lo obvio; renombrar en su lugar.
- Duplicación estructural → extraer con nombre de dominio, no “utils” basura.

### Variant dispatch (Repeated Switches)

Smell (Fowler): el mismo `switch` / `if`-cascade sobre un discriminador (`type`, `status`, `kind`, enum) en **varios sitios**, o un árbol de ramas que crece con cada variante nueva → violación de **Open/Closed** (extender = modificar el núcleo).

**Fix:** un **registry map** (`Record<Discriminator, Handler>`) o **Strategy** (objeto/clase por variante) compartido por build, validate, route, etc. Handlers con nombre de dominio (`handlePending`, `buildPayloadForKindA`).

**Umbral operativo:**

| Situación | Acción |
| --- | --- |
| 1 sitio, ≤2 ramas homogéneas | `switch` / `if` aceptable |
| ≥3 ramas del mismo discriminador en un sitio | Extraer mapa + handlers |
| Mismo discriminador en 2+ sitios | Un mapa compartido; no duplicar el árbol de casos |
| Control flow declarativo de UI (`@switch`, `match` exhaustivo en vista) | OK para montar presentación; no duplicar la lógica de negocio en otro sitio |

Rule dedicada: `discriminator-dispatch.mdc`. Detalle en `docs/standards/clean-code.md`.

### Unit composition (Divergent Change)

Smell (Fowler): un **host** (page, route view, handler, flow, **service, component, store, registry**) cambia por motivos distintos — modal + formulario + búsqueda + grid, o un service con start/continue/persist/prompts en el mismo archivo. SRP de función no basta.

**Fix:** **Compositor** orquesta (ruta, agregado, cableado). **Concern** = un trabajo nombrable sin “y”, con estado o ciclo de vida propio → extraer **aunque se use una vez**. Composición **recursiva**: el concern extraído puede ser host después. Un slot de caja no es un archivo.

**Umbral operativo:**

| Situación | Acción |
| --- | --- |
| 1 trabajo + lista homogénea | Queda en el compositor (loop DRY) |
| ≥3 clusters de estado independientes | Partir en concerns |
| ≥2 overlays / diálogos / paneles con ciclo propio | Cada uno es un concern |
| ≥2 pasos nombrables en service / flow / store | Cada paso no trivial → archivo |
| Archivo de aplicación sobre presupuesto (acordado en setup, default típico 300) | Partir **antes** de añadir |

**Corte al partir:** orquesta (facade) / política (reglas puras) / I/O (persistencia, HTTP, LLM, adapters → modelo canónico). Copy operador y mappers de error → presentación/boundary, no I/O. Bajo presupuesto → un archivo; al partir → carpeta del concepto. El spec sigue el corte.

**Contrapeso DRY:** la escalera de markup homogéneo (loop, 2+ sitios) **no** impide extraer concerns heterogéneos. Sin este contrapeso, los agentes meten todo en un solo `.html`/`.tsx`.

Rule dedicada: `unit-composition.mdc` (+ opcional `html-template-dry.mdc` en SPA). Detalle en `docs/standards/feature-first.md`. No crear una rule extra `fat-module.mdc`.

## Feature boundaries

- Lógica de un dominio vive **dentro de su caja feature**.
- `core` / `ui` solo para lo genuinamente compartido (auth global, design system, config).
- Al tocar código legacy fuera de caja: boy scout — acercarlo a feature-first en el mismo cambio, sin Big Bang.
- El layout puede ser capas o módulos; el corte compositor/concern sigue aplicando aunque la carpeta no se llame `features/`.

## Testing

- Tests = especificación del comportamiento, no espejo de implementación.
- Nombres de test en lenguaje de dominio.
- JS/TS: **Vitest** como runner target (no Jest).
- Cubrir caminos felices y bordes relevantes; evitar tests que solo afirman mocks.
- `verify` corre la suite completa en cambios medios/grandes.
- Guard tests de copy operador: contrato de códigos, mappers con fallback, regex anti-jerga como última línea — ver § Copy-as-Code.

## Copy-as-Code y frontera de errores (Lenguaje Ubicuo)

Base: ideas de DDD (*Ubiquitous Language*) + separación entre lo que sabe la infraestructura y lo que ve el usuario final. Usar al generar `docs/standards/clean-code.md` y al reforzar la rule `clean-code.mdc`. Ejemplos siempre en el **stack del repo**.

### Cuándo aplica

| Contexto | Aplicación |
| --- | --- |
| App con usuarios no técnicos (operadores, analistas, staff de negocio) | **Recomendado** — estándar por defecto |
| Dashboard técnico / admin de infra | Relajar copy operador; mantener logs técnicos |
| MVP mono-idioma, un solo flujo | Copy-as-Code mínimo en la feature tocada |
| Producto multi-idioma desde día 1 | Copy-as-Code con keys estables; planificar i18n (ver § Idioma del copy) |

**Alcance (migrate-on-touch):** aplica estricto a código **nuevo** o al **alcance tocado** de una feature. No exige refactor masivo de legacy no modificado (boy scout al tocar).

### Lenguaje Ubicuo en UI

- La interfaz y los mensajes de operador hablan el **vocabulario del dominio** acordado con producto/negocio.
- Prohibido exponer en vistas, toasts, modales o estados de carga:
  - nombres de librerías, SDKs o proveedores externos;
  - terminología de infraestructura (`timeout`, `schema`, `payload`, códigos HTTP crudos, stack traces);
  - claves internas, IDs técnicos o discriminadores crudos del backend sin mapear.
- Los **identificadores de código** (variables, tipos, APIs) pueden permanecer en inglés; el **copy visible** sigue el idioma acordado del producto.

### Copy-as-Code (catálogo tipado)

- Centralizar textos visibles por feature en un catálogo tipado (p. ej. `{feature}/copy.{ext}` o `{feature}/models/copy.{ext}` según convención del stack).
- Usar constantes `as const` / equivalente idiomático y mapas `Record<Estado, string>` (o enum + mapa) para estados, badges, empty states y mensajes recurrentes.
- Prohibido hardcodear cadenas de negocio en templates/vistas.
- Prohibido interpolar discriminadores crudos; siempre pasar por función o mapa del catálogo (`labelForStatus(status)`).

**Estados de UI, no solo errores:** loading, empty, partial success, stale, disabled — todo pasa por el catálogo, no solo los fallos.

### Frontera de errores (presentación al operador)

> **No confundir con seguridad:** `security.md` prohíbe filtrar stack traces, rutas internas, detalle de infraestructura y datos sensibles al cliente. Esta frontera **sí** permite mensajes de negocio concretos y accionables, siempre que sigan siendo seguros (sin revelar implementación).

**Regla:** los errores de red, base de datos, colas o servicios externos **no llegan crudos** a la capa de presentación.

**Contrato preferido (input estable):**

```ts
// Backend / boundary — forma canónica (adaptar al lenguaje del repo)
type OperatorFacingError = {
  code: string;           // estable, documentado, p. ej. 'PRESS_SEARCH_TIMEOUT'
  retryable?: boolean;
  traceId?: string;       // id de correlación del sistema — solo en fallos técnicos
};

// Presentación — mapper puro
const OPERATOR_MESSAGES: Record<string, string> = {
  PRESS_SEARCH_TIMEOUT: 'La búsqueda de prensa tardó demasiado. Puedes reintentarlo.',
  PRESS_SEARCH_UNAVAILABLE: 'No se pudo completar la búsqueda de prensa.',
};

function toOperatorMessage(error: OperatorFacingError, fallback: string): string {
  return OPERATOR_MESSAGES[error.code] ?? fallback;
}
```

**Anti-patrón:** parsear `error.message` con `includes('timeout')`, nombres de SDKs, etc. Solo aceptable como puente temporal en legacy; marcar deuda y migrar a códigos.

**Criterio de calidad del mensaje:** cada copy de error debería responder, cuando sea posible:
1. **Qué pasó** (en lenguaje de negocio).
2. **Qué puede hacer el usuario** (reintentar, revisar datos, contactar soporte).
3. **Si es recuperable** (`retryable` → toast vs modal bloqueante).

**Observabilidad:** detalle técnico completo (provider, stack, query) → **solo logs server-side / telemetría**, nunca UI.

### Correlación de errores (referencia de soporte)

Distinguir **fallo técnico** de **error de negocio esperado**:

- **Fallo técnico (p. ej. 5xx):** exponer al cliente un **identificador de correlación opaco** (`traceId`) tomado del id de invocación/operación del runtime. No es stack ni detalle de infraestructura: es la etiqueta que soporte busca en logs/telemetría. La UI lo pinta como referencia: `Error interno… (ref: <traceId>)`.
- **Error de negocio esperado (p. ej. 4xx: validación, credenciales, permisos):** **sin** referencia — es un resultado normal, no un bug; pintar `(ref: …)` sería ruido. El boundary puede incluirla en el body, pero la UI **no** la muestra.

Regla de UI: **pintar la referencia solo cuando el fallo es técnico**, nunca en un error de negocio.

> Matiz sobre «nunca UI»: lo que nunca cruza es el **detalle** (stack, provider, rutas, query). El **id opaco de correlación** sí puede exponerse — es lo que permite localizar el fallo sin adivinar por hora/usuario.

Guard test: un fallo técnico lleva `traceId`; un error de negocio no; la vista no pinta ref en un 4xx.

### Dos fronteras distintas (no mezclar)

| Frontera | Traduce | Vive típicamente en |
| --- | --- | --- |
| **Errores → copy operador** | `PRESS_SEARCH_TIMEOUT` → mensaje accionable | boundary API, services de presentación, mappers puros |
| **Datos externos → dominio (ACL de adapters)** | JSON de tercero → modelo canónico interno | capa I/O / adapters (ver `feature-first.md` § corte orquesta/política/I/O) |

Usar el término **ACL** preferentemente para adapters/datos. Para errores, preferir **frontera de errores** u **operator message boundary**.

### Idioma del copy e i18n

- Detectar idioma del producto en Explore (Principio de idioma en la skill).
- **Mono-idioma:** Copy-as-Code con catálogo tipado es suficiente.
- **Multi-idioma (actual o previsto):** diseñar **keys estables** desde el inicio (`preMatch.pressError`) aunque el catálogo empiece en un solo locale; evitar strings sueltas imposibles de migrar. El mecanismo concreto (archivos por locale, librería i18n del stack) se acuerda en setup — la skill no impone un vendor.
- **Idioma híbrido habitual:** identificadores/APIs en inglés; copy UI y errores de operador en el idioma del producto.

| Estado del repo | Patrón |
|-----------------|--------|
| Sin i18n | Copy-as-Code mono-idioma |
| Multi-idioma previsto | Keys estables; catálogo tipado |
| **i18n ya activo** | **Integrar:** catálogo = origen; pipeline existente = entrega; no reemplazar |
| Reemplazo pedido | Plan explícito + `extend-domain-standards` |

### Glosario de dominio (opcional)

Si el producto tiene vocabulario propio, proponer `docs/standards/domain-glossary.md`:

| Término interno / API | Término UI | Notas |
| --- | --- | --- |
| `player_ingest` | Captación de jugador | |
| `corpus` | Plantilla reunida | No confundir con término técnico homónimo |

### Guard tests (candado)

Complementar la frontera con tests en tres capas:

1. **Contrato:** la API/boundary solo emite códigos conocidos (deny-list de códigos no mapeados).
2. **Mapper:** cada código documentado tiene copy; existe fallback para desconocidos.
3. **Regex anti-jerga (última línea):** en vistas críticas, verificar que HTML/copy renderizado no contenga términos técnicos proscritos acordados en el repo.

La regex **no sustituye** contrato + mapper; evita regresiones cuando alguien filtra jerga a la UI.

Rule operativa: bullet en `clean-code.mdc`. Detalle en `docs/standards/clean-code.md` y `docs/standards/testing.md`.

## Safety belt

- Linter restrictivo + typecheck estricto + tests: el entorno corrige a la IA.
- No debilitar reglas “porque el legacy no pasa”: planificar endurecimiento con el usuario.
