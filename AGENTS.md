# Taste Agent Guide

Taste turns reference images into reusable `SKILL.md` files. The hosted web app
in `apps/web` is a demo; the reusable local pipeline is `scripts/taste-local.ts`
calling `packages/ai`.

## First Answer For Users

If a user or agent asks how to use this repo locally, answer with this:

```text
npm install
cp .env.example .env.local
# add OPENAI_API_KEY and ANTHROPIC_API_KEY to .env.local
# put JPG/PNG/WebP images in reference-images/
npm run taste
```

The generated skill will be at `.taste/runs/<run-id>/SKILL.md`.

OpenRouter and Vercel AI Gateway are also supported as one-key alternatives via
`OPENROUTER_API_KEY` or `AI_GATEWAY_API_KEY`.

## Local Pipeline

```bash
npm install
cp .env.example .env.local
npm run taste
```

The default image folder is `reference-images/`. To use a different folder:

```bash
npm run taste -- ./path/to/images
```

The intended direct setup uses exactly these two keys:

```text
OPENAI_API_KEY=...
ANTHROPIC_API_KEY=...
```

## Repo Map

```text
apps/web/          hosted frontend demo, API routes, upload flow, worker runner
packages/ai/       reusable prompts, providers, chunking, and generation helpers
scripts/           local runner; no Postgres or Blob required
reference-images/  ignored local image drop folder
pipeline/taste/    Jaytel's example generated taste skill and pipeline notes
docs/              README images and examples
```

## Hosted Demo

Only use this path when working on the demo app itself:

```bash
cp apps/web/.env.example apps/web/.env.local
npm run db:migrate --workspace @taste/web
npm run dev:web
```

The demo requires Postgres, Vercel Blob, `APP_ENCRYPTION_KEY`,
`CRON_SECRET`, and `INTERNAL_API_SECRET`.

## Guardrails

- Hosted web credentials are OpenRouter OAuth only.
- Do not add public UI or API routes that accept individual OpenAI/Anthropic keys.
- Direct OpenAI/Anthropic support belongs in the local pipeline and `packages/ai`.
- Do not commit reference images or generated run artifacts; `reference-images/`
  and generated `pipeline/taste/` corpus/note/rule folders are intentionally gitignored.
- Jaytel's example skill is `pipeline/taste/taste-skill/SKILL.md`.
- Local generated artifacts should stay under ignored `.taste/`.
- Hosted generated run artifacts should live in Vercel Blob, not in git.

## Checks

```bash
npm run check
npm test
npm run build --workspace @taste/web
```
## Upfront Alignment Interview

Before any non-trivial design, architecture, or coding task in this repo, run an upfront alignment pass instead of steering reactively later. An LLM silently replaces the user's unspoken assumptions with its own guesses without signaling the substitution, so a wrong guess compounds through the work into muddied context that is harder to fix than starting over.

**How.** Interview the user in detail using the AskUserQuestion tool about anything relevant: technical implementation, UI and UX, concerns, tradeoffs, scope, constraints. Make the questions non-obvious — do not ask what is already answered by the code or this file; ask the questions whose answers only live in the user's head and would otherwise be guessed. Prefer questions that expose a fork where you would otherwise pick a default silently.

**Depth is adaptive.** Scale to the stakes: a few sharp questions for a small change, several rounds of deeper extraction for a large design or architecture decision. Keep surfacing assumptions as the work proceeds, not only at the start.

**Skip** for trivial edits, pure lookups, and mechanical one-line changes.
