pkgbase=transmission
pkgname=(transmission-cli)
pkgver=2.77.20210308
pkgrel=1
arch=(armv7h)
url="http://www.transmissionbt.com/"
license=(MIT)
pkgdesc="Fast, easy, and free BitTorrent client (CLI tools, daemon and web client) (cfpp2p mod)"
depends=(curl libevent)
makedepends=(git automake intltool)
conflicts=(transmission-cli)
replaces=(transmission-cli)
provides=(transmission-cli)
source=(git+https://github.com/cfpp2p/transmission.git#commit=2.77plus14734-19
        transmission-web-client::git+https://github.com/cfpp2p/transmission.git#commit=f104a68aa2a5b76914cdebaf7ded7ba09defd23b
        transmission-web-client.patch
        transmission-session.patch
        transmission-libsystemd.patch
        transmission-glib.patch
        transmission-autoconf-2.70.patch
        transmission.systemd
        transmission.sysusers
        transmission.tmpfiles)
sha256sums=("SKIP"
            "SKIP"
            "44d4fdd410223767efb508acd15377f0b6632679c900bb5c7ee1a39e544b6ad8"
            "876a7156a0a9657795d49b7f244dfc38dc0f9bb7a2264f8851129e172d2cf70b"
            "9f8f4bb532e0e46776dbd90e75557364f495ec95896ee35900ea222d69bda411"
            "2a4d4973f188f0a7008f06d8148ad25aba312fdf446d0c9206f2d67bb7f6310b"
            "10232d1f9518cd5f0c33b0b05f949670050b41af25bb3b51c81a59df7b564a66"
            "685fd0b51816da0dcb8c8a5b776187ae26306337c4effbfb277b2dd065caea10"
            "641310fb0590d40e00bea1b5b9c843953ab78edf019109f276be9c6a7bdaf5b2"
            "4b79c2ac1592fd54926f202c15183c4085f279ed1b673b81df2a6f5e82415f28")

prepare() {
  cd $pkgbase

  chmod +x ./third-party/miniupnp/updateminiupnpcstrings.sh
  chmod +x ./update-version-h.sh
  chmod +x ./autogen.sh

  patch -p1 -i "$srcdir/transmission-glib.patch"
  patch -p1 -i "$srcdir/transmission-libsystemd.patch"
  patch -p1 -i "$srcdir/transmission-session.patch"
  patch -p1 -i "$srcdir/transmission-autoconf-2.70.patch"

  cd "../transmission-web-client/web-client-cfp" \
    && patch -p1 -i "$srcdir/transmission-web-client.patch" && cd -
}

build() {
  cd $pkgbase

  ./autogen.sh

  CFLAGS="${CFLAGS//-Werror=format-security}" \
  ./configure \
    --prefix=/usr \
    --enable-utp \
    --enable-lightweight \
    --enable-cli \
    --enable-daemon

  make
}

package() {
  cd $pkgbase
  
  for dir in daemon cli utils; do
      make -C "$dir" DESTDIR="$pkgdir" install
  done

  mkdir -p "$pkgdir/usr/share/transmission/web"
  cp -rfT "$srcdir/transmission-web-client/web-client-cfp" \
    "$pkgdir/usr/share/transmission/web"
  chmod -R 0644 "$pkgdir/usr/share/transmission/web"
  chmod -R a+X "$pkgdir/usr/share/transmission/web"

  install -Dm644 "$srcdir/transmission.systemd" \
    "$pkgdir/usr/lib/systemd/system/transmission.service"
  install -Dm644 "./COPYING" \
    "$pkgdir/usr/share/licenses/transmission-cli/COPYING"

  install -Dm644 "$srcdir/transmission.sysusers" \
    "$pkgdir/usr/lib/sysusers.d/transmission.conf"
  install -Dm644 "$srcdir/transmission.tmpfiles" \
    "$pkgdir/usr/lib/tmpfiles.d/transmission.conf"
}
