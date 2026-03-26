# 🚢 BayesTanic — Deployment Guide
## Supabase + Netlify Setup (Free, No Credit Card)

---

## STEP 1 — Create Your Supabase Database (5 minutes)

1. Go to **https://supabase.com** → click "Start your project"
2. Sign up with GitHub or email (free, no credit card)
3. Click **"New project"**
   - Name: `bayestanic`
   - Database password: choose a strong one (save it!)
   - Region: pick the one closest to you (e.g. South Asia)
   - Click **"Create new project"** — wait ~2 minutes

---

## STEP 2 — Create the Database Tables

Once your project is ready:

1. In the left sidebar, click **"SQL Editor"**
2. Click **"New query"**
3. Paste the entire block below and click **"Run"**

```sql
-- Table 1: Track every page visit
CREATE TABLE visits (
  id BIGSERIAL PRIMARY KEY,
  visited_at TIMESTAMPTZ DEFAULT NOW(),
  user_agent TEXT
);

-- Table 2: Store every prediction with ALL user inputs
CREATE TABLE predictions (
  id BIGSERIAL PRIMARY KEY,
  passenger_name TEXT DEFAULT 'Anonymous',
  age INTEGER,
  sex TEXT,
  pclass TEXT,
  fare NUMERIC,
  port TEXT,
  siblings_spouse INTEGER,
  parents_children INTEGER,
  family_size INTEGER,
  is_alone BOOLEAN,
  survived BOOLEAN,
  survival_probability INTEGER,
  pdf_downloaded BOOLEAN DEFAULT FALSE,
  predicted_at TIMESTAMPTZ DEFAULT NOW()
);

-- Table 3: Visitor manifest (people who sign in)
CREATE TABLE visitors (
  id BIGSERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  role TEXT,
  message TEXT,
  signed_at TIMESTAMPTZ DEFAULT NOW()
);

-- Allow public read/write (needed for static site with anon key)
ALTER TABLE visits      ENABLE ROW LEVEL SECURITY;
ALTER TABLE predictions ENABLE ROW LEVEL SECURITY;
ALTER TABLE visitors    ENABLE ROW LEVEL SECURITY;

CREATE POLICY "public insert visits"      ON visits      FOR INSERT WITH CHECK (true);
CREATE POLICY "public select visits"      ON visits      FOR SELECT USING (true);
CREATE POLICY "public insert predictions" ON predictions FOR INSERT WITH CHECK (true);
CREATE POLICY "public select predictions" ON predictions FOR SELECT USING (true);
CREATE POLICY "public update predictions" ON predictions FOR UPDATE USING (true);
CREATE POLICY "public insert visitors"    ON visitors    FOR INSERT WITH CHECK (true);
CREATE POLICY "public select visitors"    ON visitors    FOR SELECT USING (true);
```

You should see: **"Success. No rows returned."** ✅

---

## STEP 3 — Get Your API Keys

1. In the left sidebar click **"Project Settings"** (gear icon)
2. Click **"API"**
3. Copy two things:
   - **Project URL** — looks like: `https://abcxyz.supabase.co`
   - **anon public key** — a long string starting with `eyJ...`

---

## STEP 4 — Paste Keys Into index.html

Open `index.html` in any text editor (Notepad, VS Code, etc.)

Find these two lines near the top of the `<script>` section:

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

Replace with your actual values:

```javascript
const SUPABASE_URL = 'https://abcxyz.supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGci...your full key here...';
```

Save the file.

---

## STEP 5 — Deploy on Netlify (2 minutes)

1. Go to **https://netlify.com** → Sign up free
2. On the dashboard, find the box that says **"Deploy manually"**
   or go to: **Sites → Add new site → Deploy manually**
3. **Drag and drop the entire `bayestanic-netlify` folder** onto the Netlify page
4. Wait ~10 seconds
5. Netlify gives you a URL like: `https://bayestanic-abc123.netlify.app` 🎉

That's it! Your site is live.

---

## STEP 6 — (Optional) Custom Domain Name

1. In Netlify: **Site settings → Domain management → Add custom domain**
2. Buy a domain from Namecheap/GoDaddy and point it to Netlify
   - Or use the free `.netlify.app` URL — totally fine!

---

## How to View Your Data

Every prediction a user makes is stored in Supabase.
To see it:

1. Go to **supabase.com → your project**
2. Click **"Table Editor"** in the left sidebar
3. Click on `predictions` — you'll see every user's inputs:
   - Their name, age, gender, class, fare, port, family size
   - Whether they survived, their probability %
   - Whether they downloaded the PDF
   - Timestamp

Same for `visitors` (people who signed the manifest) and `visits` (page views).

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Green dot not showing in nav | Check your SUPABASE_URL and ANON_KEY are correct, no extra spaces |
| "Error saving" on visitor form | Make sure you ran the SQL in Step 2, including the POLICY lines |
| Site shows but data not saving | Open browser Console (F12) and check for red errors |
| Want to update the site | Just drag the folder to Netlify again — it re-deploys automatically |

---

## What Gets Stored

| Table | Data |
|---|---|
| `predictions` | Name, age, sex, class, fare, port, family, survived, probability, PDF downloaded |
| `visitors` | Name, role, message, timestamp |
| `visits` | Page load timestamp, browser info |

---

*BayesTanic · Built with Gaussian Naive Bayes · Supabase · Netlify*
