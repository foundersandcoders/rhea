# Rhea Post-MVP Hardening: Roadmap Overview

**28 tasks across 5 milestones.** Files: `.claude/roadmaps.json` (machine-readable), `docs/roadmaps/POST_MVP_HARDENING.md` (full task list with Mermaid dependency diagram).

> This phase is a migration of Rhea's legacy hand-maintained roadmap (`docs/dev/roadmap/*.md`, tracked per-submodule since October 2025) into the tracked format. Open MVP milestones and outstanding tasks from Rhea, Metis, Themis and Theia were carried forward; completed work stays in the legacy files as historical record and isn't duplicated here.

---

## What we're building

Rhea's two core workflows, Metis (standalone module generation) and Themis (multi-module course building), reached functional MVP in October 2025. A system-wide consistency audit in December 2025 confirmed the architecture holds together (shared prompt factories, centralised validation, aligned schemas) but flagged one real duplication issue and recommended follow-up documentation and tests. This phase works through that audit's remediation alongside a larger structural change already in motion: replacing Metis's XML-upload requirement with a guided form workflow that mirrors Themis's UX, so new users no longer need to hand-author XML to generate a module.

Themis itself carries technical debt from its rapid October 2025 build-out: an SSE parsing vulnerability that can crash generation mid-course, a stream-cleanup race condition, and a sequential generation flow that gets slow on large courses. None of these block current use, but they're real correctness and performance risks worth clearing before Themis sees heavier use. Theia (export/preview) is close to feature-complete; its main gap is the module-XML upload-and-resume path that would let users pick a previously generated module back up mid-workflow.

Four submodules (Tethys, Thalassa, Mnemosyne, Eos) exist only as stubs with no defined requirements. Rather than omit them, they're tracked as an explicit deferred backlog milestone so their existence stays visible on the roadmap without competing for attention against the four modules with active work.

## Milestone sequence and the reasoning behind it

**M1: Rhea Platform Polish** goes first because 1RH.1 (workflow card refactor, supporting multiple subflows per workflow) is a hard dependency for the Metis Generator work in M2 — the homepage needs to expose "Generator" and "Updater" as distinct entry points before the Generator route can exist. The rest of M1 is the audit remediation (validation dedup, schema cross-refs, regression tests) plus two standalone polish items (ARIA, Svelte 5 migration) that have no dependents and can interleave freely.

**M2: Metis Form Workflow** is the largest structural change in this phase. It splits Metis into a form-based Generator (`/metis/generate`) for new users and a modernised Updater (`/metis/update`) for XML-based editing, reusing the existing generation pipeline unchanged. 2ME.1 depends on 1RH.1; 2ME.2 (Updater modernisation) deliberately depends on 2ME.1 rather than running in parallel, since the Updater work is expected to reuse UI patterns the Generator establishes first. The remaining M2 tasks (boilerplate text, tech-stack validation, subsection toggling, granularity selection, session persistence) are independent smaller features that round out Metis parity with Themis.

**M3: Themis Technical Debt** is entirely independent of M1/M2 and can run in parallel. It's grouped separately because it's risk-reduction work on an already-shipped feature, not new capability — worth tracking distinctly so it doesn't get deprioritised behind visible feature work.

**M4: Theia Upload & Export** closes out Theia's one substantial remaining gap (module XML upload/resume; course JSON upload already works) plus two smaller export enhancements (PDF, analytics).

**M5: Dormant Modules Backlog** holds Tethys, Thalassa, Mnemosyne and Eos as `deferred` placeholder tasks. None have defined requirements yet; each needs a scoping conversation with the user before any real roadmap work can be written for it.

## Decisions that shaped the structure

- **Legacy docs weren't deleted or restructured.** `docs/dev/roadmap/*.md` remains as the detailed historical record (commit references, PR numbers, line counts, implementation notes) for everything already completed. This roadmap only carries forward what's still open.
- **"Beyond MVP: Future Features" sections were excluded.** Each submodule's legacy file has a speculative future-features list (PDF templates, collaborative editing, version history, etc.). Those aren't included as tasks here; they're genuinely later-phase material, not part of the current hardening push.
- **2ME.2 depends on 2ME.1 rather than running independently**, even though the legacy doc only lists it as a soft "Dependencies: 2.5 (Generator workflow establishes patterns)". Made it a hard dependency since the Updater refactor explicitly reuses UI patterns from the Generator per the legacy plan.
- **Dormant modules got a deferred milestone rather than being omitted.** Silently dropping them would understate the project's actual scope; marking them deferred keeps them visible without implying they're ready for work.

## External blockers (flag early)

None currently. All M5 backlog items are blocked on a requirements conversation with the user rather than any external party, and aren't wired as roadmap gates since that conversation hasn't happened yet.
