# SkillStore publication requirements

Register every skill in [`skillstore-teams.yaml`](skillstore-teams.yaml).
Submit the skill files and manifest changes in the same PR.

## Add a skill

Put `SKILL.md` and all resources in `skills/<name>/`. The directory name,
frontmatter `name`, and manifest key must match. Use lowercase letters,
digits and hyphens; start with a letter, 3–64 characters.

Fill in all three fields:

```yaml
skills:
  ydb-new-skill:
    release: "1.0.0"
    short_description: "Краткое описание назначения скилла."
    teams: [ydb-app-developers]
```

- `release`: our version in `MAJOR.MINOR.PATCH` format; start at `"1.0.0"`.
- `short_description`: a non-empty, concise description for the Store card.
- `teams`: one or more existing SkillStore Team slugs, all listed in `managed_teams`.

Each skill directory must have exactly one manifest entry, and vice versa.
Follow the [skill content requirements](../docs/authoring.md).

## Update a skill

Increase `release` whenever any file in the skill directory changes, including
resources and file additions or deletions. Use PATCH for fixes, MINOR for
compatible additions, and MAJOR for incompatible changes.

Never reuse or decrease a release number. A rollback also needs a new version.
Changing only Team membership does not require a version bump.
Keep the version in the manifest; do not add `version` to `SKILL.md` frontmatter.

## Remove a skill

Delete both its directory and manifest entry in the same PR.
Keep its Teams in `managed_teams`, even if they become empty, until their
bindings have been cleaned up. Do not reuse a deleted name for a different skill.
