dnl Process this file with autoconf to produce a configure script.
AC_INIT(servald, 0.9)
AC_CONFIG_SRCDIR([serval_types.h])
AC_CONFIG_MACRO_DIR([m4])

CPPFLAGS="$CPPFLAGS -D_GNU_SOURCE"

dnl Set $host_os, which is needed by javac detection.
AC_CANONICAL_SYSTEM

dnl Various GCC function and variable attributes
AX_GCC_FUNC_ATTRIBUTE(aligned)
AX_GCC_FUNC_ATTRIBUTE(alloc_size)
AX_GCC_FUNC_ATTRIBUTE(error)
AX_GCC_FUNC_ATTRIBUTE(format)
AX_GCC_FUNC_ATTRIBUTE(malloc)
AX_GCC_FUNC_ATTRIBUTE(unused)
AX_GCC_FUNC_ATTRIBUTE(used)
AX_GCC_VAR_ATTRIBUTE(section)
AX_GCC_VAR_ATTRIBUTE(section_seg)

dnl Init pkg-config
PKG_PROG_PKG_CONFIG()

dnl Specify default instance path
AC_ARG_VAR([INSTANCE_PATH], [default instance path for servald])
AS_IF([test "x$INSTANCE_PATH" != x], [AC_DEFINE_UNQUOTED([INSTANCE_PATH], ["$INSTANCE_PATH"], [default instance path])])

dnl Specify default Serval config directory
AC_ARG_VAR([SERVAL_ETC_PATH], [default Serval config directory])
AS_IF([test "x$SERVAL_ETC_PATH" != x], [AC_DEFINE_UNQUOTED([SERVAL_ETC_PATH], ["$SERVAL_ETC_PATH"], [default config directory])])

dnl Specify default Serval run directory
AC_ARG_VAR([SERVAL_RUN_PATH], [default Serval run directory])
AS_IF([test "x$SERVAL_RUN_PATH" != x], [AC_DEFINE_UNQUOTED([SERVAL_RUN_PATH], ["$SERVAL_RUN_PATH"], [default run directory])])

dnl Specify default Serval log directory
AC_ARG_VAR([SERVAL_LOG_PATH], [default Serval log directory])
AS_IF([test "x$SERVAL_LOG_PATH" != x], [AC_DEFINE_UNQUOTED([SERVAL_LOG_PATH], ["$SERVAL_LOG_PATH"], [default log directory])])

dnl Specify default system log directory
AC_ARG_VAR([SYSTEM_LOG_PATH], [default system log directory])
AS_IF([test "x$SYSTEM_LOG_PATH" != x], [AC_DEFINE_UNQUOTED([SYSTEM_LOG_PATH], ["$SYSTEM_LOG_PATH"], [default system log directory])])

dnl Specify default Serval tmp directory
AC_ARG_VAR([SERVAL_TMP_PATH], [default Serval tmp directory])
AS_IF([test "x$SERVAL_TMP_PATH" != x], [AC_DEFINE_UNQUOTED([SERVAL_TMP_PATH], ["$SERVAL_TMP_PATH"], [default Serval tmp directory])])

dnl Specify default Rhizome store directory
AC_ARG_VAR([RHIZOME_STORE_PATH], [default Rhizome store directory])
AS_IF([test "x$RHIZOME_STORE_PATH" != x], [AC_DEFINE_UNQUOTED([RHIZOME_STORE_PATH], ["$RHIZOME_STORE_PATH"], [default Rhizome store directory])])

dnl Check for programs.
AC_PROG_CC

dnl Check for compiler options
AX_APPEND_COMPILE_FLAGS(
    -Wno-empty-body \
    -Wno-unused-value \
    -Wno-unused-function \
    -Wno-unused-parameter \
    -Wno-unused-variable \
    -Wno-unused-but-set-variable \
    -Wno-missing-field-initializers \
    -Wno-deprecated-declarations,
[CFLAGS_SQLITE], [-Werror])
AC_SUBST([CFLAGS_SQLITE])

dnl Math library functions for spandsp
AC_CHECK_HEADERS([math.h], [INSERT_MATH_HEADER="#include <math.h>"])
AC_CHECK_HEADERS([float.h])

dnl Check for a working Java compiler, keep going if unsuccessful.
dnl *** Kludge: override AC_MSG_ERROR because AC_PROG_JAVAC does not have
dnl *** [if-found] and [if-not-found] action parameters.
pushdef([AC_MSG_ERROR], defn([AC_MSG_WARN]))
AC_PROG_JAVAC
popdef([AC_MSG_ERROR])
AC_SUBST([JAVAC])

