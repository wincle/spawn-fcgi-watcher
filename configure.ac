#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ(2.61)
AC_INIT([spawn-fcgi-watcher],[1.6.4])
AC_CONFIG_SRCDIR([src/spawn-fcgi.c])
AC_CONFIG_HEADER([config.h])

AM_INIT_AUTOMAKE([-Wall -Werror foreign])

# Checks for programs.
AC_PROG_CC
AC_PROG_MAKE_SET

dnl @synopsis TRY_CFLAGS [compiler flags]
dnl @summary check whether C compiler supports given flags and adds them to CFLAGS
AC_DEFUN([TRY_CFLAGS],
[dnl
  AC_MSG_CHECKING([if $CC supports $1])
  AC_LANG_PUSH([C])
  ac_try_cflags_saved_cflags="${CFLAGS}"
  CFLAGS="${CFLAGS} $1"
  AC_COMPILE_IFELSE([AC_LANG_PROGRAM([])],
    [AC_MSG_RESULT([yes])],
    [
      AC_MSG_ERROR([no])
      # options not supported, remove them:
      CFLAGS="${ac_try_cflags_saved_cflags}"
    ]
  )
  AC_LANG_POP([C])
])

# Checks for libraries.

# Checks for header files.
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS([arpa/inet.h errno.h fcntl.h getopt.h grp.h netdb.h \
                  netinet/in.h netinet/tcp.h pwd.h stdio.h stdlib.h \
                  string.h sys/ioctl.h sys/socket.h sys/stat.h sys/time.h \
                  sys/types.h sys/un.h sys/wait.h unistd.h signal.h pthread.h])


# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_UID_T
AC_TYPE_PID_T
AC_HEADER_TIME
AC_CHECK_TYPES(socklen_t,,,[#include <sys/types.h>
#include <sys/socket.h>])

## solaris needs -lsocket -lnsl
AC_SEARCH_LIBS([socket],[socket])
AC_SEARCH_LIBS([inet_addr],[nsl socket])
AC_CHECK_LIB([pthread], [pthread_create], [LIBS="$LIBS -lpthread"],
			[AC_SEARCH_LIBS([pthread_create], , , AC_MSG_ERROR([libpthread is missing]))])

# Checks for library functions.
AC_FUNC_CHOWN
AC_FUNC_FORK
AC_FUNC_MALLOC
AC_FUNC_SELECT_ARGTYPES
AC_FUNC_STAT
AC_CHECK_FUNCS([dup2 memset putenv select socket strerror strtol issetugid inet_pton pthread_create pthread_join signal])


# Check for IPv6 support

AC_ARG_ENABLE(ipv6,
  AC_HELP_STRING([--disable-ipv6],[disable IPv6 support]),
  [case "${enableval}" in
   yes) ipv6=true ;;
    no) ipv6=false ;;
     *) AC_MSG_ERROR(bad value ${enableval} for --enable-ipv6) ;;
  esac],[ipv6=true])

if test x$ipv6 = xtrue; then
  AC_CACHE_CHECK([for IPv6 support], ac_cv_ipv6_support,
    [AC_TRY_LINK([[
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
    ]], [[
struct sockaddr_in6 s; struct in6_addr t=in6addr_any; int i=AF_INET6; s; t.s6_addr[0] = 0;
    ]], [ac_cv_ipv6_support=yes], [ac_cv_ipv6_support=no])])

  if test "$ac_cv_ipv6_support" = yes; then
    AC_DEFINE(HAVE_IPV6,1,[Whether to enable IPv6 support])
  fi
fi


# check for extra compiler options (warning options)
if test "${GCC}" = "yes"; then
  TRY_CFLAGS([-Wall -W -Wshadow -pedantic -Wno-unused-parameter -Wno-unused-result])
  TRY_CFLAGS([-std=gnu99])
fi

AC_ARG_ENABLE(extra-warnings,
 AC_HELP_STRING([--enable-extra-warnings],[enable extra warnings (gcc specific)]),
 [case "${enableval}" in
   yes) extrawarnings=true ;;
    no) extrawarnings=false ;;
     *) AC_MSG_ERROR(bad value ${enableval} for --enable-extra-warnings) ;;
  esac],[extrawarnings=false])

if test x$extrawarnings = xtrue; then
  TRY_CFLAGS([-g -O2 -g2 -Wall -Wmissing-declarations -Wdeclaration-after-statement -Wcast-align -Winline -Wsign-compare -Wnested-externs -Wpointer-arith -Wl,--as-needed -D_FORTIFY_SOURCE=2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security -Wno-unused-parameter -Wno-unused-result])
fi

AC_CONFIG_FILES([Makefile src/Makefile doc/Makefile])
AC_OUTPUT
