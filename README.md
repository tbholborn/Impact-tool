# LIVE Sustainability Skills Compass — deployment package

This is the same mock-up published as a Claude artifact, packaged as plain files so
Think Beyond can host it on its own branded URL for the THE·TEAM pitch. It's a
static site — two files, no backend, no build step:

```
index.html   the whole tool (structure, styling, behaviour)
data.json    the illustrative dataset the tool reads and renders
CNAME.example  template for a GitHub Pages custom domain (see below)
.nojekyll    tells GitHub Pages to serve the files as-is
source/      the dummy-data workbook + the script that generates data.json
```

Everything in the tool is clearly labelled **illustrative / dummy data** — no real
THE·TEAM people, suppliers or events are represented. Keep that labelling intact
wherever this gets deployed.

## Before you deploy: preview it locally

Opening `index.html` by double-clicking it will show a blank error message —
browsers block `index.html` from fetching `data.json` over the `file://`
protocol. Serve the folder instead:

```bash
cd deploy   # this folder
python3 -m http.server 8000
```

Then open `http://localhost:8000` in a browser. (Any static server works —
`npx serve`, VS Code's "Live Server" extension, etc.)

## Option A — GitHub Pages (recommended: free, versioned, easy custom domain)

1. **Create a repo.** On github.com, create a new repository under the Think
   Beyond org (or your own account) — e.g. `theteam-skills-compass`. It can be
   public or private; a custom domain on GitHub Pages currently requires the
   repo to be public unless your org is on GitHub Enterprise.

2. **Push these files.** From this folder:

   ```bash
   git init
   git add .
   git commit -m "LIVE Sustainability Skills Compass — mock-up"
   git branch -M main
   git remote add origin https://github.com/<your-org-or-username>/theteam-skills-compass.git
   git push -u origin main
   ```

3. **Turn on Pages.** In the repo: **Settings → Pages** → under "Build and
   deployment", set **Source: Deploy from a branch**, **Branch: main / (root)**,
   then Save. GitHub will build the site and give you a URL like
   `https://<your-org>.github.io/theteam-skills-compass/` within a minute or two.

4. **Point a Think Beyond subdomain at it (white-labelled URL).** This is the
   part that makes the link read as yours instead of github.io:

   - Pick a subdomain — e.g. `skills-compass.thinkbeyond.consulting` (the
     included `CNAME.example` uses this as a placeholder).
   - Rename `CNAME.example` to `CNAME` and replace its contents with your real
     subdomain (just the domain, nothing else), then commit and push it.
   - Ask whoever manages DNS for `thinkbeyond.consulting` to add a **CNAME
     record**: `skills-compass` → `<your-org-or-username>.github.io`.
   - Back in **Settings → Pages**, enter the same subdomain under "Custom
     domain" and save. Once DNS has propagated (minutes to a few hours), tick
     **Enforce HTTPS**.
   - The tool is now live at `https://skills-compass.thinkbeyond.consulting`.

## Option B — Netlify (fastest if you don't want to touch GitHub)

1. Go to [app.netlify.com](https://app.netlify.com) → **Add new site → Deploy
   manually**, then drag this whole `deploy` folder onto the page. It's live on
   a `*.netlify.app` URL within seconds — no `CNAME` file needed here.
2. **Site settings → Domain management → Add a custom domain** → enter your
   Think Beyond subdomain and follow Netlify's on-screen DNS instructions
   (usually one CNAME record, same idea as above). Netlify issues a free SSL
   certificate automatically once DNS resolves.
3. To update later: re-drag the folder, or connect the same GitHub repo from
   Option A for automatic deploys on every push.

## Keeping it private

Neither option password-protects the page by default — anyone with the link
can open it. If THE·TEAM needs it access-controlled rather than just
unlisted:

- **Netlify**: paid plans include a built-in password-protect toggle per site.
- **GitHub Pages**: keeping the repo private only restricts Pages if your org
  is on GitHub Enterprise; otherwise consider a simple hosted alternative with
  auth (Cloudflare Pages + Access, Vercel password protection, or a
  Think-Beyond-managed server).

For a pitch walkthrough, an unlisted-but-public link is usually sufficient —
just don't link it from anywhere public.

## Updating the data later

The workbook in `source/THETEAM_Dummy_Dataset.xlsx` is the single source of
truth for every number in the tool. To change the illustrative data (new
categories, different scores, more sample rows):

1. Edit `source/THETEAM_Dummy_Dataset.xlsx` (or regenerate it — see Think
   Beyond's `build_dataset.py` from the earlier project files).
2. Regenerate `data.json`:

   ```bash
   cd deploy
   python3 source/export_data.py
   ```

3. Re-deploy: commit + push (GitHub Pages) or re-drag the folder (Netlify).

`index.html` never needs to change for a data update — it always reads
whatever is in `data.json` at runtime.

## When real fieldwork replaces the dummy data

This package is wired for the illustrative mock-up only. Moving to real THE·TEAM
data means replacing `source/THETEAM_Dummy_Dataset.xlsx` (or the
`export_data.py` pipeline) with a feed from wherever the real survey/interview
data ends up living — see the tool brief's note on Microsoft Forms → Power
Automate → Excel/SharePoint as the likely real-world source. At that point
this also stops being a public mock-up and the access-control question above
becomes a real requirement, not a nice-to-have.