dnl Check for JNI includes, keep going if not present.
if test -n "$JAVAC"; then
    dnl *** Kludge: override AC_MSG_ERROR because AC_JNI_INCLUDE_DIR does not have
    dnl *** [if-found] and [if-not-found] action parameters.
    pushdef([AC_MSG_ERROR], defn([AC_MSG_WARN]))
    AC_JNI_INCLUDE_DIR
    for JNI_INCLUDE_DIR in $JNI_INCLUDE_DIRS; do
        CPPFLAGS="$CPPFLAGS -I$JNI_INCLUDE_DIR"
    done
    popdef([AC_MSG_ERROR])
fi

dnl XXX Isn't this pointless? we are always linked against libc
AC_CHECK_LIB(c,srandomdev)

AC_CHECK_LIB(m,sqrtf,[LDFLAGS="$LDFLAGS -lm"])
AC_CHECK_LIB(nsl,callrpc,[LDFLAGS="$LDFLAGS -lnsl"])
AC_CHECK_LIB(dl,dlopen,[LDFLAGS="$LDFLAGS -ldl"])

dnl Solaris hides nanosleep here
AC_CHECK_LIB(rt,nanosleep)

AC_CHECK_FUNCS([getpeereid bcopy bzero bcmp lseek64])
AC_CHECK_TYPES([off64_t], [have_off64_t=1], [have_off64_t=0])
AC_CHECK_SIZEOF([off_t])

dnl There must be a 64-bit seek(2) system call of some kind
AS_IF([test "x$have_lseek64_t" = "xno" -a "x$ac_cv_sizeof_off_t" != x8 ], [
    AC_MSG_ERROR([Missing lseek64(2) system call])
])

AC_CHECK_HEADERS(
    stdio.h \
    errno.h \
    stdlib.h \
    strings.h \
    unistd.h \
    string.h \
    arpa/inet.h \
    sys/socket.h \
    sys/mman.h \
    sys/time.h \
    sys/ucred.h \
    sys/statvfs.h \
    sys/stat.h \
    sys/vfs.h \
    poll.h \
    netdb.h \
    linux/ioctl.h \
    linux/netlink.h \
    linux/rtnetlink.h \
    net/if.h \
    netinet/in.h \
    ifaddrs.h \
    net/route.h \
    signal.h \
    jni.h \
    ucred.h \
    sys/filio.h \
    sys/endian.h \
    sys/byteorder.h \
    sys/sockio.h \
    sys/socket.h
)
AC_CHECK_HEADERS(
    linux/if.h
,,, [
#ifdef HAVE_SYS_SOCKET_H
#include <sys/socket.h>
#endif
])

AC_SUBST([HAVE_JNI_H], [$ac_cv_header_jni_h])

dnl libsodium

m4_define([LIBSODIUM_MESSAGE], [
*** If your platform provides a libsodium development package, then
*** install it and try again, eg, on Debian/Ubuntu:
***     sudo apt-get install libsodium-dev
*** If using a libsodium built locally from source, ensure that the
*** $1 environment variable is set, eg, with the command:
***     . libsodium/settings.sh
*** See INSTALL.md and doc/Development.md for more information.
])

AC_CHECK_HEADERS(
    sodium.h,, [AC_MSG_ERROR([missing sodium.h LIBSODIUM_MESSAGE([C_INCLUDE_PATH])])]
)

AC_CHECK_LIB(sodium, sodium_init,, [AC_MSG_ERROR([missing libsodium LIBSODIUM_MESSAGE([LIBRARY_PATH])])])

dnl Check if the Linux gettid() and tgkill() system calls are supported.
AC_CHECK_FUNCS([gettid tgkill])
AC_CACHE_CHECK([Linux thread system calls], ac_cv_have_linux_threads, [
    ac_cv_have_linux_threads=no
    AC_COMPILE_IFELSE(
        [AC_LANG_PROGRAM([
                #include <unistd.h>
                #include <sys/syscall.h>
                #include <signal.h>
            ],
            [syscall(SYS_tgkill, getpid(), syscall(SYS_gettid), SIGHUP)]
        )],
        [ac_cv_have_linux_threads=yes]
    )
])
AS_IF([test "x$ac_cv_have_linux_threads" = xyes],
      [AC_DEFINE([HAVE_LINUX_THREADS], 1, [Linux threads are supported - gettid(2) and tgkill(2).])])

