# API Reference

This page documents each `/api/v0` endpoint in turn. See the
[API overview](index.md) for the base URL, authentication, the error
model, the timestamp format, and the shared streaming and retention behavior
of the download endpoints.

## GET /api/v0/logs/interfaces { #logs-interfaces }

List the interfaces the unit knows about. Every other endpoint on this page
needs an `interface` value, so this is where a script starts.

The list covers interfaces with stored data and interfaces attached to the
unit right now. A unit that has recorded nothing still reports the interfaces
it can record from.

This endpoint takes no query parameters.

### Response

```json
[
  {
    "interface": "can0",
    "entry": "can0",
    "state": "online",
    "raw": true,
    "decoded": true
  },
  {
    "interface": "Kvaser USBcan Pro 5xHS [SN 10822] (channel 0)",
    "entry": "Kvaser_USBcan_Pro_5xHS_SN_10822_channel_0",
    "state": "disconnected",
    "raw": true,
    "decoded": false
  }
]
```

- `interface`: the name the unit shows. Pass this value as the `interface`
  query parameter on the other endpoints.
- `entry`: the sanitized storage key. It is the first part of export file
  names, and the interface name that appears inside candump lines. This value
  works as `interface` too.
- `state`: whether the interface is attached to the unit right now. See
  [Interface state](#interface-state).
- `raw`: whether [`logs/raw`](#logs-raw) has data for this interface.
- `decoded`: whether [`logs/decoded`](#logs-decoded) has data for this
  interface. This is `false` for every interface when no DBC file is
  configured.

`raw` and `decoded` describe stored data, and `state` describes hardware. The
two are independent. An `online` interface can hold no data yet, and a
`disconnected` one is listed because it still holds data. When both `raw` and
`decoded` are `false`, downloading that interface returns
`404 unknown_interface`.

The list is sorted by `entry`. A unit with no stored data and no attached
interfaces returns `200` with an empty array. Unreachable storage returns
`503`, so an empty array always means "nothing there", never "could not
check".

### Interface state { #interface-state }

| Value | Meaning |
| --- | --- |
| `online` | Attached and bus-on. Recording can be running. |
| `offline` | Attached but bus-off. Bring the interface up before it records. |
| `disconnected` | Not attached, but data is stored under this name. The hardware was unplugged, or the data was imported. The stored data is still downloadable. |
| `unknown` | The unit could not query its CAN backend, usually a missing or broken driver, so it cannot tell whether the interface is attached. |

The list carries no timestamps. To find the stored time range of one
interface, call [`raw/estimate`](#logs-estimate) without `from` and `to`, then
read `first_record_us`.

```bash
curl -fs 'http://localhost:36300/api/v0/logs/interfaces'
```

### Errors

| Status | Error code | Meaning |
| --- | --- | --- |
| `503` | `unavailable` | The storage backend is unreachable. |

## GET /api/v0/logs/raw { #logs-raw }

Download recorded raw CAN frames over a time range, as a compact binary
stream or transcoded to candump text.

### Query parameters

| Parameter | Required | Meaning |
| --- | --- | --- |
| `interface` | Yes | Interface name as the unit shows it. SocketCAN names such as `can0` and full Kvaser descriptions both work. The server normalizes them. |
| `from` | No | Microsecond Unix timestamp. Exclusive lower bound on record timestamps. This is also the resume handle. |
| `to` | No | Microsecond Unix timestamp. Exclusive upper bound. Defaults to now. |
| `format` | No | Omit for the binary stream. Use `log` for candump text, or `log.zip` for candump text inside a zip archive. See [Output formats](#output-formats). |
| `part` | No | Requires `format`. 1-based part number of a segmented export. Only affects the download file name. |
| `export_id` | No | Requires `format`. A token you choose (1 to 64 characters of `A-Z`, `a-z`, `0-9`, `_`, `-`) so you can follow the export with [`GET /api/v0/logs/export/status`](#export-status). |

The response is `200` with chunked transfer encoding. See
[Streaming and retention](index.md#streaming-and-retention) for resume
and retention behavior.

```bash
# Everything recorded for can0, as the binary stream:
curl -fO 'http://localhost:36300/api/v0/logs/raw?interface=can0'

# A Kvaser interface. URL-encode the description:
curl -f -o kvaser.zst \
  'http://localhost:36300/api/v0/logs/raw?interface=Kvaser%20USBcan%20Pro%205xHS%20%5BSN%2010822%5D%20(channel%200)'
```

### Output formats

The raw endpoint can return data in three formats.

**Binary stream (default).** Omit `format`. The body is the unit's stored
records concatenated as a sequence of zstd frames. Standard zstd tooling
decompresses the whole stream in one pass:

```bash
zstd -d kvaser.zst -o kvaser.bin
```

This is the most compact format. It is best when you record often and want
to move data efficiently, but you need to parse the decompressed binary
frames yourself.

**candump text (`format=log`).** The raw endpoint transcodes to candump text
on the server, one frame per line. This is the format `candump -l` writes and
most CAN tools read:

```text
(1752700000.003950) can0 10EF1721#0203C3007FFFFFFF R
```

The response `Content-Type` is `text/plain`. Text is much larger than the
binary form, so the body is compressed on the wire based on your
`Accept-Encoding` header (`zstd` preferred, then `gzip`). Browsers and
`curl --compressed` decompress it for you.

```bash
# One hour of can0 as a plain candump log, compressed on the wire:
curl -f --compressed -o can0.log \
  'http://localhost:36300/api/v0/logs/raw?interface=can0&format=log&from=1752700000000000&to=1752703600000000'
```

The interface name inside each line is a sanitized key. Kvaser descriptions
contain spaces, and the candump line format is separated by spaces, so the
description is not used verbatim.

**candump text in a zip (`format=log.zip`).** The same candump text, stored
as the single entry of a zip archive (`Content-Type: application/zip`, no
transfer-layer compression). Use this for segmented exports whose parts
should stay compressed on disk. The archive is zip32: an entry that would
exceed 4 GiB stops the stream, so slice large ranges into parts first (see
[`raw/estimate`](#logs-estimate)).

**The transcoding queue.** Transcoding to text uses one CPU core for the
duration of the download, so **only one text export runs at a time**. Further
requests wait in a bounded queue. When the queue is full you get
`429 export_queue_full`. Retry later.
[`GET /api/v0/logs/export/status`](#export-status) reports the queue state
if you want to check before requesting. The binary stream is not queued.

To follow a specific export, add `export_id=<token>` to the download, then
poll [`GET /api/v0/logs/export/status?id=<token>`](#export-status).

### Errors

| Status | Error code | Meaning |
| --- | --- | --- |
| `400` | `invalid_request` | Missing `interface`, non-numeric `from` or `to`, `from` greater than `to`, or a duplicated query parameter. `from` equal to `to` is a valid empty range and returns `200` with an empty body. |
| `404` | `no_data` | Nothing has ever been recorded on this unit. |
| `404` | `unknown_interface` | No data has been recorded for that interface. This is the usual "nothing yet" answer. |
| `413` | `export_too_large` | Text formats only. The estimated output is larger than the unit's configured limit. Download in parts. |
| `429` | `export_queue_full` | Text formats only. A text export is already running and the wait queue is full. Retry later. |
| `503` | `unavailable` | The storage backend is unreachable. |

## GET /api/v0/logs/decoded { #logs-decoded }

Download DBC-decoded signals over a time range, as a binary stream. This
requires a DBC file to be configured in the portal; without one there are no
decoded signals to return.

### Query parameters

| Parameter | Required | Meaning |
| --- | --- | --- |
| `interface` | Yes | Interface name as the unit shows it. SocketCAN names such as `can0` and full Kvaser descriptions both work. The server normalizes them. |
| `from` | No | Microsecond Unix timestamp. Exclusive lower bound on record timestamps. This is also the resume handle. |
| `to` | No | Microsecond Unix timestamp. Exclusive upper bound. Defaults to now. |

The decoded endpoint does not support `format`. It returns only the binary
stream: a sequence of zstd frames, this time carrying decoded signal batches
rather than raw frames. Decompress with standard zstd tooling and parse the
binary frames yourself.

```bash
curl -f -o decoded.zst \
  'http://localhost:36300/api/v0/logs/decoded?interface=can0&from=1752700000000000&to=1752703600000000'
```

### Errors

| Status | Error code | Meaning |
| --- | --- | --- |
| `400` | `invalid_request` | Missing `interface`, non-numeric `from` or `to`, `from` greater than `to`, or a duplicated query parameter. `from` equal to `to` is a valid empty range and returns `200` with an empty body. |
| `409` | `no_dbc_configured` | No DBC file is configured, so decoded data cannot exist. Upload a DBC in the portal first. |
| `404` | `no_decoded_data` | A DBC is configured, but no decoded data exists yet. |
| `404` | `unknown_interface` | No data has been recorded for that interface. |
| `503` | `unavailable` | The storage backend is unreachable. |

## GET /api/v0/logs/raw/estimate { #logs-estimate }

Estimate the size of a raw export and, optionally, compute part boundaries
for slicing a large range. It answers from record metadata only, without
reading frame payloads, so it is cheap to call — for example, on every change
of a time-range picker.

### Query parameters

| Parameter | Required | Meaning |
| --- | --- | --- |
| `interface` | Yes | Interface name, same as [`logs/raw`](#logs-raw). |
| `from` | No | Microsecond Unix timestamp, exclusive lower bound. |
| `to` | No | Microsecond Unix timestamp, exclusive upper bound. Defaults to now. |
| `format` | No | The output format to estimate for. Defaults to `log`. |
| `segment_bytes` | No | When set, compute part boundaries for slicing. See [Slicing large exports](#slicing-large-exports). |

### Response

```json
{
  "entry": "can0", "first_record_us": 1752700000000000,
  "frames": 6100000,
  "stored_bytes": 36000000, "uncompressed_bytes": 146000000,
  "estimated_output_bytes": 321000000,
  "max_file_bytes": 2147483648,
  "cut_points_us": []
}
```

`first_record_us` is the timestamp of the first record in the range. It is
`null` when the range holds nothing, so check it before you start a download.
Call the endpoint without `from` and `to` to find the oldest data the unit
still holds for that interface.

`entry` is the sanitized storage key, the same value
[`logs/interfaces`](#logs-interfaces) returns.

`estimated_output_bytes` is the size to show a user before they start a
download. `max_file_bytes` is the unit's configured per-file limit (default
2 GiB). When the estimate is larger than this limit, download the range in
parts instead of one file.

All numbers are estimates: the binary-to-text expansion is a measured
average, and retention can shrink the range between the estimate and the
download.

### Slicing large exports

To get the part boundaries, add `segment_bytes=<max_file_bytes>` to the
request. The response then fills in `cut_points_us`: the timestamp of the
last record of each part except the final one.

Part `k` of `n` then downloads from [`logs/raw`](#logs-raw) with:

- `from` = the previous cut point, or the original `from` for the first part.
  `from` is exclusive, so the previous part's last record is not repeated.
- `to` = this part's cut point plus 1, or the original `to` for the final
  part. This includes the cut record in the part.

Slices are exact and leave no gaps.

### Errors

Returns `400 invalid_request`, `404 unknown_interface`, or `503 unavailable`,
with the same meanings as [`logs/raw`](#logs-raw).

## GET /api/v0/logs/export/status { #export-status }

Report the state of the text-transcoding export queue. Only the `format=log`
and `format=log.zip` downloads are queued, so this endpoint concerns text
exports; the binary stream is never queued.

### Query parameters

| Parameter | Required | Meaning |
| --- | --- | --- |
| `id` | No | An `export_id` token that was passed to a [`logs/raw`](#logs-raw) download. Adds that export's lifecycle to the response. |

### Response

Without `id`, the response reports overall queue occupancy:

```json
{ "active": true, "queued": 2 }
```

`active` is whether a text export is currently running. `queued` is how many
are waiting behind it.

With `id=<token>`, the response also follows that specific export:

- `id_state` — one of `"queued"`, `"active"`, `"done"` (kept for the most
  recent exports), or `"unknown"` (the download never reached the server).
  `"done"` means the stream ended; it cannot tell a finished download apart
  from a client that disconnected partway through.
- `id_filename` — the file name the export is served under, once it starts
  streaming.
