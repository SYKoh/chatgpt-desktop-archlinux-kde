# ChatGPT Desktop Client — Local Arch Packaging (Auto-fetch Latest)

Repackages OpenAI's official ChatGPT desktop client (`.deb`) into a native Arch Linux package (`.pkg.tar.zst`).
**For local use only — this project does not maintain any Arch/AUR repository.**

> **English** | [简体中文](./README.zh-CN.md)

## Table of contents

- [Features](#features)
- [Build](#build-no-root-required)
- [Install](#install-requires-root--run-in-your-own-terminal)
- [Uninstall](#uninstall)
- [Update](#update)
- [Optional configuration](#optional-configuration)
- [Credits](#credits)

## Overview

- Upstream: <https://developers.openai.com/codex/app>
- Official deb manifest: `https://persistent.oaistatic.com/codex-app-prod/linux/deb/dists/stable/main/binary-amd64/Packages`
- Structure: Electron app, executable `ChatGPT` (based on Codex), menu entry `chatgpt`.
- Reference: <https://aur.archlinux.org/packages/chatgpt-desktop>

## Features

- **Auto-fetch latest version**: `pkgver()` reads the newest version from the official manifest on every build; `package()` downloads the matching `.deb` with `curl` and verifies it against the `SHA256` listed in the official `Packages` manifest — a real integrity check, not a makepkg `SKIP`.
- **Custom launcher**: supports `~/.config/chatgpt-flags.conf` (one Chromium/Electron flag per line, `#` for comments).
- Standard Arch installation: `/usr/bin/chatgpt` → `../lib/chatgpt/codex-launcher`; license under `/usr/share/licenses/chatgpt/`; Debian-only `lintian` and `doc` removed.

## Build (no root required)

```sh
makepkg --config ./makepkg.local.conf
```

> Note: makepkg 7.1.0 uses fakeroot automatically at packaging time so file ownership becomes root.
> `./makepkg.local.conf` only sets a clean `PACKAGER` (it sources the system default config first).
> An internet connection is required (fetches the latest `.deb` from OpenAI's official CDN).

## Install (requires root — run in your own terminal)

```sh
sudo pacman -U ./chatgpt-<version>-1-x86_64.pkg.tar.zst
```

## Uninstall

```sh
sudo pacman -R chatgpt
```

## Update

No files need to be changed — just rebuild to follow the latest version:

```sh
makepkg --config ./makepkg.local.conf
sudo pacman -U ./chatgpt-*-1-x86_64.pkg.tar.zst
```

## Optional configuration

- Custom launch flags: write to `~/.config/chatgpt-flags.conf`, for example:

  ```
  --force-device-scale-factor=1.25
  --disable-gpu
  ```

- The package ships `/etc/apparmor.d/chatgpt` (protected via `backup=`). AppArmor is not enabled by default on Arch; it is present only for compatibility.
- The `codex` binary is provided by the app itself (`x-scheme-handler/codex` protocol handler) and is not exposed as a standalone command.

## Credits

This repository's code was authored with the assistance of **DeepSeek**.
