# Native Recursive Search for KDE KFileWidget

A downstream KDE Frameworks KIO patch that adds a native recursive filename search box directly to `KFileWidget`, the file picker used by KDE applications and the KDE XDG desktop portal.

## Features

- Native search field in the KFileWidget toolbar.
- `Ctrl+F` focus shortcut.
- 250 ms debounce.
- Recursive search via the existing `filenamesearch:` KIO worker from `kio-extras`.
- Searches from the current real folder.
- Clearing search restores the original folder.
- Synthetic search URLs stay out of navigation history.
- Hidden-file preference is honored.
- Search field focus is preserved during programmatic URL changes.

The final development iteration introduced `_kfilePreserveFocusOnUrlChange` and adjusted `enterUrl()` so the file view does not steal focus while the search field should remain active.

## Development target

Tested on Debian 13 / Plasma 6 with:

- `kf6-kio 6.13.0-6`
- `libkf6kiofilewidgets6 6.13.0-6`
- `kio-extras 4:25.04.3-1`

Modified source:

```text
src/filewidgets/kfilewidget.cpp
```

Final local patch:

```text
~/Documents/kfile-search-patch/kfile-native-search-v6.patch
```

## Debian build

Use a clean package rebuild:

```bash
cd kf6-kio-6.13.0
fakeroot debian/rules clean
DEB_BUILD_OPTIONS="nocheck parallel=$(nproc)" fakeroot debian/rules binary
sudo dpkg -i ../libkf6kiofilewidgets6_6.13.0-6_amd64.deb
```

Restart the KDE portal:

```bash
systemctl --user restart   plasma-xdg-desktop-portal-kde.service   xdg-desktop-portal.service
```

Do not use an incremental `-nc` rebuild while validating this patch; stale binaries caused misleading test results during development.

## Packaging recommendation

Use a local package suffix, e.g.:

```text
6.13.0-6+mbi1
```

Otherwise APT can silently replace the patched library with the official package of the same version.

## Contributions

Useful enhancement areas:

- rebasing to newer KF6/KIO;
- search result UX and filtering;
- accessibility;
- automated tests for focus/history/folder restoration;
- reducing private focus-management state;
- evaluating a clean upstream implementation.

## Licensing

This project is a patch against KDE KIO. Preserve the licensing terms and copyright notices of the upstream source being modified. Verify the exact source header before adding a standalone repository license.
