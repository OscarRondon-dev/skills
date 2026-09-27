---
name: setup-project-stack
description: >-
  Propose and install a product-shaped JS/TS dependency stack (Angular,
  React/Next, Nest/Node): product grilling, need catalog, npm latest stable +
  OSV gate, then docs/stack.md and installs. Use when the user asks for
  setup-project-stack, bootstrap libraries, starter deps, which packages to
  install, or a safe library stack for a new or existing app.
disable-model-invocation: true
---

# Setup Project Stack

Prompt-driven bootstrap (como `setup-matt-pocock-skills` / `setup-repo-standards`). **Explorar → perfil de producto → needs ON/OFF → gate → borrador → escribir.** No es un `npm i` de moda.

El stack **sale de la idea**. El [catálogo](catalog.md) es el default **cuando el need está encendido**, no el techo ni el mínimo. Se pueden proponer libs que no estén en la lista del usuario si el producto las pide.

## Objetivo

Dejar en el repo:

| Capa | Artefacto |
|------|-----------|
| Contrato | `docs/stack.md` (need → paquete@version → porqué → gate) |
| Código | installs con el package manager del repo, **solo** tras OK explícito |

## No hace

- Lint, tests, Vitest, ESLint, `.cursor/rules/`, `docs/standards/*` → `setup-repo-standards`
- Issue tracker, `CONTEXT.md`, ADRs, `docs/agents/*` → `setup-matt-pocock-skills`
- Auditar o parchear código; migrar de framework; CI remoto
- Profundidad vendor/cloud → `extend-domain-standards` solo si el usuario lo pide
- Instalar las 10 libs favoritas por inercia

## Principios

1. **Need, no marca.** Preguntar necesidades; el paquete es un mapeo por ecosistema.
2. **YAGNI.** “Estaría chulo” = skip. Documentar el hueco, no la dependencia.
3. **Existente manda.** Si una dep ya cubre el need, no sustituir (p. ej. `date-fns` vs Temporal) salvo que el usuario lo pida.
4. **Framework detectado.** Ejemplos y paquetes solo de Angular, React/Next o Nest/Node. Otro ecosistema → preguntar; no inventar.
5. **Preferencias del usuario** (cuando el need está ON y el paquete encaja): Zod, Temporal, TanStack Table, better-auth, Motion, Fontsource, Chart.js, Zustand, pragmatic-drag-and-drop, nuqs. Si no encaja (Zustand en Angular, nuqs en Nest), usar la columna del catálogo.
6. **Versiones en vivo.** Nunca fiarse de una versión escrita en esta skill. Resolver estable en npm y pasar el gate.
7. **Gate = avisar y preguntar.** CVE, abandono o `postinstall` raro → no instalar en silencio; presentar ficha y esperar.
8. **Idioma.** Docs existentes; si no hay, español.

## Process

```
Task Progress:
- [ ] 1. Explore
- [ ] 2. Present findings
- [ ] 3. Oleada 0 — perfil (Q1–Q5 + extras)
- [ ] 4. Confirmar perfil cerrado
- [ ] 5. Oleada 1 — seguridad (needs ON)
- [ ] 6. Oleada 2 — núcleo
- [ ] 7. Oleada 3 — UX (puede ser todo skip)
- [ ] 8. Gate (versión + OSV + mantenimiento)
- [ ] 9. Draft
- [ ] 10. Write (solo con OK)
- [ ] 11. Done
```

Una oleada por mensaje. En cada una: tabla need → paquete recomendado → skip/ya cubierto. **Recomendación en negrita.** No escribir aún.

### 1. Explore

Leer, no asumir:

- `package.json`, lockfile (`pnpm-lock.yaml`, `package-lock.json`, `yarn.lock`)
- Señales de framework: `angular.json`, `next.config.*`, `nest-cli.json`, deps
- FE / API / ambos; package manager
- `docs/stack.md` (run previo)
- Deps actuales → mapear a needs del [catálogo](catalog.md) (cubierto / hueco)
- Idioma de README/docs

Si no hay repo o no hay `package.json`, parar: hace falta un proyecto JS/TS. No hacer `npm init` salvo que el usuario lo pida.

Si el framework no es Angular, React/Next ni Nest/Node: decirlo y preguntar cómo describirlo. No rellenar el catálogo con React “por defecto”.

### 2. Present findings

Resumen corto: framework, package manager, needs ya cubiertos, huecos. Luego oleada 0.

### 3. Oleada 0 — perfil

No propone paquetes. Corta libs. Formato:

```
❓ **Q1** — **<título>**: <cuerpo + opciones>

➡️ <recomendación>
```

**Ronda 1 — siempre estas cinco:**

❓ **Q1** — **¿Quién entra el día 1?**  
nadie (sin login) / invitados que tú creas / cualquiera se registra / SSO corporativo obligatorio.

➡️ Si no está claro: **invitados que tú creas**. Registro abierto mete más libs y más ataque.

❓ **Q2** — **¿Lo puede tocar un desconocido en internet?**  
sí / no (VPN, intranet, localhost).

➡️ “Luego ya veremos” cuenta como **sí**.

