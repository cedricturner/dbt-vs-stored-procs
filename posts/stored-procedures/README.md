# The stored procedure is not your enemy

When teams migrate from stored procedures to dbt, they usually describe it as modernizing their stack. Better SQL practices. Version control. Modularity. Tests.

That framing is accurate — but it undersells the actual change. And because it undersells it, teams often adopt dbt, get the syntax right, and still wonder why debugging feels the same.

The migration isn't primarily about how you write SQL. It's about what your pipeline leaves behind when it runs.

---

## What people think today

The standard pitch for moving away from stored procedures goes something like this: your transformation logic is buried in a monolithic procedure, hardcoded with schema names, impossible to test, impossible to parallelize. dbt breaks it into modular files with clear dependencies, version-controlled in Git, with lineage you can visualize.

All true. But this frames the problem as organizational — messy code that needs cleanup.

The deeper problem is architectural. And it has nothing to do with how tidy the code is.

---

## Why that thinking is incomplete

Here's what a stored procedure actually does at runtime:

It creates temp tables, transforms data through a series of steps, produces a final output, and ends. When the session ends, the temp tables disappear. The intermediate work is gone.

If the procedure fails at step 4, the outputs from steps 1, 2, and 3 die with it. To debug, you re-run the whole thing — adding print statements, adding debug tables, trying to reconstruct what happened.

This isn't a stored procedure problem specifically. It's a problem with any pipeline that treats intermediate state as disposable. Python scripts have the same issue: DataFrames live in memory, and when the process ends, they go with it.

The pipeline runs. The pipeline ends. Nothing survives.

The result isn't just slow debugging. It's a slow accumulation of distrust. When you can't inspect what happened — when every failure starts from scratch — you stop trusting the pipeline. You start working around it.

---

## A clearer way to think about it

dbt models are real tables in your warehouse.

Not temp tables. Not in-memory DataFrames. Persistent, queryable tables that exist after the run completes — whether it succeeded or failed.

That sounds like a small implementation detail. It isn't.

When a dbt run fails at step 4, steps 1, 2, and 3 are still sitting in your warehouse. You can query them. You can see exactly what state the data was in before the failure. You can re-run only the failed model without touching what's already there.

The shift is from *execution* to *artifacts*.

A stored procedure executes and disappears. A set of dbt models executes and leaves evidence. Every step is a table. Every table is a checkpoint. The pipeline is inspectable by design — not as an afterthought, not as a debugging mode you turn on.

There's a second change that follows from this one. Because each model is a real, standalone table, they can be independently referenced. When another team needs "clean orders," they point to `ref('stg_orders')` instead of copying the filter logic into their own procedure. The definition lives in one place. It can't drift.

The stored procedure encoded the same logic — it just kept it invisible, context-dependent, and tied to a single execution flow.

---

## Why this matters

There's a practical consequence and a cultural one.

**Practically:** debugging becomes inspection instead of archaeology. The difference between "re-run everything from scratch to reproduce the failure" and "query the intermediate table and see exactly where it went wrong" sounds small. It isn't. At 3am, it's the difference between 45 minutes of setup and 5 minutes of diagnosis. Multiplied across a team, across a year, it adds up to a lot of recovered time.

**Culturally:** when the pipeline leaves artifacts, people start trusting it. Not because it breaks less — but because when it breaks, you can see what happened. That changes the relationship with the tool.

Most data teams don't rebuild because the platform is unreliable. They rebuild because they can't see inside it. When you can't inspect a system, you can't develop confidence in it, and without confidence you build around it instead of on top of it.

Making intermediate state persistent doesn't just improve debugging. It improves adoption.

---

## The thing worth carrying forward

When you evaluate any data pipeline — not just stored procedures — ask: *what does it leave behind when it runs?*

If the answer is only the final output, you're one failure away from starting over blind. If the answer is every intermediate step, as a queryable table, you're debugging from evidence instead of memory.

That's what the stored procedure migration is actually about. The SQL syntax is almost beside the point.
