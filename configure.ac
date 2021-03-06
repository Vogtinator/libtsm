#
# libtsm - build configuration script
# Copyright (c) 2012-2013 David Herrmann <dh.herrmann@gmail.com>
#

AC_PREREQ(2.68)

AC_INIT([libtsm],
        [4.0.0],
        [https://github.com/Aetf/libtsm/issues/new],
        [libtsm],
        [https://github.com/Aetf/libtsm])
AC_CONFIG_SRCDIR([src/tsm/libtsm.h])
AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_HEADER(config.h)
AC_USE_SYSTEM_EXTENSIONS
AC_SYS_LARGEFILE
AC_CANONICAL_HOST

AM_INIT_AUTOMAKE([foreign 1.11 subdir-objects dist-xz no-dist-gzip tar-pax -Wall -Werror -Wno-portability])
AM_SILENT_RULES([yes])

AC_SUBST(PACKAGE_DESCRIPTION, ["terminal-emulator state machine"])

#
# Don't add a default "-g -O2" if CFLAGS wasn't specified. For debugging it is
# often more convenient to have "-g -O0". You can still override it by
# explicitly setting it on the command line.
#

: ${CFLAGS=""}

AC_PROG_CC
AC_PROG_CC_C99
AM_PROG_CC_C_O
m4_ifdef([AM_PROG_AR], [AM_PROG_AR])
AC_PROG_SED
AC_PROG_MKDIR_P
AC_PROG_LN_S
AC_PROG_GREP
AC_PROG_AWK

LT_PREREQ(2.2)
LT_INIT

#
# We need xkbcommon for keysym definitions. If it's not found, we use our own
# private copy of xkbcommon-keysyms.h.
#

PKG_CHECK_MODULES([XKBCOMMON], [xkbcommon],
                  [have_xkbcommon=yes], [have_xkbcommon=no])
AC_SUBST(XKBCOMMON_CFLAGS)
if test "x$have_xkbcommon" = "xyes" ; then
        AC_DEFINE([BUILD_HAVE_XKBCOMMON], [1], [Have xkbcommon library])
fi
AM_CONDITIONAL([BUILD_HAVE_XKBCOMMON], [test "x$have_xkbcommon" = "xyes"])

#
# Test for "check" which we use for our test-suite. If not found, we disable
# all tests.
#

PKG_CHECK_MODULES([CHECK], [check],
                  [have_check=yes], [have_check=no])
AC_SUBST(CHECK_CFLAGS)
AC_SUBST(CHECK_LIBS)
AM_CONDITIONAL([BUILD_HAVE_CHECK], [test "x$have_check" = "xyes"])

#
# Optionally, look for gtk+-3 and friends for our gtktsm example.
# This is linux-only as it uses epoll and friends. Therefore, it's disabled by
# default.
#

AC_MSG_CHECKING([whether to build gtktsm])
AC_ARG_ENABLE([gtktsm],
              [AS_HELP_STRING([--enable-gtktsm],
                              [whether to build gtktsm])])
if test "x$enable_gtktsm" = "x" ; then
        enable_gtktsm="no (default)"
fi
AC_MSG_RESULT([$enable_gtktsm])

PKG_CHECK_MODULES([GTKTSM], [gtk+-3.0 cairo pango pangocairo xkbcommon],
                  [have_gtktsm=yes], [have_gtktsm=no])
AC_SUBST(GTKTSM_CFLAGS)
AC_SUBST(GTKTSM_LIBS)

if test ! "x$have_gtktsm" = "xyes" ; then
        if test "x$enable_gtktsm" = "xyes" ; then
                AC_ERROR([GtkTsm dependencies not found])
        else
                enable_gtktsm="no"
        fi
elif test "x${enable_gtktsm% *}" = "xyes" ; then
        enable_gtktsm="yes"
else
        enable_gtktsm="no"
fi
AM_CONDITIONAL([BUILD_ENABLE_GTKTSM], [test "x$enable_gtktsm" = "xyes"])

#
# debug mode
# If --enable-debug is given, we enable several non-standard debug options. We
# enable a lot of debug options by default, so this option is really only for
# extended developer debug modes.
#

AC_MSG_CHECKING([whether to build with debugging on])
AC_ARG_ENABLE([debug],
              [AS_HELP_STRING([--enable-debug],
                              [whether to build with debugging on])])
if test "x$enable_debug" = "x" ; then
        enable_debug="yes (default)"
fi
AC_MSG_RESULT([$enable_debug])

if test "x${enable_debug% *}" = "xyes" ; then
        enable_debug="yes"
        AC_DEFINE([BUILD_ENABLE_DEBUG], [1], [Enable debug mode])
else
        enable_debug="no"
        AC_DEFINE([NDEBUG], [1], [No Debug])
fi
AM_CONDITIONAL([BUILD_ENABLE_DEBUG],
               [test "x$enable_debug" = "xyes"])

#
# Enable gcc compiler optimizations. We enable them by default but allow
# disabling them for better backtraces during debugging.
#

AC_MSG_CHECKING([whether to enable code optimizations])
AC_ARG_ENABLE([optimizations],
              [AS_HELP_STRING([--disable-optimizations],
                              [whether to disable code optimizations])])
if test "x$enable_optimizations" = "x" ; then
        enable_optimizations="yes (default)"
fi
AC_MSG_RESULT([$enable_optimizations])

if test "x${enable_optimizations% *}" = "xyes" ; then
        enable_optimizations="yes"
else
        enable_optimizations="no"
fi
AM_CONDITIONAL([BUILD_ENABLE_OPTIMIZATIONS],
               [test "x$enable_optimizations" = "xyes"])

#
# Check which linker is used
#

AM_CONDITIONAL([BUILD_HAVE_GNU_LD],
               [test "x$with_gnu_ld" = "xyes"])

#
# Detect darwin targets
#

case "$host_os" in
  darwin*)
    $is_darwin = "xyes";;
esac

AM_CONDITIONAL([BUILD_IS_DARWIN],
               [test "x$is_darwin" = "xyes"])

#
# Makefile vars
# After everything is configured, we create all makefiles.
#

AC_CONFIG_FILES([Makefile
                 src/tsm/libtsm.pc])
AC_OUTPUT

#
# Configuration output
# Show configuration to the user so they can check whether everything was
# configured as expected.
#

AC_MSG_NOTICE([Build configuration:

               prefix: $prefix
          exec-prefix: $exec_prefix
               libdir: $libdir
           includedir: $includedir
              host-os: $host_os

  Miscellaneous Options:
               gtktsm: $enable_gtktsm
                debug: $enable_debug
        optimizations: $enable_optimizations
       building tests: $have_check

        Run "${MAKE-make}" to start compilation process])
