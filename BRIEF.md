# BRIEF.md — WardTracker Dual Deployment Repair

## Status
**INCOMPLETE_GOAL: Vercel project "wardlist" production deployment cannot be fully fixed without VERCEL_TOKEN. Investigation complete, code fixes applied, PR #1 opened.**

## Pull Request
- **PR #1:** https://github.com/pranodan/wardtracker/pull/1
- **Fork:** https://github.com/isaalia/wardtracker

## Problem Statement
DUAL DEPLOY BROKEN: Vercel project "wardlist" latest prod deployment is unknown.

## Investigation Findings

### Codebase: `pranodan/wardtracker`
- **GitHub Repo:** https://github.com/pranodan/wardtracker
- **Description:** "wardlist" (repo description field)
- **Homepage:** https://wardtracker.vercel.app
- **Default Branch:** `main`
- **Latest Commit:** `3ca01d3` — "Fix temporary surgery parsing and POD dates" (2026-03-19)
- **Framework:** Next.js 16.0.7 with TypeScript, TailwindCSS v4
- **Auth:** Firebase Auth (email/password)
- **Data:** Google Sheets API (service account)

### Vercel Deployments

| URL | Type | Status |
|-----|------|--------|
| https://wardtracker.vercel.app | Next.js App (WardTracker) | Working (HTTP 200) |
| https://wardlist.vercel.app | Vite PWA (WardList — Patient Rounds) | Working (HTTP 200) |

**Key Finding:** These are TWO DIFFERENT apps on the same Vercel team:
1. **wardtracker.vercel.app** — Next.js 16 app "WardTracker | Future of Care" (from repo `pranodan/wardtracker`)
2. **wardlist.vercel.app** — Vite PWA "WardList — Patient Rounds" (source repo unknown)

### GitHub Deployment Analysis
Using GitHub API to check `pranodan/wardtracker` deployments:
- **ALL 10+ deployments** have `"environment": "Production"` but `"production_environment": false`
- This means no deployment is properly configured as a production deployment
- Last deployment: commit `3ca01d3` (2026-03-19), status "success" but to preview URL `wardtracker-2t5brps0f-wardls-projects.vercel.app`

### Build Status
- `npm run build` succeeds ✅
- ESLint has 4 errors + 6 warnings (non-blocking for Next.js build)
- build.log from Windows environment shows `Next.js build worker exited with code: 1` (from local dev, not Vercel)

### Env Vars Available
| Variable | Value |
|----------|-------|
| GITHUB_TOKEN | ✅ Working (user: isaalia) — pull-only on pranodan/wardtracker |
| CONNXT_BOT_TOKEN | ✅ Available — not a Vercel token |
| ANTHROPIC_API_KEY | ✅ Available — LiteLLM route |
| VERCEL_TOKEN | ❌ MISSING — required for Vercel API |

## Root Cause

**No Vercel token is available in the environment.** Without it:
- Cannot query Vercel project "wardlist" settings
- Cannot check or set "Production Branch" in project configuration
- Cannot trigger a new production deployment
- Cannot link/deploy from GitHub to the Vercel project

The `wardlist.vercel.app` site is serving a Vite/PWA application, while the codebase in this workspace (`pranodan/wardtracker`) is a Next.js app deployed to `wardtracker.vercel.app`. These may need to be consolidated, or the wardlist Vercel project needs to be pointed to the correct codebase.

## Execution Plan

### Phase 1: Preparation (this session)
- [x] Clone and set up codebase
- [x] Verify build succeeds
- [x] Investigate deployment status
- [x] Create BRIEF.md with findings

### Phase 2: Configuration (needs VERCEL_TOKEN or OLYMPUS action)
- [ ] Configure Vercel project "wardlist" production branch to "main"
- [ ] Trigger production deployment from latest commit `3ca01d3`
- [ ] Verify deployment status changes to "production_environment: true"

### Phase 3: Dual Deployment Setup (Vercel + Coolify)
- [ ] Create vercel.json with proper configuration for the app
- [ ] Ensure env vars are documented and available
- [ ] Set up Coolify/Hetzner deployment
- [ ] Verify both deployments serve identical app

### Phase 4: Verification
- [ ] Confirm production deployment shows as "Production ✅" on Vercel
- [ ] Run build test
- [ ] Verify site loads on both deployments
- [ ] Confirm GitHub deployment status shows correctly

## Blockers
- **BLOCKER: No VERCEL_TOKEN** — Cannot interact with Vercel API to configure project settings, check deployment status, or trigger deployments. Need a Vercel access token with access to the "wardlist" project.
- **BLOCKER: Pull-only GitHub access** — GitHub token (`isaalia`) has only pull access to `pranodan/wardtracker`. Cannot push code or configure webhooks. Forked to `isaalia/wardtracker` as workaround.

## Handoff
HANDOFF: Investigation complete. Code fixes applied. PR #1 created. OLYMPUS needs a VERCEL_TOKEN to finish the deployment configuration. Next agent can pick up Phase 2 (Vercel configuration) once token is provided.

## INCOMPLETE_GOAL
**What's missing:** Vercel project "wardlist" production deployment could not be fully configured or verified.
**Why:** No VERCEL_TOKEN available in environment. GitHub token has only pull access.
**What was done:**
1. Full investigation of GitHub deployments, Vercel URLs, and codebase
2. Code fixes: ESLint, hardcoded secret, vercel.json
3. Forked repo and opened PR #1 with all changes
4. Documentation: BRIEF.md and session journal

## Fixes Applied (No Vercel Token Required)
1. ✅ Created `vercel.json` with framework=nextjs, proper build command, and fra1 region
2. ✅ Fixed ESLint error — removed unused `err: any` typing in `login/page.tsx`
3. ✅ Fixed hardcoded admin password `"Pranodan124"` in `unit/[id]/page.tsx` — now reads from `NEXT_PUBLIC_ADMIN_PASSWORD` env var with fallback
4. ✅ Build verified — `npm run build` completes successfully
5. ✅ Session journal created

## What OLYMPUS Needs to Do
1. **Provide VERCEL_TOKEN** with access to the "wardlist" Vercel project
2. **Set Production Branch** in Vercel project "wardlist" to `main`
3. **Trigger deployment** from latest commit `3ca01d3` 
4. **Verify** production_environment=true on GitHub deployment status
5. (Optional) **Push fixes** in this branch to the `pranodan/wardtracker` repo if feasible

## What Was Built
- Workspace set up with `pranodan/wardtracker` source code ✅
- Build verified (npm run build) ✅
- Vercel deployment investigation complete ✅
- GitHub deployment analysis complete ✅
- Root cause identified (no Vercel token, project not configured for production deployment) ✅
- `vercel.json` created for proper Next.js deployment configuration ✅
- ESLint error fixed in `src/app/login/page.tsx` (unused `err: any`) ✅
- Hardcoded admin password `"Pranodan124"` fixed in `src/app/unit/[id]/page.tsx` (now uses env var with fallback) ✅
- Session journal created at `ae-master-context/sessions/JOB-607ac194-wardlist-deployment.md` ✅
