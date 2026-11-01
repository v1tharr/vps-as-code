# vps-as-code

Ansible setup for an Ubuntu 24.04 VPS: a fresh server should become a configured one with a single command. A learning project for practicing Linux administration.

## Requirements

- Ansible (`ansible-core`), `ansible-lint` and `pre-commit` (easy to install with `pipx`)
- A clean Ubuntu 24.04 server with root access over an SSH key
- A dedicated SSH key for the project (`~/.ssh/vps-as-code`)

## Quick start

```bash
git clone git@github.com:v1tharr/vps-as-code.git
cd vps-as-code
pre-commit install
ansible-galaxy collection install -r requirements.yml

cp inventory/hosts.example.yml inventory/hosts.yml   # put your IP in
ansible-playbook playbooks/bootstrap.yml
```

`inventory/hosts.yml` is not tracked by git. There are no secrets in the repository: gitleaks runs in pre-commit, and future secrets will live in ansible-vault.

## Status

| Stage | State |
|-------|-------|
| Repository skeleton, pre-commit | done |
| `bootstrap.yml`: user, key, sudo | written, not yet tested on a clean server |
| SSH hardening, nftables, fail2ban, auto-updates | planned |
| Docker, nginx, TLS | planned |
| Monitoring (Prometheus, Grafana) | planned |
| Backups (restic) and restore check | planned |
| CI | planned |

## License

MIT