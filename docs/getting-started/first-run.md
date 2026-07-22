# First Run

Once Edge Insights is installed, open the portal:

- **Windows** — start "Edge Insights" from the Start menu or the desktop
  shortcut. The application starts the services and opens the portal.
- **Linux desktop** — start "Edge Insights" from the application menu.
  The application starts the services and opens the portal in its own
  window.
- **Embedded device** — open `http://<device-hostname-or-ip>:36300` in a
  browser on another machine on the same network.

## Initial setup

The portal guides you through a short setup on first launch:

- **Welcome** — a short introduction to the portal.
- **CAN Settings** — select the CAN interface to capture from and set its
  bitrate. See [CAN Settings](../portal/can-settings.md) for details.
- **DBC upload** — upload a DBC file so Edge Insights can decode raw CAN
  frames into named signals.

If no CAN hardware is connected, you can use the bundled demo data
instead. See the [Import wizard](../portal/import-wizard.md).

The [Portal Guide](../portal/index.md) describes each part of the portal:
CAN settings, importing data, and the Grafana dashboards.
