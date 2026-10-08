# Queries and plans with YDB EM MCP

Use this workflow when EM MCP is connected. Read [`ydb-core`'s EM workflow](../../ydb-core/SKILL.md#em-mcp) for session discovery and target selection. Keep SQL construction in this skill and execution in the server.

## Inspect, explain, execute

1. Inspect the current tool schemas and confirm `cluster_name` and `database`. Use `ydb-describe` with `detail: "schema"` for referenced tables whose schema is unknown. Do not invent columns or indexes.
2. Construct or review the query for the target release. For unfamiliar syntax or optimization, call `ydb-exec-query` with `action: "explain-query"`, the complete `query`, and the confirmed target. Treat this as plan inspection, not execution.
3. Resolve parser or semantic errors by explaining the revised query. Do not change `action` to execution just to test a fix. A successful explain validates a plan; it does not demonstrate better runtime performance.
4. Execute with `action: "execute-query"` only when execution is within the user's request. Before DDL/DML, show the exact query, target, and effect and obtain explicit confirmation for that operation. Classify every statement in a script; a leading `SELECT` does not make the rest read-only.
5. For exploratory reads, select only needed columns and bound the input by a key range or suitable index where possible. `LIMIT` bounds returned rows, not scan cost. Explain a potentially broad query and agree on the workload before executing it.
6. Report what actually ran, its target, the returned result or error, and any truncation. On connection or access errors, stop that execution path and report the failure; do not silently retry through another target or identity.

Example: obtain a plan for a known table and columns without executing the query:

```json
{
  "cluster_name": "<user-selected cluster>",
  "database": "<user-selected database>",
  "query": "SELECT order_id, status FROM `app/orders` WHERE order_id = 42 LIMIT 1;",
  "action": "explain-query",
  "stats": "none"
}
```

Replace the placeholders and verify the schema first. Use the exact tool name and input schema exposed by the runtime. EM's implementation currently makes only the target fields required in the query tool's JSON Schema, but a useful call still needs explicit `query` and `action`.

## Slow-query workflow

- Get the query and target; without a target, provide only a syntax-level review and ask for context before accessing a database.
- Describe referenced tables and explain the original query. Ground the diagnosis in the returned schema and plan.
- Propose a revision, explain it, and compare the plans. Explain how to measure the expected improvement.
- If the user requests runtime measurement, execute the agreed query with `stats: "basic"` or `"full"` as needed. This runs the query; statistics are not a non-executing validation mode. Do not run writes just to collect performance data.
- For a time-window question, use `ydb-get-graph-data` if advertised, with an explicit `target`, `from`, `until`, and bounded `maxDataPoints`. These database-level graphs provide context; they do not by themselves attribute latency to one query.

## Interface limits

- This query tool's current schema exposes `cluster_name`, `database`, `query`, `action`, and `stats`. Do not invent typed-binding, transaction-mode, timeout, or row-budget parameters. If the task requires parameter binding that this tool cannot provide, use the documented parameterized execution route or report the limitation. Do not interpolate untrusted values into SQL to bypass it.
- The documented `action` values are `explain-query` and `execute-query`; `stats` values are `none`, `basic`, and `full`. Recheck the live schema before using a deployment with a different interface.
- MCP tool annotations are hints; `ydb-exec-query` can execute both reads and writes. Explain and execution are distinguished by `action`.
- An unavailable feature-flags tool leaves feature state unknown. Check the target release and obtain the missing evidence before recommending a version-dependent feature.

Sources: [EM AI-assistant configuration](https://docs.yandex-team.ru/ydb-tech/devops/enterprise-manager/ai-assistant) and the implementation reference in [`ydb-core`](../../ydb-core/SKILL.md#em-mcp) (query actions, describe, and graph data).
