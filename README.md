# setup-papyrus (GitHub action)

Downloads and caches the Papyrus-Desktop RCP (bundles JustJ Java 21 JRE),
installs the OS-level prerequisites that headless SWT use needs, and
exposes the install path.

Tested against Papyrus-Desktop **7.1.0** (Eclipse 2025-06). The
`papyrus-version` / `release-train` inputs let you pin to other versions.

## Supported runners

| Runner OS               | Architecture | Archive                                |
| ----------------------- | ------------ | -------------------------------------- |
| `ubuntu-latest` (Linux) | x86_64       | `…linux.gtk.x86_64.tar.gz`             |
| `macos-latest` (macOS)  | aarch64 / x86_64 | `…macosx.cocoa.{aarch64,x86_64}.dmg` |
| `windows-latest` (Win)  | x86_64       | `…win32.win32.x86_64.zip`              |

Linux runners additionally get a font set wide enough to render
macOS-authored models (`fonts-liberation`, `fonts-dejavu`, `fonts-noto`,
`fonts-crosextra-carlito`, `fonts-crosextra-caladea`,
`ttf-mscorefonts-installer`) plus the SWT runtime libraries (`libgtk-3-0`,
`libxrender1`, etc.) and `xvfb` / `x11-utils`. macOS and Windows runners
already ship enough fonts and a native display server, so no additional
packages are installed there.

## Quick start

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: thomas-worm/setup-papyrus@v1
        id: papyrus
      - run: echo "Papyrus is at ${{ steps.papyrus.outputs.papyrus-home }}"
```

## Inputs

| Input             | Default    | Description |
| ----------------- | ---------- | --- |
| `papyrus-version` | `7.1.0`    | Papyrus-Desktop version. |
| `release-train`   | `2025-06`  | Eclipse release train this version was built against. |
| `cache`           | `true`     | Cache the extracted install across runs. Disable only if you're debugging the download step itself. |

## Outputs

| Output             | Description |
| ------------------ | --- |
| `papyrus-home`     | Path containing the plugins folder and the JRE (`Contents/Eclipse` inside the macOS `.app`, the install root on Linux/Windows). |
| `papyrus-launcher` | Path to the Papyrus-Desktop native launcher (`papyrus-desktop` / `Papyrus-Desktop.app/Contents/MacOS/papyrus-desktop` / `papyrus-desktop.exe`). |

## Notes

- The RCP bundles JustJ Java 21, so no separate `setup-java` step is
  needed.
- The first run takes ~2-3 minutes for the ~800 MB download. Cache hits
  bring it down to a few seconds.
- The cache key is scoped per OS+arch, so caches don't collide across
  runners in a matrix job.
- macOS `.dmg` archives are mounted with `hdiutil`, copied out, and the
  quarantine flag is cleared so the launcher can start non-interactively.
