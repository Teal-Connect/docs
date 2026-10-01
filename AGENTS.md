# Docs — Agent Guidelines

Mintlify site. Config is `docs.json` (not the starter-kit `mint.json`). CLI is `mint` (`npm i -g mint`). The old `mintlify` binary is gone.

## Verify every change

CLI success is not enough. After any MDX, OpenAPI, or `docs.json` change, prove the preview actually renders.

1. Run from **this repo root** (the directory with `docs.json`). Never from the workspace parent (`ws2/`, etc.). From there, `mint broken-links` walks sibling repos and reports fake MDX parse errors on ansible, skills, payroll-api markdown, and similar.
2. Commands need unsandboxed / full OS network. Cursor sandbox fails with `uv_interface_addresses`.

```bash
mint validate
mint broken-links
mint dev --port 3333
```

3. Open the preview (`http://localhost:3333/...`) and exercise the changed pages the way a reader would:
   - Nav group and page title
   - OpenAPI playground (method, path, auth, request/response examples, error status tabs)
   - MDX copy vs the endpoint
4. Do not claim the docs work from `mint validate` / `mint broken-links` alone, or from a screenshot. Browse first. `mint broken-links` only checks that internal docs links resolve.
