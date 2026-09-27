# Catálogo de needs

Fuente de mapeo para `setup-project-stack`. **No hay versiones aquí** — se resuelven en el gate.

Usar la columna del framework detectado. Si el paquete “preferido del usuario” no encaja, no forzarlo.

## Preferencias del usuario (cuando el need está ON)

| Need | Preferencia JS | No forzar en |
|------|----------------|--------------|
| Validación | `zod` | — (sirve en los tres) |
| Fechas | Temporal + `@js-temporal/polyfill` | — |
| Tablas | TanStack Table | Nest (sin UI) |
| Auth | `better-auth` | Angular (preguntar); Nest API pura (ver fila Auth) |
| Animación | `motion` | Angular (ver columna); Nest |
| Fuentes | `@fontsource/*` | Nest |
| Gráficas | `chart.js` | Nest; oleada 3 skip si Q5 vanity |
| Estado UI global | `zustand` | Angular (signals); Nest |
| Drag and drop | `@atlaskit/pragmatic-drag-and-drop` | Nest |
| Estado URL | `nuqs` | Angular (router); Nest |

Otras libs **sí** se proponen si el need está ON: TanStack Query, schema de env, DOMPurify, helmet, rate-limit, `jose`, `argon2`, `decimal.js`, `ky`, CASL, etc.

## Cubierto en repo existente

Si `package.json` ya tiene un equivalente razonable, marcar **ya cubierto** y no reinstalar:

| Need | Ejemplos que ya cubren |
|------|------------------------|
| Validación | `zod`, `valibot`, `yup`, `joi`, `class-validator` |
| Fechas | `@js-temporal/polyfill`, `date-fns`, `luxon`, `dayjs` (no Moment) |
| Query | `@tanstack/react-query`, `@tanstack/angular-query-experimental` |
| Estado UI | `zustand`, `@ngrx/store`, signals de dominio ya usados |
| Auth | `better-auth`, `@auth/core`, MSAL, Passport ya cableado |
| HTTP client | `ky`, `ofetch`, `axios` ya usado, `HttpClient` Angular |
| Charts | `chart.js`, `echarts`, etc. |

Moment / `request` / `node-uuid` = cubierto **mal**: proponer reemplazo, no “ya cubierto”.

## Oleada 1 — seguridad

Encendido típico: validación si hay input; env si hay secretos/config; sanitizar si Q3 ≠ no; auth si Q1 ≠ nadie; HTTP endurecido si Q2 = sí y hay API/servidor; dinero si Q4 = sí; autorización si hay más de “logueado / no”.

| Need | Angular | React / Next | Nest / Node |
|------|---------|--------------|-------------|
| Validación | `zod` | `zod` | `zod` (schemas HTTP; `nestjs-zod` solo si ya hay Nest y el usuario quiere el glue) |
| Env tipado | `zod` al bootstrap | `@t3-oss/env-core` o `@t3-oss/env-nextjs` + `zod` | `@nestjs/config` + `zod` |
| HTML untrusted | `dompurify` / `isomorphic-dompurify` | igual | sanitizar solo si el servidor emite HTML de usuarios |
| Auth | **preguntar** (cookies, MSAL, etc.). No default `better-auth`. | `better-auth` si hay usuarios | API: `jose` + `argon2`. Producto de usuarios completo: `better-auth` si el usuario lo quiere |
| JWT | — | `jose` en servidor si hace falta | `jose` (no `jsonwebtoken` por inercia) |
| Passwords | — | `argon2` si hay secretos propios | `argon2` |
| HTTP endurecido | — | middleware Next: headers + rate-limit en login/APIs públicas | `helmet` + `@nestjs/throttler` o `rate-limiter-flexible` |
| Autorización | policies / guards del framework; `@casl/ability` si hay roles de verdad | igual | guards Nest; CASL si roles > 2 |
| Dinero | `decimal.js` | `decimal.js` | `decimal.js` |

Cookie **httpOnly** como recomendación de sesión. Token en `localStorage` = olor; documentarlo y no proponer libs que lo asuman.

## Oleada 2 — núcleo

| Need | Angular | React / Next | Nest / Node |
|------|---------|--------------|-------------|
| Datos de servidor | `@tanstack/angular-query-experimental` si hay fetching de listas/cache; si signals+`HttpClient` bastan, skip | `@tanstack/react-query` (hueco frecuente aunque no esté en la lista del usuario) | — |
| Estado UI global | **signals** (no `zustand`) | `zustand` **solo** si hay estado de cliente de verdad (no para cache de server) | — |
| Estado URL | `Router` query params | `nuqs` si los filtros se pegan en el chat | — |
| Fechas | `@js-temporal/polyfill` | igual | Temporal o `luxon` en servidor; mismo modelo mental |
| Forms | forms del framework + `zod` | TanStack Form o RHF + `zod` | DTOs + `zod` |
| HTTP client | `HttpClient` (ya está) | `ky` o `fetch`; no Axios “porque sí” | — |

## Oleada 3 — UX

Todo skip si Q5 no describió pantalla o la UX es vanity.

| Need | Angular | React / Next | Nest / Node |
|------|---------|--------------|-------------|
| Tablas | `@tanstack/angular-table` (o `@tanstack/table-core` + UI). &lt;50 filas → HTML, skip lib | `@tanstack/react-table` | — |
| Virtualización | `@tanstack/angular-virtual` si 1k+ filas | `@tanstack/react-virtual` | — |
| Gráficas | `chart.js` | `chart.js` | — |
| DnD | `@atlaskit/pragmatic-drag-and-drop` | igual | — |
| Animación | `@angular/animations` o GSAP si el repo ya va por ahí; `motion` solo si el usuario lo quiere en Angular | `motion` | — |
| Fuentes | `@fontsource/<familia>` (self-hosted) | igual | — |
| Iconos | un solo set (`lucide-angular` o el que ya use el design) | `lucide-react` | — |
| Toasts | snackbar del design system, o skip | skip hasta que Q5/extra lo pida | — |

Tamaño de lista (si Q5 es una tabla): &lt;50 → sin table lib; 50–1k → table; 1k+ → table + virtual. “No sé, miles” → table, virtual después.

## Denylist (no proponer como default)

- `moment`, `request`, `node-uuid`
- `jsonwebtoken` por inercia → `jose`
- `lodash` entero
- Axios cuando `fetch` / `HttpClient` / `ky` bastan
- Chart.js / Motion / DnD / Table en oleada 3 si Q5 dijo vanity
- Auth client-side con token en storage web
- Instalar `@casl/ability` para un solo rol “logueado”

## Ecosistema desconocido

Si no es Angular / React-Next / Nest-Node: no usar esta tabla. Preguntar needs; proponer candidatos con la misma lógica (validar, no Moment, gate OSV) y dejar constancia en `docs/stack.md` de que el mapeo fue ad hoc.
