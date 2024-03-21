# Maintainer: Jakub Klinkovský <lahwaacz at archlinux dot org>
# Contributor: Mark Wagie <mark dot wagie at proton dot me>
# Contributor: Kien Dang <mail at kien dot ai>
# Contributor: Julie Shapiro <jshapiro at nvidia dot com>

pkgname=nvidia-container-toolkit
pkgver=1.14.6
pkgrel=2
pkgdesc="NVIDIA container runtime toolkit"
arch=(x86_64)
url="https://github.com/NVIDIA/nvidia-container-toolkit"
license=(Apache-2.0)
depends=(glibc libnvidia-container=$pkgver)
makedepends=(go)
backup=('etc/nvidia-container-runtime/config.toml')
# we cannot use LTO as otherwise we do not get reproducible package with full RELRO
options=('!lto')
source=("$pkgname-$pkgver.tar.gz::$url/archive/v$pkgver.tar.gz")
b2sums=('6d0dc186a49b2d1cb09fda3f3c4e3361e22f8891cba96cfaa14f2b70f887040b5b637125f7581159aa4a3e0f4c0542f0899e1d0708806767091a9cc34828deac')

prepare() {
  cd "$pkgname-$pkgver"
  mkdir -p build
}

build() {
  cd "$pkgname-$pkgver"

  # set GOPATH so makepkg puts source files into the debug package
  export GOPATH="$srcdir"

  # FIXME: nvml requires lazy binding (-Wl,-z,lazy) which prevents FULL RELRO: https://github.com/NVIDIA/nvidia-container-toolkit/issues/49
  go build -v \
    -buildmode=pie \
    -mod=vendor \
    -modcacherw \
    -ldflags "-compressdwarf=false -linkmode external -extldflags \"$LDFLAGS -Wl,-z,lazy\" -X github.com/NVIDIA/$pkgname/internal/info.version=$pkgver" \
    -o build ./...
}

check() {
  cd "$pkgname-$pkgver"
  PATH="$PATH:$PWD/build" go test -v ./...
}

package() {
  cd "$pkgname-$pkgver"

  # install binaries
  install -vDm 755 build/nvidia-{ctk,container-runtime{,.cdi,.legacy,-hook}} -t "$pkgdir/usr/bin/"
  ln -s nvidia-container-runtime-hook "$pkgdir/usr/bin/nvidia-container-toolkit"

  # install the license
  install -vDm 644 LICENSE -t "$pkgdir/usr/share/licenses/$pkgname/"

  # generate the default config
  "$pkgdir"/usr/bin/nvidia-ctk --quiet config --config-file="$pkgdir"/etc/nvidia-container-runtime/config.toml --in-place
}
