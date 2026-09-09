# Maintainer: sykoh <local build, not published to any repository>
# ChatGPT desktop client, auto-fetched from OpenAI's official Linux .deb repo.
# Upstream: https://developers.openai.com/codex/app
# Official deb package manifest:
#   https://persistent.oaistatic.com/codex-app-prod/linux/deb/dists/stable/main/binary-amd64/Packages

pkgname=chatgpt
pkgver=26.903.61454
pkgrel=1
pkgdesc="ChatGPT by OpenAI — desktop client (auto-fetched from official .deb repo)"
arch=('x86_64')
url="https://developers.openai.com/codex/app"
license=('custom:OpenAI')
depends=(
  gtk3 libnotify nss nspr alsa-lib libpulse mesa at-spi2-core at-spi2-atk
  gdk-pixbuf2 libcups libusb pango cairo libxcb libxrandr libxss
  libxcursor libxtst systemd-libs dbus libdrm libxkbcommon
  libglvnd libx11 libxcomposite libxdamage libxfixes libxext
  glib2 expat vulkan-driver xdg-utils
)
makedepends=(curl)
optdepends=('git: for Git repository integration')
source=('chatgpt-launcher.sh')
sha256sums=('236e56c45557d86758ae79becbe5ef087094af6eacdecc32f953129aed3cad08')

options=('!strip')

# The .deb ships an AppArmor profile; treat it as a config file so any local
# modifications survive later package upgrades.
backup=('etc/apparmor.d/chatgpt')

# OpenAI official deb repository metadata (stable/amd64).
_upstream_pkgs_url="https://persistent.oaistatic.com/codex-app-prod/linux/deb/dists/stable/main/binary-amd64/Packages"
_upstream_deb_base="https://persistent.oaistatic.com/codex-app-prod/linux/deb/pool/main/c/chatgpt"

# Determine the latest upstream version from the official package manifest.
# Note: makepkg expands `source` at parse time, so a versioned source URL is
# not possible here; the .deb is instead fetched directly inside package().
pkgver() {
  curl -fsSL "$_upstream_pkgs_url" | awk '
    $1=="Package:" && $2=="chatgpt" { p=1; next }
    p && $1=="Version:" { print $2; exit }
  '
}

package() {
  cd "$srcdir"

  local deb="chatgpt_${pkgver}_amd64.deb"

  # Fetch the official SHA256 for exactly this version from the manifest.
  local sha
  sha="$(curl -fsSL "$_upstream_pkgs_url" | awk '
    $1=="Package:" && $2=="chatgpt" { p=1; next }
    p && $1=="SHA256:" { print $2; exit }
  ')"

  curl -fsSL -o "$deb" "${_upstream_deb_base}/${deb}"
  echo "${sha}  ${deb}" | sha256sum -c -

  # Extract the .deb payload into the package root.
  bsdtar -xOf "$deb" data.tar.xz | bsdtar --no-same-owner -xJf - -C "$pkgdir"

  # Install the custom launcher (supports ~/.config/chatgpt-flags.conf).
  install -Dm755 "${srcdir}/chatgpt-launcher.sh" \
    "${pkgdir}/usr/lib/chatgpt/codex-launcher"

  # Ship the license the Arch way.
  install -Dm644 "${pkgdir}/usr/share/doc/chatgpt/copyright" \
    "${pkgdir}/usr/share/licenses/${pkgname}/copyright"
  ln -sf /usr/lib/chatgpt/LICENSES.chromium.html \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSES.chromium.html"

  # Drop Debian-only metadata and doc files.
  rm -rf "${pkgdir}/usr/share/doc" "${pkgdir}/usr/share/lintian"

  # Defensive: ensure entry points are executable regardless of archive modes.
  chmod 755 \
    "${pkgdir}/usr/lib/chatgpt/codex-launcher" \
    "${pkgdir}/usr/lib/chatgpt/ChatGPT" \
    "${pkgdir}/usr/lib/chatgpt/resources/codex" \
    "${pkgdir}/usr/lib/chatgpt/resources/codex-code-mode-host"
}
