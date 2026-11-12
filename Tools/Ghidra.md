# Ghidra

Ghidra is a free, open-source reverse-engineering suite from the NSA. It disassembles and **decompiles** binaries back into readable C-like pseudocode, which makes it the go-to tool for understanding malware, closed-source apps, firmware, and CTF binaries without paying for IDA Pro.

## Install

```bash
# Needs a JDK (17+)
# Download from the official site and run:
./ghidraRun            # Linux/macOS
# ghidraRun.bat on Windows
```

## Core workflow

1. Create a **project** and import the binary (`File -> Import File`).
2. Let the **auto-analysis** run (accept the defaults the first time).
3. Use the **Symbol Tree** to find `main`, exports, and interesting functions.
4. Read the **Decompiler** window (right pane) - this is the killer feature.
5. Rename variables/functions as you understand them; add comments.

## What you use it for

- Reverse malware to understand behavior and extract IOCs
- Find hardcoded secrets, keys, and logic in closed-source apps
- Analyze firmware (routers, IoT) and native mobile libraries
- Solve RE and pwn CTF challenges (see [Reverse Engineering Basics](../Reverse%20Engineering/Reverse%20Engineering%20Basics.md))
- Study a vulnerable function before writing an exploit ([OSED](../Certifications/OSED.md) work)

## Handy features

- **Decompiler** - C pseudocode for most architectures (x86, ARM, MIPS, etc.)
- **Patch / assemble** instructions
- **Scripting** - Java or Python (Ghidrathon) to automate analysis
- **Version Tracking / BSim** - diff binaries, find known functions
- **Collaborative mode** - shared projects for teams

## Tips

- Rename as you go; a well-annotated listing is half the battle.
- Cross-references ("x" key / right-click) show who calls a function and what reads a variable.
- Pair with a debugger (gdb, x64dbg, or Ghidra's own debugger) for dynamic analysis.

## Resources

- [Ghidra official site](https://ghidra-sre.org/)
- [Ghidra on GitHub](https://github.com/NationalSecurityAgency/ghidra)
