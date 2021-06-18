# Luraph_Deobfuscator
Automatically deobfuscate Luraph scripts. Tested on Luraph scripts between v11.5 and v11.8.1 (May 1, 2020 release)

Requires Java 8 to run.

# Binary Release (Version 1.0.0)
https://github.com/TheGreatSageEqualToHeaven/LuraphDeobfuscator/releases/download/v1.0.0/LuraphDevirtualizer.jar

# Discord Group
Come discuss this project in my Lua discord 

https://discord.gg/cdhcryv

Add me on discord

 ```The Great Sage Equal To Heaven#7780```

# Motivation
This project was created for intellectual purposes as a way to explore code deobfuscation techniques on Lua scripts obfuscated by Luraph, which is a tool used to obfuscate Lua scripts. This Lua obfuscator has been used by cybercriminals as a way of protecting malicious exploits they sell for Robolox or bots for World of Warcraft. This project may be of interest to game security developers to prevent cheating in their games by discovering how hackers are breaking their code.

Luraph scripts are extremely complicated to tackle, especially in an automated matter. The process is essentially.

- Lex and parse input Lua file to generate parse tree
- Convert parse tree into abstract syntax treee
- Optimize the abstract syntax tree
- Rename variables, populate symbol table.
- Detect and rename variables and function names into names such as ```get_float64```, etc
- Detect VM handlers
- Find all encryption information in Lua VM
- Load and decrypt bytecode using above information.
- Decrypt and populate chunks by symbolically executing Lua code (constants, instructions, prototypes).
- Process VM handler redirection and decryption (bytecode redirection).
- Remove antidecompiler tricks from optimized bytecode
- Remove junk code and further optimize bytecode using various techniques and algorithms.
- Generate luac file

LuraphDeobfuscator provides a Lua deobfuscation engine which can be ported to other Lua obfuscators trivially. This source code can be used, with minor modifications, for a vast array of things related to Lua. I emplore the community to reuse my code and engine to create other Lua deobfuscators, as long as they state they are using my code and provide a link back to this project.

# Simple usage
Generate a .luac file from an obfuscated Luraph VM script


```java -jar LuraphDeobfuscator.jar -i obfuscated.lua -o obfuscated.luac```

You may now use a Lua decompiler such as unluac to decompile the code back into Lua. I recommend unluac for decompilation and luadec for generating disassemblies with pseudocode.

# Advanced Use: Introduction

I have written the sample Lua code below:
```lua
local x = 0

local name = "name: "

for i = 0, 10 do
	x = x + i
end

for i = 0, 10 do
	name = name .. i .. ","
end

print(x)
print(name)
```

Ive uploaded the obfuscated version of that Luraph script to pastebin here: https://pastebin.com/SubyTCBL

Here is a screenshot of what the obfuscated code looks like:

