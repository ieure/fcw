# Fluke Compiler Wrapper

This is a Python 3 script which uses DOSBox to compile scripts for the
Fluke 9000 series of digital troubleshooters, using Fluke’s `9LC.EXE`
program.

## Usage

FCW passes its arguments to 9LC, so you use it the same way. FCW does
some basic checking of arguments so as to avoid spinning up DOSBox,
only to receive an error.

```
$ ./fcw ./APPLE-II.S


            9010A Language Compiler - Version 1.2
          Copyright(c) 1983 John Fluke Mfg. Co., Inc.



compiling ...

Program  1 -  127 bytes
Program  2 -  171 bytes
Program  7 -   42 bytes
Program  8 -   21 bytes
Program  9 -    8 bytes
Program 10 -   91 bytes
Program 11 -  269 bytes
Program 12 -  388 bytes
Program 13 -  288 bytes
Warning:  incorrect byte count
Program 14 -  450 bytes
Program 15 -   44 bytes
Program 20 -  799 bytes
Program 21 -   82 bytes
Program 22 -   56 bytes
Program 23 -  453 bytes
Program 24 -  480 bytes
Warning:  incorrect byte count
Program 25 -  430 bytes
Warning:  incorrect byte count
Program 26 -  556 bytes
Program 27 -   23 bytes
Program 28 -  164 bytes
Program 30 -   97 bytes
Program 31 -   29 bytes
Program 32 -  502 bytes
Program 40 -   63 bytes
Program 41 -  229 bytes
Warning:  incorrect byte count
Program 42 -   84 bytes
Program 50 -   45 bytes
Program 51 -  236 bytes
Program 52 -  169 bytes
Warning:  incorrect byte count
Program 53 -   62 bytes
Program 54 -  181 bytes
Program 60 -  239 bytes
Program 61 -  304 bytes
Program 70 -  247 bytes
Program 71 -  217 bytes
Program 72 -  248 bytes
Program 73 -   98 bytes
Program 74 -   96 bytes
Program 76 -   28 bytes
Program 77 -   27 bytes
Program 78 -   13 bytes
Program 79 -   12 bytes
Warning:  incorrect byte count
Program 80 -   93 bytes
Program 81 -  181 bytes
Warning:  incorrect byte count
Program 82 -  294 bytes
Program 83 -   65 bytes
Program 84 -   24 bytes
Program 85 -  174 bytes
Warning:  incorrect byte count
Program 86 -  180 bytes
Program 87 -  228 bytes
Warning:  incorrect byte count
Program 88 -   50 bytes
Program 89 -   23 bytes
Program 97 -   18 bytes
Program 98 -   18 bytes
Program 99 -  404 bytes
Warning:  incorrect byte count

TOTAL = 9920 bytes
$ ls -l APPLE-II.H
-rw-r--r-- 1 ieure staff 22400 Jan 16 11:33 APPLE-II.H
```

## Portability

FCW should be portable to most any UNIXy system. It should run on
Windows, except for the `dosbox_cmd,` function, which makes
assumptions about the OS. However, since Windows can run 9LC directly,
this isn’t likely to be a problem.

## Limitations

Due to the way DOS works, the output of 9LC is buffered until it
terminates; it doesn’t update line-by-line like it does when run
directly. Because DOS is a single-tasking OS, the standard output is
buffered until the process completes, since it cannot be consumed by
another process at the same time. FCW does provide the 9LC output, but all at once, at the end of compilation.

Because 9LC predates FAT32’s long filename support, scripts in non-8.3
files will fail to compile.

9LC is fairly limited, and requires that all include files (including
pod definitions) exist in the same directory as your script. For
convenience, a copy of the pod definitions ships with NFC, in the
`pod/` directory.

As with 9LC, FCW can only compile a single file at a time. Since
DOSBox is booted for each invocation, this is somewhat less efficient
than one might wish. This may be improved in the future.

## Design

FCW mounts three virtual drives into the DOSBox environment:

 - `C:`. The `build/` directory of FCW, which contains the
   9010a compiler.
 - `D:`. The directory containing the source file to compile.
 - `E:`. A temporary directory containing 9LC and DOSBox output.
