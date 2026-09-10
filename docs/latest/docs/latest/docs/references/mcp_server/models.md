# Models

> The Klique MCP server's models module, with tools and resources for the model registry and for serving endpoints and their container instances.

The Klique MCP server exposes functionalities to work with models: searching, registering, editing and
deleting model records, as well as model serving, the fleet of serving endpoints and container instances
behind them.

## Tools

### list_models

Search and list the models visible to you, with filtering, projection, ordering and pagination.

**Parameters**

- `id` (list[string]) - Filter by model ids.
- `name` (string) - Filter by name regex. Unanchored and case-sensitive, matching anywhere in the name:
  `train` behaves like `.*train.*`, while `*train*` is invalid. Anchor with `^`/`$` for a whole-name
  match, and write `\.` for a literal dot.
- `user` (list[string]) - Filter by creating-user ids.
- `project` (list[string]) - Filter by project ids.
- `task` (list[string]) - Filter by creating-task ids.
- `ready` (boolean) - Restrict to ready or not-ready models.
- `framework` (list[string]) - Filter by framework.
- `uri` (list[string]) - Filter by storage URI.
- `tags` (list[string]) - User tag filters (`-` prefix excludes a tag).
- `system_tags` (list[string]) - System tag filters (`-archived` excludes archived models).
- `last_update` (list[string]) - Range constraints on the last-update timestamp (`>`/`>=`/`<`/`<=`
  prefixes).
- `search_text` (string) - Free-text search.
- `only_fields` (list[string]) - Projection: only these dotted field paths are returned.
- `order_by` (list[string]) - Sort fields (`-` prefix for descending).
- `page` (integer) - Zero-based page index.
- `page_size` (integer) - Models per page.
- `include_subprojects` (boolean) - Include subproject models when `project` is set (API server version `>=2.26`).
- `scroll_id` (string) - Scroll-based paging cursor (API server version `>=2.15`).
- `refresh_scroll` (boolean) - Refresh the scroll cursor (API server version `>=2.15`).
- `size` (integer) - Scroll page size (API server version `>=2.15`).

**Returns**

A list of model objects, each with the model's identifying and descriptive fields available to you.

Each call returns a single page, not the full set. With no `page_size`, the server defaults to its
own maximum (500), and a full page only means *at least* that many models exist, not exactly that
many. To read everything, pass `page_size` (useful up to 500; larger values are capped, not
rejected) and increase `page` until a page comes back shorter than requested.

### create_model

Register a new model from a URI and return its ID, for importing an artifact not produced by a task.

**Parameters**

- `uri` (string, required) - Storage URI of the model artifact.
- `name` (string, required) - The new model's name, unique within the company.
- `comment` (string) - Free-text comment.
- `tags` (list[string]) - User tags to attach.
- `system_tags` (list[string]) - System tags to attach.
- `framework` (string) - Framework name (case-insensitive).
- `design` (object) - Network design JSON.
- `labels` (object) - Label name -> ID map.
- `ready` (boolean, default `false`) - Mark the model ready on creation.
- `public` (boolean, default `false`) - Create the model as public.
- `project` (string) - Owning project ID.
- `parent` (string) - Parent model ID.
- `task` (string) - Associated task ID.
- `metadata` (object) - Metadata items keyed by name, each a `{key, type, value}` object (API server version `>=2.13`).

**Returns**

The ID of the newly created model, as a string.

### edit_model

Edit the editable fields of an existing model by ID. Only the supplied fields are changed.

**Parameters**

- `model` (string, required) - The ID of the model to edit.
- `uri` (string) - New storage URI.
- `name` (string) - New name.
- `comment` (string) - New comment.
- `tags` (list[string]) - New user tags.
- `system_tags` (list[string]) - New system tags.
- `framework` (string) - New framework name (case-insensitive).
- `design` (object) - New network design JSON.
- `labels` (object) - New label name -> ID map.
- `ready` (boolean) - Whether to mark the model ready.
- `project` (string) - New owning project ID.
- `parent` (string) - New parent model ID.
- `task` (string) - New associated task ID.
- `iteration` (integer) - Iteration number; updates the task's stats.
- `metadata` (object) - New metadata items keyed by name, each a `{key, type, value}` object
  (API server version `>=2.13`).

**Returns**

The edit outcome: `updated` (models changed, `1` or `0`) and `fields`, the changed values.

### delete_model

Delete a single model by ID. Without `force`, the call fails when the model is in use.

**Parameters**

- `model` (string, required) - The ID of the model to delete.
- `force` (boolean) - Delete despite usage by other tasks or a published creating task.

**Returns**

The deletion outcome: `deleted`, and `url`, the storage URL of the removed file, when present.

### list_loading_instances

List model-serving containers that are registered but not yet serving, i.e. still loading their model.

**Parameters**

None.

**Returns**

A list of loading-instance objects describing each container's model and how long it has been loading.

### get_serving_endpoint_metrics

Fetch time-series metric charts for one model-serving endpoint over a time range.

**Parameters**

- `endpoint_url` (string, required) - The URL of the endpoint to chart.
- `from_date` (integer, required) - Range start, seconds since epoch.
- `to_date` (integer, required) - Range end, seconds since epoch.
- `interval` (integer, required) - Bucket interval in seconds, minimum 1.
- `metric_type` (string, default `requests`) - Metric to chart: one of `requests`, `requests_min`,
  `latency_ms`, `cpu_count`, `gpu_count`, `cpu_util`, `gpu_util`, `ram_total`, `ram_used`, `ram_free`,
  `gpu_ram_total`, `gpu_ram_used`, `gpu_ram_free`, `network_rx`, `network_tx`.
- `instance_charts` (boolean, default `true`) - Include per-instance charts alongside the aggregated
  total.

**Returns**

The metrics-history payload: the interval actually used, plus aggregated and per-instance time-series data.

## Resources

### klique://serving/endpoints

The collection of all registered model-serving endpoints for your company, with statistics
aggregated across the serving container instances behind each one.

**Parameters**

None.

**Returns**

A JSON list of endpoint objects summarizing each endpoint's instances, request volume, latency and
last update.

### klique://serving/endpoints/{endpoint_id}

Detailed information for one serving endpoint, including a per-instance breakdown of every serving
container behind it.

**Parameters**

- `endpoint_id` - The endpoint URL identifying the endpoint, URL-encoded into the path.

**Returns**

A JSON object with endpoint-level metadata and a per-instance breakdown of the containers behind it.

## Prompts

### serving_health

Produce a health summary of the model-serving fleet, or of a single endpoint, by composing the
module's serving reads.

**Parameters**

- `endpoint_url` (string) - Focus the report on a single endpoint (by URL). If omitted, summarizes the
  whole fleet.
- `include_metrics_history` (boolean, default `false`) - Also pull and summarize recent time-series
  trends.
- `lookback_hours` (integer, default `24`) - Hours of metrics history to chart when
  `include_metrics_history` is true.

**Returns**

A single instruction message walking the agent through the health check, scoped to the requested
endpoint or the whole fleet.
