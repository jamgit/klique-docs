# Tasks

> The Klique MCP server's tasks module, with tools to list, create, clone, update, stop, archive, and delete tasks, plus a prompt to compare runs.

The Klique MCP server exposes functionalities to list, create, clone, update, stop, archive,
unarchive, and delete tasks.

A task is the unit of work that runs your code, living inside a project and carrying its execution
sources (a git/code script and/or a container image).

## Tools

### list_tasks

List the tasks visible to you, filtered on any combination of the fields below and paged. With no
arguments it returns the first page of the tasks your permissions allow, not everything, and not
the tasks tagged `hidden`, which are left out unless you ask for them.

**Parameters**

- `id` (list[string]) - Task IDs to look up directly, when you already know them.
- `name` (string) - Regex matched against the task's name. Unanchored and case-sensitive, matching anywhere
  in the name: `train` behaves like `.*train.*`, while `*train*` is invalid. Anchor with `^`/`$` for a
  whole-name match, and write `\.` for a literal dot.
- `user` (list[string]) - IDs of users who created the tasks, not names. To resolve a person's name to an ID,
  use `list_users` first.
- `project` (list[string]) - Project IDs to restrict tasks to. Naming several searches all of them;
  subproject tasks are excluded unless `include_subprojects` is set.
- `parent` (string) - ID of the one parent task whose children to return.
- `type` (list[string]) - Task types to restrict the search to, e.g. `training`, `testing`.
- `status` (list[string]) - Statuses to restrict the search to.
- `status_changed` (list[string]) - Range constraints on when the status last changed.
- `tags` (list[string]) - User tag filters; prefix a tag with `-` to exclude it.
- `system_tags` (list[string]) - System tag filters; `-archived` excludes archived tasks.
- `search_text` (string) - Free-text search across the tasks' fields.
- `only_fields` (list[string]) - Projection of dotted field paths, to return less per task.
- `order_by` (list[string]) - Sort fields; prefix with `-` for descending.
- `include_subprojects` (boolean) - Also return tasks from nested subprojects.
- `search_hidden` (boolean) - Also return tasks tagged `hidden`, left out by default. This mirrors the web
  UI's show-hidden toggle: without it, results match what the UI shows; with it, hidden tasks are
  included too.
- `page` (integer) - Zero-based page index.
- `page_size` (integer) - Number of tasks per page.

**Returns**

A list of task objects, each with the task's identifying and descriptive fields.

Each call returns a single page, not the full set. With no `page_size`, the server defaults to its
own maximum (500), and a full page only means *at least* that many tasks exist, not exactly that
many. To read everything, pass `page_size` (useful up to 500; larger values are capped, not
rejected) and increase `page` until a page comes back shorter than requested.

### create_task

Create a new task and return its ID, running code from a git/code script and/or a container image.

**Parameters**

- `name` (string, required) - New task's name.
- `project` (string, required) - ID of the project the task belongs to.
- `type` (string, required) - Task type, e.g. `training`, `testing`, `inference`.
- `repository` (string) - git repository URL hosting the code.
- `branch` (string) - git branch to check out.
- `commit` (string) - git commit hash to check out.
- `entry_point` (string) - Script executed as the task's entry point.
- `requirements` (string) - pip requirements needed to run the code.
- `diff` (string) - Uncommitted git diff applied on top of `commit`.
- `docker_image` (string) - Container image the task runs in.

**Returns**

The ID of the newly created task, as a string.

### clone_task

Clone a task (by ID) into a new draft, copying its execution sources and configuration; the optional
`new_task_*` fields override the clone's metadata, and anything left unset is inherited from the source.

**Parameters**

- `task` (string, required) - ID of the source task to clone.
- `new_task_name` (string) - Name for the cloned task.
- `new_task_comment` (string) - Free-text comment for the cloned task.
- `new_task_tags` (list[string]) - User tags for the cloned task.
- `new_task_project` (string) - ID of the project to clone the task into.
- `new_task_parent` (string) - ID of the parent task of the clone.

