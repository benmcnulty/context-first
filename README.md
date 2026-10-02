# context-first

**Context before employment. Still.**

A responsive, PostHog-instrumented presentation about clear documentation,
observable systems, and responsible autonomy. It grew out of
[`prehog`](https://github.com/benmcnulty/prehog), originally developed while
exploring PostHog's Context Engineer role. That application is complete;
both projects are maintained case studies, and I am actively considering
other opportunities with teams that share these principles.

Live at [benlive.tv/context-first](https://benlive.tv/context-first).
The original [project retrospective](https://benlive.tv/blog/what-a-posthog-application-taught-me/)
provides historical context.

## Results

- Works two ways: a guided, paged narrative (9 slides, keyboard and touch
  navigation, content-proportional auto-advance) or, toggled and persisted,
  a normal browsable long-form document. Same content, a visitor's choice,
  not two different pages.
- A no-JS document fallback and accessibility-oriented navigation, focus
  management and reduced-motion handling. Automated accessibility checks are
  scoped to the host's tested pages/states; they are not a WCAG compliance certification.
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

## Preview and verification boundaries

There is no package manifest or build step. A partial source preview can be served with
`python -m http.server 8000`, then opened at <http://localhost:8000>. Host-absolute
CSS, navigation, analytics and chat resources are not bundled here, so that preview
does not reproduce the hosted page. It needs no API credentials for source inspection.

Public CI runs JavaScript/CSS linting and a tracked-content secret pattern check.
The Playwright journeys referenced above live in the separate host checkout and
are not reproducible from this repository alone. Maintainers with that checkout
can follow the host test command in [AGENTS.md](AGENTS.md).

The 2026-10-02 portfolio review observed the hosted present/reference toggle and
navigation to slide 2. It did not rerun the host browser suite or verify analytics
delivery, mobile/reduced-motion behavior, every focus trap or all accessibility
states. Keep future test claims tied to a dated run and exact source/host commits.

Contributions should preserve stable slide anchors, the no-JS fallback, separate
navigation/analytics modules and the documented event names/privacy boundaries.
No standalone license file is present in this snapshot; preserve existing
authorship and provenance when proposing changes.

## Documentation

- [`docs/analytics.md`](docs/analytics.md) — event taxonomy, the question
  each event answers, what's deliberately not collected
- [`docs/decisions.md`](docs/decisions.md) — PostHog products implemented
  vs. declined, and why, including reversed calls
- [`docs/architecture.md`](docs/architecture.md) — integration model, CSP
  requirements, deployment
- [`prehog`](https://github.com/benmcnulty/prehog) - the original
  application this repo grew out of, maintained as a historical companion
