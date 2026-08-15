# RepoPulse — Predictive Architecture Intelligence

Paste a public GitHub repo. RepoPulse builds an interactive graph of the codebase — every file is a node, every import is a connection — then goes one step further: it mines commit history for files that consistently change together even when they have no import relationship at all. That hidden coupling is invisible to tools that only read the code as it exists today (Madge, dependency-cruiser, GitHub's own dependency graph), but it's exactly what causes unexpected breakage when someone edits one file without realizing another depends on it in practice.

## How it's different
Existing dependency graph tools show you what's *technically* connected. RepoPulse also shows you what's *behaviorally* connected — pairs of files that developers have repeatedly had to change together, based on real git history, regardless of whether the code says they're related.

## How it works
1. Fetches the repo's file tree via the GitHub API and selects up to 30 core source files (prioritizing `src/`/`lib/` over tests and examples).
2. Fetches each file's raw content and extracts relative import/require statements with regex, resolving them to build the import graph.
3. Fetches the last 25 commits and, for each one, the list of files it touched.
4. Counts how often each pair of files was touched in the same commit — this co-occurrence analysis is a lightweight, unsupervised pattern-mining technique.
5. Flags pairs that co-changed repeatedly but have no direct import edge as "hidden coupling," and renders them as dashed connections distinct from solid import edges.
6. Optionally sends the hidden-coupling findings to Claude to generate a plain-English architecture risk summary; falls back to a templated summary if no API key is provided.

## Tech
- HTML, CSS, JavaScript — no build step
- D3.js for the force-directed graph
- GitHub REST API (repo info, tree, commits) — works unauthenticated within rate limits; optional token field for higher limits on large repos
- Anthropic Claude API (optional) for the AI-generated risk narrative

## Run it
Open `index.html` in a browser, or enable GitHub Pages on this repo. No backend needed — everything runs client-side against the GitHub API directly.

## Try it on
Small-to-medium, actively maintained repos with frequent small commits work best for surfacing hidden coupling — a repo where bug fixes tend to touch a couple of files together is exactly the pattern this tool is built to catch. Very large monorepos will only show a partial graph (capped at 30 files, 25 commits) to stay within public API rate limits.

## What's next
- Let users pick which 30 files to analyze instead of auto-selecting
- Scan a larger commit window using GitHub's GraphQL API to reduce request count
- Weight hidden coupling by recency, not just raw frequency
- Support monorepo-aware import resolution (workspaces, path aliases)

## Built for
ML Empowerment Build Challenge 2.0 — Best Use of Machine Learning / Data-Driven Insights tracks.