![Luraph VM](https://i.imgur.com/xZwYsOg.png)

We are now going to explore all of Luraph Deobfuscator's features:

## Advanced Use: Deobfuscation ##

Download the above script as file.lua and run the deobfuscator

```java -jar LuraphDeobfuscator.java -i file.lua -o file.luac```

Use unluac to decompile the generated bytecode and save it to file.lua

```java -jar unluac.jar file.luac > file.lua```

Deobfuscated code using luadec

```lua
local l_0_0 = 0
local l_0_1 = "name: "
for l_0_5 = 0, 10 do
  l_0_0 = l_0_0 + l_0_5
end
for l_0_9 = 0, 10 do
  l_0_1 = l_0_1 .. l_0_9 .. ","
end
print(l_0_0)
print(l_0_1)
```

Deobfuscated code using unluac

```lua
local L0_0, L1_1, L2_2, L3_3, L4_4, L5_5
L0_0 = 0
L1_1 = "name: "
for L5_5 = 0, 10 do
  L0_0 = L0_0 + L5_5
end
for L5_5 = 0, 10 do
  L1_1 = L1_1 .. L5_5 .. ","
end
L2_2(L3_3)
L2_2(L3_3)
```

Of course, we know that using a decompiler we can't always expect to get back a complete version of the source code. In this case luadec generates the complete source code but unluac misses assigning ```print``` to ```L2_2```

To get an accurate dissasembly listing, we can also use luadec's disassembler

```luadec -dis file.lua > file.dis.txt```

```lua
-dis file.lua 

; Function:        0
; Defined at line: 0
; #Upvalues:       0
; #Parameters:     0
; Is_vararg:       2
; Max Stack Size:  13

    0 [-]: LOADK     R0 K0        ; R0 := 0
    1 [-]: LOADK     R1 K1        ; R1 := "name: "
    2 [-]: LOADK     R6 K0        ; R6 := 0
    3 [-]: LOADK     R7 K2        ; R7 := 10
    4 [-]: LOADK     R8 K3        ; R8 := 1
    5 [-]: FORPREP   R6 1         ; R6 -= R8; pc += 1 (goto 7)
    6 [-]: ADD       R0 R0 R9     ; R0 := R0 + R9
    7 [-]: FORLOOP   R6 -2        ; R6 += R8; if R6 <= R7 then R9 := R6; PC += -2 , goto 6 end
    8 [-]: LOADK     R6 K0        ; R6 := 0
    9 [-]: LOADK     R7 K2        ; R7 := 10
   10 [-]: LOADK     R8 K3        ; R8 := 1
   11 [-]: FORPREP   R6 4         ; R6 -= R8; pc += 4 (goto 16)
   12 [-]: MOVE      R10 R1       ; R10 := R1
   13 [-]: MOVE      R11 R9       ; R11 := R9
   14 [-]: LOADK     R12 K4       ; R12 := ","
   15 [-]: CONCAT    R1 R10 R12   ; R1 := concat(R10 to R12)
   16 [-]: FORLOOP   R6 -5        ; R6 += R8; if R6 <= R7 then R9 := R6; PC += -5 , goto 12 end
   17 [-]: MOVE      R6 R2        ; R6 := R2
   18 [-]: MOVE      R7 R3        ; R7 := R3
   19 [-]: CALL      R6 2 1       ;  := R6(R7)
   20 [-]: MOVE      R6 R2        ; R6 := R2
   21 [-]: MOVE      R7 R3        ; R7 := R3
   22 [-]: CALL      R6 2 1       ;  := R6(R7)
   23 [-]: RETURN    R0 1         ; return 
```

# Advanced Use: Inspecting Luraph VM

If we are curious about manually inspecting the VM ourselves, we can use LuraphDeobfuscator to generate a pseudocode source listing of the Luraph VM either in psuedocode or with its graphical display

To save the VM to a file

```java -jar LuraphDeobfuscator -i file.lua -s > file.vm.lua```

To copy the VM to your clipboard (CTRL+C/CTRL+P)

```java -jar LuraphDeobfuscator -i file.lua -c```

The contents of running that command on the script we are interested can be found here:

https://pastebin.com/eK0Tu0QM

And when we look at a screenshot we can now easily read the VM code. This process is used internally by LuraphDeobfuscator in order to fully deobfuscate Luraph code.

We can see it even assigns names to functions and variables. Pretty neat!

![Optimized VM](https://i.imgur.com/LuingqO.png)

# Advanced Use: Viewing Abstract Syntax Trees

We can also view the optimized VM graphically:

```java -jar LuraphDeobfuscator -i file.lua -s```

The following window will be displayed:

![Luraph AST](https://i.imgur.com/Hv3rTQ7.png)

# Advanced Use: Dumping Bytecode

It is normally better to use luadec to generate a disassembly of bytecode, but for whatever reason if that doesn't happen or you are curious about exploring LuraphDeobfuscator's features, you can also use it to dump optimized bytecode.

```java -jar LuraphDeobfuscator -i file.lua -b > file.bytecode.txt```

```lua
DUMPING CHUNK
#UPVALUES = 0.0
#PARAMS = 0.0
#MAXSTACKSIZE = 9.0
#CONSTANTS = 7
#INSTRUCTIONS = 24
#PROTOTYPES = 0

Constant 0 = 0.0
Constant 1 = name: 
Constant 2 = 10.0
Constant 3 = 1.0
Constant 4 = ,
Constant 5 = print
Constant 6 = 

[0] LOADK 0.0 0.0
[1] LOADK 1.0 1.0
[2] LOADK 2.0 0.0
[3] LOADK 3.0 2.0
[4] LOADK 4.0 3.0
[5] FORPREP 2.0 1.0
[6] ADD 0.0 0.0 5.0
[7] FORLOOP 2.0 -2.0
[8] LOADK 2.0 0.0
[9] LOADK 3.0 2.0
[10] LOADK 4.0 3.0
[11] FORPREP 2.0 4.0
[12] MOVE 6.0 1.0
[13] MOVE 7.0 5.0
[14] LOADK 8.0 4.0
[15] CONCAT 1.0 6.0 8.0
[16] FORLOOP 2.0 -5.0
[17] GETGLOBAL 2.0 5.0
[18] MOVE 3.0 0.0
[19] CALL 2.0 2.0 1.0
[20] GETGLOBAL 2.0 5.0
[21] MOVE 3.0 1.0
[22] CALL 2.0 2.0 1.0
[23] RETURN 0.0 1.0


END CHUNK
```
