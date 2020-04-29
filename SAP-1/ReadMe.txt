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


SAP-1 Simple As Possible Computer

Here is an almost identical recreation of the SAP-1 Simple As Possible microprocessor, as published in Digital Computer Electronics by Albert Malvino and Jerald Brown.  While the book is out of print an "ebook" can be found fairly easily if you know where to look.

I used Labcenter Electronics' Proteus Design Suite to draw the schematic.  A demo version can be downloaded from http://www.labcenter.com/index.cfm under the download tab.  It can do everything except save or print, so the circuit can be simulated when opened (use the Import Legacy Design).

Unfortunately the original design uses 74LS189 16x4-bit RAM, which can only be found on eBay right now.  That means it would be difficult to actually build this circuit.  I am working on an updated design that uses 74HC chips and other commonly available parts.  For now you can try out the classic design and see how it works for yourself.


SAP-1 Updated Design

Here is the updated design for the SAP-1 microprocessor.  I've made a number of changes that should allow someone to build this circuit using parts that are readily available from a place like Digikey.  To summarize:

    All parts changed to 74HC high speed CMOS, no more worries about fanout limits and sinking vs. sourcing differences in current.  However the unused gates (not shown) will have to be properly terminated as per the usual issues with floating hi-impedance inputs.
    Some newer parts have been used (4-bit counter, octal 3-state buffer) to simplify the design and reduces the chip count.
    The 74LS189 RAM has been replaced with a 32kB CMOS Asynchronous Static RAM.  A part like the Cypress Semiconductor CY7C199CN can be used for the RAM (Digikey Part Page).
    Since the RAM uses the same pins for input and output, another set of 3-state buffers is used to output or isolate the data switches from the W-bus.
    To clean up the schematic the W-bus is only shown as a connection to a common bus.
    A logic analyzer has been added to show how the control signals are sequenced.

This design still utilizes a hardwired controller sequencer.  Any changes to the microcode require a complete re-design of this circuit which is very cumbersome.  The next design will utilize a microcoded ROM to handle the control sequence, as outlined at the end of the chapter.
Here is the memory contents of a sample program entered into the SAP-1.  It can be found on page 146 of Digital Computer Electronics (Example 10-3).



SAP-1 with a Microcoded ROM

Here is my attempt at designing a SAP-1 microprocessor with a micro-code ROM controller sequencer and a variable machine cycle.  The total number of ICs has decreased from 48 to 35.  My design deviates a bit from the block diagram shown at the end of chapter 10.  Looking at Digikey's catalogue, 16x4-bit or 16x8-bit ROMs are simply not available anymore.  I ended up using two 8Kx8-bit ROMs to give a 16-bit control word.  Obviously only a tiny fraction of the storage is used.  Also, the simulation program only has a few parallel access ROMs in its library anyway.  I took advantage of this wider address space to simplify the design.  Something like the AT28C64B (Digikey Part Page) can be used.  You will also need an EEPROM programmer; many inexpensive but adequate ones are available on eBay.

The OPCode half of the instruction register outputs directly to the address lines of the micro-code ROMs.  To simplify things, each instruction's microcode takes up a fixed block of 8-bytes, this allows for up to 8 micro-instructions for each routine.  Each instruction has 4-bits for the OPCode so there can be up to 16 OPCodes; this iteration of the SAP-1 has 8 instructions in its set.  Each machine cycle executes as follows:

    T1: Load the memory address register from the program counter.
    T2: Increment the program counter, load instruction from the RAM.
    As soon as the instruction is loaded from RAM, the ROM address now points to the microcode for that instruction.
    T3,T... Continue executing the instruction.
    NOP: This resets the Controller/Sequencer counter back to zero and the machine cycle repeats.

 To save on manually entering a program each time the simulation is run, I have added an auto-loader circuit that will load into RAM a program saved in a ROM.  In the simulation, the ROMs are loaded from a linked binary file, which can be edited with a Hex editor to change the program.  Three 2:1 data selectors (think of them as a gang operated 4PDT switch) were added to easily switch between run/execute, manual programming and auto-programming.  Unfortunately the data input circuit is now much more convoluted.

The contents of memory once the auto-loader has completed. 

And here the contents of the accumulator has been transferred to RAM by the store instruction.

The ROM allows for much more flexibility in creating and editing the instruction set.  The Jump, Store and Output memory instructions have been added to the original set.

The microcode for each instruction.  Thank-you to Kyle at 8-bit Spaghetti where I first saw this nice layout for the microcode.  He also has a build log of his own SAP-1 computer.

