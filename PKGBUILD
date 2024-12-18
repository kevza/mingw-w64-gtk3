# Maintainer: lantw44 at gmail dot com

pkgname=mingw-w64-gtk3
pkgver=3.24.41
pkgrel=1
pkgdesc='GObject-based multi-platform GUI toolkit (mingw-w64)'
arch=('any')
url='https://www.gtk.org'
install="${pkgname}.install"
license=('LGPL')
makedepends=(
  'mingw-w64-gcc'
  'mingw-w64-pkg-config'
  'mingw-w64-meson'
  'gdk-pixbuf2' # gdk-pixbuf-pixdata
  'python-packaging' # gdbus-codegen
  'sassc')
depends=(
  'mingw-w64-crt'
  'mingw-w64-adwaita-icon-theme'
  'mingw-w64-atk>=2.15.1'
  'mingw-w64-cairo>=1.14.2-3'
  'mingw-w64-fribidi>=0.19.7'
  'mingw-w64-gdk-pixbuf2>=2.30.0'
  'mingw-w64-glib2>=2.57.2'
  'mingw-w64-harfbuzz>=0.9'
  'mingw-w64-libepoxy>=1.4'
  'mingw-w64-pango>=1.41.0')
options=('!strip' '!buildflags' 'staticlibs')
source=(
  "https://download.gnome.org/sources/gtk+/${pkgver%.*}/gtk+-${pkgver}.tar.xz")
sha256sums=(
  '47da61487af3087a94bc49296fd025ca0bc02f96ef06c556e7c8988bd651b6fa')

_architectures=('i686-w64-mingw32' 'x86_64-w64-mingw32')

prepare() {
  cd "${srcdir}/gtk+-${pkgver}"
  local source_file
  for source_file in "${source[@]}"; do
    case "${source_file}" in
      *.patch)
        patch -p1 < "${srcdir}/${source_file}"
        ;;
    esac
  done
  patch -Np1 -i ../../fix_gcc_14.patch
}

build() {
  cd "${srcdir}/gtk+-${pkgver}"
  for _arch in "${_architectures[@]}"; do
    mkdir -p "build-${_arch}"
    cd "build-${_arch}"
    "${_arch}-meson" \
      --default-library both \
      -Dbroadway_backend=true \
      -Dwin32_backend=true \
      -Dprint_backends= \
      -Dgtk_doc=false \
      -Dman=false \
      -Dintrospection=false
    ninja
    cd ..
  done
}

package() {
  cd "${srcdir}/gtk+-${pkgver}"
  for _arch in "${_architectures[@]}"; do
    cd "build-${_arch}"
    DESTDIR="${pkgdir}" ninja install
    find "${pkgdir}/usr/${_arch}" -name '*.exe' -exec "${_arch}-strip" '{}' ';'
    find "${pkgdir}/usr/${_arch}" -name '*.dll' -exec "${_arch}-strip" --strip-unneeded '{}' ';'
    find "${pkgdir}/usr/${_arch}" '(' -name '*.a' -o -name '*.dll' ')' -exec "${_arch}-strip" -g '{}' ';'
    rm -r "${pkgdir}/usr/${_arch}/etc"
    cd ..
  done
}
