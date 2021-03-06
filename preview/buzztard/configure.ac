dnl $Id: configure.ac 2538 2009-07-29 21:37:04Z ensonic $

dnl
dnl current  : the interface         : MAJOR + MINOR
dnl revision : of the same interface : MICRO
dnl age      : MINOR
dnl
dnl 1: when releasing a changed version : MICRO++
dnl 2: when the interface has changed (adding functions) : MINOR++, MICRO=0
dnl 3: when the interface has changed (removing functions) : MAJOR++, MINOR=0, MICRO=0
dnl

dnl Initialise the autoconf/automake tool
AC_PREREQ(2.61)
AC_INIT([buzztard],[0.5.0],[buzztard-devel@lists.sourceforge.net])
# we can't do -Werror, because
# - we use gnu make extensions
# - we override uninstallcheck var
AM_INIT_AUTOMAKE([-Wall check-news std-options])
AC_CONFIG_SRCDIR([ChangeLog])
AC_CONFIG_HEADERS(config.h)
AC_CONFIG_MACRO_DIR(m4)

BT_MAJOR_VERSION=0
BT_MINOR_VERSION=5
BT_MICRO_VERSION=0
BT_VERSION=$BT_MAJOR_VERSION.$BT_MINOR_VERSION.$BT_MICRO_VERSION
BT_VERSION_INFO=`expr $BT_MAJOR_VERSION + $BT_MINOR_VERSION`:$BT_MICRO_VERSION:$BT_MINOR_VERSION
BT_VERSION_NUMBER=`expr $BT_MAJOR_VERSION \* 10000 + $BT_MINOR_VERSION \* 100 + $BT_MICRO_VERSION`

dnl our libraries and install dirs use major.minor as a version
BT_MAJORMINOR=$BT_MAJOR_VERSION.$BT_MINOR_VERSION
dnl we override it here for release candidates - not needed as be bump after releases
dnl BT_MAJORMINOR=0.5

dnl release year and date
BT_RELEASE_YEAR=`date +%Y`
BT_RELEASE_DATE=`date +%Y-%m-%d`

AC_SUBST(BT_MAJOR_VERSION)
AC_SUBST(BT_MINOR_VERSION)
AC_SUBST(BT_MICRO_VERSION)
AC_SUBST(BT_VERSION)
AC_SUBST(BT_VERSION_INFO)
AC_SUBST(BT_VERSION_NUMBER)
AC_SUBST(BT_MAJORMINOR)
AC_SUBST(BT_RELEASE_YEAR)
AC_SUBST(BT_RELEASE_DATE)

AC_DEFINE_UNQUOTED(PACKAGE_VERSION_NUMBER, ${BT_VERSION_NUMBER}, [version as a number])

VERSION=${BT_VERSION}

dnl m4 - everyone who develops should have aclocal installed
AC_PATH_PROG(PROG_ACLOCAL, aclocal, no)
AC_MSG_CHECKING(where to install .m4 files to)
if test "$PROG_ACLOCAL" = "no" ; then
  acdir=${datadir}/aclocal
else
  acdir=`$PROG_ACLOCAL --print-ac-dir`
  if test ! -w "$acdir" ; then
    acdir=${datadir}/aclocal
  fi
fi
AC_MSG_RESULT($acdir)
AC_SUBST(acdir)

dnl Checks for programs.
AC_PROG_CXX
AC_PROG_CC
AC_PROG_CPP
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_SED
AC_ISC_POSIX
AC_PROG_LIBTOOL
AC_PROG_CC
AM_PROG_MKDIR_P
IT_PROG_INTLTOOL
PKG_PROG_PKG_CONFIG

AC_PATH_PROG(xvfb_path, [Xvfb], [no])
if test "x$xvfb_path" != "xno" ; then
  AC_DEFINE_UNQUOTED(XVFB_PATH, "$xvfb_path", [Path of Xvfb executable to be used to run gui unit tests under])

  xfont_path=`xset q | grep fonts | sed 's/^\ *//'`
  if test "x$xfont_path" != "x"; then
    AC_DEFINE_UNQUOTED(XFONT_PATH, "$xfont_path", [X font path])
  fi
fi

dnl Initialise gettext
AM_GNU_GETTEXT_VERSION(0.12.1)
AM_GNU_GETTEXT([external])
localedir='${datadir}/locale'
AC_SUBST(localedir)
GETTEXT_PACKAGE="buzztard-$BT_MAJORMINOR"
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE,"$GETTEXT_PACKAGE", [Package Name for Gettext])
DATADIRNAME="share"
AC_SUBST(DATADIRNAME)

