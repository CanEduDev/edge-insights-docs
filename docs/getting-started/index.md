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

Linux builds are published for both x86_64 and arm64. On a desktop PC,
Edge Insights installs as a regular desktop application; on an embedded or
headless device it runs continuously as background services, with the
portal reached over the network.

## Next steps

- [Install on Windows](installation-windows.md)
- [Install on Linux](installation-linux.md) — desktop PC
- [Install on an embedded device](installation-embedded.md) — headless /
  continuous monitoring

Once installed, head to [First run](first-run.md) to open the portal and
start capturing.
