# Trace Analysis
See [readme](README.md) for instructions on how to set up the entire experiment.

## Pre-requisites
TODO

## Step 1. Disassemble the ELF File
In the example we are using ```blink.elf```. It is **32-bit little-endian**.
The Pico toolchain automatically generates a ```.dis``` file when building the
ELF, it should be found at the same ```build``` directory where the ```blink.elf```
file locates.

If the ```.dis``` file is not found, the following command can help to generate a 
similar one:  
```
arm-none-eabi-objdump -d -h blink.elf > blink.dis
```

## Step 2. Generate Human-Readable Trace
With the collected trace dump ```trace.bin``` and a ```ptm2human``` program
built using the [ptm2human-fork](https://github.com/czietz/ptm2human/) by 
[@czietz](https://github.com/czietz), we can generate a human-readable trace
file:

```
./ptm2human -e -n -i trace.bin > trace.txt
```

## Step 3. Analysis
Below is a snippet from the human-readable trace file, I included the entire 
file as ```trace_example1.txt``` under the current directory. The trace is 
captured using a **8KB circular buffer** (meaning the older traces got overwritten
by newer ones).
```
Address - Instruction address 0x0000000010001356, Instruction set Aarch32 (ARM)
ATOM - N
Commit - 1
ATOM - N
Commit - 1
ATOM - N
Commit - 1
ATOM - N
Commit - 1
ATOM - N
Commit - 1
ATOM - E
Commit - 1
```
Searching for the address ```0x10001356``` in the disassembled text file ```blink.dis```,
the related snippet is shown below,
```
100012ec <sleep_until>:
...
10001356:	6a43      	ldr	r3, [r0, #36]	@ 0x24
10001358:	429d      	cmp	r5, r3
1000135a:	d804      	bhi.n	10001366 <sleep_until+0x7a>
1000135c:	6a81      	ldr	r1, [r0, #40]	@ 0x28
1000135e:	42b1      	cmp	r1, r6
10001360:	d2dc      	bcs.n	1000131c <sleep_until+0x30>
10001362:	429d      	cmp	r5, r3
10001364:	d1da      	bne.n	1000131c <sleep_until+0x30>
10001366:	6813      	ldr	r3, [r2, #0]
10001368:	f3ef 8110 	mrs	r1, PRIMASK
1000136c:	b672      	cpsid	i
1000136e:	e8d3 8fcf 	ldaexb	r8, [r3]
10001372:	f05f 0e01 	movs.w	lr, #1
10001376:	f1b8 0f00 	cmp.w	r8, #0
1000137a:	d1f8      	bne.n	1000136e <sleep_until+0x82>
1000137c:	e8c3 ef48 	strexb	r8, lr, [r3]
10001380:	f1b8 0f00 	cmp.w	r8, #0
10001384:	d1f3      	bne.n	1000136e <sleep_until+0x82>
10001386:	f3bf 8f5f 	dmb	sy
1000138a:	6813      	ldr	r3, [r2, #0]
1000138c:	e8c3 cf8f 	stlb	ip, [r3]
10001390:	f381 8810 	msr	PRIMASK, r1
10001394:	bf20      	wfe
10001396:	e7de      	b.n	10001356 <sleep_until+0x6a>
```
