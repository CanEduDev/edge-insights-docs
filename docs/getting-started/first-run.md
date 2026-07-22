# First Run

Once Edge Insights is installed, open the portal:

- **Windows** — launch "Edge Insights" from the Start menu (or your
  desktop shortcut). The launcher starts the stack and opens the portal
  for you.
- **Linux** — open <http://localhost:36300> in your browser.

## What to expect

The portal walks you through a short setup on first launch:

- **Welcome** — a quick introduction to the portal.
- **CAN Settings** — select the CAN interface you want to capture from and
  set its bitrate. See the [Portal Guide](../portal/can-settings.md) for
  details.
- **DBC upload** — upload a DBC file so Edge Insights can decode raw CAN
  frames into named signals.

No CAN hardware handy? You can still explore Edge Insights using bundled
demo data via the [Import wizard](../portal/import-wizard.md) instead of a
live bus.

From here, the [Portal Guide](../portal/index.md) covers each part of the
portal — CAN settings, importing data, and the Grafana dashboards — in more
depth.
