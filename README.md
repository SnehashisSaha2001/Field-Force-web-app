
# Tracker App Setup

## 🚨 CRITICAL: Database Setup (Supabase)

For this app to work, you must run this SQL script in your Supabase project. This script creates the required tables and constraints exactly as required by the app.

**Note:** The order of execution is important because of Foreign Key constraints.

1. Go to [Supabase Dashboard](https://supabase.com/dashboard).
2. Open your project.
3. Go to the **SQL Editor** (icon on the left sidebar).
4. Click **New Query**.
5. Paste the SQL below and click **Run**.

```sql
-- 1. Users Table
CREATE TABLE public.users (
    user_id text NOT NULL,
    name text NOT NULL,
    email text UNIQUE,
    mobile_no text,
    role text NOT NULL CHECK (role = ANY (ARRAY['admin'::text, 'employee'::text])),
    manager_id text,
    photo_url text,
    is_online boolean DEFAULT false,
    last_sync timestamp with time zone,
    password text,
    created_at timestamp with time zone DEFAULT now(),
    updated_at timestamp with time zone DEFAULT now(),
    CONSTRAINT users_pkey PRIMARY KEY (user_id),
    CONSTRAINT users_manager_id_fkey FOREIGN KEY (manager_id) REFERENCES public.users(user_id)
);

-- 2. Daily Activity Table
CREATE TABLE public.daily_activity (
    id bigint GENERATED ALWAYS AS IDENTITY NOT NULL,
    user_id text NOT NULL,
    activity_date date DEFAULT CURRENT_DATE,
    type text NOT NULL CHECK (type = ANY (ARRAY['checkin'::text, 'checkout'::text, 'visit'::text])),
    location text,
    latitude numeric,
    longitude numeric,
    description text,
    details text,
    created_at timestamp with time zone DEFAULT now(),
    updated_at timestamp with time zone DEFAULT now(),
    CONSTRAINT daily_activity_pkey PRIMARY KEY (id),
    CONSTRAINT daily_activity_user_id_fkey FOREIGN KEY (user_id) REFERENCES public.users(user_id)
);

-- 3. Follow Ups Table
CREATE TABLE public.followups (
    id bigint GENERATED ALWAYS AS IDENTITY NOT NULL,
    user_id text NOT NULL,
    client_id text,
    subject text NOT NULL,
    notes text,
    followup_date timestamp with time zone DEFAULT now(),
    status text DEFAULT 'open'::text CHECK (status = ANY (ARRAY['open'::text, 'done'::text, 'missed'::text])),
    created_at timestamp with time zone DEFAULT now(),
    updated_at timestamp with time zone DEFAULT now(),
    CONSTRAINT followups_pkey PRIMARY KEY (id),
    CONSTRAINT followups_user_id_fkey FOREIGN KEY (user_id) REFERENCES public.users(user_id)
);

-- 4. DISABLE ROW LEVEL SECURITY (Critical for direct browser access)
ALTER TABLE public.users DISABLE ROW LEVEL SECURITY;
ALTER TABLE public.daily_activity DISABLE ROW LEVEL SECURITY;
ALTER TABLE public.followups DISABLE ROW LEVEL SECURITY;
```

## Deployment
1. Upload `index.html` to GitHub.
2. Enable GitHub Pages.
3. Visit the URL.
