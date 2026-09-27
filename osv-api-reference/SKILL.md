---
name: osv-api-reference
description: >-
  Detailed OSV (osv.dev) API reference: /v1/query, /v1/querybatch, /v1/vulns/{id}
  request/response shapes, ecosystem names, pagination and batch limits, and
  PowerShell/curl examples. Use when building or debugging OSV API calls, or
  when skill osv-vulnerability-scan needs full API detail.
disable-model-invocation: true
---

# OSV API Reference

Load this skill explicitly when constructing or debugging OSV API requests. For the full end-to-end scan workflow and report template, use **osv-vulnerability-scan**.

Base URL: `https://api.osv.dev/v1/`

Docs: https://google.github.io/osv.dev/ · https://osv.dev/

Also see progressive disclosure file: `~/.cursor/skills/osv-vulnerability-scan/reference.md`

## Endpoints

| Method | Path | Returns |
|--------|------|---------|
| POST | `/v1/query` | Full vuln objects for one package/version, purl, or commit |
| POST | `/v1/querybatch` | Per-query lists of `{id, modified}` only (order preserved) |
| GET | `/v1/vulns/{id}` | Full vulnerability record |

## Critical rules

1. **Case-sensitive** ecosystems and IDs (`PyPI`, not `pypi`).
2. Never combine top-level `version` with a **versioned** purl → **400**.
3. Never combine `commit` with `version`.
4. Use either `name`+`ecosystem` **or** `purl`, not both.
5. After `querybatch`, always `GET /v1/vulns/{id}` for severity, affected, fixed, references.
6. Honor `next_page_token` / `page_token` until exhausted.

## Pagination thresholds (approximate; may change)

| Endpoint | When pagination appears |
|----------|-------------------------|
| `/v1/query` | > ~1000 vulns **or** query > ~20s |
| `/v1/querybatch` | any query > ~1000 vulns **or** queryset > ~3000 vulns total |

HTTP/1.1 body limit ~**32 MiB**; prefer HTTP/2 for large Linux-style queries. No rate limit currently advertised.

## Payload sketches

### /v1/query

```json
{
  "package": { "name": "nokogiri", "ecosystem": "RubyGems" },
  "version": "1.18.2"
}
```

```json
{ "commit": "6879efc2c1596d11a6a6ad296f80063b558d5e0f" }
```

```json
{ "package": { "purl": "pkg:pypi/mlflow@0.4.0" } }
```

### /v1/querybatch

```json
{
  "queries": [
    { "package": { "purl": "pkg:pypi/mlflow@0.4.0" } },
    { "commit": "6879efc2c1596d11a6a6ad296f80063b558d5e0f" },
    { "package": { "name": "jinja2", "ecosystem": "PyPI" }, "version": "2.4.1" }
  ]
}
```

### /v1/vulns/{id}

```bash
curl.exe -s "https://api.osv.dev/v1/vulns/OSV-2020-111"
```

## Severity fields to read

- `severity[]` — CVSS (or similar) when present
- `database_specific.severity` — often GHSA: CRITICAL / HIGH / MODERATE / LOW
- `ecosystem_specific.severity` — ecosystem-specific labels

Map MODERATE → Medium in user-facing triage.

## Call style on Windows

Prefer:

```powershell
Invoke-RestMethod -Method Post -Uri "https://api.osv.dev/v1/query" `
  -ContentType "application/json" `
  -Body '{"package":{"name":"lodash","ecosystem":"npm"},"version":"4.17.20"}'
```

Or `curl.exe` (not the PowerShell `curl` alias).

## Ecosystems (common)

`npm`, `PyPI`, `Maven`, `Go`, `crates.io`, `RubyGems`, `NuGet`, `Packagist`, `Hex`, `Pub`, `SwiftURL`, `GIT`, `OSS-Fuzz`, plus distros (`Debian`, `Ubuntu`, `Alpine`, …).

Full/current list: https://osv.dev/ or ecosystems dump under OSV data docs.
