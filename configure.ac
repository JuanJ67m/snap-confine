AC_PREREQ([2.69])
AC_INIT([snap-confine], [1.0.39], [snapcraft@lists.ubuntu.com])
AC_CONFIG_SRCDIR([src/main.c])
AC_CONFIG_HEADERS([config.h])
AC_USE_SYSTEM_EXTENSIONS
AM_INIT_AUTOMAKE([foreign])
AM_MAINTAINER_MODE([enable])

# Checks for programs.
AC_PROG_CC_C99
AC_PROG_CPP
AC_PROG_INSTALL
AC_PROG_MAKE_SET

AC_LANG([C])
# Checks for libraries.

# Checks for header files.
AC_CHECK_HEADERS([fcntl.h limits.h stdlib.h string.h sys/mount.h unistd.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_CHECK_HEADER_STDBOOL
AC_TYPE_UID_T
AC_TYPE_MODE_T
AC_TYPE_PID_T
AC_TYPE_SIZE_T

# Checks for library functions.
AC_FUNC_CHOWN
AC_FUNC_ERROR_AT_LINE
AC_FUNC_FORK
AC_FUNC_STRNLEN
AC_CHECK_FUNCS([mkdir regcomp setenv strdup strerror])

# Allow to build without apparmor support by calling:
# ./configure --disable-apparmor
# This makes it possible to run snaps in devmode on almost any host,
# regardless of the kernel version.
AC_ARG_ENABLE([apparmor],
    AS_HELP_STRING([--disable-apparmor], [Disable apparmor support]),
    [case "${enableval}" in
        yes) enable_apparmor=yes ;;
        no)  enable_apparmor=no ;;
        *) AC_MSG_ERROR([bad value ${enableval} for --disable-apparmor])
    esac], [enable_apparmor=yes])
AM_CONDITIONAL([APPARMOR], [test "x$enable_apparmor" = "xyes"])

# Allow to build without seccomp support by calling:
# ./configure --disable-seccomp
# This is separate because seccomp support is generally very good and it
# provides useful confinement for unsafe system calls.
AC_ARG_ENABLE([seccomp],
    AS_HELP_STRING([--disable-seccomp], [Disable seccomp support]),
    [case "${enableval}" in
        yes) enable_seccomp=yes ;;
        no)  enable_seccomp=no ;;
        *) AC_MSG_ERROR([bad value ${enableval} for --disable-seccomp])
    esac], [enable_seccomp=yes])
AM_CONDITIONAL([SECCOMP], [test "x$enable_seccomp" = "xyes"])

# Enable older tests only when confinement is enabled and we're building for PC
# The tests are of smaller value as we port more and more tests to spread.
AM_CONDITIONAL([CONFINEMENT_TESTS], [test "x$enable_apparmor" = "xyes" && test "x$enable_seccomp" = "xyes" && ((test "x$host_cpu" = "xx86_64" && test "x$build_cpu" = "xx86_64") || (test "x$host_cpu" = "xi686" && test "x$build_cpu" = "xi686"))])

# Check if seccomp userspace library is available
AS_IF([test "x$enable_seccomp" = "xyes"], [
    PKG_CHECK_MODULES([SECCOMP], [libseccomp], [
      AC_DEFINE([HAVE_SECCOMP], [1], [Build with seccomp support])])
])

# Check if apparmor userspace library is available.
AS_IF([test "x$enable_apparmor" = "xyes"], [
    PKG_CHECK_MODULES([APPARMOR], [libapparmor], [
      AC_DEFINE([HAVE_APPARMOR], [1], [Build with apparmor support])])
], [
    AC_MSG_WARN([
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    X                                                     X
    X Apparmor is disabled, all snaps will run in devmode X
    X                                                     X
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX])
])

# Check if udev and libudev are available.
# Those are now used unconditionally even if apparmor is disabled.
PKG_CHECK_MODULES([LIBUDEV], [libudev])
PKG_CHECK_MODULES([UDEV], [udev])
# Check for glib that we use for unit testing
PKG_CHECK_MODULES([GLIB], [glib-2.0])

# Enable special support for hosts with proprietary nvidia drivers on Ubuntu.
AC_ARG_ENABLE([nvidia-ubuntu],
    AS_HELP_STRING([--enable-nvidia-ubuntu], [Support for proprietary nvidia drivers (Ubuntu)]),
    [case "${enableval}" in
        yes) enable_nvidia_ubuntu=yes ;;
        no)  enable_nvidia_ubuntu=no ;;
        *) AC_MSG_ERROR([bad value ${enableval} for --enable-nvidia-ubuntu])
    esac], [enable_nvidia_ubuntu=no])
AM_CONDITIONAL([NVIDIA_UBUNTU], [test "x$enable_nvidia_ubuntu" = "xyes"])

AS_IF([test "x$enable_nvidia_ubuntu" = "xyes"], [
    AC_DEFINE([NVIDIA_UBUNTU], [1],
        [Support for propietary nvidia drivers (Ubuntu)])])

# Enable special support for hosts with proprietary nvidia drivers on Arch.
AC_ARG_ENABLE([nvidia-arch],
    AS_HELP_STRING([--enable-nvidia-arch], [Support for proprietary nvidia drivers (Arch)]),
    [case "${enableval}" in
        yes) enable_nvidia_arch=yes ;;
        no)  enable_nvidia_arch=no ;;
        *) AC_MSG_ERROR([bad value ${enableval} for --enable-nvidia-arch])
    esac], [enable_nvidia_arch=no])
AM_CONDITIONAL([NVIDIA_ARCH], [test "x$enable_nvidia_arch" = "xyes"])

AS_IF([test "x$enable_nvidia_arch" = "xyes"], [
    AC_DEFINE([NVIDIA_ARCH], [1],
        [Support for propietary nvidia drivers (Arch)])])

AC_ARG_ENABLE([merged-usr],
    AS_HELP_STRING([--enable-merged-usr], [Enable support for merged /usr directory]),
    [case "${enableval}" in
        yes) enable_merged_usr=yes ;;
        no)  enable_merged_usr=no ;;
        *) AC_MSG_ERROR([bad value ${enableval} for --enable-merged-usr])
    esac], [enable_merged_usr=no])
AM_CONDITIONAL([MERGED_USR], [test "x$enable_merged_usr" = "xyes"])

AS_IF([test "x$enable_merged_usr" = "xyes"], [
    AC_DEFINE([MERGED_USR], [1],
        [Support for merged /usr directory])])

AC_CONFIG_FILES([Makefile src/Makefile tests/Makefile docs/Makefile])
AC_OUTPUT
