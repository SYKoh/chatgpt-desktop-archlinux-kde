# ChatGPT 桌面客户端 — 本地 Arch 打包（自动拉取最新版）

把 OpenAI 官方发布的 ChatGPT 桌面客户端（`.deb`）重打为 Arch Linux 本地包（`.pkg.tar.zst`）。
**纯本机使用，不维护任何 Arch/AUR 仓库。**

- 上游：https://developers.openai.com/codex/app
- 官方 deb 清单：`https://persistent.oaistatic.com/codex-app-prod/linux/deb/dists/stable/main/binary-amd64/Packages`
- 结构：Electron 应用，可执行文件 `ChatGPT`（基于 Codex），菜单入口 `chatgpt`。
- 参考 https://aur.archlinux.org/packages/chatgpt-desktop

## 特性

- **自动拉取最新版**：`pkgver()` 每次从官方清单读取最新版本号；`package()` 直接用
  `curl` 下载对应 `.deb`，并按官方 `Packages` 清单里的 `SHA256` 校验（不做 makepkg 的
  `SKIP`，是真实指纹校验）。
- **自定义启动器**：支持 `~/.config/chatgpt-flags.conf`（每行一个 Chromium/Electron 参数，`#` 注释）。
- Arch 标准安装：`/usr/bin/chatgpt` → `../lib/chatgpt/codex-launcher`，许可证放到
  `/usr/share/licenses/chatgpt/`，剔除 Debian 专用 `lintian` 与 `doc`。

## 构建（不需要 root）

```sh
makepkg --config ./makepkg.local.conf
```

> 说明：makepkg 7.1.0 在打包阶段自动使用 fakeroot，使文件属主为 root。
> `./makepkg.local.conf` 仅用于设置干净的 `PACKAGER` 信息（会先 source 系统默认配置）。
> 构建时需联网（从 OpenAI 官方 CDN 拉取最新 `.deb`）。

## 安装（需要 root，在自己的终端执行）

```sh
sudo pacman -U ./chatgpt-<版本>-1-x86_64.pkg.tar.zst
```

## 卸载

```sh
sudo pacman -R chatgpt
```

## 更新

无需改任何文件，重新构建即可自动跟随最新版：

```sh
makepkg --config ./makepkg.local.conf
sudo pacman -U ./chatgpt-*-1-x86_64.pkg.tar.zst
```

## 可选配置

- 自定义启动参数：写入 `~/.config/chatgpt-flags.conf`，例如：
  ```
  --force-device-scale-factor=1.25
  --disable-gpu
  ```
- 包内含 `/etc/apparmor.d/chatgpt`（`backup=` 保护）。Arch 默认未启用 AppArmor，仅为兼容存在。
- `codex` 二进制是应用自带的（`x-scheme-handler/codex` 协议处理），不作为独立命令暴露。
