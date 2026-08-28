# Edge Insights

Edge Insights captures CAN bus traffic, decodes it with your DBC files,
and detects timing, congestion, and error anomalies. Results are shown in
a local web portal with Grafana dashboards. All data stays on the device;
no cloud connection is required.

Edge Insights runs on Windows PCs, Linux PCs, and embedded Linux devices,
on x86_64 and arm64.

## Requirements

Edge Insights captures from one CAN interface at a time, even on hardware
with multiple channels.

**Windows**: a Kvaser CAN interface, or a Kvaser virtual channel if you
just want to try Edge Insights without hardware attached. If the Kvaser
drivers or CANlib SDK aren't already installed, the installer detects
this and installs them for you automatically.

**Linux**: a SocketCAN-compatible CAN interface, or a virtual `vcan` bus
if you just want to try Edge Insights without hardware attached. Ubuntu
22.04+ or Debian 12+ is required; other systemd-based distributions may
work but are untested. Needs `systemd` with user services
(`systemctl --user`).

Linux builds are published for both x86_64 and arm64. On a laptop or
desktop PC, Edge Insights installs from a Debian package and runs as a
normal desktop application. On an embedded or headless device, it installs
from a release tarball and runs continuously as background services. The
portal is accessed over the network.

Edge Insights requires that ports 36300-36302 are available and unbound.

## Getting started

- [Install on Windows](getting-started/installation-windows.md)
- [Install on a Linux desktop](getting-started/installation-linux.md)
- [Install on an embedded device](getting-started/installation-embedded.md)
- [First run](getting-started/first-run.md): open the portal and start
  capturing
