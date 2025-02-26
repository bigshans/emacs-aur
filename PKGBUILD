# Maintainer: Pedro A. López-Valencia <https://aur.archlinux.org/users/vorbote>

################################################################################
# CAVEAT LECTOR: This PKGBUILD is highly opinionated. I give you
#                enough rope to hang yourself, but by default it
#                only enables the features I use.
#
#        TLDR: yaourt users, cry me a river.
#
#        Everyone else: do not update blindly this PKGBUILD. At least
#        make sure to compare and understand the changes.
#
################################################################################

################################################################################
# Assign "YES" to the variable you want enabled; empty or any other value
# for NO.
#
# Where you read experimental, replace with foobar.
# =================================================
#
################################################################################
CHECK=            # Run tests. May fail, this is developement after all.

CLANG="YES"            # Use clang.

GOLD=             # Use the gold linker.

LTO=              # Enable link-time optimization. Still experimental.

JIT=              # Enable native just-in-time compilation. libgccjit is in AUR.
                  # This compiles only performance critical elisp files.
                  #
                  # To compile all elisp on demand, add
                  #    (setq comp-deferred-compilation t)
                  # to your .emacs file.

AOT=              # Precompile all included elisp. It takes a long time.
                  # You still need to enable on-demand compilation
                  # for your own packages.

CLI=              # CLI only binary.

NOTKIT=           # Use no toolkit widgets. Like B&W Twm (001d sk00l).
               
LUCID=            # Use the lucid, a.k.a athena, toolkit. Like XEmacs, sorta.
                  #
                  # Read https://wiki.archlinux.org/index.php/X_resources
                  # https://en.wikipedia.org/wiki/X_resources
                  # and https://www.emacswiki.org/emacs/XftGnuEmacs
                  # for some tips on using outline fonts with
                  # Xft, if you choose no toolkit or Lucid.
                  #
               
NOCAIRO=          # Disable here. 
               
XWIDGETS='YES'         # Use GTK+ widgets pulled from webkit2gtk. Usable.
               
DOCS_HTML=        # Generate and install html documentation.
               
DOCS_PDF=         # Generate and install pdf documentation.
               
MAGICK=           # ImageMagick 7 support. Deprecated (read the logs).
                  # ImageMagick, like flash, won't die... 
                  # -->>If you just *believe* you need ImageMagick, you don't.<<--
               
NOGZ="YES"        # Don't compress .el files.
################################################################################

################################################################################
if [[ $CLI == "YES" ]] ; then
  pkgname="emacs-nox-git"
else
pkgname="emacs-git"
fi
pkgver=29.0.50.152013
pkgrel=1
pkgdesc="GNU Emacs. Development master branch."
arch=('x86_64')
url="http://www.gnu.org/software/emacs/"
license=('GPL3')
depends_nox=('alsa-lib' 'gnutls' 'libxml2' 'jansson' 'gpm')
depends=("${depends_nox[@]}" 'm17n-lib' 'libotf' 'harfbuzz')
makedepends=('git')
provides=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs28-git' 'emacs-seq' 'emacs-nox')
conflicts=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs28-git' 'emacs-seq' 'emacs-nox')
replaces=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs28-git' 'emacs-seq' 'emacs-nox')
#source=("emacs-git::git://git.savannah.gnu.org/emacs.git")
# If Savannah fails for reasons, use Github's mirror
source=("emacs-git::git://github.com/emacs-mirror/emacs.git")
options=(!strip)
install=emacs-git.install
b2sums=('SKIP')
################################################################################

################################################################################

if [[ $GOLD == "YES" && ! $CLANG == "YES" ]]; then
  export LD=/usr/bin/ld.gold
  export CFLAGS+=" -fuse-ld=gold";
  export CXXFLAGS+=" -fuse-ld=gold";
elif [[ $GOLD == "YES" && $CLANG == "YES" ]]; then
  echo "";
  echo "Clang rather uses its own linker.";
  echo "";
  exit 1;
fi

if [[ $CLANG == "YES" ]]; then
  export CC="/usr/bin/clang" ;
  export CXX="/usr/bin/clang++" ;
  export CPP="/usr/bin/clang -E" ;
  export LD="/usr/bin/lld" ;
  export AR="/usr/bin/llvm-ar" ;
  export AS="/usr/bin/llvm-as" ;
  export CCFLAGS+=' -fuse-ld=lld' ;
  export CXXFLAGS+=' -fuse-ld=lld' ;
  makedepends+=( 'clang' 'lld' 'llvm') ;
fi

if [[ $JIT == "YES" ]]; then
  if [[ $CLI == "YES" ]]; then
    depends_nox+=( 'libgccjit' );
  else
    depends+=( 'libgccjit' );
  fi
fi

if [[ $CLI == "YES" ]]; then
  depends=("${depends_nox[@]}");
elif [[ $NOTKIT == "YES" ]]; then
  depends+=( 'dbus' 'hicolor-icon-theme' 'libxinerama' 'libxrandr' 'lcms2' 'librsvg' 'libxfixes' );
  makedepends+=( 'xorgproto' );
elif [[ $LUCID == "YES" ]]; then
  depends+=( 'dbus' 'hicolor-icon-theme' 'libxinerama' 'libxfixes' 'lcms2' 'librsvg' 'xaw3d' 'libxrandr' );
  makedepends+=( 'xorgproto' );
else
  depends+=( 'gtk3' );
  makedepends+=( 'xorgproto' );
fi

if [[ $MAGICK == "YES" ]]; then
  depends+=( 'imagemagick'  'libjpeg-turbo' 'giflib' );
