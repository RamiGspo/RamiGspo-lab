# Ansible

## Requirements

- Ansible
- `sshpass` — needed for password-based SSH to inventory hosts (`sudo apt install sshpass`)

## Usage

Run from this directory (`ansible/`), so `ansible.cfg` is picked up automatically:

```bash
ansible-playbook workstation.yml
ansible-playbook laser.yml --ask-vault-pass
```

Test connectivity to a group before running a playbook:

```bash
ansible laser_nodes -m ping --ask-vault-pass
```

Host passwords live in `inventory/group_vars/revpi/vault.yml`, encrypted with
`ansible-vault`.
