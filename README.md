# double-free-in-XPDF4.02
double free in XPDF4.02

We found a double free error when using pdfimages-XPDF4.02.
Here is the information from gdb:
How to reproduce: pdfimages crash1 /dev/null
or  pdfimages crash2 /dev/null


fack@ubuntu:~/下载/crashs/pdfimages$ gdb ./pdfimages 
GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
Copyright (C) 2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./pdfimages...done.
(gdb) set args ./SIGABRT/id:000011,sig:06,sync:qsym,src:000726 /dev/null
(gdb) b 117
Breakpoint 1 at 0x401e47: file /home/fack/programs/xpdf-4.02/xpdf/pdfimages.cc, line 117.
(gdb) r
Starting program: /home/fack/下载/crashs/pdfimages/pdfimages ./SIGABRT/id:000011,sig:06,sync:qsym,src:000726 /dev/null

Breakpoint 1, main (argc=3, argv=0x7fffffffddb8)
    at /home/fack/programs/xpdf-4.02/xpdf/pdfimages.cc:117
117	  doc = new PDFDoc(fileName, ownerPW, userPW);
(gdb) s
PDFDoc::PDFDoc (this=0x8db410, 
    fileNameA=0x7fffffffe1b9 "./SIGABRT/id:000011,sig:06,sync:qsym,src:000726", ownerPassword=0x0, userPassword=0x0, coreA=0x0)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:157
warning: Source file is more recent than executable.
157	PDFDoc::PDFDoc(char *fileNameA, GString *ownerPassword,
(gdb) b 208
Breakpoint 2 at 0x469706: file /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc, line 208.
(gdb) c
Continuing.

Breakpoint 2, PDFDoc::PDFDoc (this=0x8db410, 
    fileNameA=0x7fffffffe1b9 "./SIGABRT/id:000011,sig:06,sync:qsym,src:000726", ownerPassword=0x0, userPassword=0x0, coreA=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:208
208	  ok = setup(ownerPassword, userPassword);
(gdb) s
PDFDoc::setup (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, userPassword=userPassword@entry=0x0)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:253
253	sGBool PDFDoc::setup(GString *ownerPassword, GString *userPassword) {
(gdb) b 266
Breakpoint 3 at 0x469419: file /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc, line 266.
(gdb) c
Continuing.
Syntax Warning: PDF version H. -- xpdf supports version 2.0 (continuing anyway)
Syntax Error: Couldn't read xref table
Syntax Warning: PDF file is damaged - attempting to reconstruct xref table...

Breakpoint 3, PDFDoc::setup (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, userPassword=userPassword@entry=0x0)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:266
266	      if (!PDFDoc::setup2(ownerPassword, userPassword, gTrue)) {
(gdb) s
PDFDoc::setup2 (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, 
    userPassword=userPassword@entry=0x0, repairXRef=repairXRef@entry=1)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:288
288			     GBool repairXRef) {
(gdb) b 312
Breakpoint 4 at 0x46933f: file /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc, line 312.
(gdb) c
Continuing.
Syntax Error (49): Illegal character '>'
Syntax Error (55): Dictionary key must be a name object
Syntax Error (63): Dictionary key must be a name object
Syntax Error (65): Dictionary key must be a name object
Syntax Error (70): Dictionary key must be a name object
Syntax Error (72): Dictionary key must be a name object
Syntax Error (74): Dictionary key must be a name object
Syntax Error (89): Invalid hex escape in name
Syntax Error (148): Illegal character '>'
Syntax Error (171): Dictionary key must be a name object
Syntax Error (176): Dictionary key must be a name object
Syntax Error: End of file inside array
Syntax Error: End of file inside array
Syntax Error: End of file inside dictionary
Syntax Error (72): Dictionary key must be a name object
Syntax Error (74): Dictionary key must be a name object
Syntax Error (89): Invalid hex escape in name
Syntax Error (148): Illegal character '>'
Syntax Error (171): Dictionary key must be a name object
Syntax Error (176): Dictionary key must be a name object
Syntax Error: End of file inside array
Syntax Error: End of file inside array
Syntax Error: End of file inside dictionary
Syntax Error (171): Dictionary key must be a name object
Syntax Error (176): Dictionary key must be a name object
Syntax Error: Catalog object is wrong type (null)
Syntax Error: Couldn't read page catalog

Breakpoint 4, PDFDoc::setup2 (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, 
    userPassword=userPassword@entry=0x0, repairXRef=repairXRef@entry=1)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:312
312	    delete catalog;
(gdb) c
Continuing.
*** Error in `/home/fack/下载/crashs/pdfimages/pdfimages': double free or corruption (out): 0x00000000008de250 ***
======= Backtrace: =========
[0x4fab31]
[0x503076]
[0x5070f7]
[0x48939b]
[0x485760]
[0x40e74e]
[0x469350]
[0x46942c]
[0x469714]
[0x401e6b]
[0x4e17c6]
[0x4e1db5]
[0x400b99]
======= Memory map: ========
00400000-00646000 r-xp 00000000 08:01 1589695                            /home/fack/下载/crashs/pdfimages/pdfimages
00846000-00890000 rw-p 00246000 08:01 1589695                            /home/fack/下载/crashs/pdfimages/pdfimages
00890000-008fb000 rw-p 00000000 00:00 0                                  [heap]
7ffff0000000-7ffff0029000 rw-p 00000000 00:00 0 
7ffff0029000-7ffff4000000 ---p 00000000 00:00 0 
7ffff7f98000-7ffff7fd9000 rw-p 00000000 00:00 0 
7ffff7ff9000-7ffff7ffa000 rw-p 00000000 00:00 0 
7ffff7ffa000-7ffff7ffd000 r--p 00000000 00:00 0                          [vvar]
7ffff7ffd000-7ffff7fff000 r-xp 00000000 00:00 0                          [vdso]
7ffffffde000-7ffffffff000 rw-p 00000000 00:00 0                          [stack]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]

Program received signal SIGABRT, Aborted.
0x00000000004ee978 in raise ()
(gdb) n
Single stepping until exit from function raise,
which has no line number information.

Program terminated with signal SIGABRT, Aborted.
The program no longer exists.
(gdb) Quit
(gdb) 
