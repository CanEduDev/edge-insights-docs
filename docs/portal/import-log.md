# Import Log

Import a CAN log file for offline analysis, or load the bundled demo data
to explore Edge Insights without live hardware. Open it from **Import
Log** in the portal sidebar.

## 1. Source

Choose a log file to upload, or use the bundled demo data, a short
CanEduDev Rover sample with its own DBC file. Supported formats: candump
(`.log`), MF4 (`.mf4`), Vector ASC (`.asc`), Kvaser text (`.txt`), and
Kvaser Memorator (`.kme…`). CAN FD is only supported in candump and MF4
files; the other formats carry classic CAN only.

![Import Log: Source](../images/portal/import-log/01-source.png)

## 2. Interface & bitrate

Choose the interface to import into and its bitrate.

![Import Log: Interface & bitrate](../images/portal/import-log/02-interface.png)

## 3. Timestamps

Keep the original recorded timestamps to recreate a past session, or shift
every frame so the last one lands at "now". Useful for demos.

![Import Log: Timestamps](../images/portal/import-log/03-timestamps.png)

## 4. DBC file (optional)

Keep the interface's current DBC file, upload a new one, or import with no
DBC. Demo data always uses its bundled DBC.

![Import Log: DBC file](../images/portal/import-log/04-dbc.png)

## 5. Review & run

Review the summary and start the import. Clearing existing data for the
interface first is recommended so results aren't mixed with a previous
import.

![Import Log: Review & run](../images/portal/import-log/05-review.png)

Once the import finishes, open the dashboards to see the results.

![Import Log: done](../images/portal/import-log/done.png)
