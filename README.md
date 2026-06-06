# ITSBASIC
This project’s purpose is to modify a TOPS-10 BASIC Interpreter to run effectively under ITS through the :DEC program. The goal is to both preserve the historic accuracy of how the BASIC Interpreter operates at the user level, while simultaneously patching the BASIC Interpreter to enable a near-seamless integration with ITS.


ITS BASIC Project 
By AltairArchaeology
Build Date: JUN 06 2026

1. Purpose. This project’s purpose is to modify a TOPS-10 BASIC Interpreter to run effectively under ITS through the :DEC program. The goal is to both preserve the historic accuracy of how the BASIC Interpreter operates at the user level, while simultaneously patching the BASIC Interpreter to enable a near-seamless integration with ITS. This distribution is intended for historical, educational, and personal non-commercial use. Upon building a PiDP-10 and running ITS, I was struck by how advanced of an operating system ITS was during its era. I was also struck by the relative absence, in common ITS distributions, of a turnkey BASIC environment comparable to what many users experienced on minicomputers and microcomputers of the 1970s and 1980s. This project was born of a desire to experience the early forms of mainframe-based BASIC, still with a largely Dartmouth-derived syntax, while also being able to port programs written on other 8-bit computers of the 1970s-1980s. 

2. License. BASIC BIN and BASIC HLP included on itsbasicdistro.tap are derived from DEC TOPS-10 BASIC material. They are not licensed under the MIT License by this project. They are believed to be governed by the DEC Personal Use License for TOPS-10 and TOPS-20, available at: https://opensimh.org/dec_36bit_license/ In summary, that license permits use and modification of DEC TOPS-10 and TOPS-20 software technology solely for personal, non-commercial use. Users are responsible for ensuring that their use of the DEC-derived files complies with that license or any other applicable rights. Original scripts, documentation, patch notes, and packaging work created for the ITS BASIC Project are licensed under the MIT License, unless otherwise noted. The MIT License does not apply to DEC-derived files, including BASIC BIN and BASIC HLP. Use of this distribution is subject to both the DEC Personal Use License terms for DEC-derived material and the MIT License terms for this project's original material.

3. Installation. To install the contents of itsbasicdistro.tap onto ITS, first run ITS from SIMH. Once ITS is running, access SIMH from the terminal by typing pdp, and then press CTRL+E to pause SIMH. Attach the .tap by typing in attach mta0 <path to the .tap image>/itsbasicdistro.tap. For example: attach mta0 /home/pidp-10/BASIC/itsbasicdistro.tap. Once attached, type cont to resume SIMH. Once logged into ITS, type in :DUMP. Upon the _ prompt appearing, type in DUMP. When prompted for FILES= type in *; * * to place all files from the .tap onto the appropriate directories. Once this is complete, type in QUIT (before this you can type in REWIND to rewind the tape, or UNLOAD to detach it from SIMH). You can now access BASIC by typing in :BASIC. 

4. Operation. The following describes the commands available in the TOPS-10 BASIC Interpreter, and describes how they were patched to work with ITS, if applicable. These commands will all work with the first three letters as abbreviations. For example, CAT will work the same as typing CATALOG. For full operating instructions, please review the DECsystem-10 BASIC Conversational Language Manual published in March of 1974.
   
| Command | Abbrev. | Purpose |
|---|---|---|
| HELP | HLP | Display BASIC help |
| NEW | NEW | Start a new program |
| SCRATCH | SCR | Clear the current program from core |
| RENAME | REN | Rename the program title in core |
| LENGTH | LEN | Show program length |
| LIST | LIS | List the program |
| RESEQUENCE | RES | Renumber lines |
| RUN | RUN | Execute the program |
| CATALOG | CAT | List files |
| SAVE | SAV | Save a program |
| OLD | OLD | Load a program |
| UNSAVE | UNS | Delete a saved program |
| REPLACE | REP | Overwrite a saved program |
| WEAVE | WEA | Merge a saved program into core |
| MONITOR | MON | Return to DDT, preserving BASIC |
| SYSTEM | SYS | Exit BASIC |
| BYE / GOODBYE | BYE / GOO | Exit BASIC and log out |

HELP (HLP) – This prints the contents of BASIC HLP and provides instruction on the commands available in the BASIC Interpreter. :DEC has BASIC look in DECSYS;BASIC HLP. By default, the .tap will place this file in the correct directory. 

NEW – This creates a new program by removing all previously typed lines of code from core, and prompting the user to enter the name for the program. Interestingly, the program name does not correspond to the file name it is saved under, so you can, for example, have a program named HELLO that is saved as HI BAS. 

