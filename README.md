
<p align="center">
<img src="GNU-Logo.png" alt="Project Image" width="150"/>
</p>

<h1 align="center">The GNU Debugger Documentation</h1>


> **> Purpose**
> This document is a professional checklist for using **GDB** to understand, analyze, and assess exploitability of **Android Native (ARM64)** binaries and shared libraries.
> Target audience:
> * Android Exploitation
> * Native Debugging
> * Vulnerability Research
> * Android Security Research


----------

## Table of Contents

* 01.Environment Preparation
* 02.Binary Reconnaissance (Before Running)
* 03.Starting GDB Session (Android)
* 04.Initial GDB Setup
* 05.Breakpoints and Flow Control
* 06.Execution Control
* 07.Registers Inspection (ARM64)
* 08.Stack Analysis
* 09.Heap Analysis
* 10.Crash and Segmentation Fault Analysis
* 11.Exploitability Assessment
* 12.Memory Protections Verification
* 13.Shared Library Debugging
* 14.Syscall and Low-Level Analysis
* 15.Post-Crash Actions
* 16.Documentation for GitHub

## Scope and Android Exploitation Focus
This checklist is **explicitly designed for Android exploitation and native debugging**. It is not a generic GDB reference.

Android-specific assumptions and context:
* Android Runtime and Native Layer (Bionic libc, linker, ART interaction)
* ARM64 (AArch64) calling conventions and register usage
* Android process model (zygote-spawned apps, isolated processes)
* Android-specific memory layout and mappings
* Use of `adb` and `gdbserver` for remote debugging
* Debugging native binaries and shared libraries inside APKs
* Analysis of crashes originating from JNI, NDK code, or native services

Out of scope by design:
* Desktop Linux exploitation workflows
* Kernel exploitation
* Generic GDB command reference
* Non-ARM architectures unless explicitly stated

The goal of this document is to guide **Android exploit developers and security researchers** in reasoning about crashes, memory corruption, and exploitability using GDB in real Android environments.

---

## One. Environment Preparation

Ensure the debugging environment is properly prepared before starting.
* Android device or emulator (ARM64)
* USB debugging enabled
* `adb` working correctly
* `gdb` installed on the host
* `gdbserver` available on the device
* `pwndbg` or `gef` configured
* Binary compiled with:
* `-g`
* `-fno-omit-frame-pointer` (if possible)

----------

## Two. Binary Reconnaissance (Before Running)

Initial static inspection of the binary before execution.

### Architecture

```bash
file binary
```

### Security Mitigations

```bash
readelf -l binary
```

### Symbols

```bash
nm -C binary
```

### Entry Point

```bash
readelf -h binary
```

----------

## Three. Starting GDB Session (Android)

### Native Binary

```bash
adb push binary /data/local/tmp/
```

```bash
gdbserver :1234 ./binary
```

```bash
gdb binary
target remote :1234
```

### Shared Library (.so)

```bash
adb shell ps -A | grep target
```

```bash
gdbserver :1234 --attach <PID>
```

----------

## Four. Initial GDB Setup

```gdb
set architecture aarch64
set print pretty on
set pagination off
```

----------

## Five. Breakpoints and Flow Control

```gdb
b main
b vuln_function
b *0xADDRESS
run
continue
```

----------

## Six. Execution Control (Critical)

```gdb
si# step one instruction
ni# next instruction
step# step into function
next# step over function
```

----------

## Seven. Registers Inspection (ARM64)

```gdb
info registers
```

Always focus on:
* `pc`
* `sp`
* `x0` to `x7` (function arguments)
* `x30` (link register)

----------

## Eight. Stack Analysis

```gdb
bt
x/40gx $sp
```

Checklist:
* Return address
* Stack frame layout
* Overwritten values

----------

## Nine. Heap Analysis

```gdb
info proc mappings
x/32gx 0xHEAP_ADDRESS
```

Look for:
* Heap corruption
* Use-after-free patterns
* Heap metadata overwrite

----------

## Ten. Crash and Segmentation Fault Analysis

```gdb
x/i $pc
info registers
```

Determine crash type:
* NULL pointer dereference
* Buffer overflow
* Use-after-free
* Double free

----------

## Eleven. Exploitability Assessment

* Program counter control
* Register influence
* Stack or heap shaping
* ASLR bypass feasibility
* NX enforcement
* RELRO status

----------

## Twelve. Memory Protections Verification

### ASLR

```gdb
info proc mappings
```

### PIE

```bash
readelf -h binary | grep Type
```

### NX

* Is the stack executable?

----------

## Thirteen. Shared Library Debugging

```gdb
info sharedlibrary
b dlopen
info functions
```

----------

## Fourteen. Syscall and Low-Level Analysis

```gdb
x/i $pc
```

* Syscall number: `x8`
* Arguments: `x0` to `x5`

----------

## Fifteen. Post-Crash Actions
* Save register state
* Save stack snapshot
* Dump relevant memory regions
* Document offsets and control points

----------

## Sixteen. Documentation for GitHub

Each vulnerability should include:
* Vulnerability description
* Root cause analysis
* Crash logs
* GDB outputs or screenshots
* Exploitability conclusion
* Mitigation notes

----------

## Professional Rule
> If you cannot explain the crash using only GDB output,
> you do not understand the bug.

----------

## Final Note
> GDB is not just a tool.
> It is the language exploit developers think in.

----------

### Usage

Use this checklist during:
* Capture The Flag competitions
* Native and Android bug bounty research
* Malware analysis
* Android exploitation research

This document is designed to be easily extended with advanced sections such as pwndbg, gef, real-world labs, and exploit walkthroughs.


---

<h3 align="center">> The END <</h3>

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

