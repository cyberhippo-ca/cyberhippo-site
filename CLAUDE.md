# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Source for the portfolio site at https://cyberhippo.ca. Plain HTML and CSS: no JavaScript, no frameworks, no build step, no package manager, no tests. Hosted on Cloudflare Pages; every push to `main` redeploys the live site, so never commit or push.

Preview locally with any static server, e.g. `python3 -m http.server 8000`. Note Cloudflare Pages serves `/writeups/foo` from `writeups/foo.html` (extensionless URLs); a plain local server needs the `.html` suffix.

## Hard constraints (enforced by the CSP in `_headers`)

`_headers` sets `default-src 'self'; style-src 'self'; img-src 'self'; script-src 'none'` plus other hardening headers. Consequently:
- No `<script>` tags, inline event handlers, or inline `style="..."` attributes / `<style>` blocks. All styling goes in `style.css`.
- No external fonts, images, scripts or trackers. Every asset must live in this repo.
- The only CORP exception is `/images/og-image.png` (cross-origin so link previews work).

## Page conventions

- Every page links `/style.css?v=N` (currently `v=9`). When `style.css` changes, bump `N` in **every** HTML file (index, 404, skills, writeups/*) so caches refresh.
- Internal links are root-relative and extensionless (`/writeups/slow-internet`, `/skills/`).
- External links: `target="_blank" rel="noopener noreferrer"` plus `<span class="sr-only"> (opens in a new tab)</span>` inside the link.
- Write-ups use a `<header class="intro">` with a breadcrumb `<nav class="breadcrumb">` (last item has `aria-current="page"`), an `<h1>`, and a `<p class="tagline">Write-up &middot; <time datetime="YYYY-MM-DD">…</time></p>`, then `<main class="article">`. Copy an existing write-up as a template.
- Write-up images go in `writeups/images/`; site images in `images/`.

## Adding a write-up

A new write-up touches four files (see commit f3f62df for an example):
1. `writeups/<slug>.html` — the article.
2. `writeups/index.html` — add a `<li>` at the top of `ul.posts` (newest first; `<h2>` titles).
3. `index.html` — "Recent write-ups" shows the latest three (`<h3>` titles); add the new one, drop the oldest, and update the count in "All write-ups (N)".
4. `sitemap.xml` — add `https://cyberhippo.ca/writeups/<slug>`.

Keep title, date and blurb consistent across the article, the write-ups index and the home page.

## Commits

Conventional-style prefixes: `feat:`, `fix:`, `content:`, `style:`, `docs:`, `refactor:`.

## Rules

- Never run `git commit`, `git push`, or anything that changes git history. John reviews every change with `git diff` and makes signed commits himself.
- For any audit or review task, list findings first and wait for approval before changing files.
- Don't change `_headers` without asking first. It controls the site's security headers.
