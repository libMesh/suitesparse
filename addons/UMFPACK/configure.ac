AC_INIT([umfpack], [5.6.1], [davis@cise.ufl.edu])

AC_CONFIG_SRCDIR([Source/umf_solve.c])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_AUX_DIR([config])

AM_INIT_AUTOMAKE([dist-bzip2 foreign])

# use silent rules if available - automake 1.11
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES(yes)])

LT_INIT
AC_PROG_CC

# Checks for header files.
AC_CHECK_HEADERS([float.h string.h stdlib.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_SIZE_T
AC_FUNC_ERROR_AT_LINE
AC_FUNC_MALLOC

# Checks and automatically links with required system libraries
AC_CHECK_LIB([m], [pow])

# Optional documentation
AX_SUITESPARSE_DOC

# Checks for required SuiteSparse libraries
PC_LIBS=

AX_CHECK_PKG_LIB(
	[suitesparseconfig],
	[SuiteSparse_config.h],
	[SuiteSparse_malloc],,
	[AC_MSG_ERROR([Could not find suitesparseconfig library])])

AX_CHECK_PKG_LIB(
	[amd],
	[amd.h],
	[amd_aat],,
	[AC_MSG_ERROR([Could not find the amd library])])

AC_PROG_F77
AC_F77_LIBRARY_LDFLAGS
PKG_CHECK_MODULES([BLAS],
    		  [blas],
		  [BLAS_PC=blas],
		  [AX_BLAS([PC_LIBS="$PC_LIBS $BLAS_LIBS"],
		   	    AC_MSG_ERROR([Cannot find blas libraries]))])

# Optional cholmod library
AC_ARG_WITH(cholmod, 
    [AC_HELP_STRING([--without-cholmod],
		    [Do not include the cholmod module])],
     [with_cholmod=$withval],
     [with_cholmod=yes])
if test x$with_cholmod = xyes; then
    AX_CHECK_PKG_LIB(
		[cholmod],
		[cholmod.h],
		[cholmod_analyze],,
		[AC_MSG_ERROR([Could not find the cholmod library])])
else
    AC_DEFINE([NCHOLMOD], [1], [cholmod bindings are not built])
    # stupid trick to avoid patching umf_cholmod.c to include config.h
    CHOLMOD_CFLAGS="-DNCHOLMOD=1"
    AC_SUBST([CHOLMOD_CFLAGS])
fi

# Optional fortran tests
AC_MSG_CHECKING([whether to enable fortran tests])
AC_ARG_ENABLE([fortran-test],
    AS_HELP_STRING([--enable-fortran-test],
		   [Enable building and running FORTRAN tests]),
    [enable_fortran_test=$enableval],
    [enable_fortran_test=no])
if test x"$enable_fortran_test" = xyes ; then
   AC_PROG_MKDIR_P
   AC_MSG_RESULT([yes])
else
    AC_MSG_RESULT([no])
fi
AM_CONDITIONAL([ENABLE_FORTRAN_TEST], [test x$enable_fortran_test = xyes])

AC_SUBST([PC_LIBS])
AC_SUBST([BLAS_PC])

AC_CONFIG_FILES([Demo/Makefile
                 Doc/Makefile
		 Include/Makefile
		 Source/Makefile
		 umfpack.pc
                 Makefile])
AC_OUTPUT