❓ **Q3** — **¿Un usuario puede publicar texto que otro usuario va a ver?**  
no / markdown / HTML rico / comentarios.

➡️ **no**, hasta que el diseño lo exija.

❓ **Q4** — **¿Hay dinero, stock, o una cifra que no puede redondear mal?**  
no / sí.

➡️ **sí** solo si un contable miraría ese campo. Un dashboard con totales no basta.

❓ **Q5** — **Describe la primera pantalla útil.** ¿Qué pasa si enviamos sin gráficas, sin drag-and-drop y sin animación?

➡️ Si no hay pantalla, **no hay producto**: oleada 3 no instala. Si “no pasa nada / se ve menos bonito” → esa lib **fuera**.

**Extras (opcionales):** hasta **2** por ronda, además de las fijas en ronda 1. Solo si se cumple todo:

1. Sale de lo que el usuario ya dijo o de una señal del repo (deps, rutas, README).
2. La respuesta enciende, apaga o cambia un paquete, un skip, o un hueco en `docs/stack.md`.
3. Misma forma (pregunta + recomendación + qué need mueve).
4. Si el producto ya está claro, **cero extras**. Si dudas, no preguntes.

Ronda 2+ solo ramas encendidas (auth cómo, roles, filas reales de la lista, URL compartible, timezones, email el día 1, etc.). Un extra no abre un árbol infinito.

Hechos del filesystem: míralos tú; no preguntes lo que se puede leer.

### 4. Perfil cerrado

Antes de paquetes, un bloque que el usuario confirma. Ejemplo de forma:

```markdown
- usuarios: invitados | amenaza: internet | HTML untrusted: no
- dinero: no
- pantalla 1: …
- oleada 3: tables ON / charts OFF / dnd OFF / motion OFF
```

Sin confirmación, no pasar a oleada 1.

### 5–7. Oleadas de paquetes

Leer [catalog.md](catalog.md). Incluir solo needs **ON**. Preferencia del usuario si encaja; si no, columna del framework.

- **Oleada 1 — seguridad:** validación, env, sanitizar, auth, endurecido HTTP, dinero, autorización.
- **Oleada 2 — núcleo:** Query, estado cliente, estado URL, fechas, forms, HTTP client.
- **Oleada 3 — UX:** tablas, charts, dnd, motion, fuentes, iconos/toasts solo si un extra o Q5 los encendió.

“Más cosas según la idea” = needs extra del catálogo o una lib justificada. No un buffet. En repo existente, marcar **ya cubierto** y no reinstalar.

### 8. Gate

Para **cada** paquete a instalar (no los ya cubiertos):

1. Última **estable** en npm (`npm view <pkg> version`). No beta/RC/nightly salvo que el usuario lo pida.
2. OSV de **esa** versión — seguir la skill `osv-vulnerability-scan` (API live; no inventar CVE/GHSA).
3. Señales: `npm view <pkg> license`, fecha de última publicación, `scripts` (`postinstall` / `preinstall`).
4. [Denylist](catalog.md) y olores (token en `localStorage`, Moment, `jsonwebtoken` por inercia).

Ficha por paquete:

```markdown
- paquete@version (ecosystem npm)
- licencia | última publicación
- OSV: limpio | IDs + severidad
- postinstall: no | sí (qué hace)
- veredicto: instalar / instalar igual (usuario avisado) / alternativa / saltar
```

Si OSV, abandono (~18 meses sin release sin motivo), licencia incompatible o `postinstall` raro: **avisar y preguntar**. No instalar en silencio. No instalar “de todos modos” sin respuesta.

### 9. Draft

Mostrar, dejar editar:

1. `docs/stack.md` según [templates/stack.md](templates/stack.md)
2. Comandos de install (`pnpm add` / `npm i` / `yarn add`) separados prod vs dev si aplica
3. Lista de skips / huecos

### 10. Write

Solo tras OK explícito.

- Crear/actualizar `docs/stack.md`
- Ejecutar los installs acordados (package manager del repo; no mezclar npm y pnpm)
- No tocar `docs/agents/`, `docs/standards/`, ni bloques `## Agent skills` / `## Coding standards`

Si `docs/stack.md` ya existía, actualizar in-place (needs nuevos, versiones re-verificadas); no duplicar.

### 11. Done

Qué se instaló, comando usado, ruta de `docs/stack.md`. Mencionar: re-ejecutar para cambiar de producto o re-gate; para una lib suelta, mejor un gate puntual que un re-bootstrap. Convivencia: Matt = issues; standards = cómo se escribe; esta skill = **qué dependencias entran y por qué**.

## Anti-patrones

- Escribir o instalar sin preguntar
- Las 10 favoritas en un CRUD interno de 8 personas
- Zustand / nuqs / better-auth / Motion en un repo donde no encajan
- Sustituir `date-fns` (u otra lib que ya cubre el need) por Temporal sin OK
- Versiones copiadas de memoria o de esta skill
- Inventar CVE
- Pisar `setup-repo-standards` o `setup-matt-pocock-skills`
- Oleada 3 si Q5 no describió pantalla o dijo que sin esa UX no pasa nada
- Extras de curiosidad (i18n “algún día”, móvil en 2027)
- Hardcodear Azure/GCP/AWS
