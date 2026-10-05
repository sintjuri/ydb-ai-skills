# Skill publication requirements

Register every skill in [`skills.yaml`](skills.yaml).
Submit the skill files and release changes in the same GitHub PR.

## Add a skill

Put `SKILL.md` and all resources in `skills/<name>/`. The directory name,
frontmatter `name`, and manifest key must match. Use lowercase letters,
digits and hyphens; start with a letter, 3–64 characters.

Fill in both fields:

```yaml
skills:
  ydb-new-skill:
    release: "1.0.0"
    short_description: "Краткое описание назначения скилла."
```

- `release`: our version in `MAJOR.MINOR.PATCH` format; start at `"1.0.0"`.
- `short_description`: a non-empty, concise description for the Store card.

Each skill directory must have exactly one manifest entry, and vice versa.
Follow the [skill content requirements](../docs/authoring.md).

## Update a skill

Increase `release` whenever any file in the skill directory changes, including
resources and file additions or deletions, or when `short_description` changes.
Use PATCH for fixes, MINOR for
compatible additions, and MAJOR for incompatible changes.

Never reuse or decrease a release number. A rollback also needs a new version.
Keep the version in the manifest; do not add it to `SKILL.md`.

## Publication and removal

Merge into `main` makes a release eligible for delivery. Internal delivery
creates a separate Arcadia PR for each new or updated skill. After review and
merge, the standard `infra` CI publishes it to SkillStore.

The Team owner adds published skills to audience bundles manually.
To remove a skill, obtain maintainer approval, delete its directory and manifest
entry in one GitHub PR, and arrange manual removal from Arcadia and the Teams.
Do not reuse a deleted name for a different skill.
