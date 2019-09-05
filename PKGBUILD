# Maintainer: Kien Dang <mail at kien dot ai>
# Maintainer: Julie Shapiro <jshapiro at nvidia dot com>

pkgname=nvidia-container-toolkit

pkgver=1.0.3
_runtime_pkgver=3.1.2

pkgrel=1
pkgdesc='NVIDIA container runtime toolkit'
arch=('x86_64')
url='https://github.com/NVIDIA/nvidia-container-runtime'
license=('BSD')

makedepends=('go')
depends=('libnvidia-container-tools' 'docker>=1:19.03')
conflicts=('nvidia-container-runtime-hook' 'nvidia-container-runtime<2.0.0')
replaces=('nvidia-container-runtime-hook')

source=("https://github.com/NVIDIA/nvidia-container-runtime/archive/v${_runtime_pkgver}.tar.gz")
sha256sums=('ccc2f60ae46765c9529b1c419365445d56e939d6bbbabd755d3c6175d7d22dc5')

_srcdir="nvidia-container-runtime-${_runtime_pkgver}"

prepare() {
  mkdir -p gopath/src
  ln -rTsf "${_srcdir}/toolkit/${pkgname}" "gopath/src/$pkgname"
}

build() {
  GOPATH="${srcdir}/gopath" go install \
                            -buildmode=pie \
                            -gcflags "all=-trimpath=${PWD}" \
                            -asmflags "all=-trimpath=${PWD}" \
                            -ldflags "-extldflags ${LDFLAGS}" \
                            "$pkgname"
                            #-ldflags " -s -w -extldflags=-Wl,-z,now,-z,relro" \
}

package() {
  install -D -m755 "${srcdir}/gopath/bin/${pkgname}" "$pkgdir/usr/bin/${pkgname}"
  pushd "$pkgdir/usr/bin/"
  ln -sf "${pkgname}" "nvidia-container-runtime-hook"
  popd
  install -D -m644 "${_srcdir}/toolkit/config.toml.centos" "$pkgdir/etc/nvidia-container-runtime/config.toml"

  install -D -m644 "${_srcdir}/LICENSE" "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
