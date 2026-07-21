# AI News Daily — Changelog

## [Unreleased] — 2026-07-21

### Added
- **2026-07-21 daily digest** (zh-tw + en) — OpenAI internal model solved Erdős conjecture but escaped sandbox; White House 30-day frontier review; Google Frozen v2 chip (6-10× TPU); Kimi K3 suspends new subscriptions; Meta Muse Spark 1.1 tops agent benchmarks; Shield AI $1.5B raise; Anduril × Archer; AIsphere + Alibaba $439M
- **2026-07-20 daily digest** (zh-tw + en) — EU DMA forces Google to open Android; Gemini 3.5 Pro third delay; Kimi K3; Oracle layoffs; Project Perception; SAP/Prior Labs; OpenAI hardware; open-source weights week
- **Archives pages** (zh-tw + en) — `/zh-tw/archives/`, `/en/archives/` using PaperMod's archives layout
- **English About page** — `/en/about/`
- **Posts section list pages** — `/zh-tw/posts/`, `/en/posts/` (require `_index.md` per section)
- **layouts/_partials/header.html override** — replaces PaperMod's `site.GetPage .KeyName` with `false` (fixes "ambiguous" error on multilingual `about` page)
- **layouts/_partials/templates/schema_json.html override** — replaces PaperMod's `site.GetPage "posts/"` with try-paths fallback across `/zh-tw/`, `/en/`, `/`

### Changed
- **hugo.yaml — restructured for zh-tw as default language**
  - `defaultContentLanguage: zh-tw` (was `en`)
  - Menu URLs updated to `/zh-tw/archives/`, `/zh-tw/about/`, `/en/archives/`, `/en/about/`
  - Identifiers renamed to unique values to avoid PaperMod GetPage ambiguity
- **content/zh-tw/_index.md and content/en/_index.md** — internal links point to `/zh-tw/posts/`, `/zh-tw/archives/`, `/en/posts/`, `/en/archives/` (matching actual permalinks, not legacy `/posts/` and `/archives/`)
- **content/{zh-tw,en}/{about,archives}.md** — removed hardcoded `url:` front matter (let Hugo auto-resolve per-language paths)

### Removed
- `content/en/info.md`, `content/en/posts/2026-07-20-demo.md`
- `content/zh-tw/info.md`, `content/zh-tw/posts/2026-07-20-demo.md`

### Known issues / Notes
- **Repo is public** (was intended private but GitHub free plan doesn't allow private + Pages; kept public since content is non-sensitive news aggregation)
- **`defaultContentLanguageInSubdir=false` does NOT strip language prefix from page URLs in Hugo v0.84+**. The root `/` shows the default language's homepage content but with site title (not zh-tw page title). All navigable pages use `/zh-tw/...` or `/en/...` paths.
- **Hugo v0.158+ deprecation warnings**: `languageName`, `LanguageDirection`, `LanguageCode`, `LanguageName` — cosmetic, will break in a future Hugo release. TODO.
- **GitHub Pages CDN cache** — `cache-control: max-age=600` may show stale content for up to 10 min after deploy. Use `?v=<timestamp>` query to bust.

## Deploy log

| Time (UTC) | Commit | Status | Note |
|---|---|---|---|
| 2026-07-21 03:51 | 3692f36 | ❌ failure | Initial deploy attempt |
| 2026-07-21 03:58 | 380a040 | ✅ success | Fixed Pages OIDC permissions |
| 2026-07-21 05:02 | 845ac6b | ✅ success | Restructured to zh-tw as default |
| 2026-07-21 05:09 | 7b6a729 | ✅ success | Override header.html + bilingual archives + English content |
| 2026-07-21 05:17 | 3a5a795 | ✅ success | Add 2026-07-21 daily digest |
| 2026-07-21 05:23 | f4aa81b | ✅ success | Override schema_json.html + add posts _index.md |
