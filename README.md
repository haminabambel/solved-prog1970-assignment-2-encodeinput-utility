Download Link: https://assignmentchef.com/product/solved-prog1970-assignment-2-encodeinput-utility
<br>
This assignment has you writing a different utility for Linux.  This utility (officially called a filter in UNIX / Linux) could be used to take an <strong><em>assembled</em></strong> program (e.g. for the Motorola 6808 Microcontroller) and generate something known as an <strong><em>S19 download file</em></strong> in order to download the assembled code to the actual embedded device.  This is similar to your ARM development environment in the MES course – you write and compile your code on a host computer and download it to the ARM board …




This application will encourage you to learn more about the common application-programming model in UNIX / Linux. The utility (or filter) that you create will take <strong><em>ANY <u>binary</u> input file</em></strong>, and transform it into its equivalent <u>S-Record output file</u>, <strong>OR</strong> an <u>assembly file</u> for use in an embedded software development environment.   The two different <u>output file formats</u> which are generated by this filter will both be <u>ASCII</u> (human readable).




<h1>Objectives</h1>

<ul>

 <li>Reinforce knowledge of binary arithmetic and the hexadecimal numbering system</li>

 <li>Reinforce knowledge of File I/O (both ASCII and binary) in programming – using actual files as well as STDIN and STDOUT</li>

 <li>Practice writing a utility that can use command-shell <em>redirecting</em> and/or <em>piping</em></li>

 <li>Practice writing a utility that requires and uses command-line arguments</li>

</ul>




<h1>Background</h1>

<h2>The S-RECORD Output of your <strong><em>encodeInput</em></strong> Filter</h2>

S-Records are often used in embedded development environments in terms of transferring data from one system to another. Development tools like EPROM burners for example accept data in this format.   As you may recall from DEF, an <strong><em>ASM</em></strong> file (assembly file) is used by embedded development tools to allow you to specify both machine codes and data (like strings) for your target system.

The <em><u><a href="http://www.amelek.gda.pl/avr/uisp/srecord.htm">Motorola S</a><a href="http://www.amelek.gda.pl/avr/uisp/srecord.htm">–</a><a href="http://www.amelek.gda.pl/avr/uisp/srecord.htm">Record File</a></u></em> format was developed a number of years ago.  It is also known as the <strong><em>SREC</em></strong> or <strong><em>S19</em></strong>  format.  It was developed in the 1970’s by Motorola (for the 6800 microprocessor) to allow you to encode your binary files (e.g. your executables) into an ASCII format file for easy downloading to an embedded system.  Visit the link above to learn more about the structure of the S-Record.

Example:

Input data:

<h3>                                                          ABCDEFGHIJKLMNOPQRST</h3>

SREC Output data:

S00700005345414ED1

S11300004142434445464748494A4B4C4D4E4F5064

S1070010515253549E

S5030002FA

S9030000FC




As you can see, the S19 (or SREC) file is made up of a number of “S” type records (S0, S1, S5 and S9 in the above example).

The general format of each of these “S” records is:

TTCCAAAADDD .. DDMM




Where  TT             – 2 characters indicating the S-Record type

CC           – 2 characters representing a single hexadecimal byte telling how many hexadecimal coded bytes follow in that line                  AAAA     – 4 characters representing a hexadecimal address where the data on this S-record line needs to be loaded into memory                                       on the embedded device

DD..DD – up to 32 characters – representing  up to 16 hexadecimal byte values representing the input data

MM – 2 characters – representing a single hexadecimal value which acts as the checksum value for that S Record




<h2>Things to Note</h2>

