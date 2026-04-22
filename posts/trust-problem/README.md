# Data teams don't have a quality problem. They have a trust problem.

Here's a pattern that shows up in nearly every data organization, usually unannounced: there are five versions of the same metric.

Not because the first four were wrong. Because nobody could tell whether they were right.

The business analyst has one. The data team has one. There's one in the legacy BI tool that nobody updated. Someone built a "quick version" in a spreadsheet before a board meeting and it became canonical. The most recent one was built by the new hire who wasn't sure which existing model to trust, so they started fresh.

The data is probably fine. The models are probably mostly correct. The problem is that nobody can demonstrate that — step by step, from source to metric — with enough confidence that the next person in builds on instead of around.

That's a trust problem. And it's almost always misdiagnosed as a quality problem.

---

## What people think today

When data quality comes up in a data organization, the response is usually to add more tests. Write assertions. Check row counts. Alert on nulls. Build a data observability layer.

That instinct isn't wrong. Tests are good. Observability tools are useful.

But they answer a narrow question: *did this thing break in a way I anticipated?*

A test catches a null column. It catches a row count that dropped below a threshold. It catches a join that produced unexpected results — if you thought to check for it.

What a test doesn't answer: *where did this number come from? What did the pipeline actually do? What would I find if I queried the intermediate model that fed this metric?*

Most data quality incidents aren't caught by tests. They're caught by an analyst who notices something feels off in a dashboard, or by a VP who asks about a number that doesn't match a spreadsheet, or by a customer who emails support. The pipeline ran fine. All the tests passed. But something is wrong, and now you have to trace it backward through a system that doesn't show its work.

---

## Why that thinking is incomplete

Tests tell you when something broke. They don't make the pipeline legible.

Those are different problems.

Legibility means: you can follow the logic from the source table to the final metric, step by step, and understand what each transformation did and why the number is what it is. Legibility means when something changes — upstream schema, new data source, modified business logic — you know exactly what it touched.

A test suite on an opaque pipeline is a burglar alarm on a building with no windows. You'll know when the alarm goes off. You won't know what caused it or where to look.

The reason teams rebuild instead of reuse isn't that they found a bug in the original model. It's usually that they couldn't tell whether a bug existed. The model works — it produces a number — but the path from raw data to that number is invisible, so you can't defend the number, you can't explain a change, and you can't feel confident building downstream of it.

Reasonable engineers, given that situation, do the rational thing: build a fresh one they can reason about.

---

## A clearer way to think about it

The trust problem has a visibility solution.

Visibility means: every intermediate step in your pipeline is a real, queryable table. Not a temp table that vanishes. Not a DataFrame that lives in memory. A persistent table you can inspect at any point after the run — or after the failure.

Visibility means: you can trace any metric back through the models that produced it. You can see which source tables feed it, which transformations touched it, which tests cover it.

Visibility means: when the pipeline runs and something seems wrong, you don't have to re-run it with debug statements — you can query the intermediate state and bisect the problem directly.

This is what dbt's artifacts are actually for. Not just the DAG visualization. Not just the documentation site. The manifest that captures the structure of every model and its dependencies. The catalog that records what was actually produced. The run results that tell you when each model last succeeded, how long it took, and what happened when it didn't.

Together, these make the pipeline legible. Not because they explain the SQL — but because they provide a complete, inspectable record of what the pipeline did and what it left behind.

---

## Why this matters

There's a hidden cost to invisible pipelines that never shows up on a dashboard: the compounding cost of rebuilding.

When analysts can't trust a model, they don't file a bug. They build around it. They add a custom filter. They create a parallel version "just for this use case." They maintain a spreadsheet. These workarounds work — individually, in the short term. But over time they accumulate into a situation where nobody is quite sure which number is right, and the answer to every data question is "it depends on which model you look at."

Making the pipeline visible breaks this cycle — not by eliminating bugs, but by making them findable. When you can trace a metric to its source in five minutes, you can decide whether to trust it. When you can see that it's been running successfully for six months without a test failure, you can build on it with confidence.

Trust isn't built by having no failures. It's built by being able to see what happened when there were.

---

## The thing worth carrying forward

Next time someone says "we have a data quality problem," ask one question before reaching for more tests: *can you trace this number back to its source, step by step, right now?*

If the answer is no — if the pipeline is a black box that produces results but doesn't show its work — you don't have a quality problem yet. You have a visibility problem.

Fix visibility first. The quality problems you actually have will become findable. And the trust problems will start taking care of themselves.
