# Projects

> Klique MCP server's projects module, with tools to list, create, update, and delete projects, plus inspect tags, users, metrics, and hyper-parameters.

The Klique MCP server exposes functionalities to work with projects: listing, creating, updating, and
deleting the projects that organize tasks, models, and datasets, and inspecting the tags, contributors,
metrics, and hyperparameters in use across one.

A project organizes the tasks, models, and datasets that belong to it.

## Tools

### list_projects

List the projects visible to you, filterable by name and pageable.

**Parameters**

- `id` (list[string]) - Project IDs to look up directly, when you already know them.
- `name` (string) - Filter projects by name regex. Unanchored and case-sensitive, matching anywhere in the
  name: `train` behaves like `.*train.*`, while `*train*` is invalid. Anchor with `^`/`$` for a whole-name
  match, and write `\.` for a literal dot.
- `basename` (string) - Regex matched against a project's own name only, ignoring the `/`-nested path above
  it. Same matching rules as `name`.
- `page` (integer) - Zero-based page index.
- `page_size` (integer) - Number of projects per page.

**Returns**

A list of project objects, each with its identifying and descriptive fields available to you.

Each call returns a single page, not the full set. With no `page_size`, the server defaults to its
own maximum (500), and a full page only means *at least* that many projects exist, not exactly that
many. To read everything, pass `page_size` (useful up to 500; larger values are capped, not
rejected) and increase `page` until a page comes back shorter than requested.

### list_project_filter_values

List the tag and contributor values that actually occur under the given projects, so you can build a
filter that matches something rather than guessing at tag names.

**Parameters**

- `project` (list[string]) - Project IDs to scope the scan to. If omitted, scans everything visible to you.
- `include_system_tags` (boolean) - Also return the system tags in use, such as `archived`, for building
  filters that include or exclude archived entities.
- `include_subprojects` (boolean) - Also scan nested subprojects when collecting contributors.

**Returns**

The tags in use on tasks and on models, and the users who contributed, each with the id to filter by.

### list_project_metrics

List which metric/variant pairs a project's tasks or models have reported, without reading any values.

**Parameters**

- `project` (string) - ID of the project to scan. Omit it and pass `ids` to scan specific tasks or models
  instead.
- `include_subprojects` (boolean) - Also scan nested subprojects.
- `model_metrics` (boolean) - Scan the project's models instead of its tasks.
- `ids` (list[string]) - Specific task or model IDs to scan instead of a whole project. For metrics from a
  known set of tasks, `list_task_metrics` is more direct.

**Returns**

The distinct metric/variant pairs reported in scope.

### list_project_hyper_parameters

List the hyper-parameter sections and names used by a project's tasks, the set of axes the project's
runs actually vary along.

**Parameters**

- `project` (string, required) - ID of the project whose hyper-parameters to list.
- `include_subprojects` (boolean) - Also include tasks from nested subprojects.
- `page` (integer) - Zero-based page index.
- `page_size` (integer) - Number of parameters per page.

**Returns**

The section/name pairs, together with the paging counts the API server reports. Unlike the other
paged listings, this one reports `remaining`, so you can tell from the response whether more are
left rather than having to infer it.

### create_project

Create a new project and return its ID.

**Parameters**

- `name` (string, required) - The new project's name; unique, `/`-nested for hierarchy.
- `description` (string) - Description for the project.
- `tags` (list[string]) - User tags to attach.
- `system_tags` (list[string]) - System tags to attach.
- `default_output_destination` (string) - Default output URL for the project's new tasks.

**Returns**

The ID of the newly created project, as a string.

### update_project

Update a project's metadata by ID. Only the supplied fields are changed.

**Parameters**

- `project` (string, required) - ID of the project to update.
- `name` (string) - New name; unique, `/` re-nests the project.
- `description` (string) - New description.
- `tags` (list[string]) - New user tags.
- `system_tags` (list[string]) - New system tags.
- `default_output_destination` (string) - New default output URL for new tasks.

**Returns**

The update outcome: `updated` (projects changed, `1` or `0`) and `fields`, the changed values.

### delete_project

Delete a project by ID. Without `force`, the call fails if it still has tasks.

**Parameters**

- `project` (string, required) - ID of the project to delete.
- `force` (boolean) - Delete despite contained tasks (unassigns them).
- `delete_contents` (boolean) - Delete contained entities instead of unassigning them.
- `delete_external_artifacts` (boolean) - Also delete external artifacts from the fileserver.

**Returns**

The deletion outcome: how many projects were deleted, plus per-type content counts and storage URLs
when contents were also deleted.
