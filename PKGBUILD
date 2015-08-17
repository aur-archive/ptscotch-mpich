# Submitter: Jed Brown <jedbrown>
# Maintainer: George Eleftheriou <eleftg>
# Contributor: Jed Brown <jedbrown>
# Contributor: eolianoe <eolianoe>
 
pkgname=ptscotch-mpich
pkgver=6.0.3
_downloadnum=34099  # gforge is insane
pkgrel=2
_prefix=/opt/mpich
pkgdesc="Software package and libraries for graph, mesh and hypergraph partitioning, static mapping, and sparse matrix block ordering. This is the parallel version using the MPICH implementation (MPICH/ptesmumps)."
url="http://www.labri.fr/perso/pelegrin/scotch/"
license=("custom:CeCILL-C")
depends=('zlib' 'bzip2' 'mpich')
arch=('i686' 'x86_64')
source=("https://gforge.inria.fr/frs/download.php/file/${_downloadnum}/scotch_${pkgver}.tar.gz")
sha256sums=('6461cc9f28319a9dbe6cc10e28c0cbe90b4b25e205723c3edcde9a3ff974d6d8')
 
prepare() {
  cd "${srcdir}/scotch_${pkgver}/src"
 
  [ -e Makefile.inc ] && rm Makefile.inc
  cp Make.inc/Makefile.inc.${CARCH/_/-}_pc_linux2.shlib Makefile.inc
 
  # Use the CFLAGS defined /etc/makepkg.conf
  sed -i "s/-O3/${CFLAGS} -fPIC/g" Makefile.inc
 
  # disable SCOTCH_PTHREAD
  sed -i "s/-DSCOTCH_PTHREAD//" Makefile.inc

  # Fix C compiler
  sed -i "s,= mpicc,= ${_prefix}/bin/mpicc," Makefile.inc
  sed -i "s,CCD\t.*=.*gcc,CCD = ${_prefix}/bin/mpicc," Makefile.inc

  # Use the mpirun of mpich for testing
  sed -i "s,mpirun,${_prefix}/bin/mpirun," check/Makefile

  # Also enable bzip2 compression
  sed -i "s/-DCOMMON_FILE_COMPRESS_GZ/-DCOMMON_FILE_COMPRESS_GZ -DCOMMON_FILE_COMPRESS_BZ2/" Makefile.inc
  sed -i "s/-lz/-lz -lbz2/" Makefile.inc
 
  # Fix the creation of directories
  sed -i "s/mkdir/mkdir\ -p/" Makefile.inc
 
  # To install headers and libs also for esmumps
  sed -i 's/scotch\*/{scotch,esmumps}\*/g' Makefile

  # fix an installation bug (which is usually ignored anyway)
  sed -i "/\.\.\/bin\/\[agm\]/d" Makefile
}
 
build() {
  cd "${srcdir}/scotch_${pkgver}/src"
 
  make ptscotch
  make -j1 ptesmumps
}
 
check() {
  cd "${srcdir}/scotch_${pkgver}/src"
 
  make ptcheck LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:"../../lib"
}
 
package() {
  cd "${srcdir}/scotch_${pkgver}/src"
 
  make install prefix="${pkgdir}/${_prefix}" includedir="${pkgdir}/${_prefix}/include/scotch"
 
  install -m 644 -D ${srcdir}/scotch_${pkgver}/doc/CeCILL-C_V1-en.txt ${pkgdir}/usr/share/licenses/${pkgname}/LICENSE
}