dnl this macro comes from gnome.m4, which we do not want to require
dnl AM_ACLOCAL_INCLUDE(./m4)

dnl enable runtime debugging code
AC_MSG_CHECKING(whether to enable runtime debugging code)
AC_ARG_ENABLE(
  debug,
  AS_HELP_STRING([--enable-debug],[enable runtime debugging code (default=no)]),
  ,
  [enable_debug="no"])
AC_MSG_RESULT($enable_debug)
if test "$enable_debug" = "yes"; then
  AC_DEFINE(USE_DEBUG, [1], [enable runtime debugging code])
  DEBUG_CFLAGS="-O0 -Wall -Werror -g"
  # DEBUG_CFLAGS="-O0 -Wall -Werror -Wno-strict-aliasing -g"
  DEBUG_LDFLAGS="-Wl,--warn-common"
  AM_CONDITIONAL(USE_DEBUG, true)
else
  AC_DEFINE(G_DISABLE_ASSERT, [1], [if no runtime debugging, do not enable g_asserts])
  AC_DEFINE(G_DISABLE_CHECKS, [1], [if no runtime debugging, do not enable g_return_if_fail variants])
  AC_DEFINE(G_DISABLE_CAST_CHECKS, [1], [if no runtime debugging, do not enable dynamic type checks])
  DEBUG_CFLAGS=""
  DEBUG_LDFLAGS=""
  AM_CONDITIONAL(USE_DEBUG, false)
fi


dnl compile with disable-deprecated switches
AC_MSG_CHECKING([whether to disable deprecated glib/gtk+/gst/etc. features])
AC_ARG_ENABLE(deprecated,
	AS_HELP_STRING([--disable-deprecated],[disable deprecated glib/gtk+/gst/etc. features]),
	set_enable_deprecated="$enableval",[
		if test -f $srcdir/autogen.sh; then
			is_vcs_version=true
			set_enable_deprecated=no
		else
			set_enable_deprecated=yes
		fi
	]
)
if test "$set_enable_deprecated" != "yes"; then
	AC_MSG_RESULT(yes)
	# ATK_DISABLE_DEPRECATED
	BT_DISABLE_DEPRECATED="-DG_DISABLE_DEPRECATED -DPANGO_DISABLE_DEPRECATED -DGDK_DISABLE_DEPRECATED -DGDK_PIXBUF_DISABLE_DEPRECATED -DGTK_DISABLE_DEPRECATED -DGST_DISABLE_DEPRECATED -DGNOME_DISABLE_DEPRECATED"
else
	AC_MSG_RESULT(no)
	BT_DISABLE_DEPRECATED=""
fi
AC_SUBST(BT_DISABLE_DEPRECATED)

dnl enable coverage analysis
AC_MSG_CHECKING(whether to enable coverage analysis)
AC_ARG_ENABLE(
  coverage,
  AS_HELP_STRING([--enable-coverage],[enable coverage analysis (default=no)]),
  ,
  [enable_coverage="no"])
AC_MSG_RESULT($enable_coverage)
if test "$enable_coverage" = "yes"; then
  COVERAGE_CFLAGS="-O0 -g -fprofile-arcs -ftest-coverage"
  COVERAGE_LIBS="-lgcov"

  AC_PATH_PROG(locv_path, [lcov], [no])
  if test "x$lcov_path" != "xno" ; then
    LCOV_VERSION=`lcov --version|head -n 1| $SED 's/^.* version //'`
    LCOV_MAJOR=`echo $LCOV_VERSION | cut -d. -f1 | $SED s/[a-zA-Z\-].*//g`
    LCOV_MINOR=`echo $LCOV_VERSION | cut -d. -f2 | $SED s/[a-zA-Z\-].*//g`
    if [[ "$LCOV_MAJOR" -lt "1" ]]; then
      AC_MSG_WARN([lcov >= 1.6 is required, you have $LCOV_VERSION])
      lcov_path="no"
    elif [[ "$LCOV_MAJOR" -eq "1" ]] && \
         [[ "$LCOV_MINOR" -lt "6" ]]; then
      AC_MSG_WARN([lcov >= 1.6 is required, you have $LCOV_VERSION])
      lcov_path="no"
    fi
  fi
else
  COVERAGE_CFLAGS=""
  COVERAGE_LIBS=""
fi
AM_CONDITIONAL(USE_LCOV, test ! x$lcov_path = xyes)


