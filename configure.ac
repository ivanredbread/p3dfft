#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.59])
AC_INIT([P3DFFT], [2.7.4], [dmitry@sdsc.edu])
AM_INIT_AUTOMAKE([no-dependencies])
AC_CONFIG_SRCDIR([build/module.F90])
AC_CONFIG_HEADER([config.h])

# initialize array index
N="0"
N1="0"

# save arguments passed in as a string
AC_DEFINE_UNQUOTED([CONFIGURE_ARGS],["$ac_configure_args"],[arguments passed to configure script])

# check whether to compile using IBM
AC_MSG_CHECKING([whether to use IBM compiler])
AC_ARG_ENABLE(ibm, [AC_HELP_STRING([--enable-ibm], [compile P3DFFT using IBM compiler])], enableibm=$enableval, enableibm=no)
AC_MSG_RESULT([$enableibm])
if test "$enableibm" = "yes"; then
    AC_DEFINE(IBM, 1, [Define if you want to compile P3DFFT using IBM compiler])
	AC_DEFINE(CC, "mpfort", [overriding mpicc to link C programs (only for IBM)])
	eval "ARRAY${N}='-DIBM'"
    N=`expr $N + 1`
	AC_SUBST(CCLD,"$FC")
    compiler="ibm"
fi
# check whether to compile using Intel
AC_MSG_CHECKING([whether to use Intel compiler])
AC_ARG_ENABLE(intel, [AC_HELP_STRING([--enable-intel], [compile P3DFFT using Intel compiler])], enableintel=$enableval, enableintel=no)
AC_MSG_RESULT([$enableintel])
if test "$enableintel" = "yes"; then
    AC_DEFINE(INTEL, 1, [Define if you want to compile P3DFFT using Intel compiler])
	eval "ARRAY${N}='-DINTEL'"
	N=`expr $N + 1`
	AC_SUBST(CCLD,"$CC")
    compiler="intel"
fi

# check whether to compile using PGI
AC_MSG_CHECKING([whether to use PGI compiler])
AC_ARG_ENABLE(pgi, [AC_HELP_STRING([--enable-pgi], [compile P3DFFT using PGI compiler])], enablepgi=$enableval, enablepgi=no)
AC_MSG_RESULT([$enablepgi])
if test "$enablepgi" = "yes"; then
    #	AC_SUBST(LDFLAGS,"$LDFLAGS -pgf90libs")
    AC_DEFINE(PGI, 1, [Define if you want to compile P3DFFT using PGI compiler])
	eval "ARRAY${N}='-DPGI'"
    N=`expr $N + 1`
	AC_SUBST(CCLD,"$CC")
    compiler="pgi"
fi

# check whether to compile using Cray
AC_MSG_CHECKING([whether to use Cray compiler])
AC_ARG_ENABLE(cray, [AC_HELP_STRING([--enable-cray], [compile P3DFFT using CRAY compiler])], enablecray=$enableval, enablecray=no)
AC_MSG_RESULT([$enablecray])
if test "$enablecray" = "yes"; then
   	AC_DEFINE(CRAY, 1, [Define if you want to compile P3DFFT using CRAY compiler])
    eval FCFLAGS="\$FCFLAGS' -em -ef'"
    eval "ARRAY${N}='-DCRAY'"
    N=`expr $N + 1`
    FORTRAN_LIB=""
	AC_SUBST(CCLD,"$CC")
    compiler="cray"
fi

# check whether to compile using GNU
AC_MSG_CHECKING([whether to use GNU compiler])
AC_ARG_ENABLE(gnu, [AC_HELP_STRING([--enable-gnu], [compile P3DFFT using GNU compiler])], enablegnu=$enableval, enablegnu=no)
AC_MSG_RESULT([$enablegnu])
if test "$enablegnu" = "yes"; then
    AC_DEFINE(GNU, 1, [Define if you want to compile P3DFFT using GNU compiler])
	eval "ARRAY${N}='-DGNU'"
    N=`expr $N + 1`
	if (( `echo $HOSTNAME | grep -c "mira\|cetus\|vesta"` > 0 )); then
	    AC_SUBST(CCLD,"$FC")
	else
	    AC_SUBST(CCLD,"$CC")
	fi
    compiler="gnu"
