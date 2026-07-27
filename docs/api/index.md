# API Overview

Edge Insights exposes an HTTP API so you can integrate a running unit with
your own tools and workflows. Use it to connect Edge Insights to scripts,
analysis pipelines, or other systems, instead of working through the portal
by hand.

This page covers the design of the API: how to reach it, versioning,
authentication, the error model, and the behavior shared by the data
endpoints. The [API reference](reference.md) documents each endpoint one
by one.

/// warning | Experimental
The API is under development, meaning `v0` is experimental. During Beta
there is no backwards-compatibility guarantee: endpoints, parameters, and
responses can change in any release, and `v0` can change in ways that break
existing scripts. The move to `v1` will also not preserve backwards
compatibility.

The version is part of the URL path. Use `v0` in your scripts, test against
each Edge Insights update, and expect a future `v1` to differ.
///

## Base URL

The API is served by the portal on port `36300`:

```text
http://<unit-address>:36300/api/v0
```

Replace `<unit-address>` with `localhost` on the machine running Edge
Insights, or the unit's hostname or IP address from another machine on the
network. If you have changed the portal port with `PORTAL_PORT`, use that
port instead.

The examples on these pages use `localhost`, so you can copy and run them
as they are on the unit itself. From another machine, replace `localhost`
with the unit's address.

## Authentication

The Beta API has no authentication. Anyone who can reach port `36300` can
download the unit's full CAN history. This matches the portal, which is also
unauthenticated in Beta.

Edge Insights assumes it runs on a trusted network. Restrict access at the
network layer: keep the unit on a private network, or reach it over a VPN.
Do not expose port `36300` to the public internet.

A future release will add authentication. It will be introduced so that it
does not silently break existing `v0` scripts.

## Error model

Errors use one JSON shape everywhere:

```json
{ "error": "<stable_code>", "message": "<human explanation>" }
```

`error` is a stable code you can match on in scripts. `message` is a
human-readable explanation and can change between releases.

An unknown path returns `404` with error code `not_found`. A known path
called with the wrong method returns `405` with `method_not_allowed`. If you
see `not_found` on an endpoint listed in the reference, the unit is running a
different API version.

Each endpoint's own error codes are listed with it in the
[API reference](reference.md).

## Timestamps

All timestamps in the API — the `from` and `to` query parameters and the
`cut_points_us` values in a size estimate — are **microseconds** since the
Unix epoch. For example, `1752700000000000` is a whole number of
microseconds, not seconds or milliseconds.

## Streaming and retention

The two download endpoints, `logs/raw` and `logs/decoded`, stream their
responses with chunked transfer encoding. There is no `Content-Length`,
because the size is not known while streaming, and an empty time range
returns `200` with an empty body. Nothing is written to the unit's disk
during an export, and memory use stays constant regardless of export size.

**Resuming.** Because responses are streamed, a dropped connection appears as
a truncated body. To resume, send the request again with `from` set to the
timestamp of the last batch you fully received. `from` is exclusive, so
nothing is skipped and only the missing remainder is sent:

```bash
curl -f -o part2.zst \
  'http://localhost:36300/api/v0/logs/raw?interface=can0&from=1752700123000000'
cat part1-good-prefix.zst part2.zst | zstd -d > full.bin
```

**Retention.** The unit removes old records on an hourly retention job, and
it evicts data under storage pressure. A long export can race both, so the
range you asked for can shrink while the download runs. This is by design:
the unit's main job is recording, not archival. A response with less data
than expected means the window moved. The API cannot tell "pruned" apart
from "never recorded".

## Endpoints

Start with `logs/interfaces`. It gives you the interface names that every
other endpoint needs.

| Endpoint | What it returns |
| --- | --- |
| [`GET /api/v0/logs/interfaces`](reference.md#logs-interfaces) | Interfaces with stored data or attached now, and their state |
| [`GET /api/v0/logs/raw`](reference.md#logs-raw) | Raw CAN frames, as a compact binary stream or as candump text |
| [`GET /api/v0/logs/decoded`](reference.md#logs-decoded) | DBC-decoded signals, as a binary stream |
| [`GET /api/v0/logs/raw/estimate`](reference.md#logs-estimate) | Export size estimate and slice boundaries for a range |
| [`GET /api/v0/logs/export/status`](reference.md#export-status) | Status of the transcoding export queue |
