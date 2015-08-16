# Contributor:  Gergely Imreh <imrehg(at)gmail(dot)com>
#               Daniel Großmann <grossy(at)gmx(dot)com>
pkgname=navit-svn
pkgver=6002
pkgrel=1
pkgdesc="Bleeding-edge (SVN) version of the modular, touch screen friendly car navigation system with GPS tracking, realtime routing engine and support for various vector map formats."
arch=('i686' 'x86_64')
url="http://wiki.navit-project.org/index.php/Main_Page"
license=('GPL')
provides=('navit')
conflicts=('navit')
depends=('glu' 'freeglut' 'dbus-glib' 'cairo' 'imlib2' 'sdl_image' 'gtk2' 'gpsd' \
        'postgresql-libs')
makedepends=('cmake' 'subversion' 'gcc' 'libtool' 'automake' 'autoconf' \
             'gettext' 'glib2' 'libxt' 'librsvg')
optdepends=('cegui>=0.5.0: OpenGL gui' 'quesoglc: OpenGL gui')
options=(!libtool)
install=navit-svn.install

_svntrunk=https://navit.svn.sourceforge.net/svnroot/navit/trunk/navit
_svnmod=navit

build() {
  cd ${srcdir}

  if [ -d $_svnmod/.svn ]; then
    (cd $_svnmod && svn up -r $pkgver)
  else
    svn co $_svntrunk $_svnmod -r $pkgver 
  fi

  msg "SVN checkout done or server timeout"
   
  if [ $(grep -c '^SET(LIBDIR lib)' $_svnmod/CMakeLists.txt) -ne 1 ]; then
    msg "Correcting CMakeLists.txt"
    sed -i 's/^ENDIF (UNIX AND NOT ANDROID AND NOT APPLE)$/ENDIF (UNIX AND NOT ANDROID AND NOT APPLE)\nSET(LIB_DIR lib)/' $_svnmod/CMakeLists.txt
  fi
  msg "Starting make..."
  
  # Create build directory
  if [ -e "$_svnmod-build" ]; then
    rm -r "$_svnmod-build"
  fi
  mkdir $_svnmod-build
  cd $_svnmod-build

  pwd
  # Finally, build!
  cmake ../navit \
    -DCMAKE_INSTALL_PREFIX=/usr/ \
    -DCMAKE_LIBDIR=lib \
    -Dgraphics/opengl=TRUE \
    -DSAMPLE_MAP=FALSE 
    
  make 
  #make DESTDIR=${pkgdir} install 

  # Remove build directory
  #rm -rf ${srcdir}/$_svnmod-build
}

package() {
  cd $_svnmod-build
  make DESTDIR=${pkgdir} install
}
