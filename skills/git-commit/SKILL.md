---
name: git-commit
description: Generate evidence-based Conventional Commits messages from Git diffs or supplied changes, and create commits when requested. Use for requests such as "commit", "提交代码", "写提交信息", "write a commit message", or summarizing staged/unstaged changes into a commit subject and body. Do not use for general Git troubleshooting, code review, or release notes without a commit-message task.
---

# Git Commit

Produce a concise commit message that accurately describes the selected change. Distinguish writing a message from executing a commit: a request for wording alone does not authorize repository mutations.

## Inspect and select the change

1. Follow repository instructions and the user's requested scope and language. Inspect recent commit subjects for useful scope and language conventions; default to English if none apply.
2. In a repository, start with `git status --short`, `git diff --cached --stat`, and `git diff --cached`. Inspect `git diff` and relevant untracked files when the requested scope includes them or the index is empty. Read relevant source context when a diff alone does not establish the intent. A file list or diff summary alone is insufficient evidence for behavioral claims.
3. For a message request without an explicit scope, use staged changes when present. If nothing is staged, use the relevant working-tree changes and say that the proposal describes unstaged changes. Keep unstaged edits to a partially staged file out of a staged-change message.
4. For an actual commit, use the selected staged changes by default. Stage only paths or hunks clearly covered by the user's request; avoid blanket staging that could include unrelated work. If the index contains unrelated changes or the intended scope is materially ambiguous, clarify before altering it or committing.
5. If the user supplies a diff or a sufficient description, work from it without requiring repository access. If there is no change to describe, report that instead of inventing a message. Ask for missing evidence only when needed to determine the message.

Group related files by purpose, not by file count. Use one primary type per commit. For independent changes, suggest separate messages; do not split or rearrange the index unless requested.

## Message format

```text
type(scope)!: subject

Optional body explaining motivation and material behavior.

Optional footers
```

The scope, `!`, body, and footers are optional. Omit parentheses when there is no scope.

| Type | Use for |
|------|---------|
| feat | new user-visible capability |
| fix | correction of incorrect behavior |
| refactor | internal restructuring without behavior change |
| perf | speed or memory improvement |
| docs | documentation-only change |
| test | test-only change |
| build | build tooling or dependencies |
| ci | CI configuration |
| chore | maintenance that does not fit the above |

Choose the type from the effect, not the filenames or size of the patch. Tests accompanying a fix belong under `fix`. Use an established scope that identifies the affected subsystem; omit it if none fits. Avoid inventing project-specific scopes.

### Subject and body

- Write an imperative subject in English, or equivalent direct wording in the requested language, without a trailing period. Aim for a full header of 50 characters or fewer; preserve clarity and honor repository-specific limits.
- State the concrete change, avoiding vague phrases such as `update code` or `fix bug`.
- Add a body only when it helps explain why the change is needed, important behavior, or a migration. Do not repeat the subject or narrate every edited file.
- Ground claims in the diff or supplied context. Do not invent motivation, performance results, test outcomes, issue numbers, or compatibility guarantees.
- Include issue references and trailers only when supplied or verified. Preserve required repository trailers.

### Breaking changes

Mark a change as breaking only when existing consumers must adapt: for example, a removed public API, incompatible configuration, or a changed contract. An additive feature, a compatible bug fix, or an internal rename is not automatically breaking merely because behavior changes.

Use `!` after the type/scope and/or a `BREAKING CHANGE:` footer. For a real incompatibility, prefer both and explain what consumers must change using verified migration details. Do not label an incompatible public API removal as a behavior-preserving refactor.

## Deliver or commit

For message-only requests, return one recommended message in a fenced `text` block. Keep any needed scope note outside the block. Provide alternatives only when requested or when proposing separate commits for independent changes.

When the user requests a commit, execute it once the scope is clear; do not stop at a draft or request redundant approval. Pass the message safely, for example through a temporary file and `git commit -F`, so shell metacharacters and multiline bodies remain literal. Do not amend, reset, discard changes, bypass hooks, or push unless authorized. If a hook fails, report the failure and resolve it within scope; do not claim a successful commit.

After a successful commit, verify the resulting commit and working-tree status. Report the short hash and subject, plus any remaining changes relevant to the request. Report validation only if actually performed.

## Examples

Compatible fix with related tests:

```text
fix(parser): accept empty JSON objects
```

Additive feature with a useful motivation:

```text
feat(cache): add persistent disk caching

Reuse cached responses across process restarts to avoid
redundant network requests.
```

Incompatible API removal with a verified replacement:

```text
feat(api)!: remove the v1 endpoint

BREAKING CHANGE: /v1/users is removed. Use /v2/users.
```
