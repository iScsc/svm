# SVM 🧪
## Building a **Super Virtual Machine** for malware obfuscation

### Context
#### Malware obfuscation: Why and How?

In malware devlopment, it's common to *obfuscate* (make unintelligible) the behavior of our malicious program. The goal is to make the reverse engineering task difficult for those who wants to disect and understand how our malware works!

Here are common ways to do this:
- **A Packer** takes a piece of code, and obfuscate it thanks to encryption, compression or any other custom technics that makes the code unintelligible. Then, when the program runs, the packers *unpack* the obfuscated code, loads it in the program memory and executes it. The main purpose of a packer is to make *static analysis* (analyze of the sample without running it, simply by reading the available code) impossible.
- **A Virtual Machine (VM)** (which is a bad name because it has nothing to do with virtualization like VirtualBox and VMWare) adds a layer of virtualization in the binary execution. Instead of using the actual memory and registers provided by the OS, it creates it's own set of registers, stack, head inside the malware RAM. To do so, the VM reimplements the whole logic of execution by parsing the opcodes of the program and managing the memory and registers. Most of the time, the opcodes of the VM are custom to add complexity for the reverse engineers and for decompilers like IDA.

> 📝 **Note**:
>
> Most of the time, malware samples use several obfuscation layers at the same time! So you may find malwares with multiple packers and with an extra layer of VM inside!
> That's why even if these obfuscation technics are not that difficult to reverse alone, they can be a real nightmare when mixed together.

#### A little example of VM

Let's take a really simple example: adding two numbers in registers.
A way to do it in assembly code could be:

```asm
mov eax, 3          ; stores 3 in eax
mov ebx, 1          ; stores 1 in ebx
add eax, ebx        ; adds eax and ebx and stores the result (4) in eax
```

A VM written in Python could implement this step like this:

```python
# Init the VM
r1 = 0
r2 = 0
ip = 0

# The obfuscated binary
sample = "m13m21a"

# The VM implementation
while ip < len(sample):
    print(f"r1: {r1}, r2: {r2}: ip: {ip}")
    if sample[ip] == "m":
        if sample[ip + 1] == "1":
            r1 = int(sample[ip + 2])
            ip += 3
        elif sample[ip + 1] == "2":
            r2 = int(sample[ip + 2])
            ip += 3
        else:
            raise Exception("Unknown opcode")
    elif sample[ip] == "a":
        r1 += r2
        ip += 1

print()
print("final results:")
print(f"    r1: {r1}, r2: {r2}: ip: {ip}")
```
will give the following output
```
r1: 0, r2: 0: ip: 0
r1: 3, r2: 0: ip: 3
r1: 3, r2: 1: ip: 6

final results:
    r1: 4, r2: 1: ip: 7
```

So, thanks to the VM, we translated a small chunk of assembly which was easy to understand in a unintelligible string `m13m21a`. The only way for the reverse engineer to understand this piece of code is to reverse the VM (which can be very difficult in some cases) and then translate the obfuscated code!

### SVM Goals

- Language (must choose depending on the team within): Python, C, C++, Rust
- **NO REQUIREMENT**: No need to know assembly (we will create our own), or binary internals (stack, heap, registers, function calls...). The goal of this project is to discover all of these concepts by rebuilding them! 

---

- [ ] Building a simple VM to obfuscate a crackme (string management, simple crypto)
- [ ] Add a support for Windows API to try to simulate real malware behavior
- [ ] Bypass Windows Defender
- [ ] Add random code to trick the reverser
- [ ] Build a simple compiler to automate VM obfuscation
- [ ] Build a simple decompiler to automate VM deobfuscation (involve a reverser team who doesn't know the packer)

---

*Sorry for the typos, didn't take the time to correct them 🙃*

