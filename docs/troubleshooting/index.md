# Troubleshooting

## Windows blocks the download or the installer

Windows can show security warnings when you download the installer
(Microsoft Edge) or run it (Windows SmartScreen). Both are expected. See
[Security warnings](../getting-started/installation-windows.md#security-warnings)
on the Windows installation page for what to do.

## CAN interface not found

**Linux**: check which interfaces are available:

```bash
ip link show
```

If you don't have CAN hardware attached, you can create a virtual bus for
testing:

```bash
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
```

**Windows**: open Kvaser Device Guide and confirm your Kvaser device
appears there. If you just installed the Kvaser drivers, reboot your PC
before checking again. A restart is often required before a newly
installed device is enumerated.

## Sharing the CAN bus with other tools

While capture is running, Edge Insights owns and configures the CAN
interface: it sets the bus parameters and brings the bus on when capture
starts, and takes it back down when capture stops.

You can run other tools alongside Edge Insights to observe the same bus,
but they must not try to reconfigure it:

- **Kvaser**: other tools, e.g. CanKing, `candump` via canlib, custom
  canlib applications, can read and write frames alongside Edge Insights
  as long as they open their channel without init access. Any attempt
  from such a tool to change bus parameters or driver mode will silently
  do nothing while Edge Insights holds the bus. Reads and writes still
  work, but configuration changes appear to succeed without taking
  effect.
- **Linux (SocketCAN)**: other processes can open sockets on the same
  interface to read or write frames, but avoid running `ip link set` on
  that interface while capture is running. Changing the interface state
  from outside can leave it out of sync with what Edge Insights expects.

If you need a tool to have exclusive control of the bus, e.g. to
reconfigure bus parameters, stop capture first by toggling the bus off in
the portal, then start your tool.

## The portal loads, but every save or upload fails

The portal opens normally and shows current data, but any action that
changes something, e.g. saving CAN settings, uploading a DBC file, or
toggling an interface, shows a toast like "Failed to save CAN config. See
details." Expanding the details shows:

```json
{ "error": "host_not_allowed", "message": "Request Host is not an address of this unit" }
```

This happens when you reach the unit through a name it doesn't recognize
as its own: a custom DNS record, a reverse proxy, or a tunnel.

Add the name (or IP) to `PORTAL_ALLOWED_HOSTS`, a comma-separated list of
extra values the check accepts. Ports are ignored:

```
PORTAL_ALLOWED_HOSTS=unit.corp.example,10.0.0.7
```

On an installed unit, add the line to
`~/.local/share/ced-edge-insights/runtime/env`, then restart the portal:

```bash
systemctl --user restart analytics-portal
```

Only add names that actually resolve to this unit. Each entry widens what
the check accepts, so an unused or stale entry is a small extra risk for
no benefit.

## Still stuck

Use [Help & Support](../portal/support.md) in the portal to send your
application logs to CanEduDev support. You can also reach out to us at
[support@canedudev.com](mailto:support@canedudev.com).