dnl valgrind integration for tests
AC_MSG_CHECKING(where Valgrind is installed on your system)
AC_ARG_WITH([valgrind],
        AS_HELP_STRING([--with-valgrind],[where Valgrind is installed on your system (default is no)]),
        [ac_cv_use_valgrind=$withval], [ac_cv_use_valgrind=no])dnl
AC_MSG_RESULT($ac_cv_use_valgrind)
VALGRIND=$ac_cv_use_valgrind
AC_SUBST(VALGRIND)
AM_CONDITIONAL(USE_VALGRIND, test x$VALGRIND != xno)

VALGRIND_CPPFLAGS=
if test $ac_cv_use_valgrind != no; then
   AC_DEFINE(HAVE_VALGRIND, 1, [Define to 1 if valgrind is to be used.])
   if test -f $ac_cv_use_valgrind/valgrind.h; then
      VALGRIND_CFLAGS=-I$ac_cv_use_valgrind
   elif test -f $ac_cv_use_valgrind/include/valgrind.h; then
      VALGRIND_CFLAGS=-I$ac_cv_use_valgrind/include
   elif test -f $ac_cv_use_valgrind/valgrind/valgrind.h; then
      VALGRIND_CFLAGS=-I$ac_cv_use_valgrind/valgrind
   elif test -f $ac_cv_use_valgrind/include/valgrind/valgrind.h; then
      VALGRIND_CFLAGS=-I$ac_cv_use_valgrind/include/valgrind
   elif test -f $ac_cv_use_valgrind/valgrind/include/valgrind.h; then
      VALGRIND_CFLAGS=-I$ac_cv_use_valgrind/valgrind/include
   fi
fi
AC_SUBST(VALGRIND_CFLAGS)


dnl define custom path for desktop files
AC_MSG_CHECKING(where to install desktop files)
AC_ARG_WITH(
  desktop_dir,
  AS_HELP_STRING([--with-desktop-dir],[install desktop files to DIR/applications (default=$prefix/share/)]),
  ,
  [with_desktop_dir="$prefix/share/"])
AC_MSG_RESULT($with_desktop_dir)
AC_SUBST(with_desktop_dir)


dnl Checks for libraries.

dnl mandarory deps
PKG_CHECK_MODULES(BASE_DEPS, \
  glib-2.0 >= 2.10.0 gobject-2.0  >= 2.10.0 gthread-2.0 >= 2.10.0 gmodule-2.0 >= 2.10.0 \
  gnome-vfs-2.0 >= 2.10 gnome-vfs-module-2.0 >= 2.10 \
  libxml-2.0 >= 2.6.0 \
  gstreamer-0.10 >= 0.10.14 gstreamer-controller-0.10 >= 0.10.14 \
  gstreamer-base-0.10 >= 0.10.14 \
  gstreamer-plugins-base-0.10 >= 0.10.14 \
)
# gstreamer-app-0.10 >= 0.10.22

PKG_CHECK_MODULES(GUI_DEPS, \
  gtk+-2.0 >= 2.6.0 \
  libgnomecanvas-2.0 \
)
# still the pixbuf loader should have svg support
# librsvg-2.0 \