elif [[ ! $NOX == "YES" ]] && [[ ! $CLI == "YES" ]]; then
  depends+=( 'libjpeg-turbo' 'giflib' );
elif [[ $CLI == "YES" ]]; then
  depends+=();
fi

if [[ ! $NOCAIRO == "YES" ]] && [[ ! $CLI == "YES" ]] ; then
  depends+=( 'cairo' );
fi

if [[ $XWIDGETS == "YES" ]]; then
  if [[ $LUCID == "YES" ]] || [[ $NOTKIT == "YES" ]] || [[ $CLI == "YES" ]]; then
    echo "";
    echo "";
    echo "Xwidgets support **requires** GTK+3!!!";
    echo "";
    echo "";
    exit 1;
  else
    depends+=( 'webkit2gtk' );
  fi
fi

if [[ $DOCS_PDF == "YES" ]]; then
  makedepends+=( 'texlive-core' );
fi
################################################################################

################################################################################
pkgver() {
  cd "$srcdir/emacs-git"

  printf "%s.%s" \
    "$(grep AC_INIT configure.ac | \
    sed -e 's/^.\+\ \([0-9]\+\.[0-9]\+\.[0-9]\+\?\).\+$/\1/')" \
    "$(git rev-list --count HEAD)"
}

# There is no need to run autogen.sh after first checkout.
# Doing so, breaks incremental compilation.
prepare() {
  cd "$srcdir/emacs-git"
  [[ -x configure ]] || ( ./autogen.sh git && ./autogen.sh autoconf )
}

if [[ $CHECK == "YES" ]]; then
check() {
  cd "$srcdir/emacs-git"
  make check
}
fi

build() {
  cd "$srcdir/emacs-git"

  local _conf=(
    --prefix=/usr
    --sysconfdir=/etc
    --libexecdir=/usr/lib
    --localstatedir=/var
    --mandir=/usr/share/man
    --with-gameuser=:games
    --with-sound=alsa
    --with-modules
# Beware https://debbugs.gnu.org/cgi/bugreport.cgi?bug=25228
# dconf and gconf break font settings you set in ~/.emacs.
# If you insist you'll need to read that bug report in *full*.
# Good luck!
   --without-gconf
   --without-gsettings
  )

################################################################################

################################################################################

if [[ $CLANG == "YES" ]]; then
  _conf+=( '--enable-autodepend' );
fi

if [[ $LTO == "YES" ]]; then
  _conf+=( '--enable-link-time-optimization' );
fi

if [[ $JIT == "YES" ]]; then
  _conf+=( '--with-native-compilation' );
fi

if [[ $CLI == "YES" ]]; then
  _conf+=( '--without-x' '--with-x-toolkit=no' '--without-xft' '--without-lcms2' '--without-rsvg' '--without-jpeg' '--without-gif' '--without-tiff' '--without-png' );
elif [[ $NOTKIT == "YES" ]]; then
  _conf+=( '--with-x-toolkit=no' '--without-toolkit-scroll-bars' '--with-xft' '--without-xaw3d' );
elif [[ $LUCID == "YES" ]]; then
  _conf+=( '--with-x-toolkit=lucid' '--with-xft' '--with-xaw3d' );
else
  _conf+=( '--with-x-toolkit=gtk3' '--without-xaw3d' );
fi

if [[ $MAGICK == "YES" ]]; then
  _conf+=( '--with-imagemagick');
else
  _conf+=();
fi

if [[ $NOCAIRO == "YES" || $CLI == "YES" ]]; then
  _conf+=( '--without-cairo' );
fi

if [[ $XWIDGETS == "YES" ]]; then
  _conf+=( '--with-xwidgets' );
fi

if [[ $NOGZ == "YES" ]]; then
  _conf+=( '--without-compress-install' );
fi

# ctags/etags may be provided by other packages, e.g, universal-ctags
_conf+=('--program-transform-name=s/\([ec]tags\)/\1.emacs/')

################################################################################

################################################################################

  ./configure "${_conf[@]}"

  # Using "make" instead of "make bootstrap" enables incremental
  # compiling. Less time recompiling. Yay! But you may
  # need to use bootstrap sometimes to unbreak the build.
  # Just add it to the command line.
  #
  # Please note that incremental compilation implies that you
  # are reusing your src directory!
  #
  if [[ $JIT == "YES" ]] && [[ $AOT == "YES" ]]; then
    make NATIVE_FULL_AOT=1
  else
    make
  fi

  # You may need to run this if 'loaddefs.el' files become corrupt.
  #cd "$srcdir/emacs-git/lisp"
  #make autoloads
  #cd ../

  # Optional documentation formats.
  if [[ $DOCS_HTML == "YES" ]]; then
    make html;
  fi
  if [[ $DOCS_PDF == "YES" ]]; then
    make pdf;
  fi

}

package() {
  cd "$srcdir/emacs-git"

  make DESTDIR="$pkgdir/" install

  # Install optional documentation formats
  if [[ $DOCS_HTML == "YES" ]]; then make DESTDIR="$pkgdir/" install-html; fi
  if [[ $DOCS_PDF == "YES" ]]; then make DESTDIR="$pkgdir/" install-pdf; fi

  # fix user/root permissions on usr/share files
  # MOVED to install script
  # find "$pkgdir"/usr/share/emacs/ | xargs chown root:root

  # fix permssions on /var/games
  mkdir -p "$pkgdir"/var/games/emacs
  chmod 775 "$pkgdir"/var/games
  chmod 775 "$pkgdir"/var/games/emacs
  # MOVED to install script
  # chown -R root:games "$pkgdir"/var/games

}

################################################################################
# vim:set ft=sh ts=2 sw=2 et:
