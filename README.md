# Virelia

A real, deployable version of the Virelia / The Long Road fitness RPG — built with Vite + React.
This replaces the Claude-artifact-only `window.storage` API with real browser `localStorage` for
saves, and connects to a real Supabase database for Party sync (which needs a real, non-sandboxed
network connection to work at all).

## What changed from the artifact version

- **Saves**: now use `localStorage` directly (a real, standard browser API) instead of the
  artifact-only `window.storage`. Same behavior — personal, per-browser, persists across visits.
- **Party sync**: unchanged code, but now actually reachable, since this runs on the open web
  instead of inside a sandbox that blocks outbound requests to third-party domains.
- **Supabase config**: reads from environment variables (`.env`) instead of being hardcoded, so
  the real key isn't sitting in your public GitHub repo's source code.

Everything else — every Act, every quest, every piece of art, the whole game — is unchanged.

## Running it locally (optional, to test before deploying)

You'll need [Node.js](https://nodejs.org) installed (any recent version).

```bash
npm install
npm run dev
```

Opens at `http://localhost:5173`. `.env` already has real values in it, so this should just work.

## Deploying for free — the part that needs your account

This can't be done from inside this chat since it requires your own GitHub and Vercel accounts.
Here's exactly what to click.

### 1. Push this project to GitHub

- Go to [github.com](https://github.com), sign in (or make a free account)
- Click **New repository**, name it anything (e.g. `virelia`), leave it public or private, don't
  add a README/gitignore (you already have one) — click **Create repository**
- GitHub will show you a page with commands. From a terminal, inside this project folder, run:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/virelia.git
git push -u origin main
```

(Replace the URL with the one GitHub actually shows you on that page.) If you don't have `git`
installed, GitHub's page also has a "upload an existing file" option in the browser — drag this
whole folder in instead, no terminal needed. `.env` won't upload either way since it's gitignored;
that's intentional.

### 2. Connect it to Vercel

- Go to [vercel.com](https://vercel.com), sign in with your GitHub account (one click, no separate
  signup)
- Click **Add New → Project**, find the `virelia` repo you just pushed, click **Import**
- Vercel auto-detects Vite — you shouldn't need to change any build settings
- Before clicking Deploy, expand **Environment Variables** and add these two, using your real
  Supabase project's values (Supabase dashboard → Project Settings → API):
  - `VITE_SUPABASE_URL`
  - `VITE_SUPABASE_ANON_KEY` (the **publishable/anon** key — never the secret key)
- Click **Deploy**

That's it — Vercel gives you a real `https://virelia-something.vercel.app` URL. From then on, any
time you `git push` an update, Vercel automatically rebuilds and redeploys on its own.

## Your Supabase database

Already set up from earlier — the `party_rooms` and `party_members` tables and their access
policies don't need to change for this move. This project just needed a way to actually *reach*
that database, which the Claude artifact sandbox was blocking.
