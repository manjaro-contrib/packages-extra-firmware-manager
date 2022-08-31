# Maintainer: Mark Wagie <mark at manjaro dot org>

pkgname=('firmware-manager' 'libfirmware-manager')
pkgbase=firmware-manager
pkgver=0.1.2+49+g0b24411
pkgrel=2
pkgdesc="Generic framework and GTK UI for firmware updates from system76-firmware and fwupd"
arch=('x86_64' 'aarch64')
url="https://github.com/pop-os/firmware-manager"
license=('GPL3')
depends=('dbus' 'libgudev' 'openssl')
makedepends=('cargo' 'git' 'gtk3' 'setconf')
options=('!lto')
_commit=0b244118fc2a22b4fd149b1919d5a428ed919570
source=("git+https://github.com/pop-os/firmware-manager.git#commit=$_commit"
        'com.system76.FirmwareManager.policy'
        "$pkgbase.sh")
sha256sums=('SKIP'
            '310e872ec56f13764615795dbcc30e3ab8b0e4329c0d1fe34bd5aa73bc602535'
            'fb8395e19bfd54f756dad1d073135c5b41caa2ad27ee0621350fba50b2e7363b')

pkgver() {
  cd "$srcdir/$pkgbase"
  git describe --tags | sed 's/-/+/g'
}

prepare() {
  cd "$srcdir/$pkgbase"
  export RUSTUP_TOOLCHAIN=stable
  cargo fetch --target "$CARCH-unknown-linux-gnu"
}

build() {
  cd "$srcdir/$pkgbase"
  export RUSTUP_TOOLCHAIN=stable
  make prefix=/usr
}

package_firmware-manager() {
  pkgdesc="GTK application for managing system and device firmware."
  depends=('gtk3' 'libfirmware-manager' 'polkit')
  provides=("$pkgname-virtual")
  install="$pkgname.install"

  cd "$srcdir/$pkgbase"
  make prefix=/usr DESTDIR="$pkgdir/" install-{bin,notify,icons}

  install -Dm644 "$srcdir/com.system76.FirmwareManager.policy" -t \
    "$pkgdir/usr/share/polkit-1/actions"

  install -Dm755 "$srcdir/$pkgname.sh" "$pkgdir/usr/bin/$pkgname"

  setconf "$pkgdir/usr/share/applications/com.system76.FirmwareManager.desktop" Exec "$pkgname"
}

package_libfirmware-manager() {
  pkgdesc="Shared library for C which provides the firmware manager as a GTK widget."
  depends+=('fwupd')
  optdepends=('system76-firmware-daemon: For System76 firmware updates')
  provides=('libfirmware_manager.so')

  cd "$srcdir/$pkgbase"
  make prefix=/usr DESTDIR="$pkgdir/" install-ffi
}