fi

# check whether to use OpenMPI
AC_MSG_CHECKING([whether to use OpenMPI])
AC_ARG_ENABLE(openmpi, [AC_HELP_STRING([--enable-openmpi], [use the OpenMPI MPI implementation])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
case $compiler in
    ibm)
    ;;
    intel)
        if test "$ok" = "yes"; then
	        AC_SUBST(FORTRAN_LIB,"-lmpi_mpifh -limf -lifcore") 
        else
	        AC_SUBST(FORTRAN_LIB,"-limf -lifcore") #-lmpichf90 
        fi
    ;;
    pgi)
        if test "$ok" = "yes"; then
            AC_SUBST(FORTRAN_LIB,"-lmpi_mpifh -pgf90libs")
        else
            AC_SUBST(FORTRAN_LIB,"-lmpichf90 -pgf90libs")
        fi
    ;;
    cray)
    ;;
    gnu)
        if test "$ok" = "yes"; then
	        AC_SUBST(FORTRAN_LIB,"-lmpi_mpifh -lgfortran")
        else
	        AC_SUBST(FORTRAN_LIB,"-lgfortran -lmpichf90")
        fi
    ;;
    *)
esac

# check whether to use OpenMP
AC_MSG_CHECKING([whether to use OpenMP library])
AC_ARG_ENABLE(openmp, [AC_HELP_STRING([--enable-openmp], [for using the OpenMP library (disabled by default)])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
    AC_DEFINE(OPENMP, 1, [Define if you want to use the OpenMP library])
    eval "ARRAY${N}='-DOPENMP'"
    N=`expr $N + 1`
    case $compiler in
        ibm)
            if test "$ok" = "yes"; then
                eval "ARRAY1${N1}='-qsmp=omp'"
                N1=`expr $N1 + 1`
                CFLAGS=$CFLAGS" -qsmp=omp"
            fi
        ;;
        intel)
            if test "$ok" = "yes"; then
                eval "ARRAY${N}='-qopenmp'"
                N=`expr $N + 1`
                CFLAGS=$CFLAGS" -qopenmp"
            fi
        ;;
        pgi)
            if test "$ok" = "yes"; then
                eval "ARRAY${N}='-mp'"
                N=`expr $N + 1`
                CFLAGS=$CFLAGS" -mp"
            fi
        ;;
        cray)
        ;;
        gnu)
            if test "$ok" = "yes"; then
                eval "ARRAY${N}='-fopenmp'"
                N=`expr $N + 1`
                CFLAGS=$CFLAGS" -fopenmp"
            fi
        ;;
        *)
    esac
else
    case $compiler in
        cray)
            if test "$ok" = "no"; then
                eval "ARRAY${N}='-h noomp'"
                N=`expr $N + 1`
                CFLAGS=$CFLAGS" -h noomp"
            fi
        ;;
        *)
    esac
fi

# check whether to enable single precision
AC_MSG_CHECKING([whether to enable single precision])
AC_ARG_ENABLE(single, [AC_HELP_STRING([--enable-single], [compile P3DFFT in single precision (default is double precision)])], spval=$enableval, spval=no)
AC_MSG_RESULT([$spval])
if test "$spval" = "yes"; then
	AC_DEFINE(SINGLE_PREC, 1, [Define if you want to compile P3DFFT in single precision])
	eval "ARRAY${N}='-DSINGLE_PREC'"
        N=`expr $N + 1`
fi

