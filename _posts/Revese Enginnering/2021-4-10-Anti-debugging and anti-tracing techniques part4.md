---
title: Anti-debugging and anti-tracing techniques part4
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
date: '2021-4-10 10-00-00 +0000'
toc: true
toc_label: Table of Contents
toc_sticky: true
---
# Introducation 

the last week i published [IsDebuggerPresent()](https://malgamy.github.io/Anti-debugging-and-anti-tracing-techniques/)technique which function available in the kernel32.dll library.
This function is often used in malware to complexify the reverse engineering because it will take different paths in the program’s flow when the 
malware is analyzed in a user-mode debugger such as x32dbg and the most widely used anti-debugging method in Windows, Here i will
be going through anothor very commen technique that malware authors use it, [CheckRemoteDebuggerPresent()](https://docs.microsoft.com/en-us/windows/win32/api/debugapi/nf-debugapi-checkremotedebuggerpresent) from kernel32.dll.

<!-- more -->
# CheckRemoteDebuggerPresent()
![image(1)](https://user-images.githubusercontent.com/74544712/114262513-aa129280-99e0-11eb-94c3-901fa4300b60.PNG)
## Description 

* This Windows API can be used to detect if the calling process is being debugged through any debugger, but also if another process is being debugged.

# Syntax
## MSDN

```
BOOL CheckRemoteDebuggerPresent(
  HANDLE hProcess,
  PBOOL  pbDebuggerPresent
);
```
## Debugger 

```
push pbDebuggerPresent_address ---> push the address of a 32 bit variable
push Process__handle           ---> push the handle to a process ( 1 for the calling process)
// call
CheckRemoteDebuggerPresent    ---> call the API
mov eax , pbDebuggerPresent_address ] ---> check the returned result in the variable
test eax,eax
jne _debuggerfound            ----> if not zero, a debugger was found.
```
## C/C++ Code
```
BOOL bDebuggerPresent;
if (TRUE == CheckRemoteDebuggerPresent(GetCurrentProcess(), &bDebuggerPresent) &&
    TRUE == bDebuggerPresent)
    ExitProcess(-1);
```
## x86 Assembly
```
lea eax, bDebuggerPresent]
    push eax
    push -1  ; GetCurrentProcess()
    call CheckRemoteDebuggerPresent
    cmp [bDebuggerPresent], 1
    jz being_debugged
    ...
being_debugged:
    push -1
    call ExitProcess
```
## x86 Assembly
```
  lea rdx, [bDebuggerPresent]
    mov rcx, -1 ; GetCurrentProcess()
    call CheckRemoteDebuggerPresent
    cmp [bDebuggerPresent], 1
    jz being_debugged
    ...
being_debugged:
    mov ecx, -1
    call ExitProcess
```
## return Value 
* If this function return 1 ----->  Debugger Found!
* If this function return 0 --->  Debugger is not  Found!
## Bypass CheckRemoteDebuggerPresent
### Stage(1)
* I will put breakpoint in CheckRemoteDebuggerPresent() function 
* Press ```F9``` to run sample
![image(20)](https://user-images.githubusercontent.com/74544712/114263583-c1ed1500-99e6-11eb-9dce-98eac27f76b3.PNG)


### Stage(2)
* press ```f7``` to enter in this funtion and bypass the trick.
* When i press ```F8``` in function to bypass the trick , i find this function NtQueryInformationProcess() which retrieves information about the specified process.
![Capture](https://user-images.githubusercontent.com/74544712/114264674-86ede000-99ec-11eb-82f8-3ad5a6904cb3.PNG).

### Stage(3)
 * Look at ```EAX``` at register EAX --> ```0000000```
 * Look at stack in the value being debugged ---> ```FFFFFFFF```
 * Compare the two values
 * To bypass this trick must  be changed```ffffffff``` (being debugged) to 0 because any value is not equal ```0``` the sample will terminate itself.
 ![Capture2](https://user-images.githubusercontent.com/74544712/114265718-b30c5f80-99f2-11eb-9ab2-6f9dcd8f02f6.PNG)
 
### Stage(4)
 * We notice that line ``` XOR EAX , EAX ``` ----> ```EAX``` ---->``` 0```
 * I will click right in line that compare two value ---> ```Assemble```---> change ```CMP``` to ```MOV``` and click ```assemble```
![image3](https://user-images.githubusercontent.com/74544712/114266598-7131e800-99f7-11eb-8c94-2ba6ee4be5f3.PNG)

### Stage(5) 
 * Press ```F8``` 
 * We see call that terminates sample not execute 
 * Nice to bypass this trick.
 ![image4](https://user-images.githubusercontent.com/74544712/114266628-8ad32f80-99f7-11eb-9736-59a6d0d30e64.PNG)
 
## References

* https://anti-debug.checkpoint.com/techniques/debug-flags.html#using-win32-api-checkremotedebuggerpresent
* https://www.codeproject.com/Articles/29469/Introduction-Into-Windows-Anti-Debugging
* https://xorl.wordpress.com/2017/12/09/the-checkremotedebuggerpresent-anti-debugging-technique/
* https://docs.microsoft.com/en-us/windows/win32/api/winternl/nf-winternl-ntqueryinformationprocess


