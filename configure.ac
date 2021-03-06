AC_INIT([moloch], [0.10.1-GIT])
AM_INIT_AUTOMAKE([-Wall -Werror foreign])
AC_PROG_CC
AC_PROG_CXX
AC_CONFIG_HEADERS([capture/molochconfig.h])
AC_PREFIX_DEFAULT(["/data/moloch"])
AC_CONFIG_FILES([
  Makefile
  capture/Makefile
  capture/plugins/Makefile
  capture/parsers/Makefile
  db/Makefile
  tests/plugins/Makefile
  viewer/Makefile
  viewer/version.js
])

AC_CHECK_LIB(pcre, main,,AC_MSG_ERROR(please install PCRE library))
AC_CHECK_LIB(uuid, main,,AC_MSG_ERROR(please install uuid library))
AC_CHECK_LIB(magic, main,,AC_MSG_ERROR(please install magic library))


dnl Checks for pfring
AC_MSG_CHECKING(for pfring)
AC_ARG_WITH(pfring,
[  --with-pfring=DIR use pfring build directory],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
  AC_MSG_RESULT($withval)
    if test -f $withval/userland/libpcap/pcap.h -a -f $withval/userland/libpcap/libpcap.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi

      PCAP_CFLAGS="-I$withval/userland/libpcap"
      PCAP_LIBS="$withval/userland/libpcap/libpcap.a $withval/userland/lib/libpfring.a"
    elif test -f $withval/userland/libpcap/include/pcap.h -a -f $withval/userland/libpcap/lib/libpcap.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi

      PCAP_CFLAGS="-I$withval/userland/libpcap/include"
      PCAP_LIBS="$withval/userland/libpcap/lib/libpcap.a $withval/userland/lib/libpfring.a"
    else
      AC_ERROR(pcap.h or libpcap.a not found in $withval)
    fi
    ;;
esac ], [
AC_MSG_RESULT(no) ])

