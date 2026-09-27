# Testing

## Runner

- **Target:** {{TEST_RUNNER}}
- **Estado actual:** {{TEST_STATUS}}
- **Layout acordado:** {{VITEST_LAYOUT}} _(config única / projects / otro — lo acordado en setup)_

(JS/TS: Vitest. No adoptar Jest en proyectos nuevos.)

## Qué son los tests aquí

Especificación del comportamiento de dominio, no espejo de la implementación.

## Convenciones

- Nombres en lenguaje de negocio (`creates order when stock is available`).
- Colocar tests cerca del feature (caja), no en un silo global si el stack lo permite.
- Preferir tests de comportamiento sobre tests frágiles de mocks internos.
- **Spec sigue el corte de producción:** si un módulo se parte (orquesta / política / I/O), el test va junto a cada archivo o por concern — no un único spec espejo del god file.
- **`setupFiles`** acordados: {{TEST_SETUP_FILES}} _(p. ej. compilador/framework en FE; mocks de request en BE — del stack detectado)_
- **`include`** por raíz acordada: {{TEST_INCLUDE_GLOBS}}

### Varias raíces de código

Si el repo tiene frontend y backend (u otras raíces) en el mismo package:

| Layout | Cuándo |
|--------|--------|
| Config única + globs | Pocas diferencias de entorno |
| `test.projects` | Distinto `environment` o `setupFiles` por raíz |

No mezclar runners en el target acordado sin OK explícito en un re-bootstrap.

## Cuándo correr qué

| Cambio | Comando |
|--------|---------|
| Pequeño / local | test del archivo o del feature |
| Medio / grande | `{{VERIFY_COMMAND}}` (lint + typecheck + suite completa) |

## A11y automatizada _(si se acordó en setup; quitar esta sección si Section L no activó el belt)_

- **Comando:** {{A11Y_CHECK_COMMAND}}
- **Superficies:** {{A11Y_NAMED_SURFACES}}
- **Fallo en:** {{A11Y_FAIL_SEVERITY}}
- No sustituye el mínimo operativo en `ui.md`. No es parte de `{{VERIFY_COMMAND}}`.
- No añadir superficies nuevas hasta que las actuales estén limpias.

## Guard tests (copy operador y frontera de errores)

Complementan la frontera definida en `clean-code.md`. No sustituyen tests de comportamiento.

### Tres capas (de más fuerte a más débil)

| Capa | Qué verifica | Cuándo |
| --- | --- | --- |
| **Contrato de códigos** | Boundary/API solo emite códigos documentados; desconocidos → fallback o error genérico de dominio | Backend, services de boundary, clientes de API |
| **Mapper** | Cada código tiene copy; fallback existe; mensajes no contienen términos proscritos | Funciones puras `toOperatorMessage*` / equivalente |
| **Regex anti-jerga** | HTML/copy renderizado de vistas críticas sin SDKs, infra ni discriminadores crudos | Frontend; última red de seguridad |

### Lista de términos proscritos

Definir en el repo una lista acordada en setup (ejemplo inicial — **personalizar según stack**):

- Nombres de SDKs/proveedores del stack detectado
- Términos de infra genéricos: `callable`, `payload`, `schema`, `timeout`, `stack trace`
- Identificadores internos expuestos por accidente: `uid`, `_internal`, rutas de archivo

Implementación típica: test unitario con regex sobre strings del catálogo copy y, en vistas críticas, sobre HTML compilado/renderizado.

### Anti-patrones en guard tests

- Test que solo verifica que el mock devuelve el mock
- Regex como única defensa (sin contrato de códigos)
- Assert del `error.message` crudo de una librería en tests de UI

Los guard tests de copy entran en la suite normal (`test` / `verify`). No requieren belt separado salvo acuerdo explícito.

## Anti-patrones

- Tests que solo verifican que un mock fue llamado
- Snapshots opacos sin intención
- Desactivar tests para “hacer merge”
- Asumir el runner del framework CLI si el setup acordó Vitest como target
- Spec espejo de un módulo obeso (un `*.spec` que crece con el god file en vez de seguir el corte)
