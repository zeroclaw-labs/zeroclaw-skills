# sql-executor

Write and execute PostgreSQL or Supabase SQL from natural-language requests.

## Install

```bash
zeroclaw skills install sql-executor
```

## What it does

- translates user requests into SQL
- inspects schema before guessing table or column names
- executes unconfirmed read queries inside a database-enforced read-only transaction
- asks for confirmation before writes or schema changes
- works with PostgreSQL and Supabase Postgres connection strings
- avoids asking users to paste database credentials into chat

## Permissions

- `shell_exec` — required to run `psql` and optional `supabase` CLI commands

## Requirements

- `psql` available in your `PATH`
- optional: Supabase CLI if you want `supabase status` for local projects
- a database connection string stored outside the conversation in one of these env vars:
  - `DATABASE_URL`
  - `POSTGRES_URL`
  - `POSTGRES_URL_NON_POOLING`
  - `SUPABASE_DB_URL`
  - `SUPABASE_DATABASE_URL`

Do not paste database URLs or passwords into chat. Set the env var locally or use the host's local secret/config mechanism.

## Example prompts

- `List all public tables in my database`
- `Show the 20 newest users created this week`
- `Count orders by status for the last 30 days`
- `Add a nullable last_seen_at column to profiles`
- `Delete test users with email ending in example.com`

## Safety model

Read-only queries can be executed without confirmation only inside a database-enforced read-only transaction/session. If a query attempts a write or side effect and fails under that guard, it must be rerouted through the explicit mutation-confirmation flow.

For mutating queries like `insert`, `update`, `delete`, `alter`, `drop`, and `truncate`, the skill should:

1. draft the SQL
2. explain the impact
3. ask for confirmation
4. execute only after approval
5. verify the result with a follow-up query when useful

For execution, the skill passes generated SQL to `psql` through stdin/heredoc instead of embedding the SQL inside shell-quoted inline command arguments. Unconfirmed reads are wrapped in `BEGIN READ ONLY; ... COMMIT;`.

## Notes for Supabase

Supabase uses PostgreSQL, so the skill executes SQL through `psql` using the project's Postgres connection string after the user stores it outside the conversation.
For local Supabase projects, run `supabase status` in your own terminal and set one of the supported env vars from that output.
