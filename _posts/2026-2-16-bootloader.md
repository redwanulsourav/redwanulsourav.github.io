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
When the power button is pressed, the BIOS looks for a bootable media. It does so by iterating over every drive attached to the BIOS, and checking the first last two bytes of the first 512 bytes. So it checks the 511th byte (index: 510) and 512-th byte (index: 511). If that particular word is a 0xAA55, then it recognizes it as a bootable media. So, if we want to boot our operating system, we have to make a file that contains that magic number at the appropriate location.

```assembly

times 510 - ($-$$) db 0
dw 0xAA55

```

`$-$$` means whatever is the size between the start of this instruction and the beginning of file, (we could have other instructions before this line), then subtracting that from 510 to calculate how many 0s we will need to pad it with before we reach the 511-th byte.

The second and the last instruction sets the last two bytes to be `0xAA55`, `dw` stands for `data word`, we are storing a word which is two bytes on x86.

The file should be compiled as in flat binary mode. With nasm, it would be: `nasm -f bin bootloader.s`. T
