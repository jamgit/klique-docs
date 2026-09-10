# Apps

> The Klique MCP server's apps module, with tools, resources, and prompts for the app catalog and for launching, monitoring, and managing app instances.

The Klique MCP server exposes functionalities to work with applications:

* **Application catalog**: discovering, installing, cloning, and categorizing the apps a tenant may
  launch.
* **Application instances**: launching, listing, inspecting, editing, relaunching, stopping, and
  deleting the running copies of those apps.

## Tools

### get_app_info

Read app-catalog and template information behind an `op` selector to determine which info to retrieve.
Options are:

- `get_launch_template` - Launch wizard template for an app or customization.
- `get_info_template` - Dashboard layout of a running instance.
- `get_apps` - Catalog rows visible to the company.
- `get_categories` - The installed, alphabetically sorted catalog categories. Requires API server version `>=2.37`.
- `get_asset` - Binary asset bundled with an app version.

**Parameters**

- `op` (string, required) - Which app-catalog read operation to perform.
- `app` (string) - Application or customization ID; required for `get_asset`, or as an alternative to
  `instance` for `get_launch_template`; ignored by other ops.
- `instance` (string) - Application instance ID; required for `get_info_template`; for
  `get_launch_template` pins the template to that instance's app version; ignored by other ops.
- `dependent_on` (object) - `get_launch_template` only: selected values restricting the template to
  dependent fields (also applied when computing choice queries).
- `source_ui_render` (boolean) - `get_launch_template` only (API server version `>=2.34`): default for
  source rendering when `ui_render` is unset on the source.
- `include_ids` (list[string]) - `get_apps` only (API server version `>=2.25`): application-id regexes;
  only matching apps are returned.
- `exclude_ids` (list[string]) - `get_apps` only (API server version `>=2.25`): application-id regexes;
  matching apps are excluded.
- `app_usages` (boolean) - `get_apps` only (API server version `>=2.33`): also return running/pending
  counts per app and the apps-queue rollup.
- `version` (string) - `get_asset` only, and required there: the application version the asset is
  bundled with.
- `uri` (string) - `get_asset` only, and required there: the asset uri within the app bundle (e.g.
  `icon.png`).

**Returns**

The shape depends on `op`: a launch-template object, a dashboard-template object, the catalog list,
the category-name list, or an asset object (`content_base64`, `content_type`).

### list_app_instances

Search and list application instances. The search can be filtered and paginated.

**Parameters**

- `app` (string) - Application ID to filter instances by; required for non-admin callers (admins may
  omit it to list across apps).
- `status` (string) - Filter by instance status: `running` (queued/in_progress task) or `stopped`
  (everything else).
- `current_user` (boolean) - Only instances launched by the caller (API server version `>=2.17`).
- `updated_since` (integer) - Epoch-seconds lower bound; only instances updated after this time
  (API server version `>=2.17`).
- `name` (string) - Regex matched against the instance name (API server version `>=2.31`). Unanchored and
  case-sensitive, matching anywhere in the name: `train` behaves like `.*train.*`, while `*train*` is
  invalid. Anchor with `^`/`$` for a whole-name match, and write `\.` for a literal dot.
- `summary` (boolean) - Return the lighter summary shape instead of full info (API server version `>=2.31`).
- `show_archived` (boolean) - Include archived instances, hidden by default (API server version `>=2.35`).
- `include_customizations` (boolean) - Also return instances of every customization of `app` (API server
  version `>=2.36`).
- `page` (integer) - Zero-based page index, minimum 0 (API server version `>=2.31`).
- `page_size` (integer) - Instances per page, between 1 and 5000 (API server version `>=2.31`).

**Returns**

`instances` (each with instance/task fields, more in the full than summary shape) and, on the first
page only, `usage` (a running/max_allowed rollup).

Each call returns a single page, not the full set. With no `page_size`, the server defaults to its
own maximum (500), and a full page only means *at least* that many instances exist, not exactly
that many. To read everything, pass `page_size` (useful up to 5000; larger values are capped, not
rejected) and increase `page` until a page comes back shorter than requested.

### upload_app

Install or update a custom app from an `.app.conf` plus inline assets and deploy it in one call. The
only app-creation tool open to non-admins (regular users and admins get a tenant-owned app, system/root
callers get a global app deployed to all tenants). It never starts a running instance, and
re-uploading an existing app ID succeeds only for the owning tenant and user.

**Parameters**

- `config` (string, required) - Base64 of the `.app.conf` HOCON text; must include `id`, `name`,
  `provider` (overwritten by the server), `description`, `wizard`, `dashboard`.
- `execution_files` (list[object]) - Code/config files written to disk at instance start, each a
  `{file_name, contents, directory}` object with base64 `contents` and relative paths allowed
  (default directory `~/app`); total decoded size capped at 8 MB.
- `assets` (object) - Base64 app assets: `icon` and `no_info_html` (each overriding the `.app.conf`'s
  value), `extra` files (each a `{file_name, contents}` object), and the five status icons
  (active, error, idle, loading, stopped). Provide all five or none.
- `update_existing` (boolean, default `true`) - Allow updating an already-installed version; false
  rejects the upload if the version exists.
- `enable` (boolean, default `true`) - Enable the uploaded version so it can be launched.
- `make_latest` (boolean, default `true`) - Mark this version as the app's latest.

**Returns**

The upload outcome: the uploaded app ID, the registered application ids, stored controller config per
app, and whether the app is now deployed/launchable.

### clone_app

Create an independent tenant-owned copy of a visible catalog or tenant app, not a running instance.
Admin only, requiring the `app_management` company feature; may be unsupported on older API server versions.

**Parameters**

- `app` (string, required) - Source app ID to copy; must be a base app (a customization cannot be
  cloned, clone its base app instead).
- `name` (string, required) - Display name of the new app; unique per tenant+category, so a duplicate
  name in the target category is refused.
- `description` (string) - Description of the new app; defaults to the source's description.
- `category` (string, default `My Applications`) - Target category, not inherited from the source. The
  default keeps the copy private to you; pass a real category (or use `set_app_category` later) to
  share it.
- `icon` (string) - Icon for the new app.

**Returns**

The new app's ID, as `id`.

### set_app_category

Move a tenant-owned app (the app template, not a running instance) to another category, which
determines its visible audience (admin only), requiring the `app_management` company feature.

**Parameters**

- `app` (string, required) - Tenant-owned app ID to move; any app your tenant does not own reads
  as not found.
- `category` (string, required) - New category, e.g. `Admins only` (list categories via
  `get_app_info` with `op: "get_categories"`).

**Returns**

The moved app's ID, as `id`.

### launch_app_instance

Launch a new instance of an application or customization with the supplied `launch_params`, creating
and enqueuing the backing task.

**Parameters**

- `app` (string, required) - App or customization ID to launch.
- `launch_params` (object, required) - Launch params, validated against the app's launch template:
  unknown entries rejected, missing required entries fail fast, entry defaults filled.
- `task_name` (string) - Override the default task name of the new instance (API server version `>=2.31`).
- `user_groups` (list[string]) - Sharing group IDs for the new instance (API server version `>=2.32`).

**Returns**

The new instance ID, as `instance`.

### relaunch_app_instance

Stop and archive an existing app instance and launch a fresh one reusing its stored configuration and
sharing groups, returning the new instance's ID (the archived original is deleted once the new
instance reaches a terminal status); may be unsupported on older API server versions.

**Parameters**

- `instance` (string, required) - ID of the instance to relaunch.
- `launch_params` (object) - Override of the stored launch params; omitted fields reuse the instance's
  stored values.
- `task_name` (string) - Override the default task name of the new instance.
- `user_groups` (list[string]) - Replacement sharing group IDs for the new instance; reused from the
  original when omitted.

**Returns**

The new instance's ID (not the relaunched original's), as `instance`.

### edit_app_instance

Update the params (and optionally the sharing groups) of an existing app instance in place, without
restarting it. Only wizard entries flagged updatable can be changed, and readonly instances cannot be
modified at all.

**Parameters**

- `instance` (string, required) - ID of the instance to edit.
- `update_params` (object, required) - Params to update, keyed by wizard-entry name; only entries
  flagged updatable can be changed on a live instance.
- `user_groups` (list[string]) - Replace the instance's sharing groups (API server version `>=2.32`): a
  non-empty list sets the new groups, an empty list clears them, and omitting the field leaves them
  unchanged.

**Returns**

`updated`: `1` if the instance was updated, `0` otherwise.

### delete_app_instance

Delete an app instance and its task data (run history, logs, results, output models/artifacts)
permanently. With `force=false`, a stopped instance is deleted while a running one is left
untouched; `force=true` deletes it regardless of state.

**Parameters**

- `instance` (string, required) - ID of the instance to delete.
- `force` (boolean, default `false`) - Safety gate for deleting a running instance; false leaves a
  running instance untouched (reported as not deleted), true deletes it regardless of state. Deletion
  is permanent either way.

**Returns**

`deleted` (`true`/`false`) and, on a running-instance refusal, an actionable `detail`.

### stop_app_instance

Stop a running app instance without deleting it. Its record, configuration and results are kept, and
a stopped instance can later be relaunched via `relaunch_app_instance` when the app is resumable.

**Parameters**

- `instance` (string, required) - ID of the instance to stop.

**Returns**

`stopped`: `true` if this call stopped it, `false` if it was already not running.

## Resources

### klique://apps

The catalog of applications available to the calling company, one row per app version/customization
the tenant may see.

**Parameters**

- `app_usages` - Also return running/pending counts per app and an `apps_queue` rollup. Accepted
  spellings, case-insensitive: `true`/`1`/`yes` and `false`/`0`/`no`. (Requires API server
  version `>=2.33`.)
- `include` - Include filter: a URL-encoded JSON list of application-id regex strings; only matching
  apps are returned. (Requires API server version `>=2.25`.)
- `exclude` - Exclude filter: a URL-encoded JSON list of application-id regex strings; matching apps
  are dropped. (Requires API server version `>=2.25`.)
- `categories` - Also fold the live category vocabulary in under a `categories` key; accepted
  spellings, case-insensitive: `true`/`1`/`yes` and `false`/`0`/`no`.

**Returns**

The catalog rows, each with app metadata and, when requested, usage counts, plus, when requested, a
company-wide usage rollup and the live category-name list.

### klique://apps/instances/{instance_id}

Full information for one application instance, identified by its instance ID.

**Parameters**

- `instance_id` - The application instance ID, a task ID, whose info to fetch.

**Returns**

The instance's full info: identity, status/timing, embedded application config, project/queue
placement, per-instance state flags, and sharing groups.

### klique://apps/{app_id}/launch-template

The configuration-form template ("wizard") for an application, identified by the `{app_id}` path
segment.

**Parameters**

- `app_id` - The application or customization ID whose launch template to fetch; a customization ID
  renders its overlaid form.
- `instance` - An instance ID; pins the template to that instance's app version.
- `dependent_on` - A URL-encoded JSON object of selected values that restrict the template to
  dependent fields, also applied when computing choice queries.
- `source_ui_render` - Default for source rendering when `ui_render` is unset on the source. Accepted
  spellings, case-insensitive: `true`/`1`/`yes` and `false`/`0`/`no`. (Requires API server
  version `>=2.34`.)

**Returns**

The launch-wizard form object, plus, when applicable, whether the app needs an unavailable task
router and the selectable sharing groups.

## Prompts

### create_app

Walk through creating a new Klique application and getting a first instance running: choosing how the
code is delivered, writing the app configuration, installing it, and launching it.

**Parameters**

- `app_name` (string) - The name the new app should carry. If omitted, the agent is steered to ask you for
  one.
- `code_delivery_mode` (string) - How the code is delivered: `code` (inline files), `git` (a repository) or
  `docker` (an image with the code baked in). If omitted, the agent is steered to choose one with you.
- `serves_http` (boolean, default `false`) - Whether the app serves an HTTP port. One that does needs a
  port declared in its configuration.

**Returns**

A single instruction message walking the agent from an empty directory to a running instance. It
renders the same guidance as the `create-app` skill, so the two never diverge.

### monitor_app

Check on an application that is already running: find its instances, get the dashboard URL, work out
why one failed or is stuck waiting for a worker, and stop, edit, relaunch or delete it.

**Parameters**

- `app_id` (string) - The application whose instances to look at.
- `instance_id` (string) - A single instance to go straight to.

Leave both empty to be asked which app.

**Returns**

A single instruction message walking the agent through the check and the lifecycle actions. It
renders the same guidance as the `monitor-app` skill.