PKG_CHECK_MODULES(GCONF_DEPS, gconf-2.0 >= 2.2.0, [
    AM_CONDITIONAL(USE_GCONF, true)
    AC_DEFINE(USE_GCONF, [1], [Define to 1 if we can use GConf])
    BASE_DEPS_CFLAGS="$BASE_DEPS_CFLAGS $GCONF_DEPS_CFLAGS"
    BASE_DEPS_LIBS="$BASE_DEPS_LIBS $GCONF_DEPS_LIBS"
    AC_PATH_PROG(GCONFTOOL, gconftool-2, [#])
    AM_GCONF_SOURCE_2
    GCONF_DOC_TYPES=
  ],[
    AM_CONDITIONAL(USE_GCONF, false)
    GCONF_DOC_TYPES="%"
  ]
)
AC_SUBST(GCONF_DOC_TYPES)

PKG_CHECK_MODULES(GSTBT_DEPS, libgstbuzztard >= 0.5.0, [
    AC_DEFINE(USE_GSTBT, [1], [Define to 1 if we can use gst-buzztard])
    BASE_DEPS_CFLAGS="$BASE_DEPS_CFLAGS $GSTBT_DEPS_CFLAGS"
    BASE_DEPS_LIBS="$BASE_DEPS_LIBS $GSTBT_DEPS_LIBS"
  ]
)

dnl optional deps
PKG_CHECK_MODULES(BML_DEPS, libbml >= 0.5.0, [
    AC_DEFINE(USE_BML, [1], [Define to 1 if we can use bml])
    BASE_DEPS_CFLAGS="$BASE_DEPS_CFLAGS $BML_DEPS_CFLAGS"
    BASE_DEPS_LIBS="$BASE_DEPS_LIBS $BML_DEPS_LIBS"
    use_bml=yes
  ],[
    use_bml=no
  ]
)

PKG_CHECK_MODULES(HILDON_DEPS, hildon-1 libosso, [
  AC_DEFINE(USE_HILDON, [1], [Define to 1 if we can use hildon libraries])
    BASE_DEPS_CFLAGS="$BASE_DEPS_CFLAGS $HILDON_DEPS_CFLAGS"
    BASE_DEPS_LIBS="$BASE_DEPS_LIBS $HILDON_DEPS_LIBS"
    use_hildon=yes
  ],[
    use_hildon=no
  ]
)

PKG_CHECK_MODULES(HAL_DEPS, dbus-glib-1 hal, [
  AC_DEFINE(USE_HAL, [1], [Define to 1 if we can use hal libraries])
    BASE_DEPS_CFLAGS="$BASE_DEPS_CFLAGS $HAL_DEPS_CFLAGS"
    BASE_DEPS_LIBS="$BASE_DEPS_LIBS $HAL_DEPS_LIBS"
    use_hal=yes
  ],[
    use_hal=no
  ]
)

PKG_CHECK_MODULES(GSF_DEPS, libgsf-1, [
  AM_CONDITIONAL(USE_GSF, true)
  AC_DEFINE(USE_GSF, [1], [Define to 1 if we can use gsf libraries])
    BASE_DEPS_CFLAGS="$BASE_DEPS_CFLAGS $GSF_DEPS_CFLAGS"
    BASE_DEPS_LIBS="$BASE_DEPS_LIBS $GSF_DEPS_LIBS"
    use_gsf=yes
  ],[
    AM_CONDITIONAL(USE_GSF, false)
    use_gsf=no
  ]
)

# GObject Introspection
AC_ARG_ENABLE([introspection],
  AS_HELP_STRING([--enable-introspection],
     [enable introspection for this build (default=auto)]),
     , 
     [enable_introspection=auto])

case "x${enable_introspection}" in
  xyes|xauto)
    PKG_CHECK_MODULES(GOBJECT_INTROSPECTION, [gobject-introspection-1.0],[
        AM_CONDITIONAL(USE_GIR, true)
        G_IR_SCANNER=`$PKG_CONFIG --variable=g_ir_scanner gobject-introspection-1.0`
        AC_SUBST(G_IR_SCANNER)
        G_IR_COMPILER=`$PKG_CONFIG --variable=g_ir_compiler gobject-introspection-1.0`
        AC_SUBST(G_IR_COMPILER)
        G_IR_GENERATE=`$PKG_CONFIG --variable=g_ir_generate gobject-introspection-1.0`
        AC_SUBST(G_IR_GENERATE)
        GIRDIR=`$PKG_CONFIG --variable=girdir gobject-introspection-1.0`
        AC_SUBST(GIRDIR)
        TYPELIBDIR=`$PKG_CONFIG --variable=typelibdir gobject-introspection-1.0`
        AC_SUBST(TYPELIBDIR)
        enable_gir="yes"
      ],[
        AM_CONDITIONAL(USE_GIR, false)
        enable_gir="no"
      ]
    )
    ;;
  *)
    AM_CONDITIONAL(USE_GIR, false)
    enable_gir="no"
    ;;
esac

GLIB_PREFIX="`$PKG_CONFIG --variable=prefix glib-2.0`"
GTK_PREFIX="`$PKG_CONFIG --variable=prefix gtk+-2.0`"
GST_PREFIX="`$PKG_CONFIG --variable=prefix gstreamer-0.10`"
GCANVAS_PREFIX="`pkg-config --variable=prefix libgnomecanvas-2.0`"
AC_SUBST(GLIB_PREFIX)
AC_SUBST(GTK_PREFIX)
AC_SUBST(GST_PREFIX)
AC_SUBST(GCANVAS_PREFIX)

