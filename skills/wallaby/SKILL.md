---
name: wallaby
description: Improve test quality or raise coverage with Wallaby runtime evidence.
disable-model-invocation: true
---

Route the invocation before loading its reference:

- `$wallaby improve [scope and signal instructions]` selects [`references/improve.md`](references/improve.md).

After routing, read [`wallaby-cli`](../wallaby-cli/SKILL.md) first. Then read the selected subcommand reference and follow its workflow. The subcommand reference determines when to perform discovery, start or query Wallaby, select targets, edit, and verify. Begin no task work until both files have been read.

Read only the additional Wallaby references required by the selected subcommand and the reports it produces.

For either subcommand, an explicit scope limits discovery, edits, and verification. Without a scope, work across the whole repository and continue across distinct high-confidence candidates instead of stopping after the first improvement.
