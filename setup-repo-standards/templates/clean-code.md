# Clean code

Principios aplicables en este repo ({{STACK_LABEL}}). Ejemplos en el dialecto del proyecto.

## Nombres declarativos

```{{LANG}}
// ❌
const d = get(x)
const flag = true

// ✅
const {{GOOD_NAME_EXAMPLE}}
```

## Funciones pequeñas / SRP

- Una razón para cambiar.
- Si la descripción lleva “y”, partir.
- A nivel de **archivo/clase/host** (page, handler, flow, **service, component, store, registry**): smell *Divergent Change* — si cambiaría por tickets distintos, extraer concerns. El concern extraído puede volver a ser host. Ver [feature-first.md](feature-first.md#composición-de-unidades). Rule: `.cursor/rules/unit-composition.mdc`.

## Presupuesto de módulo

Un slot de caja no es un archivo. Partir **antes de añadir** si se cumple **cualquiera**:

| Situación | Acción |
| --- | --- |
| Archivo de aplicación (no generated) > {{FILE_BUDGET_LINES}} líneas | Partir; carpeta del concepto si deja de caber en un fichero |
| ≥2 trabajos/pasos/agregados nombrables sin “y” | Cada uno → archivo (orquesta / política / I/O) |
| Spec del módulo partido | El test sigue el corte; no un único spec espejo |

`{{FILE_BUDGET_LINES}}` y el lint `max-lines` (warn) se acuerdan en el setup (Section C). Si el setup omitió lint, el umbral documental sigue valiendo. Detalle del corte: [feature-first.md](feature-first.md#cómo-partir-un-módulo-orquesta--política--io).

## Early return

```{{LANG}}
// ❌ anidación profunda por guardas
// ✅ early return / guard clauses
```

(Rellenar con ejemplo real del stack.)

## Un nivel de abstracción

No mezclar I/O de bajo nivel con reglas de negocio en la misma función.

## Flags booleanos

Evitar `fn(data, true)` que cambia el comportamiento → preferir dos funciones con nombre.

## Dispatch por discriminador

Evitar `switch` / `if-else` que crecen por `type`, `status`, `kind` o enum. Smell: *Repeated Switches* (Fowler) — extender obliga a reabrir el mismo bloque en varios sitios.

**Umbral:**

| Situación | Acción |
| --- | --- |
| 1 sitio, ≤2 ramas homogéneas | `switch` / `if` OK |
| ≥3 ramas del mismo discriminador | Mapa + handlers nombrados |
| Mismo discriminador en 2+ sitios (build, validate, route…) | **Un** mapa compartido |

```{{LANG}}
// ❌ mismo árbol de casos repetido
function build(kind: Kind, data: unknown) {
  switch (kind) { case 'a': return buildA(data); case 'b': return buildB(data); }
}
function validate(kind: Kind, payload: unknown) {
  switch (kind) { case 'a': return schemaA.parse(payload); case 'b': return schemaB.parse(payload); }
}

// ✅ registry compartido
const HANDLERS = {
  a: { build: buildA, validate: schemaA.parse },
  b: { build: buildB, validate: schemaB.parse },
} as const satisfies Record<Kind, { build: Builder; validate: Validator }>;
```

En stacks con control flow declarativo de UI (p. ej. `@switch`, `match` en template), el smell aplica a **lógica de aplicación repetida**, no a un único switch de presentación.

## Comentarios

Renombrar > comentar lo obvio. Comentarios solo para *por qué* no obvio.

## Copy-as-Code y Frontera de Errores (Lenguaje Ubicuo)

- **Alcance (migrate-on-touch):** aplica estricto a código nuevo o al alcance tocado de una feature. No exige refactor masivo de legacy no modificado (boy scout al tocar).
- **Lenguaje Ubicuo en UI:** la interfaz y los mensajes de operador hablan el vocabulario del dominio acordado con producto. Prohibido exponer jerga técnica, nombres de librerías/SDKs, stack traces, códigos HTTP crudos o estados/discriminadores del backend sin mapear en vistas, toasts, modales o estados de carga.
- **Catálogo de textos tipado (*Copy-as-Code*):** centralizar textos visibles por feature en catálogos tipados (`as const`, mapas `Record<Estado, string>`). Prohibido hardcodear cadenas de negocio en templates. Incluye errores **y** estados de UI (loading, empty, partial success, stale).
- **Frontera de errores (*operator message boundary*):** los fallos de red, base de datos o servicios externos se transforman en mensajes de negocio **seguros y accionables** antes de la presentación. Preferir contrato estable `{ code, retryable?, traceId? }` → mapper puro; evitar parsear `error.message`. Detalle técnico (stack, provider, rutas) solo en logs server-side.
- **Correlación de errores (referencia de soporte):** los fallos técnicos exponen un `traceId` **opaco** (id de invocación/operación del runtime) para que soporte lo busque en logs/telemetría; los errores de negocio esperados (validación, credenciales, permisos) **no** llevan referencia. La UI pinta `(ref: …)` **solo** en fallos técnicos, nunca en un 4xx de validación.
- **Relación con seguridad:** mensajes de negocio concretos **no contradicen** "errores genéricos al cliente" de `security.md` — esa regla prohíbe filtrar implementación (stack, rutas, secrets), no exige ocultar el contexto de negocio al operador.
- **ACL de adapters (distinto):** traducir JSON/API de terceros a modelos canónicos internos vive en la capa I/O; no mezclar con mappers de copy operador (ver `feature-first.md`).
- **Idioma del copy e i18n:** mono-idioma → catálogo tipado basta. Multi-idioma actual o previsto → keys estables desde el inicio (`feature.actionError`); migrar a i18n del stack cuando corresponda. Identificadores de código pueden estar en inglés; copy visible en el idioma del producto.
- **i18n existente:** integrar (alimentar pipeline actual); no reemplazar locales, selector ni librería sin OK explícito.
- **Glosario de dominio (opcional):** si el vocabulario es rico, mantener `domain-glossary.md` como fuente de verdad término interno ↔ término UI.
- **Pruebas de candado (*Guard tests*):** (1) contrato de códigos conocidos, (2) mapper con fallback, (3) regex anti-jerga en vistas críticas como red de seguridad — ver `docs/standards/testing.md`.

### Ejemplo mínimo (adaptar al stack)

```{{LANG}}
// copy.ts — catálogo
export const FEATURE_COPY = {
  pressError: 'No se pudo completar la búsqueda de prensa. Puedes reintentarlo.',
  statusRunning: 'Preparando informe…',
} as const;

export const STATUS_LABELS: Record<'pending' | 'running' | 'done', string> = {
  pending: 'Pendiente',
  running: FEATURE_COPY.statusRunning,
  done: 'Completado',
};

// errors.ts — frontera (input estable, no parseo de strings)
const OPERATOR_MESSAGES: Record<string, string> = {
  PRESS_SEARCH_TIMEOUT: 'La búsqueda de prensa tardó demasiado. Puedes reintentarlo.',
};

export function toOperatorMessage(
  error: { code?: string },
  fallback: string,
): string {
  if (error.code && OPERATOR_MESSAGES[error.code]) {
    return OPERATOR_MESSAGES[error.code];
  }
  return fallback;
}
```
