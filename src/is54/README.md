```
       =============================================================
       COPYRIGHT NOTE: This source code, and all of its derivations,
       is subject to the "ITU-T General Public License". Please have
       it  read  in    the  distribution  disk,   or  in  the  ITU-T
       Recommendation G.191 on "SOFTWARE TOOLS FOR SPEECH AND  AUDIO
       CODING STANDARDS". In special, this software had its use kindly
       authorized by Motorola, who keeps the original copyright. See
       file cpyright on this directory.
       =============================================================
```

The ITU-T/UGST distribution of the Motorola reference implementation
IS54 program is built of the following files:


# C program code

The main `vselp` encoding PROGRAM is implemented by `vselp.c` (this is NOT a
module!). The other C programs implement its supporting functions:
```
	b_con.c     calcp.c     cunpack0.c  decorr.c
	excite.c    filt4.c     filters.c   flatv.c
	fspace.c    g_quant.c   getp.c      init.c
	interp.c    isdef.c     lag.c       makec.c
	p_ex.c      pconv.c     putc.c      r_sub.c
	rs_rr.c     split.c     t_sub.c     v_con.c
	v_srch.c    vselp.c     wsnr.c
```

There are several specific header files:
```
	r_sub.h     t_sub.h     vparams.h   vselp.h
```

And one UGST general header file:
```
	ugstdemo.h
```

There are several "inline" code files, that are either included during
compilation (32-bit C compilers) or read by the compiled program during run
time (16-bit C compilers):
```
    basis.i     edef.i      gray.i      k_table.i
```

# Makefiles

Makefiles have been provided for automatic build-up of the executable program:
```
make-vms.com: ... DCL for VAX/VMS Vax-cc compiler or the VMS port of gcc
makefile.tcc: ... makefile for MSDOS Borland bcc
makefile.cl: .... makefile for MS Visual C Compiler
makefile.djc: ... makefile for MSDOS port of gcc
makefile.unx: ... makefile for Unix, using either cc, acc (Sun), or gcc
```

# Testing
```
is54-tst.zip: ... PKZIP-compatible archive with test files for the IS54
                implementation and makefiles to automatically carry out the
                tests. BECAREFUL, makefile names are equal to the ones
                described above, but the files are DIFFERENT! This file, and
                the makefiles are in the directory "bin".
```

-- <simao@cpqd.ansp.br> --


# Original README

14/4/93 - IS54 US Cellular Coder

This package is not very user friendly, but it works. To compile
edit the Makefile to change the compilations flags.

If you are not using SPPACK headers, remove the SPPACK flag from
the compile line.

Change the hardwired pathnames ( /usr/kroon ..) in init.c to the
directory that will contain these files.

It runs successfully on SGI R4000 and Sparc stations, and it has
been claimed that it will run on a PC with Turbo C.

For questions contact Peter Kroon kroon@research.att.com  MH x7000

1. General Description

The NADC reference coder is a C language program which implements the
chosen standard NADC speech coding algorithm.  This document is a description
of the program.

1.1 Files

The NADC reference coder comprises several C language source files.  The
"main" function exists in the file `vselp.c`.  All of the other .c files
contain functions which are called at least indirectly from `vselp.c`.

The file `vparams.h` contains macro definitions and external variable
declarations which are needed by nearly every source file, so it is included
in nearly every source file.

The files with the `.i` extension contain C code that is expanded in line
with the "include" preprocessor directive.


1.2  General Flow and Structure

When the coder is run, it first prompts for running conditions (or reads them
from a file).  It then initializes parameters and tables.  These steps are
accomplished through calls to the functions getParams(), calcParams(), and
initTables().

The analysis then begins.  One frame of the input speech is high-pass filtered,
then the Fixed Point Lattice Technique is applied to find the reflection
coefficients.  These reflection coefficients are then converted to direct-form
coefficients.  To obtain coefficients for each subframe, the direct-form
coefficients for the current frame and the last frame are linearly
interpolated.  These steps are accomplished with calls to the functions
FLATV(), RCTOA(), and INTERPOLATE().

The analysis is completed by determining the excitation for each subframe.
This is done in the function T_SUB().  The input speech is first sent through
the W(z) spectral weighting filter.  The codebook searches are then performed;
the adaptive codebook search (lag search) being done first.  The gains applied
to the excitation vectors from the codebooks are then determined, and some
processing is done to set up needed states for the next subframe.  These steps
are accomplished with calls to the functions LAG_SEARCH(), DECORR(), V_SRCH(),
and G_QUANT().  Filtering is done with the functions DIR(), I_DIR() and
ZI_DIR().  Functions for constructing vectors are P_EX(), B_CON(), V_CON(),
and EXCITE().

The coded speech is written to an internal buffer, but may also be written
out to a file.  The coded speech output may be written in two formats, one
which is readable by the decoder portion of the program, and one which is
human-readable.

The synthesis is performed on either the internal codes, or on codes from a
file written out by the analysis section.  The first step in the analysis is
to convert the reflection coefficients to direct-form.  The second step
determines the two other sets of coefficients that are needed in the synthesis:
they are the numerator and denominator coefficients of the adaptive spectral
postfilter.  In the third step the coefficient sets are interpolated to
obtain the coefficients to be used in each subframe.  These steps are
accomplished by the functions ASST() and INTERPOLATE().

The excitation vector for the synthesis is built in the function R_SUB().
The speech is the synthesized through the synthesis filter, and then sent
through the adaptive spectral post-filter.  Functions used are P_EX(), B_CON(),
V_CON() and EXCITE() to generate the excitation; and DIR() and I_DIR() to
perform the filtering.

