ijvm-disasm
===========

Tool for disassembling IJVM bytecode files

Usage
-----

```
Usage: ijvm-disasm bc-file [bc-file2 ...]
```

Example
-------

Source code `fib.j`:

```
.method fib
.args 2
.define n = 1
.define OBJREF = 44

	iload n
	bipush 2
	isub
	iflt ret1

	bipush OBJREF
	iload n
	bipush 1
	isub
	invokevirtual fib

	bipush OBJREF
	iload n
	bipush 2
	isub
	invokevirtual fib

	iadd

	ireturn

ret1:
	bipush 1

	ireturn

.method main
.args 2
.define n = 1
.define OBJREF = 44

	bipush OBJREF
	iload n

	invokevirtual fib

	ireturn
```

Assembled bytecode `fib.bc` (`ijvm-asm fib.j fib.bc`):

```
main index: 1
method area: 49 bytes
00 02 00 00 15 01 10 02 64 9b 00 19 10 2c 15 01
10 01 64 b6 00 00 10 2c 15 01 10 02 64 b6 00 00
60 ac 10 01 ac 00 02 00 00 10 2c 15 01 b6 00 00
ac
constant pool: 2 words
00000000
00000025
```

`ijvm-disasm` output (`ijvm-disasm fib.bc`):

```
; ====================
; Method area:
; 0x0 - const_0
; args: 2, locals: 0
04:  15 01     iload 1
06:  10 02     bipush 2
08:  64        isub
09:  9b 00 19  iflt 0x19       ; address: 0x22
0c:  10 2c     bipush 44
0e:  15 01     iload 1
10:  10 01     bipush 1
12:  64        isub
13:  b6 00 00  invokevirtual 0 ; method location: 0x0
16:  10 2c     bipush 44
18:  15 01     iload 1
1a:  10 02     bipush 2
1c:  64        isub
1d:  b6 00 00  invokevirtual 0 ; method location: 0x0
20:  60        iadd
21:  ac        ireturn

; Label: loc_0x22
22:  10 01     bipush 1
24:  ac        ireturn

; 0x25 - main (const_1)
; args: 2, locals: 0
29:  10 2c     bipush 44
2b:  15 01     iload 1
2d:  b6 00 00  invokevirtual 0 ; method location: 0x0
30:  ac        ireturn

; ====================
; Constant area:
0: 00000000
1: 00000025
```