# Python or SQL is the wrong question

When data teams argue about Python vs SQL, they're arguing about the wrong variable.

The Python side: more expressive, easier to test, access to the full ecosystem of libraries, better for complex logic. The SQL side: readable, closer to the data, what analysts already know, easier to optimize. Both camps are largely right.

But neither side is asking the question that actually matters, which is: *where does the computation happen?*

---

## What people think today

The debate usually gets framed as a capability question. What can you do with each language? Can you write complex window functions easily? Can you run ML models inline? Can you loop over rows? Can you call an external API?

From there, teams settle on a split: SQL for querying and aggregation, Python for complex preprocessing. Use dbt for the SQL layer, Python for everything upstream. Build an orchestrator — Airflow, Prefect, whatever — to stitch it together.

That's a reasonable operational answer. But it's still treating language as the primary variable, which means you end up making architectural decisions based on syntax preference instead of what actually determines how your pipeline behaves at scale.

---

## Why that thinking is incomplete

Language is not the variable that matters. Location is.

When you transform data in Python, here's what actually happens: you pull rows out of the warehouse into memory on a machine, transform them with Python, and push the results back. The computation happens somewhere outside the warehouse — on your laptop, on an Airflow worker, on a Spark cluster.

When you transform data in SQL, the computation happens inside the warehouse. The data doesn't move. The query does.

The difference sounds abstract until you run into the ceiling.

In-memory pipelines have a ceiling: your machine's RAM, or your worker's memory allocation. When you hit it, you start doing things you shouldn't have to do — chunking data, managing memory explicitly, writing to temp files. The pipeline becomes fragile in ways that are hard to test for and harder to debug.

In-warehouse computation doesn't have that ceiling. The warehouse was designed to process large amounts of data efficiently. You're using it for what it was built for. More rows in, more rows out — scaling is the warehouse's problem, not yours.

---

## A clearer way to think about it

The question isn't "Python or SQL."

The question is: *does this transformation need to happen outside the warehouse, or can it happen inside it?*

For most data transformation work — cleaning columns, applying business logic, joining tables, computing aggregates, building metrics — the answer is: inside. The warehouse is faster at this than Python is, the data doesn't have to move, and the output is a table that lives where you need it.

For computation that genuinely can't happen inside the warehouse — calling external APIs, running ML model inference, processing unstructured data with a Python library that doesn't exist in SQL — the answer is: outside. But this isn't a Python-vs-SQL decision anymore. You're doing something SQL structurally cannot do, so the choice is obvious.

The Python/SQL debate mostly collapses under this framing. Most transformation work belongs in the warehouse. The few things that don't are clearly distinguishable. The debate only persists because teams frame it as preference rather than fit.

---

## Why this matters

There's a performance argument here, and a more interesting one underneath it.

The performance argument is straightforward: in-warehouse computation scales better, runs faster for most workloads, and doesn't require you to manage infrastructure outside the warehouse.

The more interesting argument is about what the pipeline leaves behind.

When your transformation runs in the warehouse, the output is a warehouse table. It persists. You can query it. You can see what it produced. If it fails, the work that completed is still there — inspectable, rerunnable in isolation.

When your transformation runs in Python, the output lives in memory during the process. When the process ends — whether it succeeded or failed — the intermediate state goes with it. You get the final output if it worked, and nothing if it didn't.

The same visibility argument that applies to stored procedures applies here. A pipeline that runs in the warehouse leaves artifacts. A pipeline that runs in Python leaves results — if it gets that far.

There's also an orchestration argument. When teams move from Python scripts to dbt, one of the things they notice is that they don't need a separate DAG definition anymore. The dependencies are expressed in the SQL itself, via `ref()`. The orchestration is implicit in the transformation logic — not a separate configuration file in Airflow that has to stay in sync with the code.

That's not a minor convenience. It means there's one system to understand instead of two. The mental model is simpler, and when something breaks, there's one place to look.

---

## The thing worth carrying forward

When you're designing a pipeline and reach for Python, ask one question before writing the first `pd.read_sql()`:

*Is this happening in Python because it needs to, or because it's familiar?*

If it needs to — external APIs, model inference, genuinely Python-only work — then Python is right. Use it.

If it's happening in Python because that's what you're comfortable with: the warehouse is probably better suited for this. The transformation will run faster, the intermediate state will persist, and you won't need a separate orchestrator to manage the execution order.

Language preference is a real thing. It's just not the variable that determines whether your pipeline scales, debugs cleanly, or earns the trust of the people who depend on it.
