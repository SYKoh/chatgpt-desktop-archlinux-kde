# ChatGPT 桌面客户端 — 本地 Arch 打包

把 OpenAI 官方发布的 `chatgpt_amd64.deb` 重打为 Arch Linux 本地包（`.pkg.tar.zst`）。
**纯本机使用，不维护任何 Arch/AUR 仓库。**

- 上游：https://developers.openai.com/codex/app
- 打包版本：`26.903.61454`
- 结构：Electron 应用，可执行文件 `ChatGPT`（基于 Codex），菜单入口 `chatgpt`。

## 构建

```sh
# 首次：在 PKGBUILD 同目录放好 chatgpt_amd64.deb（已在本目录）
makepkg --config ./makepkg.local.conf
```

说明：makepkg 7.1.0 在打包阶段自动使用 fakeroot，使文件属主为 root。
`./makepkg.local.conf` 仅用于设置干净的 `PACKAGER` 信息（会先 source 系统默认配置）。

## 安装（需要 root，在自己的终端执行）

```sh
sudo pacman -U ./chatgpt-26.903.61454-1-x86_64.pkg.tar.zst
```

## 卸载

```sh
sudo pacman -R chatgpt
```

## 更新

1. 下载新版本 `.deb`，替换本目录同名文件；
2. 更新 `PKGBUILD` 中的 `pkgver` 与 `sha256sums`（`sha256sum chatgpt_amd64.deb`）；
3. 重新 `makepkg --config ./makepkg.local.conf` 并 `pacman -U`。

## 备注

- 包内含 `/etc/apparmor.d/chatgpt` 配置文件（`backup=` 保护）。Arch 默认未启用
  AppArmor，该文件仅为兼容存在，不影响使用。
- `codex` 二进制是应用自带的（`x-scheme-handler/codex` 协议处理），不作为独立命令暴露。
