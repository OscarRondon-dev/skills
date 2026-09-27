---
name: dependency-sbom-osv
description: >-
  Maps dependency manifests and lockfiles to OSV API queries (ecosystem, package
  name, version). Covers package-lock.json, pnpm-lock.yaml, yarn.lock,
  requirements.txt, poetry.lock, go.mod/go.sum, pom.xml, Cargo.lock,
  Gemfile.lock, composer.lock, packages.config, and SBOM-style lists. Use when
  scanning a project for OSV vulnerabilities from lockfiles or dependency files.
disable-model-invocation: true
---

# Dependency / SBOM → OSV queries

Turn project dependency files into OSV `/v1/query` or `/v1/querybatch` payloads. Pair with **osv-vulnerability-scan** for triage and reporting.

## Workflow

```
- [ ] Detect manifest/lockfile type
- [ ] Prefer lockfile versions (exact) over range-only manifests
- [ ] Map each package → { ecosystem, name, version } or purl
- [ ] Batch via /v1/querybatch (then GET /v1/vulns/{id})
- [ ] Deduplicate transitive duplicates before querying when possible
```

## Ecosystem mapping

| File(s) | OSV ecosystem | Name / version notes |
|---------|---------------|----------------------|
| `package-lock.json`, `npm-shrinkwrap.json` | `npm` | Use `packages`/`dependencies` resolved versions; name as in registry |
| `yarn.lock` | `npm` | Resolved version after `@` / `version` field |
| `pnpm-lock.yaml` | `npm` | Use locked `version` under snapshots/packages |
| `requirements.txt`, `pip freeze` | `PyPI` | Normalize name (PEP 503); pin `==` preferred |
| `poetry.lock`, `Pipfile.lock` | `PyPI` | Use locked package version |
| `go.mod` / `go.sum` | `Go` | Module path as `name` (e.g. `github.com/foo/bar`); version `vX.Y.Z` |
| `pom.xml`, `gradle.lockfile` | `Maven` | Name often `groupId:artifactId`; version from lock/effective POM |
| `Cargo.lock` | `crates.io` | Crate `name` + `version` |
| `Gemfile.lock` | `RubyGems` | Gem name + version |
| `composer.lock` | `Packagist` | `name` like `vendor/package` + version |
| `packages.lock.json`, `packages.config` | `NuGet` | Package id + version |
| `mix.lock` | `Hex` | App/package name + version |
| `pubspec.lock` | `Pub` | Package name + version |
| `Package.resolved` | `SwiftURL` | Use as applicable for Swift packages |

When a CycloneDX/SPDX SBOM is present, prefer `purl` fields directly in OSV queries.

## PURL shortcuts

OSV accepts package URLs:

```json
{ "package": { "purl": "pkg:npm/lodash@4.17.20" } }
{ "package": { "purl": "pkg:pypi/jinja2@3.1.4" } }
{ "package": { "purl": "pkg:golang/github.com%2Fgin-gonic%2Fgin@v1.9.0" } }
{ "package": { "purl": "pkg:cargo/serde@1.0.188" } }
{ "package": { "purl": "pkg:maven/org.apache.logging.log4j/log4j-core@2.14.0" } }
```

Do not also set top-level `version` if the purl already includes `@version`.

## Extraction tips

### npm `package-lock.json` (v2/v3)

- Skip the root `""` entry.
- Prefer entries with `"node_modules/..."` keys; package name is the last path segment (scoped: last two segments `@scope/name`).
- Use the locked `"version"` field (not ranges from `package.json`).

### `requirements.txt`

- Parse lines like `Jinja2==3.1.2`; ignore flags (`-r`, `--hash`, editable `-e` without clear name/version).
- Map distribution name to PyPI normalized form for queries when needed; OSV usually accepts common spellings (`Jinja2` / `jinja2`).

### `go.mod`

- Parse `require` directives (and `go.sum` for confirmation).
- Exclude `replace` local paths unless the replacement module version is known.
- Ecosystem is `Go`; name is the module path.

### `pom.xml`

- Prefer a Gradle/Maven lockfile or dependency tree if available (resolved versions).
- Plain `pom.xml` often has ranges/properties — resolve properties before querying; skip unresolved versions.

### `Cargo.lock`

- Read `[[package]]` tables: `name` + `version`.
- Query ecosystem `crates.io`.

## Batching

1. Build a unique set of `{ecosystem, name, version}` (or purls).
2. POST `/v1/querybatch` with those queries.
3. Collect all `id` values; `GET /v1/vulns/{id}` for details (cache by id).
4. Handle per-query `next_page_token` as described in **osv-api-reference**.

Keep batches practical (hundreds of queries is fine; split if responses are huge).

## What not to do

- Do not invent versions for unpinned ranges — ask or resolve the lockfile first.
- Do not query private/internal package names against public OSV expecting coverage (note the gap).
- Do not leak registry tokens or `.npmrc`/`pip.conf` credentials when reading files.

## Hand-off

After queries return, continue with **osv-vulnerability-scan** report template and severity triage.