SCRATCH (SCR) – This removes all previously typed lines of code from core. If you have used BASICs from the 1970s or 1980s, such as MS BASIC, C64 BASIC, etc, this will equate to their version of NEW. 

RENAME (REN) – This renames the title of the program for the program loaded into core. This does NOT rename the BAS file that is saved to directories.  

LENGTH (LEN) – This tells how many characters the current program loaded into core has. 

LIST (LIS) – This lists the current program in core. LIST by itself will show every line of code as loaded into core. You can modify by using LIST 500 to only print line 500, LIST 500- to print everything from line 500 to the end of the program, or LIST 500-700 to print everything from line 500 to line 700. The last example is particularly useful when building subroutines! 

RESEQUENCE (RES) – This resequences your line numbers to either the default, which starts at 10, 20, 30, etc, or through a user specification, for example RES 1000 starts the first line of code at 1000, and RES 100,50,10 takes line 50, renumbers it to 100, and renumbers each successive line of code by 10. This can be very powerful for writing subroutines in different files before merging them (see WEAVE). 

RUN – This runs the program written in core. Unlike the 8-bit BASICs of the 1970s-1980s, this will print the title of the program, the system date, and the total calculation time once the program ends. Also, unlike the BASICs of the 1970s-1980s, any BASIC program in core requires END as the final line of the program. Don’t worry – it will tell you if you forget it! 

CATALOG (CAT) – This lists files on a recognized device or directory. Example use is CAT LARS: to view files in the LARS directory, or CAT *** to view files in the BAS directory. This has been patched to recognize ITS’ directory naming convention. It does this by first looking for hardcoded devices (SYS, BAS, etc.) before converting the typed in directory name into octal, and then finding it on ITS. As an example, typing in CAT BASIC converts the word BASIC into 424163,514300, and then goes to that ITS directory corresponding to the sixbit octal. 

SAVE (SAV) – This saves the program. Defaults are to save in the active user directory with an extension of BAS. Use examples are SAVE ALTAIR:HI to save it as HI BAS in the ALTAIR directory, or SAVE TEST*** to save it as TEST in the BAS directory. *** is hardcoded to refer to the BAS directory. You cannot use SAVE to overwrite an existing program – see REPLACE below. This command was patched to remove the TOPS-10-level system protections that it would attempt to apply to the file that are incompatible with ITS. Prior to patching, the program would save, but the Interpreter would crash. 
OLD – This loads a previously-saved program. A use example from above is OLD ALTAIR:HI or OLD TEST***. 

UNSAVE (UNS) – Yes, really. This command deletes a program. An example is UNSAVE ALTAIR:HI or UNSAVE TEST***. The program is not currently known to be recoverable if deleted. 

REPLACE (REP) – This overwrites a file that has been already saved with one of the same name. An example of use is REPLACE ALTAIR:HI or REPLACE TEST***. This was the hardest patch to get working for ITS by far. In the original, the Interpreter deletes the file from the working directory, but creates a temporary file to restore if REPLACE fails. It then saves the new file under the previous name before purging the temp file. The patch follows the spirit of this command, but removes the temporary file creation. The patched version of REPLACE effectively uses UNSAVE and then launches immediately into SAVE. In theory, this is riskier as you could lose your program if the Interpreter crashes during a REPLACE. I have never experienced this, but it could happen.  

WEAVE (WEA) – This merges the code from a specified file into core. An example of using this would be to use OLD ALTAIR:HI and WEAVE TEST***. In this case, all code from TEST*** will be loaded into core along with the code already present from using OLD ALTAIR:HI. WEAVE prioritizes the saved file over the code in core, so line numbers that conflict will be overwritten by the WEAVE file. Use RESEQUENCE on either core or the file you intend to WEAVE to help avoid this. 

MONITOR (MON) – This stops BASIC and returns the user to DDT. BASIC can be continued by typing in :CONTINUE. Surprisingly, this worked as intended without any patching! 

SYSTEM (SYS) – This kills BASIC and returns the user to DDT.  

BYE (GOODBYE or GOO) – This kills BASIC, returns the user to DDT, and then attempts to log the user out. The original on TOPS-10 would do this all from inside the BASIC Interpreter, but was non-functional in ITS. I patched this to both work, and to return the user to DDT before issuing :LOGOUT. This is safer, as if you are running other programs DDT will now not allow you to logout. You can observe this behavior by using :PEEK, CTR-Y, P, :BASIC, and then BYE.
