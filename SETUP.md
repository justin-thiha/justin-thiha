# Setup Guide — Profile README Deployment

This bundle is a complete, self-contained GitHub profile README. Follow these steps in order to deploy it.

---

## Prerequisites

- A GitHub account with username `justinthiha` (or change every occurrence below)
- Git installed locally
- A free Vercel/Heroku/etc account is **not** required — all third-party SVGs in this README are hosted services that work out of the box

---

## Step 1 — Create the profile repo

GitHub's "profile README" trick: create a repo whose name matches your username exactly. The `README.md` in that repo becomes your profile page.

```bash
# On github.com:
#   New repository
#   Owner: justinthiha
#   Repository name: justinthiha   ← must match your username exactly
#   Public, initialise with a README.
```

Then clone it locally:

```bash
git clone https://github.com/justinthiha/justinthiha.git
cd justinthiha
```

---

## Step 2 — Drop in the files

Copy the entire contents of this bundle into the repo root:

```
justinthiha/
├── README.md
├── assets/
│   ├── hero-dark.svg
│   ├── hero-light.svg
│   ├── divider.svg
│   └── metrics.svg
└── .github/
    └── workflows/
        ├── snake.yml
        └── metrics.yml
```

Commit and push:

```bash
git add .
git commit -m "feat: cinematic README rehaul with animated SVG hero"
git push origin main
```

Visit `https://github.com/justinthiha` — the README should render with the animated hero, divider, and metrics tile playing immediately. Third-party stat cards (github-readme-stats, streak, typing) will also work right away.

---

## Step 3 — Enable the snake animation

The snake animation needs a GitHub Action to generate it daily. Two things to do:

### 3a. Enable Actions on the repo

```
github.com/justinthiha/justinthiha → Settings → Actions → General
  → Allow all actions and reusable workflows
  → Workflow permissions: Read and write permissions
  → Save
```

### 3b. Run the workflow once manually

```
github.com/justinthiha/justinthiha → Actions tab
  → Select "Generate Snake Animation"
  → Run workflow → Run workflow (green button)
```

Wait ~1 minute. When it finishes:

1. A new branch called `output` will be created on the repo
2. It will contain `snake.svg` and `snake-dark.svg`
3. The README's `<picture>` snake block will start rendering immediately
4. From this point on the workflow runs daily at midnight UTC and refreshes the snake

If you ever see a broken snake image, just hit "Run workflow" again — it's idempotent.

---

## Step 4 — (Optional) Enable extended metrics

The `metrics.yml` workflow generates extended profile infographics via [lowlighter/metrics](https://github.com/lowlighter/metrics). It needs a **Personal Access Token** so it can read your private contribution data.

### 4a. Create a fine-grained PAT

```
github.com → Settings (your account) → Developer settings
  → Personal access tokens → Tokens (classic) → Generate new token (classic)
  → Note: "metrics-readme"
  → Expiration: 1 year
  → Scopes: ✅ repo, ✅ read:org, ✅ read:user, ✅ read:packages
  → Generate token → COPY IT (you won't see it again)
```

### 4b. Add it as a repo secret

```
github.com/justinthiha/justinthiha → Settings → Secrets and variables → Actions
  → New repository secret
  → Name: METRICS_TOKEN
  → Value: <paste your PAT>
  → Add secret
```

### 4c. Trigger the workflow

```
Actions tab → "Refresh Profile Metrics" → Run workflow
```

The workflow commits `assets/metrics-extended.svg` to your repo. If you want it on the README, add this somewhere appropriate:

```markdown
<img src="./assets/metrics-extended.svg" alt="Extended profile metrics">
```

I left this out of the default README because the hero/divider/custom-metrics tile already carries the visual load and adding extended metrics on top tips into noise. Enable it only if you specifically want WakaTime-style depth.

---

## Step 5 — (Optional) Tune the hero copy

Everything you might want to edit lives in **`assets/hero-dark.svg`** and **`assets/hero-light.svg`**. Both files have inline comments showing the layer structure:

```
1. Backdrop (gradient + grain)
2. Neural particle field (drifting nodes + edges)
3. Sweep highlight (the moving gradient bar)
4. Typography (name, role, tagline, signature line)
```

The text strings appear in `<text>` elements with a `<animate>` child that fades them in. To change "CEO, Monash DeepNeuron // Associate ML Engineer Candidate" or any other line, find the matching `<text>` block and edit the inner text. Don't change the `<animate>` siblings unless you want to retime the reveal.

If you change one variant, change the other so dark/light stay in sync.

---

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Hero animation doesn't play | You're viewing on a non-GitHub mirror, OR your browser blocks SMIL | Visit on github.com directly; SMIL works in Chrome/Safari/Firefox/Edge |
| Snake shows broken image | Workflow hasn't run yet, OR `output` branch missing | Step 3b — run the workflow once manually |
| Stats card shows "Could not fetch" | github-readme-stats rate-limited | Wait an hour; or self-deploy your own to Vercel (free) |
| Typing SVG doesn't appear | demolab.com transient outage | Cache will recover; consider self-deploying readme-typing-svg |
| Dark mode shows light hero | `<picture>` requires SVGs on same origin | Both heroes are in `/assets` on the same repo — should work. Hard refresh. |
| Light-mode photo only on Safari iOS with dark system theme | Known Safari iOS quirk with SVGs using prefers-color-scheme | Acceptable; documented limitation |

---

## What this README is doing differently

A note for future-you, or anyone forking this:

- **Custom hand-authored SVG hero** — most "cinematic" GitHub READMEs use a generic `capsule-render` wave. This one uses two purpose-built SVGs with SMIL animation, neural-network particle fields, and a staged typographic reveal. It loads in ~15kb vs ~5MB for an equivalent GIF.
- **`<picture>` for dark/light** — both hero, stats, streak, languages, and snake have native dark/light variants that swap with the GitHub theme.
- **Real metric tiles, not platitudes** — the impact numbers (83%, 42%, 289%) live in their own animated SVG that loads in a staggered cascade. Each one is keyed to a specific shipped project.
- **Recruiter-first narrative** — opens with the role positioning ("ML Engineer Candidate"), proves it with metrics, then earns the founder credibility second. Reverse-engineered from what gets résumés callbacks.
- **Self-updating, no maintenance** — snake regenerates daily, stats refresh on every view, streak counter is live. The only file you'll ever hand-edit is `README.md` itself.

---

Good luck. Ship it.