**Returns**

The ID of the newly cloned task, as a string.

### update_task

Update a task's metadata (`name`, `comment`, `tags`, `system_tags`, and `project`) by ID, changing
only the supplied fields; reassigning `project` moves the task into a different project.

**Parameters**

- `task` (string, required) - ID of the task to update.
- `name` (string) - New name for the task.
- `comment` (string) - New free-text comment describing the task.
- `tags` (list[string]) - New user tags.
- `system_tags` (list[string]) - New system tags.
- `project` (string) - ID of the project to move the task into.

**Returns**

The update outcome: `updated` (tasks changed, `1` or `0`) and `fields`, the changed values.

### stop_task

Request a cooperative stop of one or more running tasks.

**Parameters**

- `task` (string or list[string], required) - ID of the task to stop, or several IDs to stop together.
- `force` (boolean) - Stop a task that is not `in_progress`. Without it, such a task is refused, guarding
  against stopping the wrong one. A queued task is not `in_progress`; use `dequeue_task` instead to remove
  it from the queue.
- `status_reason` (string) - Short reason for the status change, recorded on the task.
- `status_message` (string) - Free-text message describing the status change, recorded on the task.
- `include_pipeline_steps` (boolean) - Also stop the step tasks of a pipeline controller. Without it, the
  controller stops while its already-running steps keep going.

**Returns**

The per-task outcome: a `succeeded` entry for each task that was stopped, and a `failed` entry
carrying the API server's reason for each task that was not. A single id returns the same shape as
several.

### archive_tasks

Archive tasks, taking them out of the default views without destroying them.

**Parameters**

- `tasks` (string or list[string], required) - Task IDs to archive; a single ID may be passed on its own.
- `status_reason` (string) - Short reason recorded against the change.
- `status_message` (string) - Free-text message describing the change.
- `include_pipeline_steps` (boolean) - Also archive the step tasks of any pipeline controller named. Without
  it, a controller is archived while its steps stay visible. Requires API server version `>=2.30`.

**Returns**

The per-task outcomes, `succeeded` and `failed`.

Archiving is reversible: `unarchive_tasks` undoes it, which makes it the operation to reach for
when you want a task out of the way rather than gone. `delete_task` cannot be undone.

### unarchive_tasks

Restore archived tasks to the default views by removing their `archived` system tag.

**Parameters**

- `tasks` (string or list[string], required) - Task IDs to unarchive; a single ID may be passed on its own.
- `status_reason` (string) - Short reason recorded against the change.
- `status_message` (string) - Free-text message describing the change.
- `include_pipeline_steps` (boolean) - Also unarchive the step tasks of any pipeline controller named.
  Requires API server version `>=2.30`.

**Returns**

The per-task outcomes, `succeeded` and `failed`. A task that was not archived in the first place is
reported under `succeeded` with `unarchived: false`, an outcome rather than a failure.

### delete_task

Delete a task by ID. Without `force`, the call fails unless the task is in an end state.

**Parameters**

- `task` (string, required) - ID of the task to delete.
- `force` (boolean) - Delete even if the task is not in an end state.
- `move_to_trash` (boolean) - Move the task to the trash instead of deleting it outright.
- `delete_external_artifacts` (boolean) - Also delete external artifacts from the fileserver.

**Returns**

The deletion outcome: whether the task was deleted, plus counts of affected child tasks and models and
the storage URLs of removed entities, when present.

## Prompts

### compare_runs

Compare several training runs and say which performed best, judged on the metrics they actually
reported rather than on their logs.

**Parameters**

- `tasks` (string) - The runs to compare, as you name them. If omitted, the agent is steered to find
  candidates first.
- `project` (string) - A project to draw candidate runs from. If omitted, and no `tasks` given, the agent
  is steered to ask which.
- `metric` (string) - A single metric to judge on. If omitted, every metric the runs share is compared.

**Returns**

A single instruction message walking the agent through the comparison. It reports which figure it
ranked on and which runs reported nothing, so the result is one you can check rather than a bare
leaderboard.
