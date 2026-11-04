# vps-as-code

Ansible roles that turn a fresh Ubuntu 24.04 VPS into a hardened server with one command. Built as a practice project for Linux administration.

## What it sets up

- admin user with sudo and SSH keys from the inventory
- sshd: keys only, no root login, `AllowUsers`
- nftables: default drop on input and forward, open ports come from a variable
- fail2ban: sshd jail, bans through nftables
- unattended-upgrades: automatic security updates

Docker, nginx with TLS, monitoring and backups are next, see the status table.

## Requirements

- Ubuntu 24.04 server, root access over an SSH key
- `ansible-core`, `ansible-lint`, `pre-commit` (I install them with `pipx`)
- A key for the project at `~/.ssh/vps-as-code` (it has to be loaded into ssh-agent)

Tested on Aeza.

## Usage

```bash
git clone git@github.com:v1tharr/vps-as-code.git
cd vps-as-code
ansible-galaxy collection install -r requirements.yml

cp inventory/hosts.example.yml inventory/hosts.yml   # put your IP in
# put your public keys into inventory/group_vars/all/vars.yml

ansible-playbook playbooks/bootstrap.yml   # once, as root: creates the admin user
ansible-playbook playbooks/site.yml        # everything else, as the admin user
```

Before the first connection, accept the server's host key by hand (`ssh root@<ip>`), because `host_key_checking` is on.

`inventory/hosts.yml` is not in git. There are no secrets in the repo: gitleaks runs in pre-commit, future secrets go to ansible-vault.

## Rebuilding the server

After a reinstall the host key changes:

```bash
ssh-keygen -R <ip>
ssh root@<ip>          # accept the new fingerprint, then exit
ansible-playbook playbooks/bootstrap.yml
ansible-playbook playbooks/site.yml
```

## Notes

- Changing the firewall: the nftables role validates the file with `nft -c` before applying. The ruleset only touches its own table `inet filter`, so fail2ban's table is left alone.
- The Aeza image masks `apt-daily*.service` and disables the timers, so unattended-upgrades never ran. The `auto_updates` role unmasks them.
- Only the `noble` and `noble-security` pockets are upgraded automatically. After a kernel update the server needs a manual reboot (`/var/run/reboot-required`).

## Status

| Stage | State |
|-------|-------|
| Skeleton, pre-commit | done |
| Base: user, ssh, nftables, fail2ban, auto-updates | done |
| Docker, nginx, TLS | planned |
| Monitoring (Prometheus, Grafana) | planned |
| Backups (restic) and restore check | planned |
| CI | planned |

## License

MIT
