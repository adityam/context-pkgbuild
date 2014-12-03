# Maintainer: Aditya Mahajan <adityam at umich dot edu>
pkgname=context-minimals-docker
pkgver=latest
pkgrel=1
pkgdesc="A standalone ConTeXt distribution"
url="http://www.contextgarden.net"
license=('GPL')
arch=('i686' 'x86_64')
makedepends=('rsync')
optdepends=('fontconfig: xetex support'
            'ruby: pdftex support'
            'ghostscript: support for PS and EPS images'
            'graphicmagic: support for GIF and TIFF images'
            'inkscape: support for SVG images'
            'pstoedit: support for metapost outlines for fonts'
            'zint: support for barcodes'
            'curl: loading remote content')

conflicts=()
replaces=()
backup=()
options=(!strip) # So that we don't have install binutils

install=context-minimals-docker.install

PKGEXT=".pkg.tar.gz"

# ConTeXt minimals uses a different naming convention for architectures
declare -A _platform_map

_platform_map['i686']='linux'
_platform_map['x86_64']='linux-64'

_platform=${_platform_map[$CARCH]}

_rsyncurl=rsync://contextgarden.net/minimals/setup/${_platform}/bin
_dest=/opt/context-minimals

# This is the only way to get yaourt to store the files in
# /var/abs/local/yaourtbuild so that rsyncing the next time is efficient

_gitroot="http://dl.contextgarden.net/distribution/git/"
_gitname="master"

# Font directories
_texlivefontdir="/usr/share/texmf-dist/fonts/"
_userfontdir="\$HOME/.fonts"
_osfontdir="/usr/share/fonts"

build() {
 msg "Downloading the latest scripts first"
 rsync -rlpt $_rsyncurl $srcdir || return 1
 msg "Initializing download directory"

 mkdir -p $srcdir/tex/texmf-cache || return 1

 msg "Starting download or update of ConTeXt distribution"
 env PATH=$srcdir/bin:/bin:/usr/bin:/usr/sbin:/sbin \
 mtxrun --script $srcdir/bin/mtx-update.lua  \
      --platform=$_platform --texroot=$srcdir/tex --engine=all --modules=all \
      --context=beta  --update  --force --make || return 1

 # Set the PATH

 echo "TEXMFOS=${_dest}/texmf-${_platform}"   > $srcdir/tex/setuptex
 echo "export TEXMFOS"                       >> $srcdir/tex/setuptex
 echo ""                                     >> $srcdir/tex/setuptex
 echo "unset TEXINPUTS MPINPUTS MFINPUTS"    >> $srcdir/tex/setuptex
 echo ""                                     >> $srcdir/tex/setuptex
 echo "PATH=\$TEXMFOS/bin:\$PATH"            >> $srcdir/tex/setuptex
 echo "export PATH"                          >> $srcdir/tex/setuptex
}


package()
{
 install -d $pkgdir/opt || return 1
 msg "Copying files"
 cp -r --preserve=links $srcdir/tex $pkgdir/$_dest || return 1
}
