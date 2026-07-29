# CAN Settings

Configure the CAN interface Edge Insights captures from, manage the DBC
file used to decode signals, and control baseline learning.

## Interface and bus settings

Select the interface to use and its bus parameters. Edge Insights
captures from one interface at a time; if your hardware has multiple
channels, choose which one to use here. Bus settings are locked while the
interface is up. Bring it down first to change them.

/// note
SocketCAN virtual interfaces have no physical bus parameters to set, so the
settings are greyed out, but the interface can be brought up anyway.
///

- **Bring Up** starts capture on the selected interface.
- **Bring Down** stops capture. Data captured just before bring-down is
  still processed in the background, so dashboards may keep updating
  briefly.

The **Boot Autostart** option brings up the configured interface when Edge
Insights is started.

![CAN Settings: interface offline](../images/portal/can-settings/offline.png)

![CAN Settings: interface online](../images/portal/can-settings/online.png)

## DBC file

Upload a DBC file to decode raw CAN frames into named signals. Uploading a
new file replaces the current one. Enable **J1939 Mode** to match frames
by PGN instead of exact CAN ID. Also works for NMEA2000.

![DBC File card](../images/portal/can-settings/dbc-card.png)

## Baseline

Baseline learning establishes what normal bus traffic looks like, so later
traffic can be compared against it. Set the learn duration (1–60 minutes)
and save; saving restarts the learn phase. **Restart baseline learning**
discards the current baseline and starts over.

![Baseline card](../images/portal/can-settings/baseline-card.png)

## Known CAN IDs

CAN IDs not covered by the DBC file, the baseline allowlist, or this list
are flagged as foreign traffic. IDs from an uploaded DBC file are added
automatically; add or remove IDs manually here.

IDs learned into the baseline are listed separately below. Removing one
drops it from the Detect-phase allowlist, and the change is permanent
until baseline learning is restarted (see [Baseline](#baseline)).

![Known CAN IDs card](../images/portal/can-settings/known-ids-card.png)

## Danger Zone

Clear all recorded data for an interface: raw frames, decoded signals,
and analysis results. This is not part of the normal import flow;
nothing runs unless you start it, and the action cannot be undone.

![Danger Zone card](../images/portal/can-settings/danger-zone.png)

![Danger Zone confirmation](../images/portal/can-settings/danger-zone-confirm.png)
