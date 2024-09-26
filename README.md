java c
CSSE2310 – Semester 2, 2024 
Assignment 3 
Introduction 
The goal of this assignment is to demonstrate your skills and ability in fundamental process management and communication concepts (pipes and signals), and to further develop your C programming skills with a moderately complex program.
You are to create a program (called uqzip) which allows users to compress a file (or set of files) using any one of a set of external compression programs (zip, gzip, xz or bzip2). This will create a .uqz archive file. The program will also allow decompression of the files that it creates.
The assignment will also test your ability to code to a particular programming style. guide, and to use a revision control system appropriately.
Specification 
The uqzip program will allow a user to create a .uqz archive file that will hold a compressed file (or files) that can later be extracted from the archive (decompressed) using uqzip. uqzip won’t actually do the compression itself – it will rely on external programs (such as zip, gzip, xz or bzip2 available on moss) to do the compression and will package up the resulting data into a .uqz archive. The .uqz archive file will also store the method of compression used and the filename for each compressed file stored in the archive.
When used for decompression, uqzip will extract each compressed data stream from the .uqz archive file and pipe it to an external decompression program whose output will be saved to a file in the current directory with the original filename. uqzip is to support both sequential and parallel compression and decompression. Sequential processing means files are compressed (or decompressed) one after the other. Parallel processing means files are compressed (or decompressed) in parallel – each using a separate child process of uqzip (or two child processes per file in the case of parallel decompression).
Command Line Arguments 
Your uqzip program is to accept command line arguments as follows when doing file compression, i.e. creating a .uqz archive file:
./uqzip [--nozip|--gz|--zip|--xz|--bzip2] [--parallel] [--output outFilename ] filename ...
and as follows when doing decompression (archive extraction):
./uqzip [--parallel] --extract uqz-file
The square brackets ([]) indicate optional arguments or groups of arguments. The pipe symbol (|) indicates a choice. The italics indicate placeholders for user-supplied value arguments. An ellipsis (. . . ) indicates the previous argument can be repeated. Note that the option arguments (those beginning with “--” and their associated value, if any) can be in any order.
When used for compression, uqzip will accept an optional argument specifying the compression method. When none of --bzip2, --gz, --zip, --xz or --nozip are specified then the program is to act as if --nozip is specified – i.e. no compression is used when creating the archive file.
When used for compression, an optional output file name (outFilename ) can be specified by using the --output option argument. If this argument pair is not specified, then the default filename out.uqz is to be used. It can be assumed that (i.e. we will not test a situation when) the outFilename (or default output filename) is given as a filename argument on the command line.
When used for compression, one or more existing filenames to be compressed into the .uqz archive must also be specified after any option arguments. It can be assumed that the first (or only) file name argument does not start with the characters “--”. (If the user wants to specify a file whose name does start with “--” then they should prefix the name with “./”. Any arguments after the first file name are also assumed to be file names, even if they begin with “--”.)
When used for decompression (with the --extract option argument specified), uqzip expects a filename argument to be present (uqz-file , which must be the last argument). This is the name of the .uqz archive file to be decompressed.
代 写CSSE2310 – Semester 2, 2024 Assignment 3R
代做程序编程语言If the --parallel option argument is present, then uqzip is to use child processes running in parallel to do the compression or decompression. If this argument is not present, then uqzip will use child processes running sequentially. Full details of the required functionality are provided later in this document.
Some examples of how the program might be run include the following:
./uqzip /usr/dict/share/words
./uqzip --parallel --bzip2 one.txt two.txt three.txt
./uqzip --zip --output lists.uqz --parallel list1 list2 list3 list4 list5 list6
./uqzip --output archive --xz /usr/share/dict/words local/dictionary
./uqzip --extract archive.abc
./uqzip --parallel --extract out.uqz
More details on the expected behaviour of the program are provided later in this document.
Prior to doing anything else, your program must check the command line arguments for validity. If the program receives an invalid command line then it must print the following (two line) message:
Usage: ./uqzip [--nozip|--gz|--zip|--xz|--bzip2] [--parallel] [--output outFilename] filename ...
Or: ./uqzip [--parallel] --extract uqz-file
to standard error (with a newline at the end of each line), and exit with an exit status of 14. Note that the colon (:) symbols on each line are aligned, i.e. three spaces precede “Or:”.
Invalid command lines include (but may not be limited to) any of the following:
• More than one of the option arguments --bzip2, --gz, --zip, --xz, --nozip or --extract is given on the command line
• Any of the option arguments is listed more than once.
• The --output option argument is given but it is not followed by a filename argument
• No filenames are given on the command line.
• --extract is given on the command line along with --output
• --extract is given on the command line along with more than one filename
• An unexpected argument is present.
• Any argument is the empty string.
Checking whether files exist or can be opened is not part of the usage checking (other than checking that filename values are not empty). This is checked after command line validity as described below.
File Checking 
Compression 
If uqzip is creating an archive file (i.e. --extract is NOT specified on the command line) then it must attempt to open the output file (the given outFilename from the command line, or, if none is present, then the default output filename – out.uqz) for writing. If the file already exists, it must be truncated and overwitten. If the file is unable to be opened for writing then uqzip must print the message:
uqzip: can’t write to file "filename "
to standard error (with a following newline) where filename is replaced by the name of the file that could not be opened for writing. uqzip must then exit with status 17.
The files to be compressed are not checked for existence.
Decompression 
If --extract is specified on the command line and uqzip is unable to open the given archive filename (uqz-file argument) for reading, then your program must print the message:
uqzip: unable to read from file "filename "
to standard error (with a following newline) where filename is replaced by the name of the .uqz archive file from the command line. The double quotes must be present. uqzip must then exit with status 6.
Program Behaviour – Compression (Archive Creation) 
If the command line and file checks described above are successful and an archive file is to be created (the argument --extract is NOT specified on the command line) then uqzip is to behave as described below.
First, uqzip must write out the header section for the archive file. (See Table 1 for details of the file format, including the header section.) Placeholders should initially be used for the file record offsets because these aren’t known yet. These will need to updated in the file after the compressed files are added to the archive.











         
加QQ：99515681  WX：codinghelp
