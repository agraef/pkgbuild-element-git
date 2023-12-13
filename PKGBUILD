# Maintainer: Albert Gräf <aggraef@gmail.com>
# Contributor: Christopher Arndt <aur -at- chrisarndt -dot- de>
# Contributor: Milk Brewster <aurentry@milkmiruku.com>

_pkgname=element
pkgname=$_pkgname-git
pkgver=1.0.0.175.g1cfa4191
pkgrel=1
pkgdesc='A modular audio plugin host (git version)'
arch=(x86_64)
url='https://kushview.net/element/'
license=(GPL3)
groups=(pro-audio)
depends=(gcc-libs hicolor-icon-theme)
makedepends=(alsa-lib boost git ladspa lilv lv2 suil python)
optdepends=('lua: for LUA scripting')
provides=($_pkgname ladspa-host lv2-host vst3-host)
conflicts=($_pkgname)
source=("$_pkgname::git+https://gitlab.com/kushview/element.git")
md5sums=('SKIP')

pkgver() {
  cd $_pkgname
  ( set -o pipefail
    printf "%s.g%s" "$(python3 util/version.py --build | sed -e 's/-/./')" "$(python3 util/version.py --short-hash)" ||
    git describe --long --tags 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
  )
}

build() {
  # If you have the vst2sdk package from the AUR installed, we enable its use,
  # so that the host has support for VST2 and so that the Element VST plugins
  # can be included. NB1: If you have the VST2 SDK installed in another
  # location, just change the vst2path variable below accordingly. NB2: I
  # haven't been able to figure out which version of the VST3 SDK is required
  # to make the build of the Element VST3 plugins work on Linux, so these
  # aren't enabled for now.
  vst2path=/usr/include/vst36
  if [ -d $vst2path ]; then meson $_pkgname $_pkgname-build --prefix /usr -Delement-plugins=enabled -Dvst2sdk=$vst2path; else meson $_pkgname $_pkgname-build --prefix /usr; fi
  meson compile -C $_pkgname-build
}

package() {
  depends+=(libasound.so libcurl.so libfreetype.so)
  meson install -C $_pkgname-build --destdir "$pkgdir"
  # For some reason, the Element plugins are placed into
  # /usr/packages/net.kushview.element.vst/data, and there doesn't seem to be
  # a meson variable to change that path. So we move them into the proper
  # location under /usr/lib/vst if we have them.
  if [ -d "$pkgdir/usr/packages/net.kushview.element.vst" ]; then mkdir -p "$pkgdir/usr/lib/vst" && mv "$pkgdir/usr/packages/net.kushview.element.vst/data" "$pkgdir/usr/lib/vst/net.kushview.element.vst" && rm -rf "$pkgdir/usr/packages"; fi
}
