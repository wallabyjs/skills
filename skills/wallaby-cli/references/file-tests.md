# File-analysis test artifacts

`analyze --target=file` keeps its terminal response compact by writing the complete related-test inventory to a separate Markdown artifact. The main report links it as `Covering Tests` for a source file or `Tests` for a test file and shows its size. The generated file name is derived from the analyzed path by replacing path separators with `-` and appending `.md`; for example, `src/core.ts` becomes `src-core.ts.md`.

Open this artifact only when the related test identities or details are needed. The test count and other file metrics are already present in the main report. For a large artifact, search for the relevant test name or field instead of reading the whole file.

Both artifact variants start with analysis metadata:

```md
- path: <target-file-path>
- location: line <line>, column <column>
- tests: <count>
- tests: <count>/<failed-count>
- tests: <count> (filter applied: "<test-name>" (<test-file-path>))
- tests: <count> (filter applied: "<test-name>" (<test-file-path>)) but no tests found
- lines: <line-count>
- coverage: <percent>%
- change risk anti-patterns: <number>
- size: <size>
```

Format details:

- `- path:` is always present.
- `- location:` appears when the analyze target included a resolved location.
- `- tests:` is always present. When one or more listed tests failed, the value is `<total>/<failed>`.
- A filter note appears when `--test` or `target.test` was used.
- `- lines:`, `- coverage:`, `- change risk anti-patterns:`, and `- size:` appear when Wallaby has those values for the target.
- Source-file analysis can include coverage and change-risk metrics. Test-file analysis usually includes line count and size.

## Source-file artifact

The `Covering Tests` link for a source file uses this shape:

```md
# File Analysis

- path: <source-file-path>
- tests: <count>
- lines: <line-count>
- coverage: <percent>%
- change risk anti-patterns: <number>
- size: <size>

## Covering Tests

### <test name>
- status: passed|failed|skipped|todo|disabled
- loc: <test-file-path>:<line>
- time: <time>ms
```

The inventory is a flat sequence of all tests that cover the source target. It is not grouped by test file. Each entry contains the fields available for that test; status is always present, while location and time depend on runner data.

## Test-file artifact

The `Tests` link for a test file uses this shape:

````md
# Test File Analysis

- path: <test-file-path>
- tests: <count>
- lines: <line-count>
- size: <size>

Top 5 tests by execution time:
- <test name> (<time>ms, <test-file-path>:<line>)
- ...

## Tests

### <test name>
- status: passed|failed|skipped|todo|disabled
- loc: <test-file-path>:<line>
- time: <time>ms

```
<error message or formatted assertion output>
```
Stack trace:
- <stack-file-path>:<line>
  `<stack-context-content>`
  `<stack-context-code>`

#### Logs
- loc: <log-file>:<line>
- context: ``` <runtime context> ```
```
<log message>
```

#### Covered Files
- <covered-source-file>
- ...
````

The ranked section is omitted when no ranking is available. Test entries are not grouped by file because every listed test belongs to the analyzed test file. Entry fields are shown when the formatter has data for them; `Covered Files` identifies the source files reached by each test.

## Filters and locations

When `--test` or `target.test` filters file analysis, the metadata records the filter and the artifact lists only the matching test. A resolved source location appears in metadata as `- location: line <line>, column <column>`. Filtered analysis omits ranked summaries.

If no tests are found, the report contains metadata followed by:

```md
No tests found.
```
