# Events

> The Klique MCP server's events module, with tools and a resource for a task's console log and the scalar metrics it reported.

Events cover what a task reported while it ran: its console log, and the scalar metrics you judge the
run on.

## Tools

### download_task_log

Download a task's entire console log (`stdout`/`stderr` lines) as one rendered attachment.

**Parameters**

- `task` (string, required) - ID of the task whose full console log to download.
- `line_type` (string, default `json`) - Rendering per line: `json` (one JSON object per
  line) or `text` (formatted via `line_format`).
- `line_format` (string, default `{asctime} {worker} {level} {msg}`) - Line template used
  when `line_type` is `text`; only `{asctime}`, `{timestamp}`, `{level}`, `{worker}`, `{msg}`
  placeholders are allowed.

**Returns**

The full rendered log, as a single string.

### get_task_metric_values

Read the latest scalar metric values of one or more tasks, for judging how a run turned out and
comparing runs against each other.

**Parameters**

- `task` (string or list[string], required) - ID of the task to read, or several IDs to compare runs in
  one call.

**Returns**

One entry per requested task, each carrying the task ID and its scalar metrics, with the latest and
the last hundred values of every variant.

### list_task_metrics

List which metrics and variants the given tasks actually reported, without reading any values.

**Parameters**

- `task` (string or list[string], required) - Task IDs to collect reported metric names from.
- `event_type` (string) - Restrict the scan to one event type: `training_stats_scalar`,
  `training_stats_vector`, `training_debug_image`, `plot` or `log`. Scalars (`training_stats_scalar`) are
  the numbers a run is judged on; the other types cover vectors, debug images, plots, and log lines. If
  omitted, every type is scanned.
- `model_events` (boolean) - Treat the given IDs as model IDs rather than task IDs.

**Returns**

The distinct metric names, with the variants reported under each.

## Resources

### klique://tasks/{task_id}/log

The console log (`stdout`/`stderr` lines) of a task, most recent first, read in paged batches by
timestamp.

**Parameters**

- `task_id` - ID of the task whose log to read.
- `batch_size` - Number of log lines to return.
- `navigate_earlier` - Navigation direction: `true` reads latest to earliest, `false` reads earliest
  to latest, defaults to `true`. Accepted spellings (case-insensitive): `true`/`1`/`yes` and
  `false`/`0`/`no`.
- `from_timestamp` - Navigation anchor, a millisecond UTC timestamp; returns events strictly beyond
  it in the navigation direction.
- `order` - Re-orders the returned lines relative to the navigation direction: `asc` or `desc`.
- `metrics` - Metric/variants filter, a URL-encoded JSON list of `{"metric": ..., "variants": [...]}`
  objects. Requires API server version `>=2.30`.

**Returns**

A JSON object with the batch of log events for the requested page, plus how many were returned and
how many exist in total.
