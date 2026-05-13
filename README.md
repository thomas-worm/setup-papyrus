# setup-papyrus (GitHub action)

This action downloads and caches the Papyrus-Desktop RCP
(which bundles the JustJ Java 21 JRE) and installs
the OS packages SWT/Xvfb need.

Tested against Papyrus-Desktop **7.1.0** (Eclipse 2025-06). The
`papyrus-version` / `release-train` inputs let you pin to other versions;

## Quick start

You can use the setup action in your workflows:

```yaml
jobs:
  somethingToDo:
    runs-on: ubuntu-latest
    steps:
      - uses: thomas-worm/setup-papyrus@v1
```

## Inputs

| Input             | Default    | Description |
| ----------------- | ---------- | --- |
| `papyrus-version` | `7.1.0`    | Papyrus-Desktop version. |
| `release-train`   | `2025-06`  | Eclipse release train this version was built against. |
| `cache`           | `true`     | Cache the extracted install across runs. Disable only if you're debugging the download step itself. |

## Outputs

| Output         | Description |
| -------------- | --- |
| `papyrus-home` | Absolute path to the install. |

Notes:
- Only Linux x86_64 is implemented today.
- The RCP bundles JustJ Java 21, so no separate `setup-java` step is
  needed.
- The first run takes ~2-3 minutes for the ~400 MB download. Cache hits
  bring it down to a few seconds.
