# sql-executor command reference

## Preferred execution model

Use `psql` with strict error handling and no pager. Pass SQL through stdin with a single-quoted heredoc delimiter. Do not put generated SQL inside inline shell command arguments. Generated SQL can contain quotes, semicolons, dollar-quoted strings, and user-provided literal values.

Keep SQL text in stdin and keep credentials in environment variables.

## Unconfirmed read execution

Unconfirmed reads must run under a database-enforced read-only guard. Use this pattern for reads, schema discovery, metadata inspection, and other unconfirmed SQL:

```bash
psql "$DATABASE_URL" -X -v ON_ERROR_STOP=1 -P pager=off <<'__ZEROCLAW_SQL__'
BEGIN READ ONLY;
SET LOCAL statement_timeout = '30s';

select now();

COMMIT;
__ZEROCLAW_SQL__
```

Rules:

1. Put generated read SQL between `SET LOCAL` and `COMMIT`.
2. Do not include generated transaction-control statements such as `begin`, `commit`, `rollback`, `savepoint`, or `set transaction` in the SQL body.
3. If the read-only transaction rejects the statement, stop. Do not retry outside the guard without explicit mutation confirmation.
4. Treat data-modifying CTEs and side-effecting functions as mutation-path requests even if the statement starts with `select`.
5. `EXPLAIN ANALYZE` executes the target statement and is not automatically read-only. Run it only under the read-only guard when appropriate, or require explicit mutation confirmation.

## Confirmed mutation execution

After explicit user confirmation, execute mutations through stdin/heredoc as well. Use an explicit transaction for multi-step changes when practical:

```bash
psql "$DATABASE_URL" -X -v ON_ERROR_STOP=1 -P pager=off <<'__ZEROCLAW_SQL__'
BEGIN;

-- confirmed mutating SQL here

COMMIT;
__ZEROCLAW_SQL__
```

After the mutation, run verification reads under the unconfirmed read execution pattern.

## Heredoc safety

When using a heredoc:

1. Use a delimiter that does not appear in the SQL body.
2. Single-quote the delimiter so the shell does not expand SQL text.
3. Do not echo or print the database URL.

## Common environment variables

Check these supported variables without printing their values:

1. `DATABASE_URL`
2. `POSTGRES_URL`
3. `POSTGRES_URL_NON_POOLING`
4. `SUPABASE_DB_URL`
5. `SUPABASE_DATABASE_URL`

Target-selection rule:

- If exactly one supported variable exists, use that variable name.
- If more than one supported variable exists, ask the user to choose by variable name only. Do not print or expose any values.
- If none exist, ask the user to set one locally or configure a connection through the host's local secret/config mechanism. Do not ask the user to paste a database URL or password into chat.

## Schema discovery

Run schema discovery under the unconfirmed read execution pattern.

```sql
select table_schema, table_name
from information_schema.tables
where table_schema not in ('pg_catalog', 'information_schema')
order by table_schema, table_name;
```

```sql
select table_schema, table_name, column_name, data_type, is_nullable
from information_schema.columns
where table_schema not in ('pg_catalog', 'information_schema')
order by table_schema, table_name, ordinal_position;
```

## Size limits

For exploration queries, default to `limit 100` unless the user asks for full output or aggregates.

## Mutation policy

For `insert`, `update`, `delete`, `alter`, `drop`, `truncate`, `create`, `grant`, `revoke`, data-modifying CTEs, side-effecting functions, or unguarded `EXPLAIN ANALYZE`:

1. Draft the SQL.
2. Explain the impact.
3. Ask for confirmation.
4. Execute only after confirmation.
5. Run a verification query under the read-only guard when practical.

## Supabase notes

- Supabase uses PostgreSQL, so `psql` works with the database connection string from the Supabase dashboard when that string is stored outside the conversation.
- Ask users to set a supported environment variable from their local terminal or host secret/config mechanism.
- If the Supabase CLI is installed, you can inspect local status with:

```bash
supabase status
```
