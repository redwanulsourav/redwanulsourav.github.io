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
When the power button is pressed, the BIOS looks for a bootable media. It does so by iterating over every drive attached to the BIOS, and checking the first last byte of the first 512 bytes. So it checks the 512th byte (index: 511). If that particular byte is a 0xAA55, then it recognizes it as a bootable media. So, if we want to boot our operating system, we have to make a file that has that magic number as 512-th byte. 

```asm
dw 0xAA55
```
