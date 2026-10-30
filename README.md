# deb2appimage

Convert a Debian `.deb` package into a portable `.AppImage`, install existing AppImages, and uninstall AppImages cleanly.

This script is built for [Omarchy Linux](https://omarchy.org) (Arch based). It uses `gum` for its
terminal UI and Arch package names in the dependency list below. It should work on other
Arch-based systems, but only Omarchy is supported.

## Requirements

Install these before first run:

```sh
sudo pacman -S --needed gum binutils curl tar findutils coreutils xz zstd desktop-file-utils gtk-update-icon-cache
```

| Dependency | Used for |
| --- | --- |
| `gum` | prompts, pickers, status output |
| `binutils` (`ar`) | unpacking the `.deb` archive |
| `curl` | first-run download of `appimagetool` |
| `tar`, `xz`, `zstd` | extracting `control.tar.*` and `data.tar.*` |
| `findutils`, `coreutils` | file discovery, `base64`, `mktemp`, `sed` |
| `desktop-file-utils` | refreshing the desktop database |
| `gtk-update-icon-cache` | refreshing the icon cache |

`appimagetool` is not a manual dependency. The script downloads it once to
`~/.cache/deb2appimage/` (~15 MB) and asks before doing so. Set `--yes` to skip the question.

x86_64 only.

## Install

```sh
install -Dm755 deb2appimage ~/.local/bin/deb2appimage
```

Make sure `~/.local/bin` is in your `PATH`.

## Usage

```sh
deb2appimage                 # pick a .deb interactively
deb2appimage package.deb     # convert a specific package
deb2appimage --yes file.deb  # no prompts
deb2appimage install         # pick an AppImage to install
deb2appimage install --yes ~/Downloads/foo.AppImage
deb2appimage uninstall       # pick an AppImage to remove
deb2appimage uninstall --yes ~/Applications/foo.AppImage
```

## What it does

1. Extracts the `.deb` with `ar` and `tar`.
2. Finds the `.desktop` entry, executable, and best icon inside the package.
3. Builds an `AppDir` with a generated `AppRun`.
4. Runs `appimagetool` and writes `<package>-<version>-x86_64.AppImage` to `~/Applications`.
5. Adds a launcher entry and icon under `~/.local/share`.

If the `.deb` already contains a ready-made AppImage (for example Cisco Packet Tracer), the script
extracts it unchanged instead of rebuilding it.

The converted app still needs its system libraries. Debian dependencies are **not** bundled.

`install` copies an existing AppImage into `~/Applications`, reads its bundled desktop entry and icon,
and adds a launcher entry. It does not run the app.

`uninstall` deletes the AppImage, its launcher entry, and its icon. It accepts an explicit path outside
`~/Applications` too. App data in `~/.local` and `~/.config` is left alone.

## License

MIT. See [LICENSE](LICENSE).
