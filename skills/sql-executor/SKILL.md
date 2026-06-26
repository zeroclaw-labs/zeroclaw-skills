---
name: sql-executor
description: >-
  Write and execute PostgreSQL or Supabase SQL queries from natural-language
  requests. Use when the user wants to inspect schema, run read queries,
  validate data, or safely perform confirmed database changes.
version: "0.1.0"
author: 1mpossible-code
license: MIT
category: data
tags:
  - Community
permissions:
  - shell_exec
---

# SQL Executor

Write and run SQL for PostgreSQL databases, including Supabase projects that expose a Postgres connection string.

Before execution, read [the command reference](references/commands.md).

## Goals

- turn plain-English requests into correct SQL
- inspect schema before guessing table or column names
- execute read-only queries with clear result summaries
- require confirmation before any mutation or schema change
- keep database credentials out of the conversation transcript
- avoid fragile shell quoting when sending generated SQL to `psql`

## Setup

1. Find a connection target from one of these environment variables:
   - `DATABASE_URL`
   - `POSTGRES_URL`
   - `POSTGRES_URL_NON_POOLING`
   - `SUPABASE_DB_URL`
   - `SUPABASE_DATABASE_URL`
2. If multiple supported variables exist, ask which variable name to use. Do not print their values.
3. If no supported variable exists, stop and ask the user to set one in their local environment or configure the connection through the host's local secret/config mechanism. Do not ask the user to paste a database URL or password into chat.
4. Prefer `psql` for execution.
5. If the Supabase CLI is installed and the user is working locally, `supabase status` may be used to confirm the local stack.

## Workflow

1. **Understand the request.** Decide whether the user wants schema discovery, a read query, a data fix, or a schema change.
2. **Inspect first when needed.** If table or column names are not certain, query `information_schema` before writing the final SQL.
3. **Draft the SQL.** Keep it minimal and explicit. Avoid `select *` unless the user asks for it.
4. **Classify risk.**
   - Read path: `select`, metadata queries, and non-ANALYZE `explain` statements that can run under a database-enforced read-only guard
   - Mutation path: `insert`, `update`, `delete`, `alter`, `drop`, `truncate`, `create`, `grant`, `revoke`, side-effecting functions, data-modifying CTEs, and `explain analyze`
5. **Execute safely.**
   - Unconfirmed reads may run only inside a database-enforced read-only transaction/session.
   - Any query that fails under the read-only guard because it attempted a write or side effect must be rerouted through the mutation-confirmation flow. Do not retry it outside the guard without confirmation.
   - Mutating queries must be shown to the user first with a short impact summary, then executed only after confirmation.
6. **Verify the outcome.** After a mutation, run a small follow-up query when practical.
7. **Report clearly.** Share the SQL and a concise summary of the result. Do not reveal credential values.

## Execution Rules

### 1) Connection handling
- Use only a database URL that is already available through an environment variable or host-provided local secret/config mechanism.
- Never ask the user to paste a full database URL, database password, or other credential into the conversation.
- Never print full secrets back to the user.
- If multiple database URL variables are present, ask the user to choose by variable name only.
- If the user accidentally shares a credential in chat, do not use it. Tell them to rotate it and set the replacement outside the conversation.

### 2) Command style
Use strict `psql` execution with error stopping and paging disabled. Pass generated SQL through stdin with a single-quoted heredoc delimiter. For unconfirmed reads, always combine this with the read-only transaction pattern below.

Do not interpolate generated SQL into an inline shell command. SQL often contains quotes, semicolons, dollar-quoted strings, or user-provided literal values, and shell quoting mistakes can change what reaches Postgres.

When using a heredoc:
- choose a delimiter that does not appear in the SQL body
- keep the delimiter single-quoted so the shell does not expand SQL text
- keep credentials in environment variables, not in the command text

### 3) Unconfirmed read execution
Unconfirmed reads must fail closed at the database boundary. Run every unconfirmed read, metadata inspection, and schema discovery query inside a read-only transaction/session:

```bash
psql "$DATABASE_URL" -X -v ON_ERROR_STOP=1 -P pager=off <<'__ZEROCLAW_SQL__'
BEGIN READ ONLY;
SET LOCAL statement_timeout = '30s';

select now();

COMMIT;
__ZEROCLAW_SQL__
```

Rules for unconfirmed reads:
- Put the generated read SQL between `SET LOCAL` and `COMMIT`.
- Do not include generated transaction-control statements such as `begin`, `commit`, `rollback`, `savepoint`, or `set transaction` in the user SQL body.
- If the read-only guard rejects the statement, stop and route the request through mutation confirmation.
- Treat data-modifying CTEs and side-effecting functions as mutation-path requests, even if the SQL starts with `select`.
- `EXPLAIN ANALYZE` executes the target statement and is not automatically read-only. Run it only under the read-only guard when appropriate, or require explicit mutation confirmation.

### 4) Read query defaults
- Add `limit 100` for exploratory result sets unless the user asks otherwise.
- Prefer explicit column lists.
- Add `order by` when recency or ranking matters.
- Use aggregates for counts and summaries instead of dumping huge tables.

### 5) Mutation safety
Before any mutating SQL:
- show the exact SQL
- explain which rows or objects are affected
- mention whether the action is reversible
- ask for confirmation

After confirmation:
- execute the SQL through stdin/heredoc, not inline shell quoting
- use an explicit transaction for multi-step changes when practical
- capture the affected row count or command result
- run a verification query when useful, using the read-only guard for verification reads

### 6) Supabase specifics
- Treat Supabase as PostgreSQL for SQL execution.
- If the user says “Supabase” but no connection variable is configured, ask them to set one of the supported environment variables from their Supabase project's Postgres connection settings. Do not ask them to paste the connection string in chat.
- For local Supabase, the common default target can be placed in an environment variable such as `SUPABASE_DB_URL`.

## Output format

Use this structure:

```text
Target: [environment variable name or local target label]
Mode: [read-only or mutating]
SQL:
[query]

Result:
[short summary]
```

For mutating queries awaiting approval, use:

```text
Target: [environment variable name or local target label]
Mode: mutating
Impact: [what will change]
SQL:
[query]

Awaiting confirmation.
```

## Rules

- Never invent table or column names when schema inspection is possible.
- Never run mutating SQL without confirmation.
- Never ask users to paste credentials into chat.
- Never expose full credentials in the response.
- Never embed generated SQL inside a shell-quoted inline `psql` command.
- Never run an unconfirmed read outside a database-enforced read-only transaction/session.
- If a query fails, show the relevant error and propose a corrected query.
- Prefer transactional thinking for risky multi-step changes.
- When the request is ambiguous, ask one focused question instead of guessing.