The synthesized speech is then converted to 16-bit integer representation
and put out to a file.


2.  How to Compile the Coder

This code is maintained with "make", which is a program for
maintaining programs.  If your system runs UNIX, information is
available in the "make" manual page.  If you are not planning to run
this on a UNIX machine, you will have to either use some software
management tool other than "make", or maintain the code yourself.
Even if you don't use "make" to manage this code, it would be a good
idea to understand the file that "make" uses to maintain the code,
called "makefile".  All of the source file dependencies are documented
in "makefile".  Notice, also, that the "makefile" defines "FTYPE" as
"float" globally.  If you do not use make, you will have to include a
similar definition in your management tool.

If you are on a UNIX machine, first make a subdirectory in the
directory where you have the source code, and call the new
subdirectory "obj".  Then type `make uvselp`.  The "make" program will
compile and link all the necessary object files and produce an
executable file called `uvselp`.

If you have problems compiling, first check the makefile to insure
that the compilation statements in it make sense for your C compiler.
The code is ANSI C compatible, and has been ported to as many other
compilers as we could get our hands on.


3.  How to Run the Coder

After compiling, the coder program may be run by typing `uvselp`.  The
first prompt asks whether to run both the encoder and decoder or the
decoder only.  If you want to process speech and put out a coded
speech file, choose the encoder/decoder option.  If you want to
synthesize speech from a coded speech file which already exists,
choose the decoder only option.  The format for the coded speech file
is documented below.

After the initial prompt, you will be prompted for more information
(see next section).

Finally, you will be prompted for the names of input speech (or coded
speech) files, and output speech files.  The coder expects speech data
in binary (Motorola, not Intel) format, with 16-bit samples.

To make running the coder less tedious, you may specify an options
file when the coder is run.  The program will then read from this file
rather than prompting the user.
To use a ".prm" file, run the executable by typing "uvselp filename.prm",
substituting your ".prm" filename.
The following .prm files are provided

```
   uvselp.prm  - runs encoded/decoder and produces a bstream file
   synpf.prm   - runs decoder and produces a postfiltered output
   synnpf.prm  - runs decoder and produces an unpostfiltered output
```
 All these scripts use fixed filenames
```
   INPUT - sampled input data
   OUTPUT - nonpostfiltered output
   OUTPUTPF - postfiltered output
   BSTREAM - bstream file
   LOG   - diagnostics file
```

3.1  Coder options

3.1.1  Put out codes in readable file (Encoder/decoder option only)

This option is useful for visual inspection of the coded parameters.
The format for one frame is:

frameNumber R0 k1 k2 k3 k4 k5 k6 k7 k8 k9 k10
lag code1 code2 gsp0 (of subframe 1)
lag code1 code2 gsp0 (of subframe 2)
lag code1 code2 gsp0 (of subframe 3)
lag code1 code2 gsp0 (of subframe 4)

3.1.2  Put out packed ASCII hex coded stream (Encoder/decoder option only)

This option puts out the coded parameters in a packed ASCII-hex
format, one frame per line.  All of the coded parameters for one frame
are packed into a single bit-stream according to their respective bit
allocations:
```
Bits:      Parameter:
    1-6 (6)      k1
   7-11 (5)      k2
  12-16 (5)      k3
  17-20 (4)      k4
  21-24 (4)      k5
  25-27 (3)      k6
  28-30 (3)      k7
  31-33 (3)      k8
  34-36 (3)      k9
  37-38 (2)      k10
  39-43 (5)      R0
  44-50 (7)      lag, subframe 1
  51-58 (8)      gsp0, s.f. 1
  59-65 (7)      vselp code1, s.f. 1
  66-72 (7)      vselp code2, s.f. 1
  73-79 (7)      lag, s.f. 2
  80-87 (8)      gsp0, s.f. 2
  88-94 (7)      vselp code1, s.f. 2
  95-101 (7)     vselp code2, s.f. 2
  102-108 (7)    lag, s.f. 3
  109-116 (8)    gsp0, s.f. 3
  117-123 (7)    vselp code1, s.f. 3
  124-130 (7)    vselp code2, s.f. 3
  131-137 (7)    lag, s.f. 4
  138-145 (8)    gsp0, s.f. 4
  146-152 (7)    vselp code1, s.f. 4
  153-159 (7)    vselp code2, s.f. 4
```

The bits for each coded parameter are ordered MSB first to LSB last.
This bit-stream is then sectioned into four bit hexadecimal
representation, and written out in ascii.  The final hexadecimal digit
is padded with a zero.

3.1.3  Calculate performance measures (Encoder/decoder option only)

This options calculates and displays performance information such as
SNR and weighted SNR.

3.1.4  Apply post-filter to synthesis

This option allows you to include, or not to include, spectral
post-filtering.

3.2  Floating point precision

As mentioned above, the floating point type may be defined as "float"
globally.  If more precision is required, this argument may be changed
to "double".


4.  Included speech files

Two speech files have been included: feml1.sp and male1.sp.  Also
included are the coded versions of these files: feml1.sc and male1.sc.
These coded speech files are in the format mention above.  Decoded
(re-synthesized) speech files are feml1.op, male1.op; and feml1.on,
male1.on.  The first two were reconstructed using the spectral post-
filter; the last two were reconstructed without post-filtering.
Note that due to differences in compilers and machine architecture there
is no guarantee that your output matches the output files provided. However,
there should be no perceptual differences.


5. Modifications

The source files were obtained from the IS85 document. The following
modifications were made:

  1)  The option for number of frames to process has been disabled (`vselp.c`)
  2)  getp.c has been modified to read also the filenames from the *.prm file
