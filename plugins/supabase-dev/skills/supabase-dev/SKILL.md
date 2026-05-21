---
name: supabase-dev
description: Supabase development patterns for RLS policies, migrations, edge functions, auth, storage, and TypeScript type generation
---

# Supabase Development Expert

You are an expert in Supabase. Use the Supabase MCP tools for all database operations and follow these patterns.

## MCP Tool Usage

```
list_tables          → inspect current schema before changes
execute_sql          → run queries, check data
apply_migration      → apply SQL migrations (prefer over execute_sql for schema changes)
list_migrations      → check migration history
generate_typescript_types → regenerate types after schema changes
get_advisors         → check for security/performance issues before shipping
get_logs             → debug edge function errors
get_project_url      → get connection details for client config
get_publishable_keys → get anon key for frontend
```

Always run `list_tables` before schema changes. Always run `get_advisors` before finishing.

## Row Level Security (RLS)

**Always enable RLS on every table that holds user data.**

```sql
-- Enable
alter table public.items enable row level security;

-- User owns their rows
create policy "users see own items"
  on public.items for select
  using (auth.uid() = user_id);

create policy "users insert own items"
  on public.items for insert
  with check (auth.uid() = user_id);

create policy "users update own items"
  on public.items for update
  using (auth.uid() = user_id)
  with check (auth.uid() = user_id);

create policy "users delete own items"
  on public.items for delete
  using (auth.uid() = user_id);
```

**Service role bypass pattern** (for edge functions / admin):
```sql
-- Edge functions use service_role key which bypasses RLS
-- Never expose service_role key to frontend
```

**Shared/public data pattern:**
```sql
create policy "public read"
  on public.posts for select
  using (published = true);
```

## Migrations

Use `apply_migration` with a descriptive name. Never use `execute_sql` for DDL — it won't be tracked.

```sql
-- Migration: add_user_preferences_table
create table public.user_preferences (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references auth.users(id) on delete cascade,
  theme text not null default 'system',
  notifications_enabled boolean not null default true,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);

alter table public.user_preferences enable row level security;

create policy "users manage own preferences"
  on public.user_preferences for all
  using (auth.uid() = user_id)
  with check (auth.uid() = user_id);

create index on public.user_preferences (user_id);
```

Migration naming: `add_<thing>`, `drop_<thing>`, `alter_<table>_<change>`, `create_<table>_rls`

## Edge Functions

Edge functions run Deno. Use the service role client for admin operations.

```typescript
import { createClient } from "jsr:@supabase/supabase-js@2";

Deno.serve(async (req) => {
  const supabase = createClient(
    Deno.env.get("SUPABASE_URL")!,
    Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
  );

  const { data, error } = await supabase
    .from("items")
    .select("*")
    .limit(10);

  if (error) return new Response(JSON.stringify({ error: error.message }), {
    status: 500,
    headers: { "Content-Type": "application/json" },
  });

  return new Response(JSON.stringify(data), {
    headers: { "Content-Type": "application/json" },
  });
});
```

For user-scoped operations inside edge functions, pass the JWT and use it:
```typescript
const authHeader = req.headers.get("Authorization");
const supabase = createClient(url, anonKey, {
  global: { headers: { Authorization: authHeader! } }
});
```

## TypeScript Integration

After every migration, run `generate_typescript_types` and update `database.types.ts`. Use the generated types:

```typescript
import type { Database } from "@/types/database.types";

type Item = Database["public"]["Tables"]["items"]["Row"];
type ItemInsert = Database["public"]["Tables"]["items"]["Insert"];
```

## Auth Patterns

```typescript
// Sign in with email/password
const { data, error } = await supabase.auth.signInWithPassword({
  email, password
});

// Get current user (server-side safe)
const { data: { user } } = await supabase.auth.getUser();

// Sign out
await supabase.auth.signOut();
```

Use `getUser()` server-side, not `getSession()` — `getUser()` validates with Supabase auth server.

## Storage

```typescript
// Upload
const { data, error } = await supabase.storage
  .from("avatars")
  .upload(`${user.id}/avatar.png`, file, { upsert: true });

// Public URL
const { data: { publicUrl } } = supabase.storage
  .from("avatars")
  .getPublicUrl(`${user.id}/avatar.png`);
```

Storage RLS is separate from table RLS — configure bucket policies in `Storage > Policies`.

## Common Mistakes

- `execute_sql` for DDL → use `apply_migration` instead
- Forgetting `on delete cascade` on foreign keys to `auth.users`
- Not enabling RLS before adding policies
- Exposing `service_role` key in frontend code
- Missing index on `user_id` foreign key columns
- Using `getSession()` server-side (not validated) instead of `getUser()`
