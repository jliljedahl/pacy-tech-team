---
name: database-operations
description: |
  Run SQL queries and migrations against Supabase databases using pacy-db tool.
  Use when: running migrations, querying data, checking table structure, or
  debugging database issues. Requires SUPABASE_ACCESS_TOKEN in .env.
allowed-tools:
  - Bash
  - Read
  - Write
  - Glob
---

# Database Operations

## pacy-db Tool

Custom CLI for Pacy Tech Team database operations. Uses Supabase Management API.

**Location:** `.tools/bin/pacy-db`

### Commands

```bash
# Test connection
.tools/bin/pacy-db test

# List all tables
.tools/bin/pacy-db tables

# Run SQL query
.tools/bin/pacy-db query "SELECT * FROM users LIMIT 5"

# Run SQL file
.tools/bin/pacy-db file ./migrations/001_init.sql

# Run all migrations in directory
.tools/bin/pacy-db migrate ./supabase/migrations/
```

### Requirements

The following must be in `.env`:

```env
SUPABASE_URL=https://xxxxx.supabase.co
SUPABASE_ACCESS_TOKEN=sbp_xxxxx
```

Get access token from: https://supabase.com/dashboard/account/tokens

---

## Migration Workflow

### 1. Create Migration File

```bash
# Naming convention: YYYYMMDD_description.sql
cat > supabase/migrations/20260121_add_users.sql << 'EOF'
CREATE TABLE IF NOT EXISTS users (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email       TEXT UNIQUE NOT NULL,
    created_at  TIMESTAMPTZ DEFAULT NOW()
);

ALTER TABLE users ENABLE ROW LEVEL SECURITY;

CREATE POLICY "users_read_own" ON users
FOR SELECT TO authenticated
USING (id = auth.uid());
EOF
```

### 2. Run Migration

```bash
.tools/bin/pacy-db file supabase/migrations/20260121_add_users.sql
```

### 3. Verify

```bash
.tools/bin/pacy-db tables
.tools/bin/pacy-db query "SELECT * FROM users LIMIT 1"
```

---

## Common Queries

### Check Table Structure

```bash
.tools/bin/pacy-db query "
  SELECT column_name, data_type, is_nullable
  FROM information_schema.columns
  WHERE table_name = 'TABLE_NAME'
  ORDER BY ordinal_position
"
```

### Check RLS Policies

```bash
.tools/bin/pacy-db query "
  SELECT schemaname, tablename, policyname, permissive, roles, cmd, qual
  FROM pg_policies
  WHERE tablename = 'TABLE_NAME'
"
```

### Check Indexes

```bash
.tools/bin/pacy-db query "
  SELECT indexname, indexdef
  FROM pg_indexes
  WHERE tablename = 'TABLE_NAME'
"
```

### Row Counts

```bash
.tools/bin/pacy-db query "
  SELECT relname, n_live_tup
  FROM pg_stat_user_tables
  ORDER BY n_live_tup DESC
"
```

---

## SQL Templates

### Table with Timestamps

```sql
CREATE TABLE IF NOT EXISTS table_name (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    -- columns here
    created_at  TIMESTAMPTZ DEFAULT NOW(),
    updated_at  TIMESTAMPTZ DEFAULT NOW()
);

-- Auto-update timestamp trigger
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER set_table_name_updated_at
    BEFORE UPDATE ON table_name
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at();
```

### RLS Policies

```sql
-- Enable RLS
ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;

-- Public read (anon + authenticated)
CREATE POLICY "public_read" ON table_name
FOR SELECT USING (true);

-- Service role full access
CREATE POLICY "service_all" ON table_name
FOR ALL TO service_role
USING (true) WITH CHECK (true);

-- Anonymous insert
CREATE POLICY "anon_insert" ON table_name
FOR INSERT TO anon
WITH CHECK (true);

-- Authenticated user's own records
CREATE POLICY "own_records" ON table_name
FOR ALL TO authenticated
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());
```

### Enable Realtime

```sql
ALTER PUBLICATION supabase_realtime ADD TABLE table_name;
```

---

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| `SUPABASE_ACCESS_TOKEN not found` | Missing token | Add to .env from supabase.com/dashboard/account/tokens |
| `Permission denied` | RLS blocking | Check policies, use service role |
| `already exists` | Re-running migration | Safe to ignore (uses IF NOT EXISTS) |
| `relation does not exist` | Table missing | Run migrations first |
| `timeout` | Large query/slow DB | Increase timeout or optimize query |

---

## Best Practices

1. **Always use IF NOT EXISTS** - Makes migrations idempotent
2. **Enable RLS immediately** - Don't leave tables open
3. **Name migrations with dates** - Ensures correct order
4. **Test queries first** - Use `pacy-db query` before putting in migration
5. **Backup before destructive changes** - No undo for DROP/DELETE
