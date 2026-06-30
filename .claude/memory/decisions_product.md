# Product & Architecture Decisions

The *standing* product and architecture decisions for this project — the current posture a new agent (or teammate) needs to know before touching anything. Distilled and current, not chronological.

This is distinct from `PLAN.md`'s Decisions Log, which is the dated, session-by-session "X over Y because Z" trail. When a decision in that log becomes a durable part of how the system works, lift the standing version up here so it's findable without scrolling the whole history.

<!-- One entry per standing decision. Format:
- **<short title>** — <the decision and its current rationale>.

Example:
- **No ORM** — raw SQL plus thin query helpers; the ORM's abstraction cost wasn't earning its keep for our query shapes.
- **Payments fail closed** — if the payment check can't complete, deny the request rather than letting it through.
-->
