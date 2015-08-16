# Submitter: Nathan Hulse <nat.hulse@gmail.com>

pkgname=libcompizconfig-bzr
pkgver=430
pkgrel=3
pkgdesc="Compiz configuration system library"
arch=('i686' 'x86_64')
url="https://launchpad.net/compiz-libcompizconfig"
license=('GPL')
depends=('compiz-core-bzr' 'libxml2' 'protobuf')
makedepends=('intltool' 'pkgconfig' 'cmake')
provides=('libcompizconfig')
conflicts=('libcompizconfig')
install='libcompizconfig-bzr.install'

# GSettings backend support
GSETTINGS="on"

_bzrtrunk=lp:compiz-libcompizconfig
_bzrmod=compiz-libcompizconfig

build() {
  cd "$srcdir"

  msg "Connecting to Launchpad..."
  if [[ -d "$_bzrmod" ]]; then
    cd "$_bzrmod" && bzr pull "$_bzrtrunk" -r "$pkgver"
  else
    bzr branch "$_bzrtrunk" "$_bzrmod" -q -r "$pkgver"
  fi
  msg "Bazaar checkout done or server timeout."

  rm -rf "$srcdir/$_bzrmod-build"
  msg "Creating build directory..."
  cp -r "$srcdir/$_bzrmod" "$srcdir/$_bzrmod-build"
  mkdir "$srcdir/$_bzrmod-build"/build
  cd "$srcdir/$_bzrmod-build/build"

  msg "Running cmake..." 
  cmake .. \
    -DCMAKE_INSTALL_PREFIX="/usr"                     \
    -DCOMPIZ_PLUGIN_INSTALL_TYPE="package"            \
    -DCOMPIZ_BUILD_WITH_RPATH=FALSE                   \
    -DUSE_GSETTINGS="${GSETTINGS}"                    \
    -DCOMPIZ_DISABLE_SCHEMAS_INSTALL=On               \
    -DCOMPIZ_PACKAGING_ENABLED=TRUE                   \
    -DCOMPIZ_DESTDIR="${pkgdir}"
  make
}

package() {
  cd "$srcdir/$_bzrmod-build/build"
  make install
  make findcompizconfig_install

  # Add the pesky gsettings schema files manually
  ls generated | grep -qm1 .gschema.xml
  if [ $? -eq 0 ]; then
    msg "Adding GSettings schema files..."
    install -dm755 "${pkgdir}/usr/share/glib-2.0/schemas/" 
    install -m644 generated/*.gschema.xml "${pkgdir}/usr/share/glib-2.0/schemas/" 
  fi

  # Remove empty file /etc/compizconfig/config
  rm -rf "${pkgdir}/etc"
}
