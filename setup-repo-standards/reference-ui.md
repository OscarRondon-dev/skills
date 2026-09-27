# UI / a11y — referencia interna del agente

No copiar este archivo al repo. Guía para Explore, Section L y al rellenar plantillas.

## Qué es L

Contrato de **identidad** (cómo se ve el producto) + **mínimo a11y operativo**. No es DRY de plantillas ni XSS (`html-template-dry`, `client-security`). El **copy operador** (Lenguaje Ubicuo, errores accionables) vive en `clean-code.md`; L solo enlaza tono de voz + `aria-live` para mensajes dinámicos.

## Fuente de marca

Una fuente acordada (`{{BRAND_SOURCE}}`) gana sobre skills de “sé creativo”. Inventariar en Explore **no** la canoniza. Estados: contrato | legacy-referencia | ausente (no inventar look; reutilizar lo pintado).

## Belts

Mismo patrón que `verify:security`: **fuera** de `verify` / `verify:standards` v1. Activar solo si el repo **ya tiene** checker de spec/tokens o runner a11y, o si el usuario nombra uno **después**. No instalar por defecto. A11y: superficies **nombradas**; fallo grave/crítico salvo otro umbral; crecer de una en una.

## Plantillas

Placeholders: `{{BRAND_SOURCE}}`, `{{BRAND_SOURCE_STATUS}}`, `{{STYLE_APPROACH}}`, `{{FRONTEND_UI}}`, `{{UI_SURFACE_GLOBS}}`, `{{DESIGN_CHECK_COMMAND}}`, `{{A11Y_CHECK_COMMAND}}`, `{{A11Y_NAMED_SURFACES}}`, `{{A11Y_FAIL_SEVERITY}}`. Si un belt o L se omite: borrar filas, no dejar `{{…}}`.

**Prohibido** en plantillas y defaults de esta skill: nombrar un framework CSS, un motor a11y, un CLI de marca, una ruta de login, un package manager o un cloud.

Profundidad (design system vendor, nivel WCAG de producto, pipeline de tokens) → `extend-domain-standards` con invocación explícita.
