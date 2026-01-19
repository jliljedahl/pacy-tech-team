---
name: supabase-setup
description: |
  Create and configure Supabase projects, databases, tables, and RLS policies.
  Use when: creating a new Supabase project, running SQL migrations, setting up
  PostgreSQL tables, configuring Row Level Security, or connecting to Supabase CLI.
allowed-tools:
  - Bash
  - Read
  - Write
  - Glob
---

# Supabase Setup

## CLI Available

Supabase CLI: `~/bin/supabase` (authenticated)
Organization ID: `olshfmcoszcgfwznrxgs`

### Full Workflow: New Project

```bash
# 1. Create project
~/bin/supabase projects create "Project Name" \
  --org-id olshfmcoszcgfwznrxgs \
  --region eu-central-1 \
  --db-password "GENERATE_SECURE_PASSWORD"

# 2. Get project ref from output, then get API keys
~/bin/supabase projects api-keys --project-ref PROJECT_REF

# 3. Initialize and link locally
mkdir -p /tmp/PROJECT_NAME && cd /tmp/PROJECT_NAME
~/bin/supabase init
~/bin/supabase link --project-ref PROJECT_REF --password "DB_PASSWORD"

# 4. Create migration
mkdir -p supabase/migrations
cat > supabase/migrations/00001_initial_schema.sql << 'EOF'
-- Your SQL here
EOF

# 5. Push to remote database
~/bin/supabase db push
```

### Existing Projects

```bash
# List all projects
~/bin/supabase projects list

# Get API keys
~/bin/supabase projects api-keys --project-ref PROJECT_REF
```

### Running SQL via Migrations

```bash
# Create migration file
cat > supabase/migrations/YYYYMMDDHHMMSS_name.sql << 'EOF'
CREATE TABLE example (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL
);
EOF

# Push to database (will prompt for confirmation)
~/bin/supabase db push
```

### Verify via REST API

```bash
# Test that table exists and query works
SERVICE_KEY="your-service-role-key"
curl -s "https://PROJECT_REF.supabase.co/rest/v1/table_name?select=*" \
  -H "apikey: $SERVICE_KEY" \
  -H "Authorization: Bearer $SERVICE_KEY"
```

### Important Notes

- Always run `supabase` commands from a linked project directory
- DB password is required when linking: `--password "PASSWORD"`
- Migrations are applied in alphabetical order (use timestamps)
- Direct `db execute` requires local linking first

## Setup Checklist

- [ ] Create project with CLI or supabase.com
- [ ] Get URL and anon key
- [ ] Create tables
- [ ] Enable RLS
- [ ] Create policies
- [ ] Add indexes
- [ ] Test API

## Table Template

```sql
CREATE TABLE table_name (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at      TIMESTAMPTZ DEFAULT NOW() NOT NULL,
    updated_at      TIMESTAMPTZ DEFAULT NOW() NOT NULL
    -- columns here
);

-- Auto-update timestamp
CREATE TRIGGER set_updated_at
    BEFORE UPDATE ON table_name
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at();
```

## RLS Patterns

### Enable RLS

```sql
ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;
```

### Common Policies

```sql
-- Public read
CREATE POLICY "public_read" ON table_name
FOR SELECT USING (true);

-- Authenticated insert
CREATE POLICY "auth_insert" ON table_name
FOR INSERT TO authenticated
WITH CHECK (true);

-- Own records only
CREATE POLICY "own_records" ON table_name
FOR ALL TO authenticated
USING (user_id = auth.uid());

-- Tenant isolation
CREATE POLICY "tenant_isolation" ON table_name
FOR ALL TO authenticated
USING (client_id IN (
    SELECT client_id FROM user_clients 
    WHERE user_id = auth.uid()
));
```

## Indexes

```sql
-- Foreign keys
CREATE INDEX idx_table_fk ON table_name(foreign_key);

-- Filtered columns
CREATE INDEX idx_table_status ON table_name(status);
```

## Test API

```bash
curl 'https://PROJECT.supabase.co/rest/v1/table?select=*' \
  -H "apikey: ANON_KEY" \
  -H "Authorization: Bearer ANON_KEY"
```

## Environment Variables

```env
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...
```

## Troubleshooting

| Error | Fix |
|-------|-----|
| Permission denied | Check RLS policies |
| Relation not found | Check table name, schema |
| Slow queries | Add indexes |
