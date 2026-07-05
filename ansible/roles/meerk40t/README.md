# meerk40t

Turns a headless RevPi/Raspberry Pi (Bookworm or Trixie) into a laser node
running [MeerK40t](https://github.com/meerk40t/meerk40t), controllable over
`ssh -X` from a workstation.

## Usage

```
ssh -X pi@RevPi94366.local
meerk40t
```

The GUI window is forwarded to your workstation over X11; the RevPi itself
never needs a display attached.

## Variables

| Name             | Default                          | Description                     |
|------------------|-----------------------------------|----------------------------------|
| `meerk40t_user`  | `pi`                              | User the venv and GUI run as     |
| `meerk40t_venv`  | `/home/{{ meerk40t_user }}/.venvs/meerk40t` | Path to the venv |

## Notes

- `meerk40t_user` is added to the `dialout` group for `/dev/ttyUSB0` access.
  This only takes effect on that user's next login/session, not the one
  the role ran in.
- wxPython comes from `apt` (`python3-wxgtk4.0`), not `pip` — there are no
  prebuilt ARM64 wheels, and building it from source on-device is
  impractically slow. The venv uses `--system-site-packages` to see it.
