# Troubleshooting

## Windows blocks the download or the installer

Windows can show two separate warnings when you download or run the
installer. Both are expected; click through them to continue.

**Microsoft Edge blocks the download.** After the download finishes, Edge
may show a warning that the file "isn't commonly downloaded" or "could be
dangerous."

1. Click the warning, then **Show more**.
2. Click **Keep**.

![Edge download warning](../images/troubleshooting/edge-download-warning.png)

**Windows SmartScreen blocks the installer.** When you run the downloaded
installer, Windows may show a blue "Windows protected your PC" screen.

1. Click **More info**.
2. Click **Run anyway**.

![Windows SmartScreen warning](../images/troubleshooting/smartscreen-warning.png)

## CAN interface not found

**Linux** — check which interfaces are available:

```bash
ip link show
```

If you don't have CAN hardware attached, you can create a virtual bus for
testing:

```bash
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
```

**Windows** — open Kvaser Device Guide and confirm your Kvaser device
appears there. If you just installed the Kvaser drivers, reboot your PC
before checking again — a restart is often required before a newly
installed device is enumerated.

## Sharing the CAN bus with other tools

While capture is running, Edge Insights owns and configures the CAN
interface: it sets the bus parameters and brings the bus on when capture
starts, and takes it back down when capture stops.

You can run other tools alongside Edge Insights to observe the same bus,
but they must not try to reconfigure it:

- **Kvaser** — other tools (CanKing, `candump` via canlib, custom canlib
  applications) can read and write frames alongside Edge Insights as long
  as they open their channel without init access. Any attempt from such a
  tool to change bus parameters or driver mode will silently do nothing
  while Edge Insights holds the bus — reads and writes still work, but
  configuration changes appear to succeed without taking effect.
- **Linux (SocketCAN)** — other processes can open sockets on the same
  interface to read or write frames, but avoid running `ip link set` on
  that interface while capture is running. Changing the interface state
  from outside can leave it out of sync with what Edge Insights expects.

If you need a tool to have exclusive control of the bus — for example, to
reconfigure bus parameters — stop capture first by toggling the bus off in
the portal, then start your tool.
