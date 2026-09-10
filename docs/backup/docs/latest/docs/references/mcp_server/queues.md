# Queues

> The Klique MCP server's queues module, covering tools to manage execution queues and to enqueue, dequeue, and move tasks between them.

The Klique MCP server exposes functionalities to work with queues: managing execution queues and moving
tasks between them. A queue holds pending tasks, or an application, until a worker pulls and executes
(or starts) it.

## Tools

### list_queues

List the queues visible to you, with optional name filtering and pagination.

**Parameters**

- `id` (list[string]) - Queue IDs to look up directly, when you already know them.
- `name` (string) - Filter queues by name regex. Unanchored and case-sensitive, matching anywhere in the
  name: `train` behaves like `.*train.*`, while `*train*` is invalid. Anchor with `^`/`$` for a whole-name
  match, and write `\.` for a literal dot.
- `page` (integer) - Zero-based page index.
- `page_size` (integer) - Number of queues per page.

**Returns**

A list of queue objects, each with the queue's id, name and, when present, its display name,
ownership, tags and timestamp fields.

Each call returns a single page, not the full set. With no `page_size`, the server defaults to its
own maximum (500), and a full page only means *at least* that many queues exist, not exactly that
many. To read everything, pass `page_size` (useful up to 500; larger values are capped, not
rejected) and increase `page` until a page comes back shorter than requested.

### create_queue

Create a new queue with a unique name and optional tags, returning its ID.

**Parameters**

- `name` (string, required) - New queue's name, unique within the company.
- `display_name` (string) - Human-facing label the UI shows in place of the queue's name.
- `tags` (list[string]) - User tags to attach to the queue.
- `system_tags` (list[string]) - System tags to attach to the queue.

**Returns**

The ID of the newly created queue, as a string.

### update_queue

Update a queue's metadata (`name`, `display_name`, `tags`, `system_tags`) by ID. Only the supplied fields change.

**Parameters**

- `queue` (string, required) - ID of the queue to update.
- `name` (string) - New (unique) name for the queue.
- `display_name` (string) - New human-facing label the UI shows in place of the queue's name.
- `tags` (list[string]) - New user tags.
- `system_tags` (list[string]) - New system tags.

**Returns**

The update outcome: `updated` (queues changed, `1` or `0`) and `fields`, the changed values.

### delete_queue

Delete a single queue by ID; without `force` the call fails if it still has queued tasks.

**Parameters**

- `queue` (string, required) - ID of the queue to delete.
- `force` (boolean) - Delete the queue even if it still has queued tasks.

**Returns**

The deletion outcome: `deleted` (queues deleted, `1` or `0`).

### enqueue_task

Enqueue a task, identified by ID, into a target queue identified by ID.

**Parameters**

- `task` (string, required) - ID of the task to enqueue.
- `queue` (string, required) - ID of the target queue to enqueue the task into.

**Returns**

The enqueue outcome: `queued` (tasks queued, `1` or `0`) and `updated`, the number of tasks whose
state changed, when present.

### dequeue_task

Dequeue a task by ID, removing it from whichever queue it is currently in.

**Parameters**

- `task` (string, required) - ID of the task to dequeue.

**Returns**

The dequeue outcome: `dequeued` (tasks dequeued, `1` or `0`) and `updated`, the number of tasks
whose state changed, when present.

### move_task_to_queue

Move a task by ID into a target queue by ID, dequeuing it from its current queue and enqueuing it
into the target. To move a task between projects instead, use `update_task`'s `project` field.

**Parameters**

- `task` (string, required) - ID of the task to move.
- `queue` (string, required) - ID of the target queue to move the task into.

**Returns**

The resulting enqueue outcome: `queued` (tasks queued, `1` or `0`) and `updated`, the number of
tasks whose state changed, when present.
