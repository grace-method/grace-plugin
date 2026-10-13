# GRACE Plugin

GRACE is a methodology for disciplined AI-assisted software development. This repository is the Claude Code plugin that distributes the methodology to consuming projects: skills, slash commands, methodology rules, and the templates each stage uses.

> **Status: under development.** GRACE is being actively designed and refined. The methodology, skill set, and plugin layout may change without notice. Anyone who picks this up before a stable release should expect breaking changes — including renamed skills, restructured rule files, and altered install behavior. There is no commitment to backwards compatibility at this stage and no obligation to early users.

## What this plugin ships

- **18 skills** surfaced as `/grace:*` slash commands in Claude Code: `project-start`, `project-setup`, `project-definition`, `business-analysis`, `analysis-review`, `change-assessment`, `design-review`, `testing-strategy`, `implementation`, `test-adequacy-review`, `validation`, `operations`, `retirement`, `release-planning`, `roadmap`, `risk-management`, `feedback`, and `install`.
- **Methodology rules** (`GRACE.md` and six rule files) that the `/grace:install` skill writes into a consuming project so they load into every conversation.
- **Templates** bundled with the skills that produce artifacts (business analysis, design review, implementation, etc.).
- **Version and freshness metadata** so consuming projects can tell which methodology version they're running.

## If you are reviewing this rather than installing it

Four files, in this order, are the quickest way to see how the methodology works:

1. [`methodology/GRACE.md`](./methodology/GRACE.md) — the entry point. Project types, how every change is classified by scale, and which skill each situation routes to.
2. [`methodology/rules/consultant-reflex.md`](./methodology/rules/consultant-reflex.md) — the rule that makes the assistant check a request against a goal before it writes any code.
3. [`skills/business-analysis/SKILL.md`](./skills/business-analysis/SKILL.md) — how goals, problems, solutions, and risks are elicited and traced to one another.
4. [`skills/test-adequacy-review/SKILL.md`](./skills/test-adequacy-review/SKILL.md) — a completion gate that audits a change's tests against the project's risk register, using reviewers from different vendors.

## Install

Add the marketplace, then install:

```
/plugin marketplace add https://github.com/grace-method/grace-marketplace.git
/plugin install grace@grace-marketplace
```

After installation, run `/grace:install` once in any project where you want the methodology rules loaded into every conversation. The skill writes `GRACE.md`, the rule files, and ensures a single `@.claude/GRACE.md` anchor line in the project's `.claude/CLAUDE.md`.

## Update

```
/plugin marketplace update grace-marketplace
```

After updating, re-run `/grace:install` in any project where you want the latest rules.

## File ownership

After `/grace:install`:

| Path in your project | Owner | Update behavior |
|---|---|---|
| `.claude/GRACE.md` | GRACE | Overwritten on every `/grace:install`. |
| `.claude/rules/*.md` | GRACE | Overwritten on every `/grace:install`. |
| `.claude/CLAUDE.md` | You | Only the anchor line is managed; everything else is yours. |

## Send feedback

Run `/grace:feedback` in any project where the plugin is installed. It walks you through articulating what's unclear, missing, or working poorly, applies a quick data-sensitivity check, and drafts an email to the methodology author. You review and send. Use it for anything about GRACE itself — a confusing rule, a skill that asks the wrong questions, a missing capability, or just an idea.

## License

MIT. See [LICENSE](./LICENSE).

## Source

The plugin source is built from the GRACE development repository. This repository contains only the built plugin contents; methodology development happens elsewhere.
