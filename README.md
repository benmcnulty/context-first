# context-first

**Context before employment. Still.**

A responsive, PostHog-instrumented presentation, generalized from
[`prehog`](https://github.com/benmcnulty/prehog), my original application to
the **Context Engineer** role on PostHog's **Wizard & Docs** team. I didn't
get the role. What I took from the process, reading PostHog's own public
handbook and *No Rules Rules* along the way, was worth keeping: a genuine
appreciation for a fully-remote, context-over-control way of working that
I'm now using as a standard for my own job search, not just for one
company. `prehog` stays exactly as it was, in case a future PostHog-specific
opportunity makes it relevant again; this repo reuses the same working
implementation with the narrative reframed. Live at
[benlive.tv/context-first](https://benlive.tv/context-first); the full story
of why this exists is at
[benlive.tv/blog/what-a-posthog-application-taught-me](https://benlive.tv/blog/what-a-posthog-application-taught-me/).

## Results

- Works two ways: a guided, paged narrative (9 slides, keyboard and touch
  navigation, content-proportional auto-advance) or, toggled and persisted,
  a normal browsable long-form document. Same content, a visitor's choice,
  not two different pages.
- Full no-JS fallback (every section readable without JavaScript), zero
  `wcag2a`/`wcag2aa` violations in either view mode.
- The same production PostHog JS SDK implementation `prehog` shipped first:
  Product Analytics, masked Session Replay, exception tracking, a real
  custom-rendered Survey, and one flag-gated feature, all routed through a
  PostHog-managed reverse proxy (`t.benlive.tv`) for ad-blocker resilience.
- A self-referential live event log on the page itself, showing exactly what
  this session has had accepted for delivery to PostHog, in real time
  (queued events are logged when accepted, not only once actually
  delivered; see `docs/analytics.md`).
- Deterministic Playwright coverage in the host repo
  (`tests/context-first.spec.js`), including the guarantee that
  `contextfirst_slide_viewed` never double-fires on a revisit, across both
  view modes.
- Public repo, truthful commit history, decisions documented, including
  reversed calls, rather than silently edited.

## Reviewing this implementation

This repo is meant to be read, not run. The page depends on the host site
for shared tokens, nav behavior, the consent UI, and the PostHog layer
itself (see [`docs/architecture.md`](docs/architecture.md) for exactly what
and why), so cloning it standalone won't reproduce the live experience.
The fastest path to understanding what's actually built:

1. **Start with the live page** — [benlive.tv/context-first](https://benlive.tv/context-first) — then open this repo alongside it.
2. **`index.html`** — all nine slide sections in document order. Read the HTML comments; they explain non-obvious CSS and layout decisions inline.
3. **`context-first.js`** — the controller: paging, transitions, keyboard and swipe handling, hash routing, auto-advance, focus management, the present/reference view-mode toggle and its scrollspy, and the shared modal (focus-trap) behavior every panel on the page uses. It knows nothing about PostHog.
4. **`analytics.js`** — the domain adapter onto the host site's shared analytics layer (`/js/analytics/*`, which owns PostHog init, consent gating, and delivery). It maps this page's own events onto that layer, and renders the Survey and the live-event-log panel. It knows nothing about slide mechanics.
5. **`docs/decisions.md`** — which PostHog products got implemented, which got declined, and why. Mostly inherited from `prehog`'s own history; see the provenance note at the top.
6. **`docs/analytics.md`** — the event taxonomy: what each event answers, what's deliberately not collected, and the privacy line around AI chat content.
7. **`docs/architecture.md`** — the integration model and the exact CSP requirements the host site carries for this page.
8. **`AGENTS.md`** — the same project context, restructured as a set of invariants, aimed at a coding agent picking up this repo cold.

## How it's organized

```
index.html       All nine slide sections in document order; links benlive.tv's
                 shared tokens/nav CSS in cascade order (no-JS stays readable)
context-first.css Local presentation styles built on host tokens
context-first.js  Controller: paging, transitions, keyboard, swipe, hash
                 routing, auto-advance, focus management, the present/
                 reference view-mode toggle and its scrollspy, and the
                 shared modal (focus-trap) behavior all three of the page's
                 panels use. Knows nothing about PostHog.
analytics.js     Domain adapter onto benlive.tv's shared analytics layer
                 (/js/analytics/*, which owns PostHog init, consent
                 gating, and delivery). Maps this page's own events onto
                 it, renders the Survey and the live-event-log panel.
                 Knows nothing about slide mechanics.
docs/            architecture.md, analytics.md, decisions.md
AGENTS.md        Same project context, structured for a coding agent
```

## Documentation

- [`docs/analytics.md`](docs/analytics.md) — event taxonomy, the question
  each event answers, what's deliberately not collected
- [`docs/decisions.md`](docs/decisions.md) — PostHog products implemented
  vs. declined, and why, including reversed calls
- [`docs/architecture.md`](docs/architecture.md) — integration model, CSP
  requirements, deployment
- [`prehog`](https://github.com/benmcnulty/prehog) — the original
  application this repo grew out of, kept exactly as it was
