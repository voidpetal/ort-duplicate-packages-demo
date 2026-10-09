# ORT Duplicate Package Reproducer

Root uses Yarn Classic. `npm-project` uses NPM. Both resolve
`NPM::picocolors:1.1.1`.

`tools/npm` delegates to NPM, but removes `repository.type` only from:

```text
npm info --json picocolors@1.1.1
```

NPM then reports raw VCS type `UNKNOWN`; Yarn reports `GIT`. All remaining
package metadata matches. This reproduces metadata divergence.

## Reproduce

Requirements: JDK 25, NPM, Yarn Classic, `jq`, and an ORT checkout at `96.0.0`.

```bash
DEMO_DIR="$(pwd)"
cd /path/to/ort-96.0.0
REAL_NPM="$(command -v npm)" \
PATH="$DEMO_DIR/tools:$PATH" \
./gradlew :cli:run --args="analyze -i $DEMO_DIR -o $DEMO_DIR/out -f JSON"
```

Expected result:

```text
Found 1 NPM definition file(s)
Found 1 Yarn definition file(s)
IllegalArgumentException: Unable to create the AnalyzerResult as it contains
projects and / or packages with the same ids
```