dnl check for pending gtk+ addditions
dnl ac_libs_save="$LIBS"
dnl AC_CHECK_LIB( gtk-x11-2.0, gtk_tree_view_column_set_wrap_header_widget, \
dnl   AC_DEFINE(HAVE_GTK_TREE_VIEW_COLUMN_PATCH, [1], [Define to 1 if we have the pending gtk+ patch applied]) \
dnl )
dnl LIBS="$ac_libs_save"

#dnl check for gstreamer devel additions
#ac_cflags_save="$CFLAGS"
#ac_cppflags_save="$CPPFLAGS"
#ac_ldflags_save="$LDFLAGS"
#CFLAGS="$CFLAGS $BASE_DEPS_CFLAGS"
#CPPFLAGS="$CPPFLAGS $BASE_DEPS_CFLAGS"
#LDFLAGS="$LDFLAGS $BASE_DEPS_LIBS"
#AC_CHECK_HEADERS(gst/gstpreset.h)
#LIBS="$ac_libs_save"
#CFLAGS="$ac_cflags_save"
#CPPFLAGS="$ac_cppflags_save"
#LDFLAGS="$ac_ldflags_save"

dnl Add additional libraries
BASE_DEPS_LIBS="$LIBINTL $BASE_DEPS_LIBS"

dnl test for availability of check unittest package (with a relative new function)
dnl @todo: if we configure without --enable-debug most tests wont work
PKG_CHECK_MODULES(CHECK, check >= 0.9.2,
  AM_CONDITIONAL(BUILD_CHECK_TESTS, true),
  AM_CONDITIONAL(BUILD_CHECK_TESTS, false)
)

dnl Check for gtk-doc.
GTK_DOC_CHECK([1.4])

dnl Check for xsltproc
dnl AC_PATH_PROG(xsltproc, xsltproc, no)
dnl AM_CONDITIONAL(ENABLE_XSLTPROC, test ! x$xsltproc = xno)

