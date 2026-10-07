---
name: ydb-docs
description: Finds official YDB documentation through llms.txt indexes for public YDB OSS, Yandex YDB Internal, and YDB Tech, selecting the deployment context, language, and version. Use when the user asks to find YDB documentation, locate an official reference, or verify a claim against the docs (including «найди документацию YDB» and «внутренняя документация YDB»). For implementation, query writing, or code audits, use the corresponding YDB skill; this skill handles documentation lookup. Does not cover YQL on YT.
---

# YDB Documentation

Find and read the documentation that matches the user's YDB deployment and task.

## Documentation map

| Variant | What to look for here | Agent entry point |
|---|---|---|
| YDB OSS | Public documentation of open-source YDB: concepts, YQL, SDKs, CLI, deployment, administration, and public contributor guidance. | https://ydb.tech/llms.txt — discovery hub for languages and product versions. |
| YDB Internal | Documentation for YDB installations inside Yandex: usage and instructions specific to the internal deployment environment. | https://docs.yandex-team.ru/ydb/llms.txt |
| YDB Tech | Internal documentation with the additional “YDB Development” (Разработка YDB) section: implementation details and Yandex-specific development processes. | https://docs.yandex-team.ru/ydb-tech/llms.txt |

`ydb.tech` is the public OSS site; `docs.yandex-team.ru/ydb-tech` is the internal Tech variant. The similar names do not make them interchangeable. The Internal documentation homepage is https://ydb.yandex-team.ru/docs/; its index is also available at the `docs.yandex-team.ru/ydb` address above.

Choose Internal for users of Yandex's internal installations and Tech for questions about developing YDB inside Yandex. Respect an explicitly requested variant or URL. Otherwise use OSS for public or general YDB questions; working at Yandex alone does not imply an internal deployment. If the deployment is unclear and could change the answer, clarify it.

## Workflow

1. Identify the documentation topic, deployment context, user's language, and any requested YDB version. Select the variant from the map above.
2. Fetch the selected entry point with an available web, HTTP, or authorized internal documentation tool. For OSS, follow the root index to the Russian or English documentation index and select the requested product branch; use `main` when no version is requested. For Internal and Tech, use the supplied index and its actual links; do not assume that OSS language paths or version parameters are supported there.
3. Search the index for the topic, then read the relevant linked pages. Prefer the Markdown URLs supplied by the index, preserve the version query parameter, and fetch only the pages needed.
4. Answer from the pages actually read, cite them, and make version-dependent limitations explicit. If the sources do not establish a claim, say so.

## Gotchas

- `llms.txt` is an index, not evidence for product behavior; open the relevant documentation pages before verifying a claim.
- `main` is a documentation branch, not a promise that a feature is available in a released YDB version.
- Keep the selected version when following links so the answer does not silently mix releases.
- Keep deployment-specific guidance with its documentation variant. When a question needs more than one variant, identify which source supports each part of the answer.
- A redirect to a sign-in page is not a retrieved index. Internal and Tech documentation may require corporate access; use the runtime's authorized tools or credentials without putting tokens in prompts, URLs, or output.
- YQL documentation for YT is not a substitute for YDB's dialect documentation.

## Content rules

This skill only locates and reads documentation; it does not require database access. Do not infer syntax or behavior from another database when the YDB sources do not cover the question.

If the OSS root index is unavailable, try the direct indexes:

- English: https://ydb.tech/docs/en/llms.txt?version=main
- Russian: https://ydb.tech/docs/ru/llms.txt?version=main

For a requested OSS product branch, use its version parameter as described by the root index. If the OSS indexes are unavailable, try https://ydb.tech/docs/ or a site-restricted search.

If an internal index is unavailable, try the corresponding documentation homepage (https://docs.yandex-team.ru/ydb/ or https://docs.yandex-team.ru/ydb-tech/), its navigation, or an authorized internal search. OSS pages can support shared concepts, but do not establish Yandex-specific behavior or development processes. State any access failure, variant substitution, or version mismatch rather than implying the requested documentation was verified.
