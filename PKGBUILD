# Maintainer: graysky <graysky AT archlinux DOT us>
# Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Hugo Doria <hugo@archlinux.org>

pkgbase=deluge-split
_pkgbase=deluge
pkgname=("${_pkgbase}-common" "${_pkgbase}-daemon" "${_pkgbase}-gtk" "${_pkgbase}-web" "${_pkgbase}-console")
pkgver=2.0.3
pkgrel=1
arch=('any')
url="https://deluge-torrent.org/"
license=('GPL3')
makedepends=(
  python-setuptools
  intltool
  gtk3
  python-gobject
  python-cairo
  librsvg
  libappindicator-gtk3
  #python-pygame
  libnotify
)
source=(https://ftp.osuosl.org/pub/deluge/source/2.0/$_pkgbase-$pkgver.tar.xz)
sha256sums=('7e7ae8e6ca2a2bf0d487227cecf81e27332f0b92b567cc2bda38e47d859da891')

build() {
  cd "$_pkgbase-$pkgver"
  python setup.py build

  # this is the most simple hack I can think up for splitting
  # if you know of a more elegant or cleaner way, send a PR please
  mkdir ../staging
  python setup.py install --root="../staging" --optimize=1 --skip-build
}

package_deluge-common() {
  pkgdesc="A BitTorrent client with multiple user interfaces in a client/server model"
  depends=(
  # Follows DEPENDS.md
  'python-twisted>=17.1' python-service-identity python-idna
  'openssl>=1.0.1'
  python-pyopenssl
  'python-rencode>=1.0.2'
  python-xdg
  xdg-utils
  python-six
  'python-zope-interface>=4.4.2'
  python-chardet
  python-setproctitle
  python-pillow
  python-dbus
  python-mako)
  optdepends=('python-distro: OS platform information')
  conflicts=('deluge')
  groups=("$pkgbase")

  cd "staging"

  install -pd "$pkgdir/usr/bin"
  mv usr/bin/deluge "$pkgdir/usr/bin"

  install -pd "$pkgdir/usr/share/man/man1"
  mv usr/share/man/man1/deluge.1 "$pkgdir/usr/share/man/man1"

  install -pd "$pkgdir/usr/lib/python3.7/site-packages/deluge-2.0.3-py3.7.egg-info"
  mv usr/lib/python3.7/site-packages/deluge-2.0.3-py3.7.egg-info/ "$pkgdir/usr/lib/python3.7/site-packages"

  install -pd "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
  mv usr/lib/python3.7/site-packages/deluge/core "$pkgdir/usr/lib/python3.7/site-packages/deluge"
  mv usr/lib/python3.7/site-packages/deluge/i18n "$pkgdir/usr/lib/python3.7/site-packages/deluge"
  mv usr/lib/python3.7/site-packages/deluge/plugins "$pkgdir/usr/lib/python3.7/site-packages/deluge"
  mv usr/lib/python3.7/site-packages/deluge/__pycache__ "$pkgdir/usr/lib/python3.7/site-packages/deluge"
  mv usr/lib/python3.7/site-packages/deluge/*.py "$pkgdir/usr/lib/python3.7/site-packages/deluge"

  mv usr/lib/python3.7/site-packages/deluge/ui/data "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
  mv usr/lib/python3.7/site-packages/deluge/ui/__pycache__ "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
  mv usr/lib/python3.7/site-packages/deluge/ui/*.py "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
}

package_deluge-daemon() {
  pkgdesc="Deluge backend"
  depends=(deluge-common 'libtorrent-rasterbar>=1.1.1' 'python-geoip')
  conflicts=(deluge)
  groups=("$pkgbase")

  cd "staging"

  install -pd "$pkgdir/usr/bin"
  mv usr/bin/deluged "$pkgdir/usr/bin"

  install -pd "$pkgdir/usr/share/man/man1"
  mv usr/share/man/man1/deluged.1 "$pkgdir/usr/share/man/man1"

  cd "../$_pkgbase-$pkgver"

  install -Dt "$pkgdir/usr/lib/systemd/system" \
    -m644 packaging/systemd/deluged.service
  install -Dt "$pkgdir/usr/lib/systemd/system/deluged.service.d" \
    -m644 packaging/systemd/user.conf

  echo 'u deluge - "Deluge BitTorrent daemon" /srv/deluge' |
    install -Dm644 /dev/stdin "$pkgdir/usr/lib/sysusers.d/$pkgname.conf"
  echo 'd /srv/deluge 0770 deluge deluge' |
    install -Dm644 /dev/stdin "$pkgdir/usr/lib/tmpfiles.d/$pkgname.conf"
}

package_deluge-gtk() {
  pkgdesc="Deluge GTK frontend"
  depends=("deluge-common=${pkgver}"
  gtk3
  python-gobject
  python-cairo
  librsvg)
  optdepends=('libappindicator-gtk3: allow menu via Ayatana indicators'
  #'python-pygame: audible notifications'
  'libnotify: desktop notifications')
  conflicts=(deluge)
  groups=("$pkgbase")

  cd "staging"

  install -pd "$pkgdir/usr/bin"
  mv usr/bin/deluge-gtk "$pkgdir/usr/bin"

  install -pd "$pkgdir/usr/share/man/man1"
  mv usr/share/man/man1/deluge-gtk.1 "$pkgdir/usr/share/man/man1"

  mv usr/share/appdata "$pkgdir/usr/share"
  mv usr/share/applications "$pkgdir/usr/share"
  mv usr/share/icons "$pkgdir/usr/share"
  mv usr/share/pixmaps "$pkgdir/usr/share"

  install -pd "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
  mv usr/lib/python3.7/site-packages/deluge/ui/gtk3 "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
}

package_deluge-web() {
  pkgdesc="Deluge web frontend"
  depends=("deluge-common=${pkgver}")
  conflicts=(deluge)
  groups=("$pkgbase")

  cd "staging"

  install -pd "$pkgdir/usr/bin"
  mv usr/bin/deluge-web "$pkgdir/usr/bin"

  install -pd "$pkgdir/usr/share/man/man1"
  mv usr/share/man/man1/deluge-web.1 "$pkgdir/usr/share/man/man1"

  install -pd "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
  mv usr/lib/python3.7/site-packages/deluge/ui/web "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
}

package_deluge-console() {
  pkgdesc="Deluge CLI frontend"
  depends=("deluge-common=${pkgver}" 'libtorrent-rasterbar>=1.1.1')
  conflicts=(deluge)
  groups=("$pkgbase")

  cd "staging"

  install -pd "$pkgdir/usr/bin"
  mv usr/bin/deluge-console "$pkgdir/usr/bin"

  install -pd "$pkgdir/usr/share/man/man1"
  mv usr/share/man/man1/deluge-console.1 "$pkgdir/usr/share/man/man1"

  install -pd "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
  mv usr/lib/python3.7/site-packages/deluge/ui/console "$pkgdir/usr/lib/python3.7/site-packages/deluge/ui"
}

# vim:set ts=2 sw=2 et:
