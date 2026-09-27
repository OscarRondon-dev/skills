# Reference — research y contraste

## Orden de fuentes

1. **Versión en el repo** — manifest del stack (`package.json`, lockfile, `requirements.txt`, `go.mod`, etc.)
2. **MCP de docs** — si el entorno expone un server para **la tecnología que nombró el usuario** (`GetMcpTools`; no asumir cuál hay)
3. **Documentación oficial** — URL canónica del vendor/proyecto (`WebFetch`)
4. **WebSearch** — solo para encontrar URL oficial o changelog; verificar en oficial
5. **Código del repo** — cómo se usa hoy (entrada del contraste, no verdad automática)

Evitar: blogs no verificados, SO sin cruce con oficial, docs de versión distinta a la instalada.

## Checklist de research

- [ ] Versión instalada vs documentada
- [ ] Breaking changes relevantes
- [ ] Patrón recomendado **hoy** (no deprecado)
- [ ] Qué hace el repo y si **sigue siendo** buena práctica
- [ ] 3–5 reglas accionables para el borrador de estándares

## Tabla de contraste (obligatoria en chat, antes de cualquier write)

| Tema | Repo hoy | Oficial / vigente | Recomendación |
| --- | --- | --- | --- |
| | ausente / ADR / código | | mantener / adoptar / corregir / excepción / ticket aparte |

**Criterio de recomendación:**

- **Adoptar oficial** cuando el repo no sigue buena práctica vigente o usa API/patrón deprecado.
- **Mantener repo** solo cuando el contraste demuestra alineación con oficial (o excepción justificada que el usuario aprueba).
- **Corregir** — plan gradual (boy scout / ticket); no refactor masivo sin pedido.

## Si MCP no existe o falla

`WebFetch` a docs oficiales; anotar en el informe qué fuente se usó. No inventar API.

## Una tecnología por invocación

Cola de dominios → el usuario invoca de nuevo con autorización explícita por cada uno. No encadenar writes sin OK.

## Autorización

Research y explore = solo lectura, sin permiso extra.

**Cualquier archivo en el repo** = requiere aprobación explícita del usuario (propuesta → borrador → write, o un solo “apruebo todo” explícito).
