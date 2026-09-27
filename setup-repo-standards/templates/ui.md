# UI / identidad visual

Contrato de **cómo se ve y se usa** este producto. Manda sobre skills de “sé creativo”.

## Fuente de marca

- **Archivo / origen acordado:** {{BRAND_SOURCE}}
- **Estado:** {{BRAND_SOURCE_STATUS}}
  _(contrato | legacy-referencia | ausente — no inventar look; reutilizar estilos ya presentes)_
- Si {{BRAND_SOURCE}} contradice este satélite: gana `docs/standards/*` + `.cursor/rules/*`. Alinear la fuente legacy al tocarla (boy scout), no ignorar el contrato.

## Cómo se implementa en este repo

- **Stack UI detectado:** {{FRONTEND_UI}}
- **Enfoque de estilo:** {{STYLE_APPROACH}}
  _(utility / CSS por componente / CSS-in-JS / tokens file / widgets nativos / mix — el que Explore vio, no uno de moda)_

### Hacer

- Seguir {{BRAND_SOURCE}} (colores, tipo, ritmo, componentes ya definidos).
- Reutilizar primitivas en `ui/` (o la carpeta acordada) cuando el bloque es presentación tonta y se usa en 2+ features — ver feature-first.
- Controles nativos o los del kit del repo; no reinventar un botón/dialog si ya existe.
- Boy scout **en plantillas/estilos tocados** en el mismo cambio.

### No hacer

- Inventar una paleta, tipografía o “sistema” paralelo.
- Copiar patrones visuales de otro framework o de un skill genérico de UI.
- Restyle masivo de pantallas no tocadas.
- Unificar markup con a11y distinta (ver `html-template-dry`).

Profundidad de un design system o pipeline de tokens → invocación explícita de `extend-domain-standards`.

## Copy operador y voz de producto

La identidad visual no cubre solo color/tipografía: el **tono del copy** (labels, empty states, errores) forma parte de la experiencia.

- Errores y estados async mostrados al operador siguen `clean-code.md` (Lenguaje Ubicuo + Copy-as-Code).
- Mensajes dinámicos (toasts, alertas) expuestos a lectores de pantalla: preferir regiones `aria-live` / equivalente del stack para errores importantes.
- No introducir en UI términos de marca técnica del stack (SDKs, proveedores) aunque aparezcan en logs o configs.
- **i18n:** si el producto es multi-idioma, el copy sigue keys estables acordadas en setup; la fuente de marca no sustituye el catálogo tipado por feature.

## A11y operativa (mínimo, agnóstico)

En **toda** superficie nueva o tocada:

- Landmarks / regiones (cabecera, nav, main, pie, o equivalentes de la plataforma).
- Un título de pantalla (`h1` o equivalente nativo) y headings en orden, sin saltar niveles.
- Controles nativos (`button`, `a`, `input`, `label`, o equivalentes de la plataforma) antes que `div` + gestos.
- Toda entrada con label visible o nombre accesible equivalente.
- Nombre accesible en icon-only / controles sin texto.
- Foco visible; no quitar el indicador de foco sin reemplazo equivalente.
- Respetar reduced motion / “menos animación” de la plataforma.

No sustituye una auditoría WCAG de producto. Nivel y tooling concreto → `extend-domain-standards` si el usuario lo pide.

## Belts opcionales (fuera de verify)

No forman parte de `{{VERIFY_COMMAND}}`. _(Si un comando es `omitido`, no lo invoques ni lo inventes; quita la fila.)_

| Belt | Comando | Cuándo |
|------|---------|--------|
| Spec / tokens | {{DESIGN_CHECK_COMMAND}} | Si Section L lo acordó **y** el checker ya existía (o el usuario pidió uno concreto después) |
| A11y automatizada | {{A11Y_CHECK_COMMAND}} | Superficies: {{A11Y_NAMED_SURFACES}}. Fallo: {{A11Y_FAIL_SEVERITY}} _(default: grave/crítica)_ |

Ampliar **una** superficie cuando la lista actual esté limpia. No dump de toda la app.

## Relación con otras rules

| Concern | Dónde |
|---------|--------|
| Dónde vive el markup / DRY | `html-template-dry` + feature-first |
| Extraer paneles/formularios | `unit-composition` |
| XSS / HTML crudo | `client-security` |
| Copy operador / Lenguaje Ubicuo | `clean-code.md` + glosario opcional |
| Marca + a11y mínima | esta página + `ui.mdc` |

## Anti-patrones

- Agente que “mejora” el look ignorando {{BRAND_SOURCE}}
- Tokens duplicados a mano en un cambio puntual
- Desactivar foco, landmarks o labels para cuadrar un diseño
- Meter estos belts dentro de `verify` v1