dnl Lazy way of checking for Linux
AS_IF([test "x$ac_cv_header_linux_if_h" = xyes],
      [AC_DEFINE([USE_ABSTRACT_NAMESPACE], 1, [Use abstract namespace sockets for local communication.])])

AC_CACHE_CHECK([linker -z relro option], libc_cv_z_relro, [dnl
  libc_cv_z_relro=no
  AS_IF([AC_TRY_COMMAND([${CC-cc} -v --help 2>&1 | grep "z relro" 1>&AS_MESSAGE_LOG_FD])], [
    AS_IF([AC_TRY_COMMAND([${CC-cc} -Wl,--verbose 2>&1 | grep DATA_SEGMENT_RELRO_END 1>&AS_MESSAGE_LOG_FD])], [
      libc_cv_z_relro=yes
      LDFLAGS="$LDFLAGS -Wl,-z,relro"
    ])
  ])
])

dnl Stack smashing protection is not available on all platforms
AC_MSG_CHECKING([for SSP support])
have_ssp=0
save_cflags="$CFLAGS"
CFLAGS="$CFLAGS -fstack-protector --param=ssp-buffer-size=4"
AC_LANG([C])
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([])], [
    AC_LINK_IFELSE([AC_LANG_PROGRAM([])], [
        have_ssp=1
    ])
])
AS_IF([test x"$have_ssp" = "x1"], [
    AC_MSG_RESULT([yes])
    AC_SUBST([HAVE_SSP],1)
], [
    AC_MSG_RESULT([no])
    CFLAGS="$save_cflags"
])

dnl Some platforms still seem to lack the basic single precision trig and power related function.
AC_SEARCH_LIBS([sinf], [m], AC_DEFINE([HAVE_SINF], [1], [Define to 1 if the sinf() function is available.]))
AC_SEARCH_LIBS([cosf], [m], AC_DEFINE([HAVE_COSF], [1], [Define to 1 if the cosf() function is available.]))
AC_SEARCH_LIBS([tanf], [m], AC_DEFINE([HAVE_TANF], [1], [Define to 1 if the tanf() function is available.]))
AC_SEARCH_LIBS([asinf], [m], AC_DEFINE([HAVE_ASINF], [1], [Define to 1 if the asinf() function is available.]))
AC_SEARCH_LIBS([acosf], [m], AC_DEFINE([HAVE_ACOSF], [1], [Define to 1 if the acosf() function is available.]))
AC_SEARCH_LIBS([atanf], [m], AC_DEFINE([HAVE_ATANF], [1], [Define to 1 if the atanf() function is available.]))
AC_SEARCH_LIBS([atan2f], [m], AC_DEFINE([HAVE_ATAN2F], [1], [Define to 1 if the atan2f() function is available.]))
AC_SEARCH_LIBS([ceilf], [m], AC_DEFINE([HAVE_CEILF], [1], [Define to 1 if the ceilf() function is available.]))
AC_SEARCH_LIBS([floorf], [m], AC_DEFINE([HAVE_FLOORF], [1], [Define to 1 if the floorf() function is available.]))
AC_SEARCH_LIBS([powf], [m], AC_DEFINE([HAVE_POWF], [1], [Define to 1 if the powf() function is available.]))
AC_SEARCH_LIBS([expf], [m], AC_DEFINE([HAVE_EXPF], [1], [Define to 1 if the expf() function is available.]))
AC_SEARCH_LIBS([logf], [m], AC_DEFINE([HAVE_LOGF], [1], [Define to 1 if the logf() function is available.]))
AC_SEARCH_LIBS([log10f], [m], AC_DEFINE([HAVE_LOG10F], [1], [Define to 1 if the log10f() function is available.]))

dnl Check for strlcpy (eg Ubuntu)
AC_SEARCH_LIBS([strlcpy], [], AC_DEFINE([HAVE_STRLCPY], [1], [Define to 1 if the strlcpy() function is available.]))

dnl Put all the -DHAVE_BLAH=1 definitions into config.h instead of expanding
dnl them in @DEFS@ on the command-line of every compilation invoked by make.
AC_CONFIG_HEADERS([config.h])
AC_SUBST([CONFIG_H], [config.h])

AC_OUTPUT([
    Makefile
    testconfig.sh
    java-api/Makefile
])
