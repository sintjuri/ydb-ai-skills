# Inspecting a database with YDB EM MCP

YDB Enterprise Manager exposes MCP tools at its Gateway `/meta/mcp` endpoint. Use the connection configured in the current runtime. This workflow targets **EM MCP**; a different YDB MCP implementation may expose different tools.

## Discover the session and target

1. Inspect the connected server's tools and input schemas (`tools/list`, or the runtime's equivalent). Use the runtime-exposed names, including any server prefix. The examples below use EM's unprefixed names. Do not infer availability from this reference.
2. Preserve the user's cluster and database. If either is missing, ask for it or use discovery tools available to this session. Do not silently choose the first database returned.
3. For a supplied cluster, use `ydb-get-databases` to list databases visible to the session. If `ydb-get-clusters` is available, it can discover cluster names; ordinary sessions may not receive it. If it is absent and no cluster is known, ask the user for the cluster.
4. Use `ydb-get-database-info` for the selected database; use `ydb-get-whoami` if caller identity or access needs checking. A visible cluster or database name is not proof of permission to read its data. On an access error, report it; do not switch credentials or target.

## Inspect the namespace

- Use `ydb-get-scheme-directory` with the selected `cluster_name`, `database`, and `path`. In EM's tool interface the database root is `path: ""`; nested objects are database-relative paths such as `app/orders`.
- Use `ydb-describe` on a known object with `detail: "schema"`. Request partitions or `detail: "full"` only when the task needs them. They can return large responses.
- Use `ydb-get-acl` when the user asks about object permissions. Reading ACL does not authorize changing it.
- Report the target and the objects actually observed. Distinguish an empty result, unavailable tool, denied access, and connection failure.

For example, to inspect a known table:

```json
{
  "cluster_name": "<user-selected cluster>",
  "database": "<user-selected database>",
  "path": "app/orders",
  "detail": "schema"
}
```

Pass this to `ydb-describe` using the schema advertised by the connected server; replace placeholders with confirmed context.

## Version and capability context

For recommendations that depend on a server release or feature flag, obtain the server version and relevant capability evidence from available tools or the user. `ydb-get-feature_flags` and cluster configuration tools can be absent for non-administrators; absence is not evidence that a feature is disabled. A client version is not a server version. Use documentation for the target release, and state unknown flags rather than promising support from `main` documentation alone.

`search_docs` is optional: use it only if advertised, with the search text in `query`. Validate the returned documentation against the target release. If unavailable, use the documentation lookup procedure in `ydb-core`.

## Scope

Route SQL execution and optimization to `ydb-table`. `ydb-get-database-health-check` can help when the user asks about database health, but avoid broad cluster diagnostics for a simple schema question. Changing ACL, database size, or storage state is an explicit administrative task; discovery does not authorize those calls.

Sources: [EM AI-assistant configuration](https://docs.yandex-team.ru/ydb-tech/devops/enterprise-manager/ai-assistant) and the implementation reference in [`ydb-core`](../SKILL.md#em-mcp) (tool construction, session restrictions, and database-relative paths).
