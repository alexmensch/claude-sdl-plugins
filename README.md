# Software Development Lifecycle (SDL) Plugins for Claude Code

A collection of Claude Code plugins for product managers and developers who want structured, disciplined workflows across the full feature lifecycle — from defining an idea, to planning what to build next, to shipping it.

## Installation

Add this repository as a plugin marketplace, then install the plugins you want:

```
/plugin marketplace add alexmensch/claude-sdl-plugins
/plugin install feature-definition@claude-sdl-plugins
/plugin install feature-development@claude-sdl-plugins
/plugin install roadmap@claude-sdl-plugins
```

## Plugins

### feature-definition

**Skills:** `define-feature`

Define a feature precisely before building it. Takes any unstructured idea — a sentence, a rough description, a half-formed thought — and turns it into a clean requirements table through structured brainstorming.

Invoke it with `/define-feature` inside Claude Code and describe what you want to build. The skill acts as a sparring partner: it challenges whether the feature is necessary, surfaces edge cases you may not have considered, questions whether the proposed approach is the right one, and pushes back on anything vague or speculative. The goal is to arrive at the best outcome for users, not just to rubber-stamp ideas.

The output is a Markdown requirements file saved to `requirements/` and ready to be passed directly to the `new-feature` skill in the `feature-development` plugin. When it is, `new-feature` will have no clarifying questions — the feature is already fully defined.

#### When to use this

Use `define-feature` before `new-feature` when:

- The feature idea is still rough or unvalidated
- You want a second perspective before committing to implementation
- You want to ensure edge cases and scope are explicit before writing a technical spec

---

### feature-development

**Skills:** `new-feature`, `pull-request`, `semver`

Build new product features with a strict development workflow anchored on test-driven acceptance and disciplined coding practices.

Invoke it with `/new-feature` inside Claude Code and pass it a requirements file or describe the feature in chat. The plugin orchestrates the entire process end-to-end using three specialised agents:

- **technical-spec** — Analyses the existing codebase in depth, produces a structured technical specification with acceptance criteria and edge cases, and confirms it with you before any code is written.
- **test-writer** — Takes the approved spec and writes comprehensive tests covering all acceptance criteria and edge cases. Runs in the background in an isolated worktree so tests and implementation happen in parallel.
- **code-reviewer** — Reviews all branch changes before a PR is opened. Checks for DRY violations, unnecessary complexity, duplicated patterns, and unnecessary dependencies. Presents findings for your approval before committing fixes.

#### Workflow

1. A new branch is created from the default branch.
2. You provide a requirements file or describe the feature in chat. The plugin clarifies any ambiguity, then hands the requirements to **technical-spec** which produces a detailed spec for your approval.
3. Once the spec is approved, **test-writer** starts writing tests in the background while implementation code is written in parallel against the same spec.
4. Tests are merged in and the full suite is run. Test failures are treated as implementation bugs, not test bugs — tests are never changed without your confirmation.
5. **code-reviewer** reviews all changes and presents findings. You decide which fixes to apply.
6. Linting, build verification, documentation updates, and semver versioning are handled.
7. You are prompted to commit and open a PR.
8. If a `requirements/ROADMAP.md` exists, the shipped feature is automatically moved from the planned table to the shipped table with the PR number recorded.

---

### roadmap

**Skills:** `plan-roadmap`, `communicate-roadmap`

Organise defined feature requirements into a sequenced, release-grouped plan, and communicate it in language that speaks to users rather than engineers.

#### plan-roadmap

Invoke it with `/plan-roadmap` inside Claude Code. The skill reads all requirements files in `requirements/` and helps you think through how to sequence and group them into coherent releases. It acts as a planning partner, not an order-taker: it challenges groupings that don't tell a coherent story, asks about technical dependencies, and ensures that each release delivers real incremental value to the user.

The output is `requirements/ROADMAP.md`, which contains:

- An **overview** written for a fresh LLM context — capturing the rationale and principles behind the sequencing so any future planning session can load it quickly
- A **planned table** grouped into named releases, with global sequence numbers, links to requirements files, and GUIDs
- A **shipped table** listing released features with their PR numbers

This file is also updated automatically by `new-feature` when a feature ships.

#### communicate-roadmap

Invoke it with `/communicate-roadmap` inside Claude Code. The skill reads `requirements/ROADMAP.md` and the referenced requirements files, then writes `requirements/EXTERNAL-ROADMAP.md` — a user-facing document that describes what is coming in terms of problems solved and outcomes delivered, not features built.

The voice is user-benefit first: every sentence answers "so what?" from the perspective of someone who uses the product.

#### What the roadmap plugin does not do

- It does not create or modify requirements files. Use `define-feature` for that.
- It does not implement features. Use `new-feature` for that.
- If planning surfaces an incompatibility between requirements, it flags it and directs you back to `define-feature` to resolve it.

---

## Naming conventions

Plugins and skills follow distinct naming conventions because they serve different purposes:

- **Plugins** are installation and grouping units. Their names are **nouns or noun phrases** that describe a domain or workflow phase (`feature-definition`, `feature-development`, `roadmap`). Users install plugins once; the name is organisational metadata.
- **Skills** are invocable actions within a plugin. Their names are **verb phrases** that describe what they do (`define-feature`, `new-feature`, `plan-roadmap`, `communicate-roadmap`). Users invoke skills by name in conversation; clarity here matters most.

A plugin name and a skill name should never be the same, even when a plugin contains only one skill. This keeps the hierarchy unambiguous as plugins grow.

## License

[CC BY 4.0](LICENSE)
