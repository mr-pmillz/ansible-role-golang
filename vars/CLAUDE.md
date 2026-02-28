# vars/

Internal role variables (not intended for user override).

## `main.yml`

Defines `golang_redis_filename` — the download filename template: `go{{ golang_version }}.linux-{{ golang_architecture }}.tar.gz`

## `architecture/`

Maps `ansible_facts.architecture` to Go's architecture name. Loaded via `first_found` in tasks.

| File | `ansible_facts.architecture` | `golang_architecture` |
|---|---|---|
| `default.yml` | x86_64 (fallback) | `amd64` |
| `aarch64.yml` | aarch64 | `arm64` |
| `armv7l.yml` | armv7l | `armv6l` |
| `armv6l.yml` | armv6l | `armv6l` |
| `aarch64-32.yml` | aarch64 (32-bit userspace) | `armv6l` |

## `versions/`

~543 YAML files containing SHA256 checksums. Each file has a single variable:
```yaml
golang_redis_sha256sum: '<sha256>'
```

**Naming convention:**
- `{version}-{arch}.yml` — architecture-specific checksum (e.g. `1.24.2-amd64.yml`, `1.24.2-arm64.yml`)
- `{version}.yml` — used by older versions that only had amd64 builds

Newer versions (1.24.x) have per-architecture files (`-amd64`, `-arm64`, `-armv6l`). Older versions (pre-1.10) typically have a single file for amd64 only.

**Do not hand-edit** — use `add_new_versions.py` at the repo root to generate these from the go.dev API.
