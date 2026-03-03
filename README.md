# PM Plugins for Claude Code

A collection of Claude Code plugins for product managers and developers who want structured, disciplined feature development workflows.

## Installation

Add this repository as a plugin marketplace, then install the plugin you want:

```
/plugin marketplace add alexmensch/claude-sdl-plugins
/plugin install define-feature@claude-sdl-plugins
/plugin install build-feature@claude-sdl-plugins
```

## Plugins

### define-feature

Define a feature precisely before building it. Takes any unstructured idea — a sentence, a rough description, a half-formed thought — and turns it into a clean requirements table through structured brainstorming.

Invoke it with `/define-feature` inside Claude Code and describe what you want to build. The skill acts as a sparring partner: it challenges whether the feature is necessary, surfaces edge cases you may not have considered, questions whether the proposed approach is the right one, and pushes back on anything vague or speculative. The goal is to arrive at the best outcome for users, not just to rubber-stamp ideas.

The output is a Markdown requirements table ready to be passed directly to the `new-feature` skill in the `build-feature` plugin. When it is, `new-feature` will have no clarifying questions — the feature is already fully defined.

#### When to use this

Use `define-feature` before `new-feature` when:

- The feature idea is still rough or unvalidated
- You want a second perspective before committing to implementation
- You want to ensure edge cases and scope are explicit before writing a technical spec

---

### new-feature

Build new product features with a strict development workflow anchored on test-driven acceptance and disciplined coding practices.

Invoke it with `/new-feature` inside Claude Code and describe the feature you want to build. The plugin orchestrates the entire process end-to-end using three specialized agents:

- **technical-spec** -- Analyses the existing codebase in depth, produces a structured requirements table with acceptance criteria and edge cases, and confirms the specification with you before any code is written.
- **test-writer** -- Takes the approved spec and writes comprehensive tests covering all acceptance criteria and edge cases. Runs in the background in an isolated worktree so tests and implementation happen in parallel.
- **code-reviewer** -- Reviews all branch changes before a PR is opened. Checks for DRY violations, unnecessary complexity, duplicated patterns, and unnecessary dependencies. Presents findings for your approval before committing fixes.

#### Workflow

1. A new branch is created from the default branch.
2. You describe the feature. The plugin clarifies ambiguous requirements, then hands them to the **technical-spec** agent which produces a detailed spec for your approval.
3. Once the spec is approved, **test-writer** starts writing tests in the background while implementation code is written in parallel against the same spec.
4. Tests are merged in and the full suite is run. Test failures are treated as implementation bugs, not test bugs -- tests are never changed without your confirmation.
5. The **code-reviewer** agent reviews all changes and presents findings. You decide which fixes to apply.
6. Linting, build verification, documentation updates, and semver versioning are handled.
7. You are prompted to commit and open a PR.

## License

[MIT](LICENSE)
