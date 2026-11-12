# Reverse Engineering Basics

Reverse engineering (RE) is taking a compiled program apart to understand how it works without the source code. For pentesters it shows up in malware analysis, cracking closed-source apps, firmware/IoT, native mobile libraries, and CTF challenges. You do not need to be a RE expert to benefit, but understanding the basics unlocks a whole class of targets.

## Static vs dynamic analysis

- **Static** - analyze the binary without running it: disassembly, decompilation, strings, structure. Safe, thorough, slower.
- **Dynamic** - run the binary and watch it: debuggers, tracers, memory inspection. Faster for understanding behavior, but you must run (potentially malicious) code in isolation.

Use both: static to map the code, dynamic to confirm what actually happens.

## What you need to know

- **Assembly basics** (x86-64 and ARM): registers, the stack, calling conventions, common instructions
- **Executable formats**: PE (Windows), ELF (Linux), Mach-O (macOS)
- **How high-level constructs compile** (loops, branches, function calls)
- **C fundamentals** so decompiled pseudocode reads naturally

## Core tools

- **[Ghidra](../Tools/Ghidra.md)** - free disassembler + decompiler (great starting point)
- **IDA Pro / IDA Free** - industry-standard disassembler
- **radare2 / Cutter** - open-source RE framework
- **gdb** (with pwndbg/GEF), **x64dbg** (Windows) - debuggers
- **strings, file, nm, objdump, ltrace/strace** - quick triage from the shell

## A simple workflow

```bash
file target            # what kind of binary is it?
strings -n 6 target    # quick wins: URLs, keys, messages
```

1. Triage with `file` and `strings`.
2. Load into [Ghidra](../Tools/Ghidra.md); let auto-analysis run; read `main` in the decompiler.
3. Rename functions/variables as you understand them.
4. Confirm behavior dynamically in a debugger, in an isolated VM.

## Safety

Analyze unknown or malicious binaries in an **isolated VM** with no network (or a controlled one). Snapshot before running. Never analyze malware on your host.

## Where it leads

- Malware analysis and detection engineering
- [Binary exploitation](Binary%20Exploitation.md) and exploit dev ([OSED](../Certifications/OSED.md))
- Firmware and mobile RE (with [Frida](../Tools/Frida.md))

## Practice

- [pwn.college](../Labs/pwn.college.md), [Root Me](../Labs/Root%20Me.md), crackmes.one, Microcorruption

## Related

- [Binary Exploitation](Binary%20Exploitation.md), [Ghidra](../Tools/Ghidra.md), [Buffer Overflow](../Vulnerabilities/Buffer%20Overflow.md)
