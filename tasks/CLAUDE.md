# tasks/

Single file: `main.yml` — the full role execution in order.

## Task Flow

1. **Load architecture vars** — `include_vars` with `first_found` from `vars/architecture/`, maps `ansible_facts.architecture` to Go's arch naming (e.g. `aarch64` → `arm64`)
2. **Load version vars** — `include_vars` with `first_found` from `vars/versions/`, loads SHA256 checksum for the specific version+arch combination
3. **Assert checksum** — fails the role if `golang_redis_sha256sum` is empty/undefined
4. **Create download dir** — `golang_download_dir`
5. **Download Go SDK** — `get_url` from mirror with `sha256:` checksum verification, `force: false` (skip if cached)
6. **Create install dir** — `/opt/go/{version}` as root
7. **Install unarchive deps** — `gzip` + `tar` on zypper-only systems
8. **Extract** — `unarchive` with `--strip-components=1` and `creates:` guard on `{install_dir}/bin`
9. **Template env vars** — `/etc/profile.d/golang.sh` (GOROOT, PATH, optional GOPATH)
10. **Template facts** — `/etc/ansible/facts.d/golang.fact` (exposes `ansible_local.golang.general.version` and `.home`)
11. **Re-read facts** — `setup` filter to refresh `ansible_local`

## Key Patterns

- `first_found` cascades: specific arch → general → default. This means version files can be per-architecture (`1.24.2-arm64.yml`) or shared (`1.10.3.yml` for amd64-only older versions).
- `creates:` on the unarchive step makes re-runs idempotent without re-extracting.
- `when: not ansible_check_mode` guards download and extract steps since they need actual files.
