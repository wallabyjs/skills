# Wallaby skills

Agent skills for creating, running, investigating, and improving JavaScript, TypeScript, and Python tests with [Wallaby](https://wallabyjs.com). These skills give coding agents workflows for using live test results, coverage, execution traces, and runtime values while working on a codebase.

Wallaby runs a project's existing tests through their test framework and automatically reruns affected tests as files change. The skills explain how to use that feedback to diagnose failures, verify changes, and add tests that protect observable behavior.

## wallaby

`wallaby` is a router for predefined workflows invoked through explicit subcommands. Each workflow defines its scope, execution steps, and verification criteria. The skill depends on [`wallaby-cli`](skills/wallaby-cli/SKILL.md) for test execution, runtime evidence, and verification.

Invoke it as `$wallaby <subcommand> [instructions]`.

### improve

Use this workflow to find meaningful gaps in a test suite and strengthen its assertions. It reviews uncovered branches, weak assertions, boundary behavior, failure paths, and other cases where tests may miss a defect.

By default, it considers source files with coverage at or below 95% and uses change risk to prioritize them. You can specify a file, directory, Git change set, or signal threshold. Without an explicit scope, it works across the repository. You can also request a test-quality review without using coverage, complexity, or change risk to select candidates.

Example requests:

```text
$wallaby improve
$wallaby improve src/accounts.ts
$wallaby improve the files changed in this branch
$wallaby improve src/ with coverage below 90%
$wallaby improve tests/ without using coverage, complexity, or CRAP as discovery signals
```

The workflow prefers test-only changes. When a test demonstrates a product defect, it can make a source fix backed by the intended behavior and a regression test. Coverage is a review threshold; completion depends on protecting meaningful behavior and explaining any remaining gaps.

See [SKILL.md](skills/wallaby/SKILL.md) for subcommand routing and dependencies, and the [improve workflow](skills/wallaby/references/improve.md) for discovery, verification, and reporting details.

## wallaby-cli

`wallaby-cli` gives coding agents a CLI interface to Wallaby. Through this skill, an agent can use Wallaby's test execution, coverage analysis, execution traces, runtime inspection, and snapshot management while creating tests, implementing features, fixing bugs, or reviewing code.

This skill is model-invocable: the agent can select it automatically when relevant to the task. You can ask in plain language without typing `$wallaby-cli`, and the agent chooses the commands and runtime evidence it needs.

The skill can connect to an existing Wallaby session or start one in the background. Wallaby keeps results current as files change, so the agent can establish a baseline, check affected tests while editing, and verify the full project when needed. It starts with concise reports and opens details for a specific file, test, or source location as the investigation requires.

The agent can find which tests reach a line or expression, inspect uncovered branches, follow one test's execution across files, and capture runtime values without editing source code. Test timings, complexity, and change risk help it decide where to investigate further. These capabilities are available throughout a coding task and can also support workflows defined by other skills.

The examples below illustrate tasks the agent can carry out with `wallaby-cli`. They may come from a direct user request, instructions in another skill, or the agent's own reasoning about what to investigate or verify next.

### Plan changes and establish a baseline

```text
Before changing src/accounts.ts, establish a baseline of project-wide test results and coverage, and identify the tests that cover the file.
```

```text
Establish a baseline for the account changes by running only tests/accounts.spec.ts and tests/contracts.spec.ts. Report failures and coverage for that scope.
```

```text
Which tests reach the validation expression on line 42 of src/accounts.ts, and which outcomes remain uncovered?
```

### Write tests for new behavior and bug reports

```text
Write tests for the new account suspension behavior described in the requirements. Verify that they exercise the intended paths, assert the expected state changes, and keep coverage above 95% for the affected source files.
```

```text
Reproduce the expired-coupon bug in a regression test. Use its execution trace and runtime values to confirm that it fails for the reported reason.
```

### Investigate and fix tests

```text
Fix failing tests.
```

```text
Trace "coupon / rejects an expired coupon" in tests/coupon.spec.ts across setup and source files to explain its failure, fix the cause, and verify the result.
```

```text
The test "coupon / accepts a valid coupon" passes unexpectedly. Trace its execution and check whether it reaches the validation logic.
```

```text
Investigate the incorrect checkout total. Inspect discount and total in src/checkout.ts and taxRate in src/tax.ts, then focus on values from the failing test to find the cause.
```

```text
Update snapshots in tests/receipt.spec.ts and tests/invoice.spec.ts to reflect the intended formatting changes, then verify the affected tests.
```

### Review results and compare reports

```text
Read the saved baseline reports and compare them with the current full-project results. Show new or resolved failures and coverage changes for each affected source file and the project overall.
```

```text
List the tests in tests/accounts.spec.ts with their status, timing, errors, logs, and the source files they cover.
```

```text
Compare coverage gaps across src/accounts.ts, src/contracts.ts, and src/checkout.ts in one report, including uncovered lines and partially covered expressions.
```

```text
Show the top 10 slowest tests in the project, sorted by execution time, with their names, locations, and timings.
```

```text
Show the top 5 source files with the lowest test coverage, sorted from lowest to highest, with their coverage percentages.
```

### Project instructions

Add project-specific instructions to `AGENTS.md` to tell the agent which Wallaby configuration to use and when to use Wallaby instead of invoking Jest or Vitest directly. For example:

```markdown
Use the wallaby-cli skill to run, investigate, and verify unit tests for the project.
Use `./wallaby.unit.js` as the configuration file.
Use Wallaby for this test work instead of running Jest or Vitest directly, unless the user explicitly requests a direct run.
```

See [SKILL.md](skills/wallaby-cli/SKILL.md) for environment requirements, package-manager-specific invocation commands, and report documentation.