# check whether to enable 1D decomposition
AC_MSG_CHECKING([whether to enable 1D decomposition])
AC_ARG_ENABLE(oned, [AC_HELP_STRING([--enable-oned], [for 1D decomposition (default is 2D but it can be made 1D through setting up grid 1 x n when running the code) ])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
        AC_DEFINE(ONED, 1, [Define if you want 1D decomposition])
	eval "ARRAY${N}='-DONED'"
	N=`expr $N + 1`
fi

# check whether to enable estimation
AC_MSG_CHECKING([whether to enable estimation])
AC_ARG_ENABLE(estimate, [AC_HELP_STRING([--enable-estimate], [If this argument is passed, the FFTW library will not use run-time tuning to select the fastest algorithm for computing FFTs (this option only goes with --enable-fftw) .])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
        AC_DEFINE(ESTIMATE, 1, [Define whether you want to enable estimation])
	eval "ARRAY${N}='-DESTIMATE'"
        N=`expr $N + 1`
fi

# check whether to enable the measure algorithm
AC_MSG_CHECKING([whether to enable the measure algorithm])
AC_ARG_ENABLE(measure, [AC_HELP_STRING([--enable-measure], [for search-once-for-the-fast-algorithm (takes more time on p3dfft_setup()); this option only goes with --enable-fftw.])], ok=$enableval, ok=yes)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
        AC_DEFINE(MEASURE, 1, [Define if you want to enable the measure algorithm])
	eval "ARRAY${N}='-DMEASURE'"
        N=`expr $N + 1`
fi

# check whether to enable the patient algorithm
AC_MSG_CHECKING([whehter to enable the patient algorithm])
AC_ARG_ENABLE(patient, [AC_HELP_STRING([--enable-patient], [for search-once-for-the-fastest-algorithm (takes much more time on p3dfft_setup()); this option only goes with --enable-fftw.])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
        AC_DEFINE(PATIENT, 1, [Define if you want to enable the patient algorithm])
	eval "ARRAY${N}='-DPATIENT'"
        N=`expr $N + 1`
fi

# check whether to enable C convention for processor dimensions
AC_MSG_CHECKING([whether to enable C convention for processor dimensions])
AC_ARG_ENABLE(dimsc, [AC_HELP_STRING([--enable-dimsc], [to assign processor rows and columns according to C convention, rather than "Fortran", which is default.])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
        AC_DEFINE(DIMS_C, 1, [Define if you want to enable C convention for processor dimensions])
	eval "ARRAY${N}='-DDIMS_C'"
        N=`expr $N + 1`
fi

# check whether to use MPI_Alltoall instead of MPI_Alltotallv
AC_MSG_CHECKING([whether to use MPI_Alltoall instead of MPI_Alltotallv])
AC_ARG_ENABLE(useeven, [AC_HELP_STRING([--enable-useeven], [for using MPI_Alltoall instead of MPI_Alltotallv. This method pads the send buffers with zeros to make them equal size. This options is not needed on most architectures.])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
        AC_DEFINE(USE_EVEN, 1, [Define if you want to MPI_Alltoall instead of MPI_Alltotallv])
	eval "ARRAY${N}='-DUSE_EVEN'"
        N=`expr $N + 1`
fi

# check whether to enable stride-1 data structures
AC_MSG_CHECKING([whether to enable stride-1 data structures])
AC_ARG_ENABLE(stride1, [AC_HELP_STRING([--enable-stride1], [to enable stride-1 data structures on output (this may in some cases give some advantage in performance). You can define loop blocking factors NBL_X and NBL_Y to experiment, otherwise they are set to default values.])], ok=$enableval, ok=no)
AC_MSG_RESULT([$ok])
if test "$ok" = "yes"; then
        AC_DEFINE(STRIDE1, 1, [Define if you want to enable stride-1 data structures])
	eval "ARRAY${N}='-DSTRIDE1'"
        N=`expr $N + 1`
fi

# check whether to override default value of the NBL_X
AC_MSG_CHECKING([whether to override default value of NBL_X])
AC_ARG_ENABLE(nblx, [AC_HELP_STRING([--enable-nblx], [to define loop blocking factor NBL_X])], nblval=$enableval, nblval="")
if test "$nblval" != ""; then
        AC_MSG_RESULT([yes])
        AC_DEFINE(NBL_X, $nblval, [Define if you want to override the default value of NBL_X])
	eval "ARRAY${N}='-DNBL_X=$nblval'"
        N=`expr $N + 1`
else
        AC_MSG_RESULT([no])
fi

# check whether to override default value of NBL_Y1
AC_MSG_CHECKING([whether to override the default value of NBL_Y1])
AC_ARG_ENABLE(nbly1, [AC_HELP_STRING([--enable-nbly1], [to define loop blocking factor NBL_Y1])], nblval=$enableval, nblval="")
if test "$nblval" != ""; then
        AC_MSG_RESULT([yes])
        AC_DEFINE(NBL_Y1, $nblval, [Define if you want to override the default value of NBL_Y1])
        eval "ARRAY${N}='-DNBL_Y1=$nblval'"
        N=`expr $N + 1`
else
        AC_MSG_RESULT([no])
fi

# check whether to override default value of NBL_Y2
AC_MSG_CHECKING([whether to override the default value of NBL_Y2])
AC_ARG_ENABLE(nbly2, [AC_HELP_STRING([--enable-nbly2], [to define loop blocking factor NBL_Y2])], nblval=$enableval, nblval="")
if test "$nblval" != ""; then
        AC_MSG_RESULT([yes])
        AC_DEFINE(NBL_Y2, $nblval, [Define if you want to override the default value of NBL_Y2])
        eval "ARRAY${N}='-DNBL_Y2=$nblval'"
        N=`expr $N + 1`
else
        AC_MSG_RESULT([no])
fi

# check whether to override default value of NBL_Z
AC_MSG_CHECKING([whether to override the default value of NBL_Z])
AC_ARG_ENABLE(nblz, [AC_HELP_STRING([--enable-nblz], [to define loop blocking factor NBL_Z])], nblval=$enableval, nblval="")
if test "$nblval" != ""; then
        AC_MSG_RESULT([yes])
        AC_DEFINE(NBL_Z, $nblval, [Define if you want to override the default value of NBL_Z])
        eval "ARRAY${N}='-DNBL_Z=$nblval'"
        N=`expr $N + 1`
else
        AC_MSG_RESULT([no])
fi

# check whether to use FFTW
AC_MSG_CHECKING([whether to use FFTW library])
AC_ARG_ENABLE(fftw, [AC_HELP_STRING([--enable-fftw], [for using the FFTW library])], fftwval=$enableval, fftwval=no)
AC_MSG_RESULT([$fftwval])
if test "$fftwval" = "yes"; then
        AC_DEFINE(FFTW, 1, [Define if you want to use the FFTW library])
	eval "ARRAY${N}='-DFFTW'"
        N=`expr $N + 1`
fi

# check whether to use ESSL
AC_MSG_CHECKING([whether to use ESSL library])
AC_ARG_ENABLE(essl, [AC_HELP_STRING([--enable-essl], [for using ESSL (on IBM) in 1D FFTs.])], esslval=$enableval, esslval=no)
AC_MSG_RESULT([$esslval])
if test "$esslval" = "yes"; then
        AC_DEFINE(ESSL, 1, [Define if you want to use the ESSL library instead of FFTW])
#	AC_SUBST(LDFLAGS,"$LDFLAGS -lessl")
	eval "ARRAY${N}='-DESSL'"
        N=`expr $N + 1`
fi

# check to see if FFTW or ESSL has been selected
if test "$fftwval" == "no" && test "$esslval" == "no"; then
        AC_MSG_ERROR([You must choose to use either ESSL or FFTW!])
fi

# check to see if FFTW and ESSL have both been selected
if test "$fftwval" == "yes" && test "$esslval" == "yes"; then
        AC_MSG_ERROR([You must only choose either ESSL or FFTW!])
fi

# check for fftw3
AC_MSG_CHECKING([FFTW directory])
AC_ARG_WITH(fftw, [AC_HELP_STRING([--with-fftw], [path to location of FFTW library])], withfftw=$withval, withfftw="")
if test "$withfftw" != "yes" && test "$withfftw" != ""; then
	AC_MSG_RESULT([$withfftw])
	if test -e [$withfftw/lib/libfftw3.a ]; then
		AC_SUBST(FFTW_INC, "-I$withfftw/include")
		AC_SUBST(FFTW_LIB, "$withfftw/lib/libfftw3.a")
        elif test -e [$withfftw/lib64/libfftw3.a ]; then
                AC_SUBST(FFTW_INC, "-I$withfftw/include")
                AC_SUBST(FFTW_LIB, "$withfftw/lib64/libfftw3.a")
	else
		AC_MSG_ERROR([libfftw3.a was not found in given location!])
	fi

	if test "$spval" == "yes"; then
		AC_SUBST(FFTWF, "$withfftw/lib/libfftw3f.a")
	fi
else
        AC_MSG_RESULT([none])
	if test "$fftwval" == "yes"; then
		AC_MSG_ERROR([location to FFTW was not given!])
	fi
fi

# check for essl

AC_MSG_CHECKING([ESSL directory])
AC_ARG_WITH(essl, [AC_HELP_STRING([--with-essl], [path to location of ESSL library])], withessl=$withval, withessl="")

if test "$withessl" != "yes" && test "$withessl" != ""; then

        AC_MSG_RESULT([$withessl])
        if [[ -d $withessl/lib64 ]]; then
                AC_SUBST(ESSL_INC, "-I$withessl/include")
                AC_SUBST(ESSL_LIB, "-L$withessl/lib64 -lesslbg -L$IBM_MAIN_DIR/xlf_r/bg/14.1/lib64")
        else
                AC_MSG_ERROR([essl library was not found in given location!])
		fi
else
        AC_MSG_RESULT([none])
        if test "$esslval" == "yes"; then
                AC_MSG_ERROR([location to ESSL was not given!])
        fi
fi

# construct compiler flags
N=`expr $N - 1`
TMP0=
eval TMP0="\$ARRAY0"
for i in $(seq 1 $N)
do
	eval TMP0="\$TMP0' '\$ARRAY${i}"
done

# set C compiler flags
AC_SUBST(CDFLAGS, $TMP0)

# create a new set of flags if compiler is IBM
if test "$enableibm" != "yes"; then
	AC_SUBST(FDFLAGS, $TMP0)
else
	TMP1=
	eval TMP1="-WF,-P"
	for i in $(seq 0 $N)
	do
		eval TMP1="\$TMP1','\$ARRAY${i}"
	done
	for i in $(seq 0 $N1)
	do
		eval TMP1="\$TMP1' '\$ARRAY1${i}"
	done
	AC_SUBST(FDFLAGS, $TMP1)
fi

# Custom strings
AC_SUBST(SUCCESS, "Build successful!")

# Checks for programs.
AC_PROG_FC(mpif90)
AC_CHECK_PROGS(CC, mpicc, $CC)
AC_PROG_RANLIB
AC_PROG_INSTALL
AC_PROG_MAKE_SET

# Checks for libraries.

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([stdlib.h])

# Checks for typedefs, structures, and compiler characteristics.

# Checks for library functions.
AC_FUNC_MALLOC

AC_CONFIG_FILES([Makefile
                 build/Makefile
	 	 sample/Makefile
	 	 sample/C/Makefile
	 	 sample/FORTRAN/Makefile])

AC_SUBST(TESTLIBS, $TESTLIBS)
AC_OUTPUT()
cp config.h include/config.h
AC_MSG_NOTICE([configure script complete!])

