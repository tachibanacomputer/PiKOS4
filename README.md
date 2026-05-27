# CapybaraOS

A minimal x86-64 bootable binary written in C and Assembly that loads via Multiboot and outputs to VGA text mode.

## What It Does

CapybaraOS is a bootable kernel binary that:
- Loads via GRUB using the Multiboot specification
- Initializes x86-64 protected mode
- Sets up a stack and calls kernel code
- Provides basic VGA terminal output (80×25)
- Displays text to the framebuffer at 0xB8000

## Building

```bash
as -o boot.o boot.s
gcc -c kernel.c -o kernel.o
ld -T linker.ld -o capybaraos.bin boot.o kernel.o
```

## Running

```bash
qemu-system-x86_64 -kernel capybaraos.bin
```

## Files

- **boot.s** — Multiboot header, stack initialization, entry point that calls kernel_main
- **kernel.c** — VGA terminal driver and kernel entry function
- **linker.ld** — Memory layout: entry at 1M, sections for code/data/bss with 4K alignment
- **grub.cfg** — GRUB configuration

## Architecture Details

**Boot flow:**
1. GRUB loads binary at 0x100000
2. Multiboot header (magic: 0x1BADB002) validates bootloader
3. `_start` sets stack pointer to 16KB stack top
4. `kernel_main()` initializes VGA and writes to 0xB8000

**VGA terminal:**
- 80 columns × 25 rows
- 16-color support (foreground/background)
- Character entries stored as: byte 0 = char, byte 1 = color
- Automatic wraparound and line wrapping

## License

[Slitherware License v1.8.0](LICENSE.md)
