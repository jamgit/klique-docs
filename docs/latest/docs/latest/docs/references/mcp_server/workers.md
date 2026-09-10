# Workers

> The Klique MCP server's workers module, covering the tool for listing worker machines registered to pull and execute queued tasks.

The Klique MCP server exposes functionalities to work with workers: listing the worker machines
registered to pull and execute queued tasks.

## Tools

### list_workers

List the registered workers visible to you, each with its current task, queue and activity details.

**Parameters**

- `last_seen` (integer, default `3600`) - Filter out workers inactive for longer than this
  many seconds, minimum 0; `0` disables the filter.
- `tags` (list[string]) - User tags to filter workers by (`-` prefix excludes a tag).
- `system_tags` (list[string]) - System tags to filter workers by.
- `worker_pattern` (string) - Worker name pattern to filter workers by. Unanchored and case-sensitive,
  matching anywhere in the name: `train` behaves like `.*train.*`, while `*train*` is invalid. Anchor with
  `^`/`$` for a whole-name match, and write `\.` for a literal dot.

**Returns**

A list of worker objects describing each worker's identity, current task and queue, listened
queues, activity timestamps and tags.
