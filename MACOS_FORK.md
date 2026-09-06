# MeshMac fork and maintenance guide

MeshMac is an independent, third-party macOS client for Meshtastic radios. It is not the official Meshtastic Apple app
and is not endorsed by Meshtastic LLC. It remains GPL-3.0-or-later and preserves upstream copyright notices.

## Repository layout

- `origin`: `git@github.com:ChimeraX-CMRX/Meshtastic-macOS.git`
- `upstream`: `https://github.com/meshtastic/Meshtastic-Android.git`
- `main`: periodically refreshed integration branch
- `macos-poc`: initial independent macOS proof of concept

The fork deliberately keeps upstream's module and package layout. The independent identity is confined to the desktop
host and packaging wherever possible. This makes upstream merges much cheaper than extracting the KMP modules into a
new repository.

## Local build

Requirements are JDK 25 and Android SDK platform 36. Although the desktop code itself does not use Android APIs, the
current Gradle settings configure Android projects too, so keeping the SDK installed is the reliable path.

```bash
git clone git@github.com:ChimeraX-CMRX/Meshtastic-macOS.git
cd Meshtastic-macOS
git remote add upstream https://github.com/meshtastic/Meshtastic-Android.git
./gradlew :desktopApp:test
./gradlew :desktopApp:packageDistributionForCurrentOS
```

VS Code users can run the equivalent tasks from **Terminal -> Run Task**.

## Updating from upstream

Keep rebranding commits small and macOS-specific. Do not rename the shared `org.meshtastic.*` Kotlin packages or move
the upstream modules.

```bash
git fetch upstream
git switch main
git merge --ff-only upstream/main
git push origin main
git switch macos-poc
git rebase main
./gradlew :desktopApp:test :desktopApp:packageDistributionForCurrentOS
git push --force-with-lease origin macos-poc
```

If `main` gains fork-only commits later, replace the fast-forward with a normal merge and resolve conflicts in a short
pull request. Expected recurring conflicts are `desktopApp/build.gradle.kts`, `desktopApp/Main.kt`, `UpdateChecker.kt`,
the macOS workflow, and branding assets. Shared feature and transport code should normally merge unchanged.

## Independent identity

- Product name: `MeshMac`
- Bundle ID: `io.github.chimeraxcmrx.meshmac`
- URL scheme: `meshmac`
- Default data directory: `~/.meshmac`
- Update feed: releases from `ChimeraX-CMRX/Meshtastic-macOS`

Before public distribution, replace the inherited Meshtastic icon with clearly distinct artwork and complete Developer
ID signing and Apple notarisation. Never reuse Meshtastic LLC's signing identity or release credentials.
