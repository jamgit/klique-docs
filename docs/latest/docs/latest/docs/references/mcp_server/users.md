# Users

> The Klique MCP server's users module, with tools for user accounts, roles, service accounts, invitations, and per-user preferences in your company.

The Klique MCP server exposes functionalities to work with users: managing your company's user accounts,
their roles, non-human service accounts, invited and pending members, and per-user preferences.

## Tools

### list_users

Search and list your company's user accounts, with filtering, projection, ordering and pagination
(requires an admin role for `include_auth`).

**Parameters**

- `name` (string) - regex filter on display name. Unanchored and case-sensitive, matching anywhere in the
  name: `train` behaves like `.*train.*`, while `*train*` is invalid. Anchor with `^`/`$` for a whole-name
  match, and write `\.` for a literal dot.
- `id` (list[string]) - User IDs to look up directly, when you already know them.
- `active_in_projects` (list[string]) - Restrict to users active in these project ids; an empty
  list matches users active in any project (different from omitting it), and intersects with `id`;
  an unknown project ID isn't an error, it just narrows the search to an empty result. Also
  changes which fields the result carries.
- `only_fields` (list[string]) - Projection: dotted field paths to return; omitted fields are absent
  because they weren't requested.
- `order_by` (list[string]) - Sort fields; `-` prefix sorts descending.
- `page` (integer) - Zero-based page index, minimum 0; without `page_size` it pages against the
  server's own maximum page size.
- `page_size` (integer) - Users per page, from 1 upward; values above the server's configured
  maximum (500 default) are capped rather than refused.
- `include_auth` (boolean) - Also return the authentication fields `email`, `role`, `providers` and
  `sec_groups`.
- `include_providers_info` (boolean) - Return full per-provider details in `providers` instead of
  just an update timestamp; only takes effect with `include_auth`.
- `include_activity` (boolean) - Also return each user's `activity` block; only meaningful together
  with `include_auth`. Supplying it at all, even `false`, changes which fields the result carries.

**Returns**

A list of user entries. Several fields are administrator-only (need `include_auth`) or `null`
unless `active_in_projects`/`include_activity` is set.

Each call returns a single page, not the full set. With no `page_size`, the server defaults to its
own maximum (500), and a full page only means *at least* that many users exist, not exactly that
many. To read everything, pass `page_size` (useful up to 500; larger values are capped, not
rejected) and increase `page` until a page comes back shorter than requested.

### list_service_users

List your company's service account users and their service-user limits (admins only, requires the
`service_users` company feature).

**Parameters**

None.

**Returns**

The company's service users with per-account details, plus its service-user limits. Some limits are
omitted depending on the API server version.

### allow_user_emails

Allow email addresses to sign in to your company.

**Parameters**

- `emails` (string or list[string], required) - Addresses to allow in; a single address may be passed on
  its own.
- `role` (string) - Role to give each account created from these addresses. If omitted, they become
  ordinary users.

**Returns**

The addresses for which an entry was actually added.

This does not create accounts. Each account is created by the API server the first time that person
signs in through SSO or the LDAP fixed-user provider. The `role` applies only to accounts created
from these entries: it does not change anyone who already has an account, which is `set_user_role`'s
job.

### withdraw_user_emails

Withdraw allowed email addresses whose owners have not signed in yet, cancelling an expected arrival.

**Parameters**

- `emails` (string or list[string], required) - Allowed addresses to withdraw; a single address may be
  passed on its own.

**Returns**

The addresses whose entry was actually removed.

Only an address whose owner has not signed in yet can be withdrawn. To remove somebody who already
has an account, use `remove_company_user`.

### list_pending_invitations

List the addresses invited into your company that nobody has signed in with yet.

**Parameters**

None.

**Returns**

The pending invitations, each carrying the address and when it was invited.

### remove_company_user

Remove a user from your company.

**Parameters**

- `user` (string, required) - ID of the user to remove, as `list_users` reports it.

**Returns**

An empty payload; the API server reports no detail on success.

This takes an ID, not a name. To resolve a person's name to an ID, use `list_users` first. An ID
that matches nobody is not reported as an error, so check the ID before relying on the call having
done anything.

### update_user

Update a user's profile names (display, given, family) and report what the update matched (admin
role required to edit anyone but yourself).

**Parameters**

- `user` (string, required) - User to update; read your own ID from `klique://user/self`, or
  resolve another's with `list_users`. An unknown ID isn't an error; it answers `updated: 0` with
  `fields` non-empty.
