---
layout: post
title: Basic Bootloader
date: 2026-2-16 01:36:00
description: Writing a basic bootloader that does nothing.
tags: osdev
categories: projects
featured: false
---

## The Booting Process
When the power button is pressed, the BIOS looks for a bootable media. It does so by iterating over every drive attached to the BIOS, and checking the last two bytes of the first 512 bytes. If those two bytes (a word) is exactly 0x55AA, it recognizes the drive as bootable.

So if we want to make our own OS, it must contain 0x55AA as the last word of the first 512 bytes. The first 512 bytes are also called a _bootsector_. In the bootsector, we will also setup a few other things and pass the control to the kernel.

We start by making a bootsector which contains 0x55AA as the last word. 

```asm

times 510 - ($-$$) db 0
dw 0xAA55

```
Last word should be 0x55AA, which leaves 512 - 2 = 510 bytes for us. For now, we fill those bytes up with 0. (Soon it will contain code to setup some other things, along with passing control to the kernel)

One thing to notice is that in the assembly, the last word is 0xAA55. The rationale is that x86 architectures are little endian, so the higher byte comes first. After compilation 0xAA55 becomes 0x55AA.

The file should be compiled as in flat binary mode. 

```bash

nasm -f bin bootloader.s 
```

This produces the flat binary file _bootsector_. Run this file with

```bash

qemu-system-i386 bootsector
```

QEMU boots the OS, and nothing else happens, because we didn't do any thing yet.

To see some output, we can print a character. To do that, we have to write some characters to VGA memory location. The VGA memory is located at 0xB8000. To print a character, we need to write two bytes. The first byte is the ASCII value of the character that we want to be printed, and the second byte is the attribute for that character. The attribute specifies the color of the character, among other things. We can modify the bootsector as follows:

```asm

mov ax, 0xB800
mov ds, ax
mov byte [0x0000], 'A'
mov byte [0x0001], 0x0F
jmp $
times 510 - ($-$$) db 0
dw 0xAA55
```

This bootsector should print an `A` at the very begninning of the QEMU emulator. QEMU emulator usually shows some messages at the beginning, this `A` overwrites that space. It shows our bootsector is working.


