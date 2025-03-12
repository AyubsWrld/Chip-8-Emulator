# Chip-8 Emulator
___

## Description
___

- **16 8-bit registers** : Labelled **V0** to **VF**. Each register can hold a value between *0x00* and *0xFF* ( 0x0000'0000 & 0x1111'1111 respectively).
- **VF** : Holds a flag about the result of an operation. 
- **4096 bytes of memory**: $2^{12}$
- **Address space segmented into three parts** 
  - *0x000*-*0x1FF*: Originally reserved for the CHIP-8 interpreter, but in our modern emulator we will just never write to or read from that area.
  - *0x050-0x0A0*: Storage space for the 16 built-in characters (0 through F), which we will need to manually put into our memory because ROMs will be looking for those characters.
  - *0x200-0xFFF*: Instructions from the ROM will be stored starting at 0x200, and anything left after the ROM’s space is free to use.

### 16-bit Index Register

Special register used to store memory addresses for use in operations. It's a 16-bit register because the maximum memory address ( *0xFFF* ) is too large to fit inside an 8-bit register. 

### 16-bit Program Counter

As mentioned earlier, the actual program instructions are stored in memory starting at address 0x200. The CPU needs a way of keeping track of which instruction to execute next.

The Program Counter (PC) is a special register that holds the address of the next instruction to execute. Again, it’s 16 bits because it has to be able to hold the maximum memory address (0xFFF).

An instruction is two bytes but memory is addressed as a single byte, so when we fetch an instruction from memory we need to fetch a byte from PC and a byte from PC+1 and connect them into a single value. We then increment the PC by 2 because We have to increment the PC before we execute any instructions because some instructions will manipulate the PC to control program flow. Some will add to the PC, some will subtract from it, and some will change it completely.

### 16-level Stack

A stack is a way for a CPU to keep track of the order of execution when it calls into functions. There is an instruction (CALL) that will cause the CPU to begin executing instructions in a different region of the program. When the program reaches another instruction (RET), it must be able to go back to where it was when it hit the CALL instruction. The stack holds the PC value when the CALL instruction was executed, and the RETURN statement pulls that address from the stack and puts it back into the PC so the CPU will execute it on the next cycle.

The CHIP-8 has 16 levels of stack, meaning it can hold 16 different PCs. Multiple levels allow for one function to call another function and so on, until they all return to the original caller site.

Putting a PC onto the stack is called pushing and pulling a PC off of the stack is called popping.

