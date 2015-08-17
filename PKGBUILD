# Maintainer: Marko Paasila <marko at paasila dot net>
# Contributor: Michael Gerdau <mgd at qata dot de>
pkgname=vertminer-gpu
pkgver=3.7.2
pkgver() {
  cd "$srcdir/$_gitname"
  echo $(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}
pkgrel=4
pkgdesc="AMD GPU miner for scrypt-N-based coins, like vertcoin and pandacoin."
arch=("i686" "x86_64")
url=("http://vertcoin.org")
license=("GPL3")
depends=("ncurses" "curl" "libcl" "jansson")
conflicts=("vertminer" "vertminer-gpu" "vertminer-git")
makedepends=('opencl-headers' 'git' 'amdapp-sdk' 'unzip')
optdepends=()
provides=("vertminer-gpu")
source=(
	"git+https://github.com/Bufius/vertminer-gpu.git"
	"ADL_SDK_6.0.zip"
	)
	
sha512sums=(
	'SKIP'
	'4af430f0a0eac347b0e3df39999abbf7a4281a1b01398845fd8e826b69c28b67fc062092eac0ef207ace943006ed4ccd8c6017049c2dfd3945914ea6153026f3'
	)
	
noextract=('ADL_SDK_6.0.zip')

[ "$CARCH" == "x86_64" ] && makedepends+=('yasm')


prepare() {
	unzip -jod "$srcdir/$pkgname/ADL_SDK/" "$srcdir/ADL_SDK_6.0.zip" "include/*.h"
	unzip -od "$srcdir/AMD_ADL_SDK" "$srcdir/ADL_SDK_6.0.zip" "Public-Documents/ADL*"
}


build() {

    cd "$srcdir/$pkgname"

    # do not use included jansson-2.5 but already installed package
    mv configure.ac configure.ac.orig
    sed -e 's/AC_CONFIG_SUBDIRS(\[compat\/jansson-2.5\])//' -e 's/JANSSON_LIBS="compat\/jansson-2.5\/src\/.libs\/libjansson.a"/JANSSON_LIBS="-ljansson"/' <configure.ac.orig >configure.ac
    mv Makefile.am Makefile.am.orig
    sed -e 's/^JANSSON_INCLUDES/#JANSSON_INCLUDES/' -e 's/bin_SCRIPTS\t= \$(top_srcdir)\/\*.cl/bin_SCRIPTS\t= \$(top_srcdir)\/scrypt140202.cl/' <Makefile.am.orig >Makefile.am
    pushd compat
    mv Makefile.am Makefile.am.orig
    sed -e 's/jansson-2.5//' <Makefile.am.orig >Makefile.am
    popd

    ./autogen.sh

    ./configure \
    --prefix=/usr \
    --enable-scrypt \
    --enable-opencl \

    make
}

package() {
    cd "$srcdir/$pkgname"
    make DESTDIR="$pkgdir" install
}
