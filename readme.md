# renovate-asdf-graalvm-community-repro

Minimal reproduction: Renovate doesn't update GraalVM Community `java` pins (`graalvm-community-<version>`) in asdf's `.tool-versions`.

## Current behavior

Renovate skips every case below with `unsupported-datasource`, so these pins never get updates.

```json
{ "depName": "java", "skipReason": "unsupported-datasource" }
```

## Expected behavior

| case | pin | expected update | update type |
| --- | --- | --- | --- |
| `cases/25.0.1` | `java graalvm-community-25.0.1` | `java graalvm-community-25.0.5.0.3` | patch |
| `cases/25.0.4.1.1` | `java graalvm-community-25.0.4.1.1` | `java graalvm-community-25.0.5.0.3` | patch |
| `cases/22.0.2` | `java graalvm-community-22.0.2` | `java graalvm-community-25.0.5.0.3` | major |

- `25.0.5.0.3` is the newest build at the time of writing. GitHub tags these newer builds `graal-25.<n>.<update>` (e.g. `graal-25.5.5.0.3`), so mapping release tags to versions would stop at `25.0.2`.
- Every expected version exists in asdf-java's list of installable versions, so `asdf install` works: https://raw.githubusercontent.com/halcyon/asdf-java/master/data/jdk-linux-x86_64-ga.tsv
- `renovate.json` extends `config:recommended`. These versions are JDK versions, so `workarounds:javaLTSVersions` still applies: `22.0.2` goes straight to the LTS line 25 and skips 23 and 24.
- `renovate.json` sets `minimumReleaseAge`. The updates carry release timestamps, so they're not left pending.

## How to run

```sh
git clone https://github.com/MPV/renovate-asdf-graalvm-community-repro
cd renovate-asdf-graalvm-community-repro
LOG_LEVEL=debug pnpm dlx renovate --platform=local --dry-run=lookup
```

Look for the `packageFiles with updates` log entry and the `java` deps in it.

## Links

- Issue: https://github.com/renovatebot/renovate/issues/19183
- Pull request: https://github.com/renovatebot/renovate/pull/<PR number>
