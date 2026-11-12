# RamiGspo-lab

Experimenting with AI made me realize (again) how useful it is to keep things documented,
for my future self and for anyone learning or working with me.
This is a personal lab for my Linux workstation (Ubuntu/Debian) and RevPi devices.

## Contents

| Path | Description |
|------|-------------|
| `ansible/` | Ansible playbooks and roles for Ubuntu/Debian setup |
| `docker/ai-stack/` | Docker Compose stack for local AI tools |

## AI Stack

- **Ollama** + **Open WebUI**: local LLM inference and chat UI
- **Aider**: AI pair programming CLI (installed via pip)
- **Cline**: AI coding assistant (VS Code extension)
- **Whisper**: local speech-to-text
- **Chatterbox**: local TTS with voice cloning

## Ansible

Roles are custom (no Ansible Galaxy). Run the playbooks from `ansible/`:

| Playbook | Target |
|----------|--------|
| `workstation.yml` | this machine (`localhost`) |
| `arcade.yml` | RevPi devices in the `revpi` group |

`inventory/hosts.yml` lists my own devices. Their passwords live in an
Ansible Vault, `inventory/group_vars/revpi/vault.yml`.

## Arcade (RevPi Connect 5)

`ansible/arcade.yml` turns a RevPi running Debian trixie into a retro console:
it boots straight into RetroArch, fullscreen on HDMI.

1. Flash the RevPi trixie image and connect HDMI, a gamepad and the network.
2. Optional: to copy your ROM collection (one subfolder per console, same names
   as `~/roms/`), set its local path as `retroarch_roms_source` for the host
   in your inventory.
3. Run the playbook. The first run reboots the RevPi into RetroArch:

   ```sh
   cd ansible
   ansible-playbook arcade.yml --ask-vault-pass
   ```

   Run it again after adding games: it only copies what is new.
4. In RetroArch: *Load Content* → `/home/pi/` → `roms` → console → ROM → core.

Notes:

- Ansible downloads free homebrew games into `~/roms/` (Cave Story for Mega Drive,
  µCity for Game Boy Color).
- Neo Geo needs the BIOS `neogeo.zip` next to the games in `~/roms/neogeo/`.
- PlayStation needs a BIOS in `~/.config/retroarch/system/`.
- Settings managed by Ansible live in `~/.config/retroarch/arcade.cfg`;
  everything else can be changed from the RetroArch menu.
- Quitting RetroArch restarts it. Ctrl+Alt+F2 opens a local shell; SSH always works.

## Requirements

- Docker + Docker Compose
- Ansible 2.14+
- Python 3.10+
