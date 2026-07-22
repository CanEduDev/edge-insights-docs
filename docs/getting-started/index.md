# Getting Started

Before installing Edge Insights, make sure you have what your platform
needs.

## Windows

- A Kvaser CAN interface. If the Kvaser drivers or CANlib SDK aren't already
  installed, the installer detects this and installs them for you
  automatically.

## Linux

- A SocketCAN-compatible CAN interface, or a virtual `vcan` bus if you just
  want to try Edge Insights without hardware attached
- Ubuntu 22.04+ or Debian 12+ (other systemd-based distributions may work
  but are untested)
- `systemd` with user services (`systemctl --user`)

## Next steps

- [Install on Windows](installation-windows.md)
- [Install on Linux](installation-linux.md)

Once installed, head to [First run](first-run.md) to open the portal and
start capturing.
