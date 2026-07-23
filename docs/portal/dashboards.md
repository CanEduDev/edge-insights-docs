# Dashboards

Edge Insights embeds three Grafana dashboards in the portal. Open them
from **Dashboards** in the sidebar.

![Dashboards overview](../images/portal/dashboards/overview.png)

- **Bus Overview**: how does my system work? Frame counts, rates, CAN
  IDs, and timing classification.
- **Bus Health**: what is wrong? Anomalies, errors, foreign IDs, and
  congestion, with drill-down.
- **Signal Explorer**: decoded signal values, statistics, and a signal
  catalog. Needs a DBC file loaded on the interface, see
  [CAN Settings](can-settings.md#dbc-file).

Each dashboard opens embedded in the portal, with a link to open it
directly in Grafana. The time-range picker and refresh interval at the top
apply within the embed, same as in Grafana itself. Every panel has a
**(?)** icon next to its title. Hover over it for a description of what
the panel shows.

## Bus Overview

- **At a glance**: frames in range, active CAN IDs in range, latest bus
  utilization, and how many DBC messages are loaded.
- **Bus activity**: bus utilization over time, and CAN frame rate broken
  down by ID.
- **Bus behavior**: a timing summary per CAN ID, covering classification,
  confidence, sample count, and average interval, plus the overall
  classification distribution across periodic, aperiodic, and burst
  categories.

![Bus Overview dashboard](../images/portal/dashboards/bus-overview-embed.png)

## Bus Health

- **At a glance**: total anomalies, timing anomalies, congestion windows,
  and bus errors in range.
- **What's wrong, when**: an anomaly timeline, split by type: timing,
  congestion, foreign ID, bus error.
- **Anomaly list**: every anomaly in range, with timestamp, type, CAN ID,
  interface, a summary, and a count. Filter by CAN interface or anomaly
  type using the controls at the top.

![Bus Health dashboard](../images/portal/dashboards/bus-health-embed.png)

## Signal Explorer

- **Selection**: how many signals are selected versus available, and a
  table of the selected signals with their message, unit, and CAN ID.
  Pick signals from the **Signal** filter at the top.
- **Signal time series**: a plot per selected signal.
- **Signal statistics**: a summary table per selected signal, with
  sample count, min, max, average, and range.

![Signal Explorer dashboard](../images/portal/dashboards/signal-explorer-embed.png)
