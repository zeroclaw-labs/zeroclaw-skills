# sql-executor command reference

## Preferred execution command

Use `psql` with strict error handling and no pager:

```bash
psql "$DATABASE_URL" -X -v ON_ERROR_STOP=1 -P pager=off -c "SELECT now();"
```

## Common environment variables

Check these in order and use the first one that exists:

1. `DATABASE_URL`
2. `POSTGRES_URL`
3. `POSTGRES_URL_NON_POOLING`
4. `SUPABASE_DB_URL`
5. `SUPABASE_DATABASE_URL`

## Schema discovery

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

For `insert`, `update`, `delete`, `alter`, `drop`, `truncate`, `create`, `grant`, or `revoke`:

1. Draft the SQL.
2. Explain the impact.
3. Ask for confirmation.
4. Execute only after confirmation.
5. Run a verification query when practical.

## Supabase notes

- Supabase uses PostgreSQL, so `psql` works with the database connection string from the Supabase dashboard.
- For local Supabase, the default database URL is often:

```bash
postgresql://postgres:postgres@127.0.0.1:54322/postgres
```

- If the Supabase CLI is installed, you can inspect local status with:

```bash
supabase status
```
