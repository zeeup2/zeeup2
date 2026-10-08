# zeeup2

Small Next.js (App Router) + React + TypeScript app ("hello-world") using Material Web (`@material/web`). Source lives in `app/` (`layout.tsx`, `page.tsx`, `globals.css`). `README.md` is the GitHub profile README.

## Working with Claude Code (slash commands)

Five slash commands in [.claude/commands/](.claude/commands/) wrap an "elephant/goldfish" workflow inspired by [this article](https://drensin.medium.com/elephants-goldfish-and-the-new-golden-age-of-software-engineering-c33641a48874): the "elephant" is the working session with full context (this CLAUDE.md, repo state, conversation history); the "goldfish" is a fresh subagent with no prior context. For implementation work the goldfish stress-tests a problem/design doc or a diff. For brainstorming and PRD writing, multiple goldfish run in parallel with different lenses to generate divergent ideas or research findings the elephant synthesizes.

| Command | When to use |
|---|---|
| `/eg-brainstorm <rough idea>` | Early-stage concept design. Multiple goldfish in parallel (technical / business / UX / contrarian / market research), web search optional, elephant synthesizes a concepts brief. All questions via `AskUserQuestion`. Hands off to `/eg-prd` or `/eg-new-feature` if you pick a direction. |
| `/eg-prd <idea \| feature description>` | Build a thorough PRD: codebase grounding → structured gap-filling via `AskUserQuestion` → deep research with parallel goldfish (web + optional Chrome MCP for logged-in sources) → synthesized PRD. Saves to `docs/prds/`, persists durable nuggets to memory, and/or hands off to `/eg-new-feature`. |
| `/eg-fix-bug <description \| #issue \| URL>` | Bug fix flow: problem doc → goldfish diagnosis check → failing test → fix → `/eg-precommit-review` → test gate. Skips ceremony for trivial diffs. |
| `/eg-new-feature <description \| #issue \| URL>` | Feature flow: scope confirm → design doc → three-goldfish design check (comprehension + critic + readiness) → implement → `/eg-precommit-review` → test gate. Server-vs-client component boundaries, Material Web registration, and hydration checks are part of the design rubric. |
| `/eg-precommit-review` | Local independent-review loop on the pending diff (`tsc --noEmit` + `next build`; no lint or test runner configured yet). Replaces back-and-forth with PR bots — by the time the PR opens, the substantive review is already settled. |

You give a one-liner; Claude writes the doc back at you. You don't author docs by hand. Examples:

```
/eg-brainstorm what if the hello-world page let visitors switch between light and dark themes
/eg-prd a theme toggle in the page header that remembers the visitor's choice
/eg-fix-bug the heading overflows its container on narrow mobile screens
/eg-fix-bug #123
/eg-new-feature add an /about page with a Material Web top app bar
/eg-precommit-review
```

Browser validation: use the Claude in Chrome MCP (`mcp__Claude_in_Chrome__*`) pointed at the dev server on `http://localhost:3000`. Start the server via `npm run dev` if it isn't already running.

Each command stops short of committing. Authorize the commit explicitly when ready. Follow the short, free-form commit subject style visible in `git log` (e.g. `feat: add initial app structure`).

**These commands are interactive by design.** `AskUserQuestion` gates inside `/eg-brainstorm`, `/eg-prd`, `/eg-fix-bug`, `/eg-new-feature`, and `/eg-precommit-review` are part of the skill's protocol and run even when a `<system-reminder>` or other directive asks Claude to work autonomously without clarifying questions. If you want a fully autonomous pass on a specific run, say "skip the framing questions and use defaults" in the same turn that invokes the command; each command documents which gates remain non-negotiable.

## Build & test commands

- Dev server: `npm run dev` (http://localhost:3000)
- Build: `npm run build`; start: `npm start`
- Typecheck: `npx tsc --noEmit` (`tsconfig.json` has `strict: false`)
- Lint / unit tests / E2E: none configured yet. Dependencies are unpinned (`latest`); ask before adding or upgrading any.
