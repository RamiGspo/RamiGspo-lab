# RamiGspo-lab

Experimenting with AI made me realize (again) how useful it is to keep things documented,
for my future self and for anyone learning or working with me.
This is a personal lab for my Linux workstation (Ubuntu/Debian).

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

Roles are custom (no Ansible Galaxy). Targets are my Ubuntu/Debian workstations.

## Requirements

- Docker + Docker Compose
- Ansible 2.14+
- Python 3.10+
