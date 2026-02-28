# defaults/

User-facing role variables. All are overridable by playbook consumers.

## Variables

| Variable | Default | Purpose |
|---|---|---|
| `golang_version` | `'1.26.0'` | Go SDK version to install |
| `golang_mirror` | `'https://dl.google.com/go'` | Download mirror URL |
| `golang_install_dir` | `'/opt/go/{{ golang_version }}'` | Installation path (version-specific, allows side-by-side installs) |
| `golang_download_dir` | `x_ansible_download_dir` or `~/.ansible/tmp/downloads` | Temp download cache |
| `golang_gopath` | unset | If set, configures GOPATH env var and adds `$GOPATH/bin` to PATH |

## When Updating

When bumping the default Go version, update `golang_version` here **and** update the hardcoded version strings in `molecule/default/tests/test_role.py` (test assertions reference the version literally).
