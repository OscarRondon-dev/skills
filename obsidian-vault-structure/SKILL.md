---
name: obsidian-vault-structure
description: Organize the fixed DEV-OBSIDIAN vault with independent per-topic folders, propose a logical note tree, and get user agreement before writing. Use when choosing where notes go, bootstrapping a topic folder (e.g. Komtexia), improving structure, or deciding PARA-style layout inside a topic.
disable-model-invocation: true
---

# Obsidian Vault Structure

## Vault fijo (siempre)

```text
C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN
```

Todas las notas van aquí (nube). No crear vaults en repos ni en otras rutas.

**No busques esta ruta en disco.** Úsala directamente. Prohibido Glob/Grep/Shell recursivo en `C:\Users\oscar.rondon` o OneDrive para “encontrar” el vault. Para inspeccionar un tema: `target_directory: C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN\<Tema>`.

### `.obsidian` = config, no notas

```text
DEV-OBSIDIAN/
├── .obsidian/          # SOLO configuración de Obsidian — NUNCA poner notas aquí
├── Komtexia/           # notas del tema
└── OtroTema/
```

- Tratar `.obsidian/` como zona prohibida para escritura de contenido
- No crear, editar ni “organizar” archivos dentro de `.obsidian/` salvo petición explícita
- Las carpetas de tema y los `.md` van al mismo nivel que `.obsidian`, no dentro

## Organización: carpetas independientes por tema

Un tema/proyecto/producto = una carpeta de primer nivel **hermana de** `.obsidian`:

```text
DEV-OBSIDIAN/
├── .obsidian/     # config
├── Komtexia/      # notas
├── OtroTema/
└── ...
```

Nunca mezclar temas en la raíz ni tirar `.md` sueltos sin carpeta de tema (salvo índices globales que el usuario ya tenga y pida usar).

## Layout recomendado dentro de un tema

Crear solo lo necesario, tras acuerdo:

```text
<Tema>/
├── <Tema> — index.md       # MOC / entrada
├── 00 Inbox/               # fleeting (opcional)
├── 10 Decisions/
├── 20 Sessions/
├── 30 Resources/           # permanentes / atómicas
└── 40 Archive/             # solo con acuerdo
```

Si el tema **ya tiene** otra estructura coherente, adáptate a ella; no fuerces rename masivo.

### Dónde va cada nota

| Situación | Ubicación |
|-----------|-----------|
| Idea cruda / sin clasificar | `<Tema>/00 Inbox/` |
| Decisión | `<Tema>/10 Decisions/` |
| Reunión / sesión de trabajo | `<Tema>/20 Sessions/` |
| Idea reusable / claim atómico | `<Tema>/30 Resources/` |
| Índice / navegación del tema | `<Tema>/<Tema> — index.md` o MOC en Resources |
| Terminado / inactivo | `<Tema>/40 Archive/` (solo con acuerdo) |

## Propuesta de estructura (antes de tocar disco)

Obligatorio presentar al usuario algo como:

```markdown
## Plan de notas — <Tema>

**Vault:** `C:\Users\oscar.rondon\Documents\DEV-OBSIDIAN`
**Carpeta:** `<Tema>/` (existe | se crearía)

### Árbol propuesto
- `<Tema>/<Tema> — index.md` — puerta de entrada (crear | actualizar)
- `<Tema>/10 Decisions/Decision — ….md` — …
- `<Tema>/30 Resources/….md` — …

### Enlaces
- index → cada nota nueva
- decisiones ↔ recursos relacionados

### No haré (aún)
- escribir archivos hasta que confirmes
```

Esperar confirmación o ajustes. Luego ejecutar exactamente el plan acordado.

## Mejorar estructura existente

1. Leer la carpeta del tema completa (nombres, index, frontmatter).
2. Proponer mejoras **incrementales** (1–5 cambios claros).
3. Acordar → aplicar.
4. Preferir add/link sobre move; move sobre delete.

## Safety

- Nunca borrar sin confirmación explícita
- Nunca reorganizar todo el vault de golpe
- Nunca escribir notas dentro de `.obsidian/`
- No tocar `.obsidian/` (plugins/config) salvo petición explícita