<ul>

 <li>The <strong><em>COUNT</em></strong> field (CC) of the specific S Record contains the number of bytes including the <em>address field </em>(AAAA), the <em>data fields</em> (DD..DD) and the <em>checksum field</em> (MM)</li>

 <li>In our filter we will <em>only be using 4 character address fields (representing a 2 hexadecimal byte address)</em> – this means you <u>only need to use</u> the S1 record for your data.

  <ul>

   <li>Since each S1 record is limited to containing a maximum of 16 bytes worth of data – this means that the address value contained in this field will always increment by 16</li>

   <li>This means that each S1 record will only represent 16 bytes worth of encode data. So if the assembled program that you are trying to encode for downloading is 40 bytes in length – then your resultant file will contain two S1 records representing 16 bytes of data each and one S1 record containing the remaining 8 bytes of data (16+16+8=40)</li>

  </ul></li>

 <li>The <strong><em>CHECKSUM</em></strong> field (MM) value is calculated by taking the least significant byte of the <strong><em>1’s Complement</em></strong> value of the <strong>sum</strong> of the COUNT, ADDRESS and DATA fields of the record o To calculate this value add the COUNT field’s value with the ADDRESS field’s value with each of the DATA field’s values to get a sum value o Then take the 1’s Complement (remember DEF) of this sum

  <ul>

   <li>Then strip off the value in the least significant byte of the resultant value and encode it</li>

  </ul></li>

 <li>One thing that will strike you as odd about the above coding and sample is for example how I say that the COUNT field is 2 characters in output, but represents a single hexadecimal byte o For example the above COUNT field is set to “13” which represents the hex number 0x13 (19 decimal)

  <ul>

   <li>You will remember from DEF that a value of 0x13 hex could definitely be stored in a single byte of output – so why then does the resultant</li>

  </ul></li>

</ul>

S Record output use 2 bytes to store it … it stores the character “1” in one output byte and the character “3” in another byte o This is a form of hexadecimal coded values that the S Record file uses … remember that the S Record out is always human readable

<ul>

 <li>If the output actually stored the value 0x13 in a single byte – the file wouldn’t be readable – check out the ASCII table</li>

 <li>19 decimal in the ASCII table is known as a character called “device control 3 (DC3)” which is unprintable / unreadable by a human o So the hexadecimal encoding that is really happening within this utility is:</li>

 <li>If the value that needs to be output would end up being 0xA4 as a hexadecimal value then the output will contain “A4” (ASCII code 65 followed by ASCII code 52) instead</li>

 <li>if the value 9 hexadecimal needs to be output by the program, then the output will contain “09” (ASCII code 48 followed by ASCII code 57)</li>

 <li>as you can see – each single hexadecimal byte value is translated (encoded) as 2 ASCII output characters</li>

</ul>

<ul>

 <li>The S0 record is known as the <em>header record</em> of the output file o In this utility, I want you to encode your first name in the DATA field of the S0 record</li>

</ul>

&#x25aa;   e.g. the data fields in the above example “5345414E” actually spells SEAN – 53<sub>16</sub>=83<sub>10</sub>=ASCII code for “S”

<ul>

 <li>The S5 record is found immediately after the last S1 record serves as <em>summation record</em> within the output file o The AAAA (address field) of this record represents the total number of S1 records that preceded this record in the out</li>

 <li>The S9 record is the <em>trailer record</em> in the output file o The AAAA (address field) of this record represents the address in memory on the target embedded device where the program actually starts</li>

</ul>




<h2>The ASSEMBLY FILE Output of your <strong><em>encodeInput</em></strong> Filter</h2>

The required <em>Assembly File</em> format that your utility can also produce is much more straightforward in its coding than the S-Record format.   Essentially, this format translates each byte of input data into a <strong>define constant byte</strong> (dc.b) assembly instruction.  Each line of the resultant file is allowed to contain a maximum of 16 bytes of input data.

Example:

Input data:

ABCDEFGHIJKLMNOPQRST

Assembly File Output data:

dc.b             $41, $42, $43, $44, $45, $46, $47, $48, $49, $4A, $4B, $4C, $4D, $4E, $4F, $50

dc.b             $51, $52, $53, $54




<h1>Your Task</h1>

Create a <strong>C program</strong> that has 4 <strong><em>optional</em></strong> <strong><em>switches </em></strong>(in C, you might consider these as <em>command line arguments</em>).  With the exception of the <em>help</em> switch, your filter program (called <strong>encodeInput</strong>) will take and interpret input as binary values, and produce ASCII readable output based on these switches.  The data values encoded in the output will always be the <em>hexadecimal representation</em> of the ASCII value of the input byte.

The switches that <strong>encodeInput</strong> must support are:

<ul>

 <li>-i<em>INPUTFILENAME</em> o This option tells your software the name of the input file. o If this file is not specified, your program will obtain input data from the standard input (<strong><em>stdin</em></strong> file handle) o If the file is specified and does not exist, your program will present an error</li>

 <li>-o<em>OUTPUTFILENAME</em>

  <ul>

   <li>This option tells your software the name of the output file.</li>

   <li>If this file is not specified, and an input file is specified

    <ul>

     <li>If the –srec option is <u>not</u> present, then the output filename will be the input filename, with “.asm” file extension appended</li>

     <li>g. encodeInput –iBinary-01.bin will automatically create an output file called Binary-01.bin.asm</li>

     <li>If the –srec option is present, then the output filename will be the input filename, with “.srec” appended</li>

     <li>g. encodeInput –srec –iBinary-01.bin will automatically create an output file called Binary-01.bin.srec</li>

     <li>Notice in this assignment, we are <u>not replacing the input file’s extension</u> – we are simply appending the output file’s extension</li>

    </ul></li>

   <li>If this file is not specified and no input file is specified, then all output will be sent to the standard output (<strong><em>stdout</em></strong> file handle) o If this file is specified and for some reason, it cannot be open for writing purposes, the filter will produce an error</li>

  </ul></li>

 <li>-srec o This option tells your software to output in the S-Record format

  <ul>

   <li>Without this option specified on the command line, then an Assembly File output will result</li>

  </ul></li>

 <li>-h o This option will cause the program to output help information (or <em>usage statement</em>) and exit o The usage statement is simply the name of the program and its allowable runtime switches</li>

</ul>

If any other (invalid) switches are present on the command line, the filter will display the usage statement and exit.

Because <strong>encodeInput</strong> uses run-time switches, this program <strong><u>does not need to prompt the end user for any other kinds of input</u></strong>.  Instead, if written correctly, the end user will be able to redirect or pipe textual data into the application in place of using a file!

Although it probably doesn’t need to be said – the only way that <strong>encodeInput</strong> should allow an input stream to end is when it reaches the <em>end-of-file</em>.  That is, if you are running <strong>encodeInput</strong> and reading from a file, obviously the input ends when it reaches the <em>end-of-file</em>.  Similarly when you are running the utility interactively and are entering input via the keyboard, the input ends when it reaches the <em>end-of-file</em> – not an ENTER key, and not any other special character sequence – just an <em>end-of-file</em>.  What you need to do is to simulate an <em>end-of-file</em> from the keyboard – this is done by entering a <strong><u>CTRL-D</u></strong> (pressing the CONTROL key and the D key simultaneously).

Please note that <strong><u>CTRL-D only works to end the file when entered at the beginning of the input line</u></strong> – if it is not the first character of the input, then CTRLD serves to flush (or push) the data ahead of it into the program.  For example, <strong><em>assume the “D” in the following examples represents when I press CTRL-D and “E” is when I hit enter </em></strong> – so if you enter “123D”, the CTRL-D simply causes the program to read “123”.  But if my input is “123ED” – then the CTRL-D is the first character of an input line (because it follows an ENTER) – so this will be interpreted as an EOF character in your program.

Some examples of valid command lines:

encodeInput -imyData.dat -omyData.srec -srec

<ul>

 <li>The above will convert dat to myData.srec as an S-Record file</li>

</ul>




encodeInput -h

<ul>

 <li>The above will output help (usage statement) information and exit</li>

</ul>




encodeInput -omyData.asm

<ul>

 <li>The above will convert standard input into asm as an assembly file</li>

</ul>




encodeInput –srec -imyData.dat

<ul>

 <li>The above will convert data from dat into myData.dat.srec as an S-Record file</li>

</ul>




encodeInput

<ul>

 <li>The above will convert standard input into Assembly formatted standard output</li>

</ul>




ls -l | encodeInput -odirectory.srec -srec

<ul>

 <li>The above will take the piped standard input from ls -l command and format as an S-Record file output in srec</li>

</ul>




<strong><u>NOTE</u></strong>: The runtime switches can appear in <u>any order on the command line</u>

OTHER PROGRAMMING NOTES:

<ul>

 <li>It is expected that your final solution and source code for this utility consists of at least 3 source modules and at least one header file o As before – I am asking you to do this to start you thinking about designing your solutions to properly modularize your solution approach into different source code modules (files)</li>

 <li>Remember that it is good design practice to not place any <strong><em>problem-domain</em></strong> knowledge in the main() function</li>

 <li>Also it is good design practice to not place any of your solution’s functions in the same source module as the main() function</li>

 <li>You must comply with SET Coding Standards o Make sure to comment your source code appropriately – this includes file header comments, function header comments as well as inline comments</li>

 <li>Your solution structure must include a makefile and also follow the recommended Linux development directory structure as outlined in the <u>LinuxDevelopment-Project-Code-Structure</u> document within eConestoga</li>

</ul>




<h1>Hand in</h1>