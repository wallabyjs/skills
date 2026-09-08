# Coverage-gaps report

`analyze --target=files` writes the complete batch result to `coverage-gaps.md`. The terminal output is an index; this artifact is the source of truth for which requested files were analyzed and which line-level gaps Wallaby found in them.

## Selection modes

- `analyze --target=files` and `analyze --target=files "{paths:[]}"` automatically select up to 20 source files with the lowest coverage in the current Wallaby state.
- `analyze --target=files "{paths:['src/a.ts','src/b.ts']}"` analyzes the explicit source or test paths.
- Automatic selection is a bounded shortlist. It does not mean every source file below a threshold, every high-CRAP file, or every relevant repository file was analyzed.
- The target supports paths only. Use `--target=file` for a location or test filter.

## Report shape

The report starts with:

```md
# Files Analysis

- Files analyzed: <count>
- Files with gaps: <count>
- Files with unavailable coverage: <count>
```

If one or more requested paths could not be analyzed, the report then includes every path-specific failure:

```md
### Files Analysis Errors

<formatted errors>
```

Valid paths still have file sections. Treat the batch as complete only after checking the errors and confirming that every intended path has a matching section.

### Source-file section

```md
## File: src/accounts.ts

- Coverage: <percent>%
- Change risk anti-patterns: <number>
- Covering tests: <count>

[Covering Tests](file-src@saccounts.ts.md)
[Detailed File Coverage](file-src@saccounts.ts.wcov)

### Coverage Gaps

- Lines: 43-45, 78

- Line 91: columns 12-28 `uncoveredExpression`
```

`Lines` combines consecutive fully uncovered lines into inclusive ranges. Each partially covered expression is listed separately with its source line and uncovered column range. A partial line whose precise range is unavailable is written as `uncovered expression range unavailable`.

When Wallaby has coverage but finds no gaps, the section says `No coverage gaps.` When coverage cannot be reported, it says `Coverage is unavailable.`

The linked Markdown artifact contains the full covering-test inventory. The linked `.wcov` artifact contains the complete source with coverage annotations. Their encoded names are documented in `file-tests.md` and `wcov.md`.

### Test-file section

```md
## File: tests/accounts.spec.ts

- Tests: <count>
- Failed: <count>

[Tests](file-tests@saccounts.spec.ts.md)
[Detailed File Coverage](file-tests@saccounts.spec.ts.wcov)
```

Test-file sections do not contain `Coverage Gaps`.

## Terminal summary

The inline report starts with `## Files Analysis`, repeats the three batch counts, and links to `coverage-gaps.md` as `Coverage Gaps`.

For explicit batches, its `File | Coverage | CRAP | Gaps` table includes source files with gaps. `Gaps` counts lines whose status is `none` or `partial`, not the number of uncovered expression ranges. When more than 20 source files have gaps, the table shows 20 files ordered by higher CRAP, lower coverage, and then path, while the artifact retains every analyzed file. Automatic selection shows all selected source files, including files with zero gaps. Test files use a separate `Test File | Tests | Failed` table.

The terminal shows at most three path errors. The artifact contains the complete error list.

## Navigate a large report

List all section starts:

```sh
rg -n '^## File: ' coverage-gaps.md
```

Find an exact file heading:

```sh
rg -n -F '## File: src/accounts.ts' coverage-gaps.md
```

Print only that file's section, stopping at the next file heading:

```sh
awk -v target='src/accounts.ts' '
  $0 == "## File: " target { found=1 }
  found && /^## File: / && $0 != "## File: " target { exit }
  found { print }
' coverage-gaps.md
```

After locating the gaps, read those ranges from the source file and inspect the related tests. Open the linked `.wcov` only when the complete annotated source or surrounding coverage state is needed.
