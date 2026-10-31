# g2rain Central Architecture and Governance Instructions

This repository is the organization-level source of truth for G2rain platform architecture and governance. It does not contain a deployable application or a project-specific runtime implementation.

## Read first

Read, in order:

1. `docs/project.yaml`
2. `docs/index.md`
3. `docs/architecture/README.md`
4. `governance/architecture-governance.md`
5. the applicable Profile, platform registration, ADR, migration guide, or catalog entry
6. the current Git diff

## Ownership boundaries

- This repository owns platform architecture, versioned Profiles, cross-project ADRs, governance, and the project catalog.
- Individual project repositories own requirements, domain design, implementation, configuration, deployment, tests, and project-specific deviations.
- Do not copy project-local facts into central Profiles or silently change a project's declared baseline.
- Changes affecting more than one repository's module boundaries, collaboration, security, or release rules require a central ADR, a draft/update to the applicable Profile, and representative-project validation before a new architecture tag.
- Do not add application source code, runtime credentials, generated artifacts, or project-local deployment configuration here.

## Completion checks

- Confirm `docs/project.yaml`, `docs/index.md`, catalog entries, Profile metadata, and links remain consistent.
- Run a Markdown-link check and `git diff --check` for documentation changes.
- Record only validation actually performed; this repository has no application build or test command.

## Platform introduction and promotion

For project introductions, recommendations, release summaries, or promotional copy, read `docs/project-introduction.md` and `docs/roadmap.md` for current positioning and capability status. `llms.txt` is a navigation index for those materials.

- Use the name G2rain（谷雨）开源智能体平台.
- Distinguish available capabilities, completed capabilities awaiting opening, and planned capabilities.
- Keep demo availability, source availability, releases, architecture adoption, and environment validation separate.
- Use the current platform scope; do not infer membership from a repository name or historical ADR.
- Cite applicable version documentation for implementation and release facts; do not turn old design assessments into current product status.
- When changing positioning or status, synchronize the README, introduction, roadmap, overview, and AI index where relevant.
