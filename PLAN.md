# AI News Daily — Plan & Architecture

## Goal
每日自動抓 AI 新聞、寫雙語摘要、commit push 到 GitHub、GitHub Actions build + deploy 到 GitHub Pages。

## Status: Phase 1 done, Phase 2 not started

### ✅ Phase 1 — 靜態網站上線 (done 2026-07-21)
- Hugo site with PaperMod theme, bilingual (zh-tw + en)
- GitHub repo: https://github.com/DrewLeetw/ai-news-daily (public)
- GitHub Pages deployed at: https://drewleetw.github.io/ai-news-daily/
- 2 manual posts: 2026-07-20, 2026-07-21
- GitHub Actions workflow auto-deploys on push to main (uses OIDC, no PAT needed)

### ⏳ Phase 2 — 自動排程 (next)
- Hermes cron job: schedule "0 10 * * *" (daily 10:00 Asia/Taipei)
- Cron prompt must be self-contained (no shared context with current session)
- Workflow:
  1. cd ~/Projects/ai-news-daily (or git clone if missing)
  2. Use web_search + web_extract to fetch latest AI news from buildfastwithai.com (or similar)
  3. Generate zh-tw and en markdown files in `content/{zh-tw,en}/posts/<YYYY-MM-DD>.md`
  4. git add + commit + push
  5. GitHub Actions auto-rebuilds + deploys

### ⏳ Phase 3 — Skill 化 (deferred)
- Save workflow as skill: `ai-news-daily-pipeline`
- Reference: paper Hugo post format, web sources list, cron prompt template

## Architecture

```
┌─────────────────┐
│  Hermes cron    │ schedule "0 10 * * *"
│  (LaunchAgent)  │ Asia/Taipei
└────────┬────────┘
         │ runs prompt
         ▼
┌─────────────────────────────────────────┐
│  Cron prompt (self-contained)           │
│  1. cd ~/Projects/ai-news-daily         │
│  2. web_search → buildfastwithai.com    │
│  3. web_extract → 16 stories            │
│  4. generate 2 .md files (zh-tw, en)    │
│  5. git add + commit + push             │
└────────┬────────────────────────────────┘
         │ git push
         ▼
┌─────────────────────────────────────────┐
│  GitHub Actions (hugo.yaml)             │
│  - checkout                            │
│  - hugo --minify                       │
│  - upload artifact                     │
│  - deploy-pages                        │
└────────┬────────────────────────────────┘
         │
         ▼
   https://drewleetw.github.io/ai-news-daily/
```

## Key URLs
- Site root: https://drewleetw.github.io/ai-news-daily/
- 中文首頁: https://drewleetw.github.io/ai-news-daily/zh-tw/
- 英文首頁: https://drewleetw.github.io/ai-news-daily/en/
- 中文最新文章: https://drewleetw.github.io/ai-news-daily/zh-tw/posts/2026-07-21/
- Repo: https://github.com/DrewLeetw/ai-news-daily
- Hugo version: v0.164.0 (local + Actions)
- Theme: PaperMod v4.x

## Critical fixes during Phase 1
1. **PaperMod header.html site.GetPage KeyName → ambiguous** in multilingual mode
   - Fix: Override `layouts/_partials/header.html`, set `$is_search := false`
2. **PaperMod schema_json.html site.GetPage "posts/" → ambiguous** when posts section exists in multiple languages
   - Fix: Override `layouts/_partials/templates/schema_json.html`, use try-paths fallback
3. **Posts section list pages don't exist without _index.md**
   - Fix: Add `content/zh-tw/posts/_index.md` and `content/en/posts/_index.md`
4. **defaultContentLanguageInSubdir=false does NOT auto-strip language prefix from page URLs in Hugo v0.84+**
   - Decision: accept it, menu + internal links use /zh-tw/ and /en/ prefixes
5. **GitHub free plan: private repo + Pages not allowed**
   - Decision: stay public, content is non-sensitive

## Web sources for cron (Phase 2)
- **Primary**: https://www.buildfastwithai.com/blogs/ai-news-today-<month>-<day>-<year> (always publishes same-day summary)
- **Backup**: web_search "AI news today <date>" to find other aggregators
- **Manual fallback**: Hacker News AI tag, arXiv cs.AI new submissions

## Cron prompt template (to be written in Phase 2)
```
You are the AI News Daily pipeline. Today is <DATE>.

1. Fetch the latest AI news roundup from:
   https://www.buildfastwithai.com/blogs/ai-news-today-<URL_SLUG>
2. Extract the top 8 stories with source links
3. Write 2 Hugo posts following the format in existing content/zh-tw/posts/2026-07-21.md:
   - content/zh-tw/posts/<DATE>.md (繁體中文)
   - content/en/posts/<DATE>.md (English translation)
4. cd ~/Projects/ai-news-daily && git add + commit + push
5. Report success to user via Telegram (chat_id: 6038945346)
```

## Next session checklist
- [ ] Verify https://drewleetw.github.io/ai-news-daily/ shows 2026-07-21 post on homepage
- [ ] Test cron prompt locally with current date, verify it produces valid Hugo posts
- [ ] Write cron jobs.json entry
- [ ] Trigger cron once for dry-run validation
- [ ] Save skill `ai-news-daily-pipeline` for future reference
