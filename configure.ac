AC_INIT(Buttery, 1.0, zdbiohazard2@gmail.com, buttery)
AM_INIT_AUTOMAKE(foreign)

dnl Check for `btrfs` and `pv` commands.

AC_CHECK_PROGS(BTRFS_CMD, btrfs)
if test "x$BTRFS_CMD" != "xbtrfs" ; then
    AC_MSG_WARN("The 'btrfs' command is required for proper operation.")
fi

AC_CHECK_PROGS(PV_CMD, pv)
if test "x$PV_CMD" != "xpv" ; then
    AC_MSG_WARN("The 'pv' command is required for sending snapshots.")
fi

AC_CHECK_PROGS(A2X_CMD, a2x)
if test "x$A2X_CMD" != "xa2x" ; then
    AC_MSG_ERROR("The 'a2x' command from asciidoc is required to create the manual page.")
fi

AC_CONFIG_FILES(Makefile)
AC_OUTPUT
