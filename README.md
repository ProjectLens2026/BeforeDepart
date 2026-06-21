# BeforeDepart — Go Live (GitHub → Vercel → Cloudflare)

This folder is a ready-to-deploy static site.
- `index.html` — the BeforeDepart app (single file, no build step)
- `vercel.json` — clean URLs + basic security headers

## 1) Put it on GitHub
```bash
# inside this folder
git init
git add .
git commit -m "BeforeDepart launch"
git branch -M main
git remote add origin https://github.com/<your-username>/beforedepart.git
git push -u origin main
```

## 2) Deploy on Vercel
1. Go to vercel.com/new → Import your `beforedepart` repo.
2. Framework Preset: **Other**. Build Command: **(leave empty)**. Output Directory: **(leave empty)**.
3. Click **Deploy**. You'll get a live URL like `beforedepart.vercel.app`.

## 3) Add your domains in Vercel
Project → **Settings → Domains**, add all three:
- `www.beforedepart.com`   ← primary (the main site)
- `app.beforedepart.com`   ← the app subdomain
- `beforedepart.com`       ← set this to **Redirect → www.beforedepart.com**

Vercel will show the exact DNS records to add. They're typically:

| Type  | Name | Value                  |
|-------|------|------------------------|
| CNAME | www  | cname.vercel-dns.com   |
| CNAME | app  | cname.vercel-dns.com   |
| A     | @    | 76.76.21.21            |

## 4) Add the DNS records in Cloudflare
Cloudflare dashboard → your domain → **DNS → Records**. Add the rows above.

IMPORTANT for SSL:
- Set each Vercel record's proxy status to **DNS only (grey cloud)**, OR
- If you keep it **Proxied (orange cloud)**, set **SSL/TLS → Overview → Full (strict)**.
- (Grey cloud is the simplest and avoids SSL handshake issues with Vercel.)

For the root `@`, Cloudflare supports CNAME flattening, so you may instead add:
`CNAME  @  cname.vercel-dns.com` (DNS only).

## 5) Wait for SSL (a few minutes)
Vercel auto-issues HTTPS certificates once DNS resolves. When the domains show
"Valid Configuration" in Vercel, you're live at:
- https://www.beforedepart.com
- https://app.beforedepart.com

## Updating the site later
Push to `main` on GitHub → Vercel auto-deploys. That's it.

---

## Next phase — Supabase (when you're ready)
Supabase becomes the backend that adds the "come back to it" features:
- **Auth** — let users sign in (email / Google) so trips are tied to an account.
- **Saved trips** — a `trips` table so a built plan persists across visits/devices.
- **City content DB** — move the curated city data out of `index.html` into a
  `cities` table, so you (or Claude, in batches) can add/update cities without
  editing code. This is the "generate once, serve forever" content model.
- **Affiliate analytics** — a `clicks` table to see which outbound links convert.
- **Storage** — host real city photos in a Supabase bucket (own your images).

You'd add `@supabase/supabase-js`, store the project URL + anon key as Vercel
environment variables, and read/write from the app. Ask and I'll wire this in.
