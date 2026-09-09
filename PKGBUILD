# Maintainer: sykoh <local build, not published to any repository>
# Repackaged from OpenAI's official ChatGPT desktop client (.deb) for Arch Linux.
# Upstream: https://developers.openai.com/codex/app

pkgname=chatgpt
pkgver=26.903.61454
pkgrel=1
pkgdesc="ChatGPT by OpenAI — desktop client (local repack of upstream .deb)"
arch=('x86_64')
url="https://developers.openai.com/codex/app"
license=('custom:OpenAI')
depends=(
  gtk3 libnotify nss alsa-lib mesa at-spi2-core at-spi2-atk
  gdk-pixbuf2 libcups libusb pango cairo libxcb libxrandr libxss
  libxcursor libxtst systemd-libs dbus libdrm libxkbcommon
  libglvnd libx11 libxcomposite libxdamage libxfixes libxext
)
makedepends=(fakeroot)
optdepends=('git: for Git repository integration')
source=("chatgpt_amd64.deb")
sha256sums=('2caa7df314ce37e9048359d8e6a4a78e24574a3b54d6bf510f17754b66dda775')

# Do not strip the bundled Electron/Chromium binaries; they are already stripped
# upstream and stripping a ~300 MB app.asar/binary would be slow and unnecessary.
options=('!strip')

# The .deb ships an AppArmor profile; treat it as a config file so any local
# modifications survive later package upgrades.
backup=('etc/apparmor.d/chatgpt')

package() {
  cd "$srcdir"

  # Unpack the .deb payload into $pkgdir. The Debian control files and
  # Debian-specific lintian metadata are not needed on Arch.
  ar x chatgpt_amd64.deb
  tar xf data.tar.xz -C "$pkgdir"

  # Remove Debian-only metadata that has no meaning on Arch.
  rm -rf "$pkgdir/usr/share/lintian"

  # The .deb preserves permissions; be defensive that the entry points are
  # executable regardless of how the archive was built.
  chmod 755 \
    "$pkgdir/usr/lib/chatgpt/codex-launcher" \
    "$pkgdir/usr/lib/chatgpt/ChatGPT" \
    "$pkgdir/usr/lib/chatgpt/codex" \
    "$pkgdir/usr/lib/chatgpt/codex-code-mode-host"
}