dnl Check for scrollkeeper
AC_PATH_PROG(scrollkeeper_config, scrollkeeper-config, no)
if test x$scrollkeeper_config = xno; then
  have_scrollkeeper=no
  AC_MSG_WARN([Couldn't find scrollkeeper-config, please install the scrollkeeper package])
dnl  if test x$xsltproc = xno; then
dnl    AC_MSG_WARN([Couldn't find xsltproc, please install the libxml/libxslt package])
dnl  fi
else
  have_scrollkeeper=yes
fi
AM_CONDITIONAL(ENABLE_SCROLLKEEPER, test ! x$scrollkeeper_config = xno)
SCROLLKEEPER_BUILD_REQUIRED=0.3.5
AC_SUBST(SCROLLKEEPER_BUILD_REQUIRED)

dnl Check for aspell (spellcheck the docs)
AC_CHECK_PROGS(ASPELL,[aspell],[])
AM_CONDITIONAL(WITH_ASPELL,[test "x$ASPELL" != "x"])

dnl Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([X11/Xlocale.h])
AC_CHECK_HEADERS([linux/input.h],
  [
    have_linux_input_h=yes
    LINUX_INPUT_DOC_TYPES=
  ],
  [
    have_linux_input_h=no
    LINUX_INPUT_DOC_TYPES=%
  ]
)
AC_SUBST(LINUX_INPUT_DOC_TYPES)
AM_CONDITIONAL(USE_LINUX_INPUT, test x$have_linux_input_h = xyes)

dnl Checks for typedefs, structures, and compiler characteristics.
AM_C_PROTOTYPES
test "x$U" != "x" && AC_MSG_ERROR(Compiler not ANSI compliant)
AC_C_CONST

dnl Checks for library functions.
dnl libc functions
AC_CHECK_FUNCS( sched_setscheduler)
AC_CHECK_FUNCS( mlockall)
AC_CHECK_FUNCS( getrusage)
AC_CHECK_FUNCS( setrlimit)

dnl POSIX timers
AC_CHECK_FUNCS(clock_gettime, [], [
  AC_CHECK_LIB(rt, clock_gettime, [
    AC_DEFINE(HAVE_CLOCK_GETTIME, 1)
    FEATURE_LIBS="$FEATURE_LIBS -lrt"
  ])
])

dnl Extra vars
BT_INCLUDEDIR='-I${includedir}'
dnl -Wl,--as-needed # can be put into CFLAGS to drop all unused libs
BT_CFLAGS="$DEBUG_CFLAGS $COVERAGE_CFLAGS $BT_DISABLE_DEPRECATED"
BT_LIBDIR='-L${libdir}'
BT_LIBS="$DEBUG_LDFLAGS $FEATURE_LIBS $COVERAGE_LIBS"

dnl check for desktop and mimetype utilities
AC_PATH_PROG(UPDATE_MIME_DATABASE, update-mime-database)
AC_PATH_PROG(UPDATE_DESKTOP_DATABASE, update-desktop-database)

dnl Substitutes

AC_SUBST(BT_INCLUDEDIR)
AC_SUBST(BT_CFLAGS)
AC_SUBST(BT_LIBDIR)
AC_SUBST(BT_LIBS)
AC_SUBST(BASE_DEPS_LIBS)
AC_SUBST(BASE_DEPS_CFLAGS)
AC_SUBST(GUI_DEPS_LIBS)
AC_SUBST(GUI_DEPS_CFLAGS)

SHAVE_INIT([.])
AC_CONFIG_FILES(Makefile shave shave-libtool \
  docs/Makefile \
  docs/version.entities \
    docs/help/Makefile \
      docs/help/bt-edit/Makefile \
        docs/help/bt-edit/C/Makefile \
        docs/help/bt-edit/C/buzztard-edit-C.omf \
    docs/reference/Makefile \
      docs/reference/bt-cmd/Makefile \
      docs/reference/bt-core/Makefile \
      docs/reference/bt-core/buzztard-core.types \
      docs/reference/bt-edit/Makefile \
      docs/reference/bt-ic/Makefile \
      docs/reference/bt-ic/buzztard-ic.types \
  pixmaps/Makefile \
    pixmaps/hicolor/Makefile \
    pixmaps/gnome/Makefile \
  po/Makefile.in \
  src/Makefile \
    src/lib/Makefile \
    src/lib/buzztard.xml \
      src/lib/core/Makefile \
      src/lib/core/libbuzztard-core.pc \
        src/lib/core/libbuzztard-core/Makefile \
        src/lib/core/libbuzztard-core/version.h \
      src/lib/ic/Makefile \
	  src/lib/ic/libbuzztard-ic.pc \
        src/lib/ic/libbuzztard-ic/Makefile \
		src/lib/ic/libbuzztard-ic/version.h \
    src/ui/Makefile \
      src/ui/cmd/Makefile \
      src/ui/edit/Makefile \
      src/ui/edit/buzztard-edit.desktop.in \
  tests/Makefile \
  tests/bt-cfg.sh \
    tests/lib/Makefile \
      tests/lib/core/Makefile \
    tests/ui/Makefile \
      tests/ui/cmd/Makefile \
      tests/ui/edit/Makefile \
)
AC_OUTPUT

echo "
Configuration
	Version                         : ${VERSION}
	Source code location            : ${srcdir}
	Prefix                          : ${prefix}
	Compiler                        : ${CC}

	Documentation (API)             : ${enable_gtk_doc}
	Documentation (User)            : ${have_scrollkeeper}
	Bindings Metadata (GIR)         : ${enable_gir}
	HAL for interaction ctrl.       : ${use_hal}
	Linux HID for interaction ctrl. : ${have_linux_input_h}
	Buzzmachine Host support        : ${use_bml}
	Zipped Song file support (bzt)  : ${use_gsf}
	Hildon Desktop Integration      : ${use_hildon}
        
	Debug                           : ${enable_debug}
	Coverage profiling              : ${enable_coverage}
	Use of deprecated API           : ${set_enable_deprecated}
"

if test "x${use_bml}" = "xno" -o "x${use_hal}" = "xno" ; then
echo "
Some features are not built. If you like to have them built, please check that
you have the required -devel packages installed and that they can be found in
your PKG_CONFIG_PATH ($PKG_CONFIG_PATH).
"
fi

if test ! `echo $PKG_CONFIG_PATH  | grep $prefix` ; then
echo "
You are installing buzztard libraries into a location that is not yet known to
system. In order for other buzztard modules to find them and the application to
work with all features please register the location (see README for details):
export LD_LIBRARY_PATH=$prefix/lib:\$LD_LIBRARY_PATH
export DEVHELP_SEARCH_PATH="\$DEVHELP_SEARCH_PATH:$prefix/share/gtk-doc/html"
export PKG_CONFIG_PATH="\$PKG_CONFIG_PATH:$prefix/lib/pkgconfig"
export GST_PLUGIN_PATH="\$GST_PLUGIN_PATH:$prefix/lib/gstreamer-0.10"
export XDG_DATA_DIRS="\$XDG_DATA_DIRS:$prefix/share"
"
fi

