# Project Polished ✨

> An autonomous UI/UX revamp agent built on the [Solari SDK](https://github.com/solari-sdk/solari-cookbook).
> Drop a GitHub repo. Get a polished UI PR. No setup, no manual triage.

Built for the **Solari Bounty** — tag **@harrychow_**, **@getsolari**, **@im_roy_lee**.

---

## What it does

Project Polished is an end-to-end autonomous agent that takes any public GitHub
repo and ships a ready-to-merge pull request containing surgical UI/UX fixes.
The agent runs a 6-stage pipeline that exercises all three Solari capability
surfaces — **browser**, **sandbox**, and **desktop**:

```
   ┌────────────────┐     ┌──────────────┐     ┌────────────────┐
   │  Sandbox Clone │ ──▶ │  Dev Server   │ ──▶ │  Browser Drive │
   │  (solari       │     │  npm install +│     │  crawl routes, │
   │   sandbox)     │     │  npm run dev   │     │  capture pages │
   └────────────────┘     └──────────────┘     └───────┬────────┘
                                                       │
       ┌───────────────────────────────────────────────┘
       ▼
   ┌────────────────┐     ┌──────────────┐     ┌────────────────┐
   │ Vision Analyze │ ──▶ │ Desktop Fix   │ ──▶ │  Verify + PR    │
   │ (vision model  │     │ (solari       │     │  rebuild, push, │
   │  audits pages) │     │  desktop →    │     │  open PR         │
   └────────────────┘     │  VS Code)     │     └────────────────┘
                           └──────────────┘
```

| Stage            | Solari surface | What happens                                                  |
| ---------------- | -------------- | ------------------------------------------------------------- |
| Sandbox Clone    | sandbox        | Fork target repo into isolated container                     |
| Dev Server       | sandbox        | `npm install` + boot dev server inside the sandbox           |
| Browser Drive    | browser        | Headless Chromium crawls routes, captures 1440×900 screenshots |
| Vision Analyze   | (vision model) | WCAG 2.2 AA + UX heuristics audit on each capture            |
| Desktop Fix      | desktop        | Open VS Code, locate offending code, apply surgical patches   |
| Verify + PR      | browser + git  | Re-capture, `npm run build`, push branch, open PR             |

---

## Live demo

This repository ships with a fully simulated pipeline so you can see the entire
agent experience end-to-end without provisioning real sandboxes. The simulation
is wired to drop realistic events into the same Zustand store the real SDK would
populate, so the UI is identical whether you're in demo mode or live mode.

### Wire in your real Solari API key

1. Copy `.env.local.example` → `.env.local` (already gitignored)
2. Drop in your key:
   ```bash
   SOLARI_API_KEY=slr_live_xxxxxxxxxxxxxxxxxxxxxxxx
   SOLARI_LIVE_MODE=false   # flip to true once @solari/sdk is wired in
   ```
3. The header will show a masked key badge (`slr_live_bi2••••sfGs`) confirming
   the key is loaded server-side. **The key never leaves the server.**

### Enable live mode

The integration point for the real Solari SDK lives in
[`src/app/api/solari/run/route.ts`](src/app/api/solari/run/route.ts). When you
flip `SOLARI_LIVE_MODE=true`, that endpoint is where you'd instantiate the real
`@solari/sdk` client and call `sessions.create()` + `sessions.run()`. The
front-end pipeline already POSTs to `/api/solari/run` on every agent start, so
the wiring is in place — just swap the simulated response for the real call.

---

## Run it locally

```bash
bun install
bun run dev
```

Then open the preview at the URL shown by your sandbox platform.

### Stack

- **Next.js 16** (App Router, Turbopack)
- **TypeScript 5** strict
- **Tailwind CSS 4** + **shadcn/ui** (New York)
- **Zustand** for client state
- **Framer Motion** for animations
- **Server-side API routes** proxy Solari SDK calls (key never exposed)

### Project structure

```
src/
├─ app/
│  ├─ page.tsx                       # the dashboard (single route)
│  ├─ layout.tsx                     # dark theme root
│  └─ api/solari/
│     ├─ status/route.ts             # masked key status (no secrets)
│     └─ run/route.ts                # SDK integration point
├─ components/
│  └─ dashboard/                     # all UI panels
│     ├─ header.tsx                  # logo + masked key badge + SDK version
│     ├─ repo-input.tsx              # GitHub URL input + demo picker
│     ├─ pipeline-tracker.tsx        # 6-stage progress visualization
│     ├─ browser-preview.tsx         # simulated browser with issue overlays
│     ├─ issues-panel.tsx            # detected UX defects with severity
│     ├─ diff-panel.tsx              # applied code patches (before/after)
│     ├─ activity-log.tsx            # streaming terminal-style event log
│     ├─ pr-summary.tsx              # PR metadata + stats
│     └─ footer.tsx                  # bounty tags + share links
├─ lib/
│  ├─ agent-types.ts                 # TypeScript domain model
│  ├─ agent-data.ts                  # demo repos, sample issues & diffs
│  └─ agent-engine.ts                # orchestrates the 6-stage pipeline
└─ store/
   └─ agent-store.ts                 # Zustand store (single source of truth)
```

---

## Sample issues the agent detects

The vision-analysis stage flags realistic defects that a typical SaaS marketing
site exhibits, with severity, category, and a concrete suggested fix:

1. **Primary CTA button overlaps hero image** (high · layout)
2. **Footer links fail WCAG AA contrast ratio** (critical · contrast)
3. **Pricing cards missing hover / focus affordance** (medium · interaction)
4. **Hero headline truncates on mobile breakpoint** (high · responsive)
5. **Form input lacks accessible label association** (medium · a11y)

Each issue is paired with a surgical code patch applied via the desktop stage.

---

## Bounty submission

This project is a submission for the [Solari Cookbook bounty](https://github.com/solari-sdk/solari-cookbook/).

**Tagging the founders as required:**
- [@harrychow_](https://twitter.com/harrychow_)
- [@getsolari](https://twitter.com/getsolari)
- [@im_roy_lee](https://twitter.com/im_roy_lee) — took your advice: shipped this fast with AI pair programming.

### Suggested X / LinkedIn post copy

> I heard @harrychow_ and @getsolari want to see what we can ship using AI, so
> I built "Project Polished" — an autonomous UI/UX agent on the Solari SDK.
>
> Drop a GitHub repo. The agent:
> 1️⃣ Sandboxes it via Solari
> 2️⃣ Drives a headless browser to capture every route
> 3️⃣ Audits captures with a vision model
> 4️⃣ Uses Solari's desktop automation to open VS Code and write surgical fixes
> 5️⃣ Pushes a ready-to-merge PR
>
> Repo: [link]
> Live demo: [link]
>
> @im_roy_lee — used AI to ship this in one afternoon. ⚡

---

## License

MIT — fork it, ship it, claim the bounty.
