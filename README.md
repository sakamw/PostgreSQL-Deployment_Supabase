# Supabase PostgreSQL: Connect Using `psql` + Secure with RLS

This guide covers how to:

- Connect to your **Supabase PostgreSQL** database using `psql`
- Perform basic operations
- Implement **Row Level Security (RLS)** and define **policies** for data protection

## Step 1: Get Your Supabase Connection String

1. Log into your Supabase project at [supabase.com](https://supabase.com)
2. Go to **Settings → Database**
3. Copy the connection string. It looks like:

> postgresql://postgres.kvehnizltknnrbfxrgqd:[YOUR-PASSWORD]@aws-0-eu-central-1.pooler.supabase.com:5432/postgres

After running the `psql` command to connect to your Supabase PostgreSQL database, you'll see something like this:

```sql
psql (15.1, server 14.2)
Type "help" for help.
```

### Confirming connections

Use `\conninfo` to show your current conection.

## Step 2: Run Common SQL commands

Create a Table

```sql
CREATE TABLE users (
   id SERIAL primary KEY,
   email VARCHAR(255) UNIQUE NOT NULL,
   created_at TIMESTAMP DEFAULT NOW()
);
```

## Step3: Secure Data Using Row Level Security (RSL)

Row Level Security (RSL) is a Postgres feature that restricts access to individual rows based on conditions you define. It's essential in apps where users should only aaccess their own data.

Enable RLS on a Table

```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
```

## Step 4: Create Access Policies

Policies define **who can do what** with each row. Supabase uses `auth.uid()` to reference the current logged-in-user.

1. SELECT Policy - Read Own Rows

```sql
CREATE POLICY "Users can view own data" ON users
  FOR SELECT
  USING (auth.uid() = id);
```

1. INSERT Policy - Add Own Rows

```sql
CREATE POLICY "Users can insert own data" ON users
  FOR INSERT
  WITH CHECK (auth.uid() = id);
```

1. UPDATE Policy - Edit Own Rows

```sql
CREATE POLICY "Users can update own data" ON users
  FOR UPDATE
  USING (auth.uid() = id);
```

## Policy Rules

| Operation | Clause       | Purpose                           |
| --------- | ------------ | --------------------------------- |
| SELECT    | `USING`      | Filters which rows can be read    |
| INSERT    | `WITH CHECK` | Validates inserted rows           |
| UPDATE    | `USING`      | Filters which rows can be updated |
| UPDATE    | `WITH CHECK` | Validates allowed updates         |
| DELETE    | `USING`      | Filters which rows can be deleted |
