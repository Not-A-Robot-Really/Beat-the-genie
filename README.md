# Beat the Genie

A neal.fun-style toy: make a wish, the genie grants it exactly as worded, then finds (and cites) a specific loophole to ruin it. Three wishes per visitor, each scored 0–100 on how airtight the wording was, with a leaderboard of the best (and worst) wishes ever made.

Single static HTML file — no build step, no server. Drop `index.html` and `data.json` in a repo and turn on GitHub Pages.

## Setup

1. **Host it.** Push `index.html` and `data.json` to a repo, then enable GitHub Pages (Settings → Pages → deploy from branch).
2. **API key.** Visitors paste their own key via the gear icon (top right). It's stored only in their browser and sent straight to the provider — this site never sees or stores it.
3. *(Optional)* **Site-wide repo link.** Open `index.html`, find `var GITHUB_REPO = "";` near the top of the `<script>` block, and set it to `"your-username/your-repo"`. This turns on the "Open GitHub Issue" button so visitors can submit leaderboard entries for you to merge into `data.json`.

## About the "default API key" option

There's also a `DEFAULT_API_KEYS` constant near the top of the script. **Read the big warning comment above it before touching it.** Short version: this is a public static file, so anything you put there is visible to anyone who views the page source — bots that scrape GitHub for exposed keys included. If you fill it in:

- Set a hard spending/usage limit on that key in your provider's console first.
- Treat the key as disposable — rotate it if you ever see unexpected usage.
- The actually-safe version of "no visitor has to bring a key" is a small serverless proxy (Cloudflare Worker, Netlify Function, Vercel Edge Function) that holds the key server-side and this page calls instead of the provider directly. That's a bigger change than this file alone — happy to build that version if you want it.

## The leaderboard and `data.json`

GitHub Pages is static — this page can't write back into its own repo on its own. So:

- `data.json` is the **seed/shared leaderboard**, loaded on page visit.
- Each visitor's own completed games are also kept in their browser's `localStorage` and merged into the displayed list (so they always see their own runs, even before you've merged anything).
- When someone clicks **"Submit to leaderboard"**, the site builds a JSON entry (`name`, `score`, `date`, and the three `wishes` with each one's `wish`, `loophole`, `proof`, `outcome`, and `score`) and either:
  - opens a pre-filled GitHub Issue on your repo (if you set `GITHUB_REPO`), so you can review and paste the entry into `data.json` yourself, or
  - just shows the JSON to copy, if no repo is configured.

If you want this fully automatic with no manual merging, swap the leaderboard storage for a real backend (Cloudflare KV / Supabase / a Google Sheet via Apps Script, etc.) — the `fetchSeedLeaderboard()` and `submitToLeaderboard()` functions are the two places to redirect.

## Files

- `index.html` — the whole site.
- `data.json` — shared leaderboard data (a few example entries included; replace or clear them).