dnl Checks for libpcap
AC_MSG_CHECKING(for libpcap)
AC_ARG_WITH(libpcap,
[  --with-libpcap=DIR use libpcap build directory],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
  AC_MSG_RESULT($withval)
    if test -f $withval/pcap.h -a -f $withval/libpcap.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi

      PCAP_CFLAGS="-I$withval"
      PCAP_LIBS="$withval/libpcap.a"
    elif test -f $withval/include/pcap.h -a -f $withval/lib/libpcap.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi

      PCAP_CFLAGS="-I$withval/include"
      PCAP_LIBS="$withval/lib/libpcap.a"
    else
      AC_ERROR(pcap.h or libpcap.a not found in $withval)
    fi
    ;;
esac ], [
  if test "x$PCAP_LIBS" != "x"; then
      BLAHBLAH=1
  elif test -f ${prefix}/include/pcap.h; then
     PCAP_CFLAGS="-I${prefix}/include"
     PCAP_LIBS="-L${exec_prefix}/lib -lpcap"
  elif test -f /usr/include/pcap/pcap.h; then
     PCAP_CFLAGS="-I/usr/include/pcap"
     PCAP_LIBS="-lpcap"
  else
    TMP=$LIBS
    LIBS="-lpcap $LIBS"
    AC_TRY_LINK([#include <pcap.h>], pcap_open_offline("",""), LIBPCAP_FOUND=1,LIBPCAP_FOUND=0)
    LIBS=$TMP
    if test $LIBPCAP_FOUND = 1 ; then
      PCAP_LIBS="-lpcap"
    else
      AC_ERROR(libpcap not found)
    fi
  fi
AC_MSG_RESULT(yes) ])

# Do we have libnl?
# Modfied version of whats in libpcap configure.ac.  Thank you libpcap!
#
AC_ARG_WITH(libnl,
AC_HELP_STRING([--without-libnl],[disable libnl support @<:@default=yes, on Linux, if present@:>@]),
        with_libnl=$withval,,)

if test x$with_libnl != xno ; then
        have_any_nl="no"

        #
        # Try libnl 3.x first.
        #
        AC_CHECK_LIB(nl-3, nl_socket_alloc,
        [
                #
                # Yes, we have libnl 3.x.
                #
                PCAP_LIBS="$PCAP_LIBS -lnl-genl-3 -lnl-3"
                have_any_nl="yes"
        ])

        if test x$have_any_nl = xno ; then
                #
                # Try libnl 2.x
                #
                AC_CHECK_LIB(nl, nl_socket_alloc,
                [
                        #
                        # Yes, we have libnl 2.x.
                        #
                        PCAP_LIBS="$PCAP_LIBS -lnl-genl -lnl"
                        have_any_nl="yes"
                ])
        fi

        if test x$have_any_nl = xno ; then
                #
                # No, we don't; do we have libnl 1.x?
                #
                AC_CHECK_LIB(nl, nl_handle_alloc,
                [
                        #
                        # Yes.
                        #
                        PCAP_LIBS="$PCAP_LIBS -lnl"
                        have_any_nl="yes"
                ])
        fi

        if test x$have_any_nl = xno ; then
                #
                # No, we don't have libnl at all.
                #
                if test x$with_libnl = xyes ; then
                        AC_MSG_ERROR([libnl support requested but libnl not found])
                fi
        fi
fi


AC_SUBST(PCAP_CFLAGS)
AC_SUBST(PCAP_LIBS)

dnl Checks for libnids
AC_MSG_CHECKING(for libnids)
AC_ARG_WITH(libnids,
[  --with-libnids=DIR use libnids build directory],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
  AC_MSG_RESULT($withval)
    if test -f $withval/src/nids.h -a -f $withval/src/libnids.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi
      NIDS_CFLAGS="-I$withval/src"
      NIDS_LIBS="$withval/src/libnids.a"
    elif test -f $withval/include/nids.h -a -f $withval/lib/libnids.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi
      NIDS_CFLAGS="-I$withval/include"
      NIDS_LIBS="$withval/lib/libnids.a"
    else
      AC_ERROR(nids.h or libnids.a not found in $withval)
    fi
    ;;
esac ], [
  if test -f ${prefix}/include/nids.h; then
     NIDS_CFLAGS="-I${prefix}/include"
     NIDS_LIBS="-L${exec_prefix}/lib -lnids"
  elif test -f /usr/include/nids.h; then
     NIDS_CFLAGS=""
     NIDS_LIBS="-lnids"
  else
    TMP=$LIBS
    LIBS="-lnids $LIBS"
    AC_TRY_LINK([#include <nids.h>], nids_init(), LIBNIDS_FOUND=1,LIBNIDS_FOUND=0)
    LIBS=$TMP
    if test $LIBNIDS_FOUND = 1 ; then
      NIDS_LIBS="-lnids"
    else
      AC_ERROR(libnids not found)
    fi
  fi
AC_MSG_RESULT(yes) ])
AC_SUBST(NIDS_CFLAGS)
AC_SUBST(NIDS_LIBS)

dnl Checks for yara
AC_MSG_CHECKING(for yara)
AC_ARG_WITH(yara,
[  --with-yara=DIR use yara build directory],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
  AC_MSG_RESULT($withval)
    if test -f $withval/libyara/yara.h -a -f $withval/libyara/.libs/libyara.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi
      YARA_CFLAGS="-I$withval/libyara"
      YARA_LIBS="$withval/libyara/.libs/libyara.a"
    elif test -f $withval/include/yara.h -a -f $withval/lib/libyara.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi
      YARA_CFLAGS="-I$withval/include"
      YARA_LIBS="$withval/lib/libyara.a"
    else
      AC_ERROR(yara.h or yara.a not found in $withval)
    fi
    ;;
esac ], [
  if test -f ${prefix}/include/libyara/yara.h; then
     YARA_CFLAGS="-I${prefix}/include/libyara"
     YARA_LIBS="-L${exec_prefix}/lib -lyara"
  elif test -f /usr/include/libyara/yara.h; then
     YARA_CFLAGS=""
     YARA_LIBS="-lyara"
  else
    TMP=$LIBS
    LIBS="-lyara $LIBS"
    AC_TRY_LINK([#include <yara.h>], yr_init(), LIBYARA_FOUND=1,LIBYARA_FOUND=0)
    LIBS=$TMP
    if test $LIBYARA_FOUND = 1 ; then
      YARA_LIBS="-lyara"
    else
      AC_ERROR(yara not found)
    fi
  fi
AC_MSG_RESULT(yes) ])
AC_SUBST(YARA_CFLAGS)
AC_SUBST(YARA_LIBS)


dnl Checks for GeoIP
AC_MSG_CHECKING(for GeoIP)
AC_ARG_WITH(GeoIP,
[  --with-GeoIP=DIR use GeoIP build directory],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
  AC_MSG_RESULT($withval)
    if test -f $withval/libGeoIP/GeoIP.h -a -f $withval/libGeoIP/.libs/libGeoIP.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi
      GEOIP_CFLAGS="-I$withval/libGeoIP"
      GEOIP_LIBS="$withval/libGeoIP/.libs/libGeoIP.a"
    elif test -f $withval/include/GeoIP.h -a -f $withval/lib/libGeoIP.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi
      GEOIP_CFLAGS="-I$withval/include"
      GEOIP_LIBS="$withval/lib/libGeoIP.a"
    else
      AC_ERROR(GeoIP.h or GeoIP.a not found in $withval)
    fi
    ;;
esac ], [
  if test -f ${prefix}/include/GeoIP/GeoIP.h; then
     GEOIP_CFLAGS="-I${prefix}/include/GeoIP"
     GEOIP_LIBS="-L${exec_prefix}/lib -lGeoIP"
  elif test -f /usr/include/GeoIP/GeoIP.h; then
     GEOIP_CFLAGS=""
     GEOIP_LIBS="-lGeoIP"
  else
    TMP=$LIBS
    LIBS="-lGeoIP $LIBS"
    AC_TRY_LINK([#include <GeoIP.h>], GeoIP_open(NULL, GEOIP_MEMORY_CACHE), LIBGEOIP_FOUND=1,LIBGEOIP_FOUND=0)
    LIBS=$TMP
    if test $LIBGEOIP_FOUND = 1 ; then
      GEOIP_LIBS="-lGeoIP"
    else
      AC_ERROR(GeoIP not found)
    fi
  fi
AC_MSG_RESULT(yes) ])
AC_SUBST(GEOIP_CFLAGS)
AC_SUBST(GEOIP_LIBS)


dnl Checks for glib2, these are wrong
AC_MSG_CHECKING(for glib2)
AC_ARG_WITH(glib2,
[  --with-glib2=DIR use glib2 build directory],
[ case "$withval" in
  yes|no)
     AC_MSG_RESULT(no)
     ;;
  *)
  AC_MSG_RESULT($withval)
    if test -f $withval/glib/glib.h -a -f $withval/glib/.libs/libglib-2.0.a; then
      owd=`pwd`
      if cd $withval; then
        withval=`pwd`;
        cd $owd;
      fi
      GLIB2_CFLAGS="-I$withval/glib -I$withval -I$withval/gmodule -I$withval/gobject"
      GLIB2_LIBS="$withval/gio/.libs/libgio-2.0.a $withval/gobject/.libs/libgobject-2.0.a $withval/gthread/.libs/libgthread-2.0.a $withval/gmodule/.libs/libgmodule-2.0.a $withval/glib/.libs/libglib-2.0.a"
    else
      AC_ERROR(glib.h or libglib-2.0.a not found in $withval)
    fi
    ;;
esac ], [
  GLIB2_CFLAGS=`pkg-config gio-2.0 gobject-2.0 gthread-2.0 glib-2.0 gmodule-2.0 --cflags`
  GLIB2_LIBS=`pkg-config gio-2.0 gobject-2.0 gthread-2.0 glib-2.0 gmodule-2.0 --libs`
AC_MSG_RESULT(yes) ])
AC_SUBST(GLIB2_CFLAGS)
AC_SUBST(GLIB2_LIBS)

AC_OUTPUT
