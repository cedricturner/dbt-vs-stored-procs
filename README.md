# cedric turner · how I think about data systems

The most expensive thing a data organization does isn't building pipelines.

It's building the same pipeline twice — because nobody trusted the first one.

This is a collection of posts about why that happens, and what actually changes it. Not "here's how to use dbt." More like: here's the underlying problem that explains why these tools exist, and why teams still struggle even after adopting them.

I care about the gap between *this works* and *this gets used*. That gap is almost never a technical problem.

---

## Posts

**[The stored procedure is not your enemy](posts/stored-procedures/)**
The real problem isn't the technology. It's that your pipeline was designed to execute — not to be inspected.

**[Data teams don't have a quality problem. They have a trust problem.](posts/trust-problem/)**
Tests tell you when something broke. They don't tell you what happened. Those are different problems.

**[Python or SQL is the wrong question](posts/python-vs-sql/)**
The language doesn't matter. Where the computation runs does.

---

## Interactive explainers

If you prefer learning by doing, I also maintain a set of interactive walkthroughs on the same topics:

- [dbt vs Stored Procedures](https://cedricturner.github.io/dbt-artifacts/dbt-vs-stored-procs/) — step through a real migration
- [dbt vs Python Scripts](https://cedricturner.github.io/dbt-artifacts/dbt-vs-scripts/) — see what changes when you move computation into the warehouse
- [dbt Connection Modes](https://cedricturner.github.io/dbt-artifacts/dbt-connection-modes/) — how dbt connects to your warehouse, and what each option actually means
- [dbt + AI / Conversational Analytics](https://cedricturner.github.io/dbt-artifacts/dbt-ai-llm/) — how the Semantic Layer becomes the contract between governed metrics and AI agents

---

## About

I'm a Solutions Architect with a background in data engineering, moving toward product and technical marketing.

My interest is in how technical systems get adopted — or don't — and why the gap between "this works" and "this gets used" is usually a visibility problem, not a capability problem.

I'm drawn to the human side of technical decisions: why teams rebuild things that already exist, why they distrust platforms they helped build, and what makes a system feel legible enough to actually build on.
