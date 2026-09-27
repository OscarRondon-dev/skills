# verify:touched config shape (agent reference)

Filled into `verify-touched.config.mjs` at Write time. Derive every value from **Explore + Section C/E** — never copy from another repository.

## `scopedTests`

**`null`** — always run full `test` command.

**Object** — when Explore found a repeatable module/box path pattern:

```javascript
scopedTests: {
  fallback: 'full', // or 'skip' when no module matched in diff
  moduleIdFromPath: (normalizedPath) => {
    const m = normalizedPath.match(/YOUR_MODULE_PATH_REGEX/);
    return m ? m[1] : null;
  },
  buildArgs: (moduleId) => ({
    command: '{{TEST_CMD}}',
    args: [/* runner-specific scoped args for moduleId */],
  }),
}
```

Examples of patterns (pick one that matches the repo):

- `src/features/([^/]+)/`
- `packages/([^/]+)/src/`
- `apps/([^/]+)/`
- `modules/([^/]+)/`

Test scoping command varies by runner (Vitest `--dir`, `--project`, path filter, etc.) — document the chosen form in `tooling.md`.

## `package.json` scripts (typical)

```json
{
  "lint:touched": "node scripts/verify-touched.mjs --lint-only",
  "verify:touched": "node scripts/verify-touched.mjs"
}
```

Adjust if scripts live elsewhere or the package manager wraps `node` differently.

## `lint`

| Linter | Typical `command` + `argsPrefix` |
|--------|----------------------------------|
| ESLint | `npx`, `['eslint']` |
| Biome | `npx`, `['biome', 'check']` |

`pathPrefixes` = code roots from Explore (`src/`, `packages/foo/src/`, …).
