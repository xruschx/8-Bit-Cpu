To edit the saved program, open the "Program ROM.bin" file in a Hex editor.  The SAP-1 has only 16 bytes of accessible memory, so only the frist 16 bytes of the file will be loaded into RAM by the automatic loader.  The pre-loaded sample program will demonstrate every instruction except for HALT.  Consult the Instruction Set document for more information about the code.

Sample Program:
Address	Contents
0h	1C ;LDA Ch
1h	3D ;SUB Dh
2h	5A ;STO Ah
3h	3E ;SUB Eh
4h	5B ;STO Bh
5h	2F ;ADD Fh
6h	60 ;OUT A
7h	7A ;OUT Ah
8h	7B ;OUT Bh
9h	46 ;JMP 6h
Ah	FF ;Unused, will be overwritten by the program
Bh	FF ;Unused, will be overwritten by the program
Ch	FC ;Program Data
Dh	52 ;Program Data
Eh	55 ;Program Data
Fh	44 ;Program Data