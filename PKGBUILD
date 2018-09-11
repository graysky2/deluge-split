# Maintainer: graysky <graysky AT archlinux DOT us>
# Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Hugo Doria <hugo@archlinux.org>

pkgbase=deluge-split
_pkgbase=deluge
pkgname=("${_pkgbase}-common" "${_pkgbase}-daemon" "${_pkgbase}-gtk" "${_pkgbase}-web" "${_pkgbase}-console")
pkgver=1.3.15+14+gb8e5ebe82
_major=1.3.15
pkgrel=2
arch=('any')
url="http://deluge-torrent.org/"
license=('GPL3')
makedepends=(intltool pygtk librsvg python2-mako git)
_commit=b8e5ebe8220c8365e186127e381a2d2dbaf9e460  # 1.3-stable
source=(
"git://git.deluge-torrent.org/deluge.git#commit=$_commit"
'untag-build.patch' 'deluged.service' 'deluge-web.service'
)
sha256sums=('SKIP'
            'fbd17f13765f5560bab01a81a42aff0f2f757a4a6fa29379ae31d95b9721e4f2'
            '58a451bb6cf4fe6ff78a4fb71d51c5910340a2de032ff435c3c7365015ab538f'
            'c3f2d6ad5bc9de5ffd9973d92badbe04a9ecf12c0c575e13d505a96add03275a')

prepare() {
  cd $_pkgbase
  patch -Np1 -i ../untag-build.patch
  sed -i '1s/python$/&2/' \
    deluge/ui/Win32IconImagePlugin.py \
    deluge/ui/web/gen_gettext.py
}

pkgver() {
  cd "$_pkgbase"
  git describe | sed 's/^deluge-//;s/-/+/g'
}

build() {
  cd "$_pkgbase"
  python2 setup.py build
}

package_deluge-common() {
  pkgdesc="A BitTorrent client with multiple user interfaces in a client/server model"
  depends=(python2-xdg                      python2-twisted python2-pyopenssl
  python2-chardet python2-setuptools python2-service-identity)
  conflicts=('deluge')
  groups=("$pkgbase")

  cd "$_pkgbase"
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1

  rm -rf "$pkgdir"/usr/bin/
  rm -rf "$pkgdir"/usr/share/
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge/ui/{web,gtkui,console}
}

package_deluge-daemon() {
  pkgdesc="Deluge backend"
  depends=(deluge-common libtorrent-rasterbar)
  conflicts=(deluge)
  groups=("$pkgbase")

  cd "$_pkgbase"
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1

  rm -f  "$pkgdir"/usr/bin/deluge-{gtk,web,console}
  rm -f  "$pkgdir"/usr/share/man/man1/deluge-{gtk,web,console}.1
  rm -rf "$pkgdir"/usr/lib/
  rm -rf "$pkgdir"/usr/share/{icons,pixmaps,applications}

  install -Dm644 ../deluged.service "$pkgdir"/usr/lib/systemd/system/deluged.service
  echo 'u deluge - "Deluge BitTorrent daemon" /srv/deluge' |
    install -Dm644 /dev/stdin "$pkgdir/usr/lib/sysusers.d/deluge.conf"
  echo 'd /srv/deluge 0775 deluge deluge' |
    install -Dm644 /dev/stdin "$pkgdir/usr/lib/tmpfiles.d/deluge.conf"
}

package_deluge-gtk() {
  pkgdesc="Deluge GTK frontend"
  depends=("deluge-common=${pkgver}" pygtk librsvg python2-notify)
  conflicts=(deluge)
  groups=("$pkgbase")
  optdepends=('python2-pygame: audible notifications'
              'python2-libappindicator: appindicator notifications')

  cd "$_pkgbase"
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1

  find   "$pkgdir"/usr/bin ! -name 'deluge-gtk' -type f -exec rm -f {} +
  find   "$pkgdir/usr/share/man/man1" ! -name 'deluge-gtk.1' -type f -exec rm -f {} +
  rm -r  "$pkgdir"/usr/lib/python2.7/site-packages/deluge/*.{py,pyc,pyo}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge/{i18n,data,core,plugins}
  rm -f  "$pkgdir"/usr/lib/python2.7/site-packages/deluge/ui/*.{py,pyc,pyo}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge/ui/{web,console}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge-$_major-py2.7.egg-info/
}

package_deluge-web() {
  pkgdesc="Deluge web frontend"
  depends=("deluge-common=${pkgver}" python2-mako)
  conflicts=(deluge)
  groups=("$pkgbase")

  cd "$_pkgbase"
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1

  find   "$pkgdir/usr/bin" ! -name 'deluge-web' -type f -exec rm -f {} +
  find   "$pkgdir/usr/share/man/man1" ! -name 'deluge-web.1' -type f -exec rm -f {} +
  rm -f  "$pkgdir"/usr/lib/python2.7/site-packages/deluge/*.{py,pyc,pyo}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge/{i18n,data,core,plugins}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge/ui/{gtkui,console}
  rm -f  "$pkgdir"/usr/lib/python2.7/site-packages/deluge/ui/*.{py,pyc,pyo}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge-$_major-py2.7.egg-info/
  rm -rf "$pkgdir"/usr/share/{icons,pixmaps,applications}

  install -Dm644 ../deluge-web.service "$pkgdir/usr/lib/systemd/system/deluge-web.service"
}

package_deluge-console() {
  pkgdesc="Deluge CLI frontend"
  depends=("deluge-common=${pkgver}")
  conflicts=(deluge)
  groups=("$pkgbase")

  cd "$_pkgbase"
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1

  find   "$pkgdir/usr/bin" ! -name 'deluge-console' -type f -exec rm -f {} +
  find   "$pkgdir/usr/share/man/man1" ! -name 'deluge-console.1' -type f -exec rm -f {} +
  rm -f  "$pkgdir"/usr/lib/python2.7/site-packages/deluge/*.{py,pyc,pyo}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge/{i18n,data,core,plugins}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge/ui/{gtkui,web}
  rm -f  "$pkgdir"/usr/lib/python2.7/site-packages/deluge/ui/*.{py,pyc,pyo}
  rm -rf "$pkgdir"/usr/lib/python2.7/site-packages/deluge-$_major-py2.7.egg-info/
  rm -rf "$pkgdir"/usr/share/{icons,pixmaps,applications}
}

# vim:set ts=2 sw=2 et:
