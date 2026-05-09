# sql-executor

Write and execute PostgreSQL or Supabase SQL from natural-language requests.

## Install

```bash
zeroclaw skills install sql-executor
```

## What it does

- translates user requests into SQL
- inspects schema before guessing table or column names
- executes read-only queries directly
- asks for confirmation before writes or schema changes
- works with PostgreSQL and Supabase Postgres connection strings

## Permissions

- `shell_exec` — required to run `psql` and optional `supabase` CLI commands

## Requirements

- `psql` available in your `PATH`
- optional: Supabase CLI if you want `supabase status` for local projects
- a database connection string in one of these env vars:
  - `DATABASE_URL`
  - `POSTGRES_URL`
  - `POSTGRES_URL_NON_POOLING`
  - `SUPABASE_DB_URL`
  - `SUPABASE_DATABASE_URL`

## Example prompts

- `List all public tables in my database`
- `Show the 20 newest users created this week`
- `Count orders by status for the last 30 days`
- `Add a nullable last_seen_at column to profiles`
- `Delete test users with email ending in example.com`

## Safety model

Read-only queries can be executed immediately.

For mutating queries like `insert`, `update`, `delete`, `alter`, `drop`, and `truncate`, the skill should:

1. draft the SQL
2. explain the impact
3. ask for confirmation
4. execute only after approval
5. verify the result with a follow-up query when useful

## Notes for Supabase

Supabase uses PostgreSQL, so the skill executes SQL through `psql` using the project's Postgres connection string.
For local Supabase projects, a common default URL is:

```bash
postgresql://postgres:postgres@127.0.0.1:54322/postgres
```
