# $Id: PKGBUILD 139147 2015-09-03 17:29:59Z anatolik $
# Maintainer: Timothy Redaelli <timothy.redaelli@gmail.com>
# Contributor: Guillaume ALAUX <guillaume@archlinux.org>
# Contributor: Florian Pritz <bluewind at jabber dot ccc dot de>
pkgname=('wireshark-cli' 'wireshark-gtk' 'wireshark-qt')
pkgbase=wireshark
pkgver=2.1.0.6bdfa95
pkgrel=1
arch=('i686' 'x86_64')
license=('GPL2')
makedepends=('qt5-base' 'gtk3' 'krb5' 'libpcap' 'bash' 'gnutls' 'portaudio'
             'lua52' 'python' 'desktop-file-utils' 'hicolor-icon-theme')
url='http://www.wireshark.org/'
source=("${pkgbase}::git+https://code.wireshark.org/git/wireshark"
        "disable-c++-compat.patch"
        "fix-voip-player-crash.patch")
sha1sums=('SKIP'
          '366fcfe94fd44396726397ede241faf929c234e4'
          '6bfce2a6dc20895a55a45923949ed2762593bb15')

prepare() {
  cd ${pkgbase}
  sed -i 's/$(AM_V_RCC)rcc/&-qt4/p' ui/qt/Makefile.am
  patch -Np1 -i ../disable-c++-compat.patch
  patch -Np1 -i ../fix-voip-player-crash.patch
}

build() {
  cd ${pkgbase}

  ./autogen.sh
  ./configure \
      --prefix=/usr \
      --with-qt=yes \
      --with-gtk3=yes \
      --with-pcap \
      --with-zlib \
      --with-lua \
      --with-portaudio \
      --with-ssl \
      --with-krb5
  make all
}

package_wireshark-cli() {
  pkgdesc='a free network protocol analyzer for Unix/Linux and Windows - CLI version'
  depends=('krb5' 'libgcrypt' 'libcap' 'libpcap' 'bash' 'gnutls' 'glib2' 'lua52')
  install=wireshark.install
  conflicts=(wireshark)

  cd ${pkgbase}

  make DESTDIR="${pkgdir}" install

  #wireshark uid group is 150
  chgrp 150 "${pkgdir}/usr/bin/dumpcap"
  chmod 754 "${pkgdir}/usr/bin/dumpcap"
  rm "${pkgdir}/usr/bin/wireshark-gtk" "${pkgdir}/usr/bin/wireshark" \
     "${pkgdir}/usr/share/applications/wireshark.desktop" \
     "${pkgdir}/usr/share/icons/hicolor/"{"16x16","32x32","48x48"}"/apps/wireshark.png"

  # Headers
  install -dm755 "${pkgdir}"/usr/include/${pkgbase}/{epan/{crypt,dfilter,dissectors,ftypes,wmem},wiretap,wsutil}

  install -m644 color.h config.h register.h ws_symbol_export.h "${pkgdir}/usr/include/${pkgbase}"
  for d in epan epan/crypt epan/dfilter epan/dissectors epan/ftypes epan/wmem wiretap wsutil; do
    install -m644 ${d}/*.h "${pkgdir}"/usr/include/${pkgbase}/${d}
  done
}

package_wireshark-gtk() {
  pkgdesc='a free network protocol analyzer for Unix/Linux and Windows - GTK frontend'
  depends=('gtk3' 'portaudio' 'wireshark-cli' 'desktop-file-utils' 'hicolor-icon-theme')
  install=wireshark-gtk.install
  replaces=(wireshark)
  conflicts=(wireshark)

  cd ${pkgbase}

  install -Dm755 .libs/wireshark-gtk "${pkgdir}/usr/bin/wireshark-gtk"
  for d in 16 32 48; do
    install -Dm644 image/hi${d}-app-wireshark.png  \
                   "${pkgdir}/usr/share/icons/hicolor/${d}x${d}/apps/wireshark.png"
  done

  for d in 16 24 32 48 64 128 256 ; do
    install -Dm644 image/WiresharkDoc-${d}.png \
                   "${pkgdir}/usr/share/icons/hicolor/${d}x${d}/mimetypes/application-vnd.tcpdump.pcap.png"
  done

  install -d "${pkgdir}/usr/share/applications"
  sed -e 's/^Name.*=Wireshark/& (GTK)/g' -e '/Exec=/s/wireshark/&-gtk/g' wireshark.desktop > "${pkgdir}/usr/share/applications/wireshark-gtk.desktop"
}

package_wireshark-qt() {
  pkgdesc='a free network protocol analyzer for Unix/Linux and Windows - Qt frontend'
  depends=('qt5-base' 'wireshark-cli')
  install=wireshark-qt.install

  cd ${pkgbase}
  install -Dm755 .libs/wireshark "${pkgdir}/usr/bin/wireshark"

  install -Dm644 wireshark.desktop "${pkgdir}/usr/share/applications/wireshark.desktop"
}