- `name` (string) - New display (full) name; also propagates to the user's authentication record.
- `given_name` (string) - New given (first) name; an empty string stores an empty name rather than
  clearing the field.
- `family_name` (string) - New family (last) name; an empty string stores an empty name rather than
  clearing the field.

**Returns**

`updated` (matched, not necessarily changed) and `fields` (what was applied); `fields` non-empty
with `updated: 0` means no such user, empty means no field was passed.

### list_assignable_roles

List the roles assignable through this server, each paired with its display label, for use with
`set_user_role` (enterprise API server only).

**Parameters**

None.

**Returns**

`role` (the identifier to pass elsewhere) and, when reported, `display_name` (a human label); a
missing `display_name` doesn't mean the role can't be assigned.

### set_user_role

Set a user's role within your own company, from the values `list_assignable_roles` returns (requires
an admin role; enterprise API server only).

**Parameters**

- `user` (string, required) - ID of the user whose role to set, resolved via `list_users` or
  `klique://users/{user_id}`; never your own, and an unknown ID is refused as an error, not
  `updated: 0`.
- `role` (string, required) - Role to assign: `admin`, `user` or `consumer`; any other value is
  rejected before any API server call is made.

**Returns**

`updated`: number of documents actually modified (not merely matched); `0` means the role already
matched or the target holds a `system`/`root` role.

### update_service_user

Update a service (account) user's name and/or impersonation grant, and report what the write matched
(requires an admin role).

**Parameters**

- `user` (string, required) - ID of the service user to update (from `list_service_users`); the
  API server never verifies the target is actually a service account, so a wrong ID silently updates a
  human user instead.
- `name` (string) - New name for the service account; an empty string changes nothing, since the
  API server skips a falsy name.
- `allow_running_as_owner` (boolean) - Impersonation grant: `true` lets the account run a task as
  another user, as that task's owner rather than as itself; `false` clears its per-account maximum
  role; omit to leave the grant unchanged.

**Returns**

`updated`: records matched (not necessarily changed); `0` means either no such ID or no updatable
field was passed. The two aren't distinguishable here.

### set_user_preferences

Merge changes into your own stored preferences (a Klique web UI's per-user settings). There is no
way to write another user's preferences here.

**Parameters**

- `preferences` (object, required) - The changes to merge, keyed by path: a plain key replaces the
  whole value; a dotted key merges into that path. No key may start with `$`, at any depth, and a
  `.` is only a top-level path separator; a `null` value is kept, stored as `null`.
- `return_updated` (boolean, API server default returns them) - Whether a write also returns the whole
  merged preferences object; `false`, or a no-op write, leaves `fields.preferences` absent.

**Returns**

`updated` (documents matched; a no-op merge answers `0`, since nothing is written) and
`fields.preferences`, the whole merged object when returned.

## Resources

### klique://user/self

Your own record (ID, display name, role, and the companies you belong to), resolved from the
presented token, so it takes no ID. Treat any `enterprise_packages[].url` as a secret and never echo
it back.

**Parameters**

- `return_inactive_companies` - Also list companies you joined that have since been deleted, marked
  only by `active: false`; accepted spellings, case-insensitive: `true`/`1`/`yes` and `false`/`0`/`no`.
- `get_supported_features` - Also return `supported_features`, the company features enabled for you,
  and `trial_features`, each with a `name` and an `ongoing`/`ended` status; same accepted spellings as
  `return_inactive_companies`.

**Returns**

Your user record plus session/company extras (terms-of-use status, settings, and, if requested,
supported/trial features); anything not requested or unsupported comes back `null` rather than
missing.

### klique://user/self/preferences

Your own preferences (the free-form settings object a Klique web UI keeps for you); resolved from
the token, so it takes no parameters, and there is no way to read another user's here.

**Parameters**

None.

**Returns**

The preferences object itself, with no envelope; free-form and arbitrarily nested, so no key is
guaranteed. An empty object means none are set.

### klique://users/{user_id}

The record of one user account, looked up by the `user_id` path segment.

**Parameters**

- `user_id` - The ID of the user account to read.

**Returns**

The user's profile fields; the authentication fields are never included here, always `null`.

### klique://users

The roster of your company's user accounts, read with no arguments: the quick way to enumerate who
exists.

**Parameters**

None.

**Returns**

A list of user profiles, no authentication fields.
