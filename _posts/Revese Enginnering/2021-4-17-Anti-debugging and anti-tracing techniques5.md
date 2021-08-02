---
title: Anti-debugging and anti-tracing techniques part5
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
date: '2021-4-17 10-00-00 +0000'
toc: true
toc_label: Table of Contents
toc_sticky: true
---
# Introducation 

This article focuses on how to detect debug strings outputted by an application that calls Win32 API OutputDebugString.
And how bypass it using a debugger

<!-- more -->
#  OutputDebugString
![Outputdebuggingstring](https://user-images.githubusercontent.com/74544712/115109147-5f5ec080-9f74-11eb-86c2-280a0d115376.PNG)
## Description 
The Kernel32 outputDebugString () function that uses to detect a debugger and explains different behaviour depended on the version of windows and it
Outputs a string to a debugger to attach it.
## Syntax
### c++
```
#include <Windows.h>

int main()
{
	// Make sure there isn't an error state
	SetLastError(0);
	// Send a string to the debugger
	OutputDebugStringA("Hello, debugger");
	if (GetLastError() != 0)
	{
		MessageBoxA(NULL, "Debugger Detected", "", MB_OK);
	}
	return 0;
}
```
### 32Bit Process
```
xor ebp, ebp
push ebp
push esp
call OutputDebugStringA
cmp fs:[ebp+34h], ebp ;LastErrorValue
je being_debugged
```
### 64Bit Process
```
xor ebp, ebp
enter 20h, 0
mov ecx, ebp
call OutputDebugStringA
cmp gs:[rbp+68h], ebp ;LastErrorValue
je being_debugged
```
## Return value

### Windows XP
* If the process is being debugged     ---> EAX will return 0 
* If the process is not being debugged ---> EAX will return 1 

### Windows Vista and above
* If the process is being debugged     ---> EAX will return 1
* If the process is not being debugged ---> EAX will return 0
changes between window Xp and Windows Vista or above are reason of changes in window internal

## Bypass OutputDebugString()
Before dipping into discussing how bypass this technique let us demonstrate ways to detect the debugger

1- check the returned the value in EAX which depending on the Windows version

 
2- GetLastError in Windows XP . If you don't have a Ring3 debugger, you'll get an error message that calls this API after the OutputDebugString. If EAX == 0 --> a debugger has been detected

 
3- Through SEH Works in all Windows Versions from XP and above, not tested in Windows 8.


4- Olly Debugger would fail if you submit a string with the format % s % s % s. (tested up to OllyDbg v1.10).This is just a debugger-specific trick that takes advantage of a bug
and Olly Debugger will crash in this case 

## demo
To bypass this trick, we do not execute code inside this function to prevent detecting the debugger 

### Stage(1)

* we will breakpoint in OutPutDebugStringA() API by pressing ```F2```
* press ```F7``` to enter in code function of OutPutDebugStringA()

![demo1](https://user-images.githubusercontent.com/74544712/115112785-75c24780-9f87-11eb-8738-5b655f7ecc8f.PNG)

### Stage(2)

* AFter entering the code function, my goal is to prevent executing the code inside this API(), so I will assemble frist Instruction yo ``` RET 4 ```
* the value 4 to pop the parameter ``` "%s%s%s%s%s%s%s%s%s%s%s%s%s%s%s%s%s%s%s%" ``` from the stack to prevent the sample detectes the olly with bug 

![demo2](https://user-images.githubusercontent.com/74544712/115112965-29c3d280-9f88-11eb-852e-4e7b27e878cb.PNG)

### Stage(3)

* After executing ```RET 4 ```,  debugger will bypass this trick.

## References 
* parctical malware analysis 
* https://docs.microsoft.com/en-us/windows/win32/api/debugapi/nf-debugapi-outputdebugstringa
* https://www.aldeid.com/wiki/OutputDebugString
* https://www.deepinstinct.com/2017/12/27/common-anti-debugging-techniques-in-the-malware-landscape/

 


