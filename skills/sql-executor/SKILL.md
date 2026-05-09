---
name: sql-executor
description: >-
  Write and execute PostgreSQL or Supabase SQL queries from natural-language
  requests. Use when the user wants to inspect schema, run read queries,
  validate data, or safely perform confirmed database changes.
license: MIT
metadata:
  author: 1mpossible-code
  version: "0.1.0"
  category: data
---

# SQL Executor

Write and run SQL for PostgreSQL databases, including Supabase projects that expose a Postgres connection string.

Before execution, read [the command reference](references/commands.md).

## Goals

- turn plain-English requests into correct SQL
- inspect schema before guessing table or column names
- execute read-only queries with clear result summaries
- require confirmation before any mutation or schema change
- prefer simple, reversible, well-explained SQL

## Setup

1. Find a connection string from one of these environment variables:
   - `DATABASE_URL`
   - `POSTGRES_URL`
   - `POSTGRES_URL_NON_POOLING`
   - `SUPABASE_DB_URL`
   - `SUPABASE_DATABASE_URL`
2. If none exist, ask the user for the database URL or for the exact env var name to use.
3. Prefer `psql` for execution.
4. If the Supabase CLI is installed and the user is working locally, `supabase status` may be used to confirm the local stack.

## Workflow

1. **Understand the request.** Decide whether the user wants schema discovery, a read query, a data fix, or a schema change.
2. **Inspect first when needed.** If table or column names are not certain, query `information_schema` before writing the final SQL.
3. **Draft the SQL.** Keep it minimal and explicit. Avoid `select *` unless the user asks for it.
4. **Classify risk.**
   - Read-only: `select`, `explain`, metadata queries
   - Mutating: `insert`, `update`, `delete`, `alter`, `drop`, `truncate`, `create`, `grant`, `revoke`
5. **Execute safely.**
   - Read-only queries may be executed immediately.
   - Mutating queries must be shown to the user first with a short impact summary, then executed only after confirmation.
6. **Verify the outcome.** After a mutation, run a small follow-up query when practical.
7. **Report clearly.** Share the SQL, the command used, and a concise summary of the result.

## Execution Rules

### 1) Connection handling
- Use the first available supported environment variable.
- Never print full secrets back to the user.
- If multiple database URLs are present, ask which one to use.

### 2) Command style
Use strict `psql` execution with error stopping and paging disabled:

```bash
psql "$DATABASE_URL" -X -v ON_ERROR_STOP=1 -P pager=off -c "SELECT now();"
```

For multi-line queries, use a quoted SQL string that preserves formatting.

### 3) Read query defaults
- Add `limit 100` for exploratory result sets unless the user asks otherwise.
- Prefer explicit column lists.
- Add `order by` when recency or ranking matters.
- Use aggregates for counts and summaries instead of dumping huge tables.

### 4) Mutation safety
Before any mutating SQL:
- show the exact SQL
- explain which rows or objects are affected
- mention whether the action is reversible
- ask for confirmation

After confirmation:
- execute the SQL
- capture the affected row count or command result
- run a verification query when useful

### 5) Supabase specifics
- Treat Supabase as PostgreSQL for SQL execution.
- If the user says “Supabase” but gives no connection info, ask for the Postgres connection string from the Supabase dashboard.
- For local Supabase, the common default URL is `postgresql://postgres:postgres@127.0.0.1:54322/postgres`.

## Output format

Use this structure:

```text
Target: [database target]
Mode: [read-only or mutating]
SQL:
[query]

Result:
[short summary]
```

For mutating queries awaiting approval, use:

```text
Target: [database target]
Mode: mutating
Impact: [what will change]
SQL:
[query]

Awaiting confirmation.
```

## Rules

- Never invent table or column names when schema inspection is possible.
- Never run mutating SQL without confirmation.
- Never expose full credentials in the response.
- If a query fails, show the relevant error and propose a corrected query.
- Prefer transactional thinking for risky multi-step changes.
- When the request is ambiguous, ask one focused question instead of guessing.
