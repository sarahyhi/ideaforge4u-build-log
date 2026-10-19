# IdeaForge — Build Log

A running record of how IdeaForge was built: what changed, when, and why.
Git commits record *what* changed in the code; this log records the decisions behind it.

- **Live app:** https://ideaforge4u.lovable.app
- **Built with:** Claude (ideation, spec), Miro + Miro Intelligence (structuring), Lovable (code generation, hosting)
- **Times:** Amsterdam local time (CEST)

---

## Phase 0 — Spec (before 2026-04-15)

- **Idea & positioning** — Defined the product as "the step before Miro": a tool to capture and develop raw ideas before bringing them into a collaborative workspace.
- **Spec written** — Used Claude and Miro Intelligence to turn the idea into a one-page *Problem Definition & MVP Scope*: problem, target user, why current tools fail, a three-mode flow (Capture / Expand / Structure), MVP scope, and success metric.
- **Success metric** — A user captures an idea, answers AI questions, and downloads a shareable structured document in one 20-minute session.

## Phase 1 — Hackathon build (2026-04-15, Miro × Lovable hackathon)

| Time | Change | Notes |
|---|---|---|
| 16:55 | Shared the Miro board link with Lovable | The Miro connector could not access the board. |
| 16:57 | Pasted the spec text directly into Lovable | This pasted spec became the source of truth for everything generated afterwards. |
| 16:58 | Answered Lovable's design questions | Chose Warm Sand palette, Space Grotesk + DM Sans, single-column layout, product name *IdeaForge*. |
| 17:02 | **v0.1** — First version generated | Landing page, three modes, idea history. AI was simulated: Expand cycled through 7 scripted questions; Structure filled a fixed template. |
| 18:35 | Enabled Lovable Cloud | Backend required before real AI could be added. |
| 18:40 | **v0.2** — Real AI connected | Two server functions call the Lovable AI Gateway (model: `google/gemini-3-flash-preview`, Lovable's default — the spec had named GPT-4). Expand asks one challenging question per turn; Structure generates a fixed-section Markdown document. |

## Phase 2 — Agent integrations (2026-07-17)

| Time | Change | Notes |
|---|---|---|
| 11:52 | Created the `idea-challenger` skill | An instruction set for Lovable's build agent. It is not used by the app at runtime. |
| 12:00 | Requested MCP (agent integrations) | First chose OAuth protection. |
| 12:02 | **Decision:** switched MCP to public | Lovable noted the tools are stateless and touch no user data, so OAuth would add a full sign-in flow for little benefit. Trade-off: anyone with the endpoint can call the tools and consume AI credits. |
| 12:04 | MCP server live | Exposes `challenge_idea` and `structure_idea` to external agents (e.g. Claude, Cursor). |

## Phase 3 — UX refinements (2026-07-18 → 07-22)

| Date / time | Change | Notes |
|---|---|---|
| 07-18 16:23 | Added "Save & new idea" button | — |
| 07-18 16:26 | Changed it to "Save" only | Saving should not start a new idea. Currently a visual confirmation; ideas are not yet persisted. |
| 07-18 19:21 | Moved mode selector below the content, centered | — |
| 07-22 02:16 | CTA copy kept on a single line | — |

## Phase 4 — Analytics (2026-08-04 → 08-05)

| Date / time | Change | Notes |
|---|---|---|
| 08-04 13:59 | **Decision:** Google Analytics over Adobe Analytics | GA has a Lovable connector; Adobe does not. |
| 08-04 14:10 | GA4 connected | Page views tracked on every route change. |
| 08-04 14:15 | Added visitor ID, session ID, new/returning user type | Enables segmentation by user type. |
| 08-04 14:23 | Added funnel events | CTA clicks (nav / hero / footer), save, mode change, Expand session start and messages, document generation and success, download. |
| 08-05 14:21 | Fixed: GA received no data | The tag loaded but sent no hits, due to a formatting bug in how events were passed to Google's script. Fixed and verified. |
| 08-05 17:23 | Security update | Upgraded framework packages to resolve critical and high-severity dependency vulnerabilities. |

## Phase 5 — Documentation (2026-08-12)

| Date / time | Change | Notes |
|---|---|---|
| 08-12 17:26 | Added README | — |
| 08-12 17:29 | README formatting fix | — |

## Phase 6 — Build log & public sync (2026-10-19)

| Time | Change | Notes |
|---|---|---|
| 21:00 | Architecture review | Reverse-engineered the build from the code and the full Lovable build history, to understand how each part works and where to optimize. Found that the Miro → Lovable integration never connected, and that the first version's AI was simulated. |
| 21:15 | **Decision:** keep a timeline-style build log, in English | Short entries in the style of per-push records, written to be shared publicly. |
| 21:33 | Added this build log to the private repo (`docs/BUILD_LOG.md`) | Commits record *what* changed; this log records *why*. |
| 21:44 | Created the public repo `ideaforge4u-build-log` | **Decision:** only the README and build log are public; the code stays private. The public version leaves out prompt text and endpoint URLs. |
| 21:46 | **Decision:** publish through a pull request, not directly | Every update opens (or updates) a PR in the public repo; nothing goes public until it is reviewed and merged. |
| 21:58 | Set up the access token for the sync | Fine-grained token, limited to the public repo (contents and pull requests only). |
| 22:02 | Tried GitHub Copilot to add the sync workflow | It could not write to the repo from its session, so the workflow was added with Claude instead. |
| 22:13 | Sync workflow added via PR in the private repo; merged 22:15 | A GitHub Action copies `README.md` and `docs/BUILD_LOG.md` to the public repo whenever either changes. |
| 22:17 | Fixed: first sync run failed | The token had been saved as a secret in the public repo instead of the private one. Moved it; the next run succeeded. |
| 22:22 | First public PR merged | README and build log published. |
| 22:25 | README now links to the build log | The link points to the public file, so it works from both repos. Confirmed that a second change updates the open PR instead of opening a new one. |
| 22:36 | Logged today's work in this build log | — |

---

## Current state

- **Capture** — Free writing, no AI.
- **Expand** — AI asks one focused question per turn, drawing on the captured idea and the full conversation so far.
- **Structure** — AI writes a Markdown document: Problem Statement, Target User, Solution Approach, 5 Key Ideas, Research Notes, Next Steps. Downloadable as `.md`.
- **Analytics** — GA4 with funnel events.
- **Agent access** — Public MCP endpoint with two tools.
- **Build log** — Kept in the private repo; README and build log sync to a public repo through a reviewed pull request.

## Known limitations

- Ideas live in browser memory only; a page refresh clears them. The backend is enabled but not yet used for storage.
- Research Notes come from the model's general knowledge, not live web research, and should be treated as hypotheses to verify.
- The spec's market research and visual references features were not built.
- Expand has no explicit coverage tracking or stopping point; the model decides which areas to probe and when.

## Candidate next steps (not yet decided)

- Persist ideas and conversations in the database so Save works for real.
- Ground Research Notes with web search, or relabel them as assumptions.
- Add coverage tracking and a closing step to Expand.
- Keep app and MCP prompts in one shared source.
- Restrict or retire the public MCP endpoint.
- Compare models on question quality.

---

## How to add an entry

Append one row to the current phase (or start a new phase):

| Date / time | Change | Notes |
|---|---|---|
| YYYY-MM-DD HH:MM | What changed, in one line | Why — the decision or trade-off |
