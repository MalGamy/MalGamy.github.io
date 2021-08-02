---
title: Anti-debugging and anti-tracing techniques part_2
layout: single
comments: true
share: true
related: true
author_profile: true
permalink: "/:title/"
tags:
- malware analysis 
- anti-debugging
- anit-tracing
categories:
- Articles
date: '2021-4-7 10-00-00 +0000'
toc: true
toc_label: Table of Contents
toc_sticky: true
---

---> Anti-debugging tricks are meant to ensure that the codes are not working under the
influence of a debugger. Say we have a program with an anti-debugging code in it. The
behavior of the program is just as if it were running without an anti-debugging code. The
story becomes different, however, when the program is being debugged. While debugging,
we encounter code that goes straight to exiting the program or jumps into code that doesn't
make sense.

<!-- more -->

![600px-cover-example](https://user-images.githubusercontent.com/74544712/113822522-d084c380-977d-11eb-8736-8614c55f27a2.png)

## Process-Environment-Block
 * PEB is high level user mode structure that holds some important information about the current process under it is field values-some field being structures
 themselves to hold more data. it is the address of the PEB structure in the TEB.ProcessEnvrionmentBlock member.The TEB structure is located at the start address of the
 segment memory pointed by the Fs segment selector, and the ProcessEnvrionmentBlock member is 30 Offset from the start 
 of TEB structure.
 
```
mov   eax , dword ptr fs:[18h]     ----> get address of TEB structure
mov   eax , dword ptr ds:[eax+30h] ----> get address of PEB structure
movzx eax , byte ptr ds: [eax+2]   ----> get value of PEB.BeingDebugged
test  eax , eax                    ----> if not zero, a Debugger has been detected
jne   debugger_detected
 ```

 * Every process has it is own PEB and the the Windows Kernel will also have access to the PEB of every user-mode process so it can keep track of certain
 data stored within it. so The Process Environment Block (PEB) is an important thing.
 


* The PEB structure comes form Windows Kernel although is accessible in user-mode. The PEB comes form the Thead Environment Block (TEB) which also
happens to be commonly referred to as the Thread Information Block (TIB). The TEB is responsible for holding data about the current thread – every
thread has it’s own TEB structure.



* Thread Environment Block or the Process Environment Block have been used for malicious purposes in the past but Microsoft 
has made a lot of changes over the recent years. in the past rootkits would inject a DLL into another running process. 

## Syntax 
 * The PED structure is defined as follows:

![22596B3557A07D4826](https://user-images.githubusercontent.com/74544712/113814506-0f148100-9772-11eb-8ec0-e268e4dd9d83.png)

## BeingDebugged
* This method is just another way to check BeingDebugged flag of PEB without calling IsDebuggerPresent().
## Description

* Instead of calling IsDebuggerPresent(), some packers manually check the PEB (Process Environment Block) for the BeingDebugged flag so when view PEB in WinDbg

```
kd> dt ntdll!_PEB
   +0x000 InheritedAddressSpace : UChar
   +0x001 ReadImageFileExecOptions : UChar
   +0x002 BeingDebugged    : UChar   ---> Indicates whether the specified process is currently being debugged
   +0x003 SpareBool        : UChar
   +0x004 Mutant           : Ptr32 Void
   +0x008 ImageBaseAddress : Ptr32 Void
   +0x00c Ldr              : Ptr32 _PEB_LDR_DATA
   +0x010 ProcessParameters : Ptr32 _RTL_USER_PROCESS_PARAMETERS
   +0x014 SubSystemData    : Ptr32 Void
   +0x018 ProcessHeap      : Ptr32 Void
   [SNIP]
   ```
## Syntax
### 32Bit Process
```
mov eax, fs:[30h]
cmp byte ptr [eax+2], 0
jne being_debugged

```
### 64Bit Process
```
mov rax, gs:[60h]
cmp byte ptr [rax+2], 0
jne being_debugged
```
### WOW64 Process
```
mov eax, fs:[30h]
cmp byte ptr [eax+1002h], 0
```
### C/C++ Code
```
#ifndef _WIN64
PPEB pPeb = (PPEB)__readfsdword(0x30);
#else
PPEB pPeb = (PPEB)__readgsqword(0x60);
#endif // _WIN64
 
if (pPeb->BeingDebugged)
    goto being_debugged;
```
## Return value
* If byte ptr [eax+2] returns 1, it means the the program is being debugged and the jump at offset 0x4010D8 won't be taken.
   

## Bypass BeingDebugged Chek
### stage(1)
* let's open the samples in x32dbg and breakpoint in 763E38F0.

![breakpoint](https://user-images.githubusercontent.com/74544712/113815852-23f21400-9774-11eb-99b0-c3367b0a1c01.PNG)

### stage(2)
* The PEB structure has been loaded into the EAX register.

* I will follow the value of eax in the dump.

![Stage2](https://user-images.githubusercontent.com/74544712/113817407-906e1280-9776-11eb-90e9-9a9f3b0eca85.PNG)

### stage(3)
* we see that offset 0x2 in the PEB structure, the value is 0x1. It corresponds to the BeingDebugged flag
.
* Right click on it and select ```Binary``` > ```Fill with 00's```
![stage3](https://user-images.githubusercontent.com/74544712/113818742-85b47d00-9778-11eb-8e92-31bf8a93b392.PNG)

## References 
* parctical malware analysis. 
* Mastering reverse engineering.
* https://www.aldeid.com/wiki/PEB-Process-Environment-Block/BeingDebugged
* https://docs.microsoft.com/en-us/windows/win32/api/winternl/ns-winternl-peb
* https://reverseengineering.stackexchange.com/questions/19178/automating-bypassing-anti-debug-checks
