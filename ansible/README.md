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

## Wifi

`wifi_ssid` and `wifi_password` also live in
`inventory/group_vars/revpi/vault.yml`. To add or change them:

```bash
ansible-vault edit inventory/group_vars/revpi/vault.yml
```

The `wifi` role enables the external antenna (rebooting once, only the first
time), sets the regulatory domain (`wifi_country`, default `DE`), and
connects to `wifi_ssid` via NetworkManager. If the SSID isn't in range, the
playbook fails with an error instead of connecting silently to nothing.
