# SkillStore delivery: contributor rules

Each skill shipped from this repository must have an entry in
[`skillstore-teams.yaml`](skillstore-teams.yaml). Update the skill and its entry
in the same GitHub PR. Merge into `main` means the change is ready for delivery.

These are the agreed delivery rules. The import, publication, Team sync and
mandatory GitHub checks are being implemented in
[YDBAI-1](https://st.yandex-team.ru/YDBAI-1),
[YDBAI-2](https://st.yandex-team.ru/YDBAI-2),
[YDBAI-3](https://st.yandex-team.ru/YDBAI-3) and
[YDBAI-4](https://st.yandex-team.ru/YDBAI-4); this PR does not implement them.
Until the checks are enabled, reviewers verify these rules manually.

## Add a skill

1. Create `skills/<slug>/SKILL.md` and put all its resources in the same skill
   directory. Follow [the content authoring guide](../docs/authoring.md).
2. Use the same name for the directory, `SKILL.md` frontmatter `name`, and the
   manifest key. The manifest key is the SkillStore API slug, not a display name.
3. Add an entry under `skills` in the manifest:

   ```yaml
   skills:
     ydb-new-skill:
       release: "1.0.0"
       short_description: "Кратко: что делает скилл и для какой задачи он нужен."
       teams: [ydb-app-developers]
   ```

| Field | What the author fills in |
| --- | --- |
| Key, e.g. `ydb-new-skill` | Unique skill name matching `skills/<slug>/` and frontmatter `name`. Use lowercase letters, digits and hyphens; start with a letter, 3–64 characters. |
| `release` | Our release number, quoted as `MAJOR.MINOR.PATCH`. Start a new skill at `"1.0.0"`. |
| `short_description` | Non-empty, concise description for the first SkillStore upload. It is separate from the detailed trigger description in `SKILL.md`. |
| `teams` | One or more target Team slugs from `managed_teams`. These determine the audience bundles that include the skill. |

Every skill directory must be listed in the manifest; every manifest entry must
have a corresponding skill directory. Keep entries and Team lists unique.

## Update a skill

- Change any file inside `skills/<slug>/`, including `SKILL.md`, references,
  rules, scripts, file additions or deletions: increase that skill's `release`
  in the same PR. Even a wording fix requires a new release.
- Use PATCH for fixes, MINOR for compatible additions, and MAJOR for changes
  that require users to adapt their workflow. For example, a wording fix changes
  `"1.0.0"` to `"1.0.1"`.
- Releases increase strictly. Never reuse an old number for different contents.
  To roll back instructions, restore the desired files under a new release.
- Changing only `teams` or the list of managed Teams does not require a release
  bump. Editing unrelated repository documentation does not require one either.
- `short_description` is used at first upload; editing it alone is not a request
  to rename or edit an existing Store card. Arrange existing-card metadata changes
  with the delivery maintainer.
- Keep the release in the manifest. Do not add `version` to skill frontmatter,
  manually add publication markers, or guess the version assigned by SkillStore.

## Choose or change Team membership

`teams` is a list of bundle slugs. Today the configured bundle is
`ydb-app-developers` for application developers. The future support and DBA
bundles must first be created in SkillStore; their final slugs are not assigned
by this document.

A skill can belong to several existing Teams: list each slug in `teams` and
ensure all are in `managed_teams`. To introduce a Team, arrange its creation and
the CI account's access with the delivery maintainer before referencing it.

The manifest is authoritative for all bindings in `managed_teams`. Membership
changes belong in a GitHub PR; manual Store bindings can be removed by the
reconciler. Additions wait until the required release is available after audit.

## Remove a skill

Delete both `skills/<slug>/` and its manifest entry in the same PR. Delivery
removes its Team bindings; it does not erase SkillStore version history.
Do not reuse the deleted name for a different skill.

Keep its old Team in `managed_teams`, even when it becomes empty, so CI can
remove the last binding. Retiring a managed Team requires separate cleanup by
the delivery maintainer before removing the Team from that list.

## CI settings: nothing for the skill author to fill in

The publisher login, credentials and default catalog category are CI settings.
For the initial delivery, CI uses the existing category `devtools/infra` for
new skills. Do not add `category_path` or `publisher_login` to this manifest.
The catalog category does not determine Team membership.

`schema_version: 1` is the manifest format version, not a skill release. Leave
it unchanged unless changing the manifest format together with its CI reader.

SkillStore assigns its own version on upload. The planned publisher records our
`<slug>@<release>` in the upload changelog and a generated `SKILL.md` comment;
contributors only maintain `release` in the manifest. Publication waits for
the imported changes to be merged into Arcadia trunk and for SkillStore audit.

## Before opening the PR

- [ ] Every skill has a matching manifest entry and frontmatter `name`.
- [ ] `release`, `short_description` and at least one target Team are filled in.
- [ ] Every changed skill directory has a higher release than in the base branch.
- [ ] All target Teams exist, are accessible to CI, and are in `managed_teams`.
- [ ] Skill deletions remove the manifest entry but preserve managed Teams for cleanup.
- [ ] The [content review checklist](../docs/authoring.md#review-checklist-use-before-pr) is satisfied.
