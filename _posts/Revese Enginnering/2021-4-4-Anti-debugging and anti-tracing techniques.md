---
title: Anti-debugging 0x01
classes: wide
header:
  teaser: /img/Anti.png
ribbon: MidnightBlue
categories:
  - Revese Enginnering 
toc: true
---
1--> Malware authors use Anti-Reverse Engineering Techniques a lot to impede the reverse engineering process of the malware and malware analyst runs malware samples in debugger to analyze the functionality and behavoir.

2--> the malware sample plays a lot of tricks to recognizes the debuggers that are running with the help of Anti-reverse engineering techniques, when malware recognizes the dubuggers ,it hide the malicious functionality or it may terminate.

<!-- more -->

3--> I will presents several anti-debugging techniques that used on windows NT-base operationg systems.Anti-debugging techniques are ways for a program to detected if it runs under control of a debugger.they are used by commercial executable protector,packers and malicious software to prevent or is slow-down the process of reverse-engineering.

## IsDebuggerPresent

![600px-IsDebuggerPresent-example](https://user-images.githubusercontent.com/74544712/113628587-61b44700-9665-11eb-897c-e9cc80efcb1b.png)



### Description 

[IsDebuggerPresent()](https://docs.microsoft.com/en-us/windows/win32/api/debugapi/nf-debugapi-isdebuggerpresent) is a function available in the kernel32.dll library. This function is often used in malware to complexify the reverse engineering because it will take different paths in the program's flow when the malware is analyzed in a user-mode debugger such as x32dbg and the most widely used anti-debugging method in Windows
### Syntax

```BOOL IsDebuggerPresent();
```
### Return value
IsDebuggerPresent returns 1 if the process is being debugged or returns 0 if the process is not being debugged . This API simply reads the PEB!BeingDebugged byte-flag (located at offset 2 in the PEB structure Circumventing it is as easy as setting PEB!BeingDebugged to 0.
### bypass IsDebuggerPresent with x32dbg 
if you want your application never check it do this:
#### --> stage(1)
* Press ```Alt + e``` or open view and select ```Symbol Info``` modules window.

![جميل](https://user-images.githubusercontent.com/74544712/113626598-a8ed0880-9662-11eb-88b8-318f62bcf6d5.png)

#### --> stage(2)

* Select ```C:\WINDOWS\sysWoW64\kernel32.dll ``` and press ```ctrl + N```.

* select ```IsDebuggerPresent``` and press enter.

* press ```f2```

![modification](https://user-images.githubusercontent.com/74544712/113626714-cc17b800-9662-11eb-872d-5f953da28651.png)

#### --> stage(3) 

* run the program and wait your program break on this op-code.
 
* press some ```f8``` until come back to your code.

* looking up for something like ``` TEST EAX,EAX ``` and after some thing like je jnz and etc, beware the output of IsDebuggerPresent is saved in ```EAX```.

![text2](https://user-images.githubusercontent.com/74544712/113631729-2c5e2800-966a-11eb-8179-cbbc512ef415.png)


#### --> stage(4)

* if jump happen on this op-code change it to ```nop``` and if doesn't happen change it to ```jmp```.

![text3](https://user-images.githubusercontent.com/74544712/113631773-3c760780-966a-11eb-885e-792037610ac3.PNG)


* save your program. if you don't know how to save modifed code in ```x32dbg``` just search it.

#### Reference

* https://docs.microsoft.com/en-us/windows/win32/api/debugapi/nf-debugapi-isdebuggerpresent

* https://stackoverflow.com/questions/10330147/how-do-i-bypass-isdebuggerpresent-with-ollydbg

* https://www.aldeid.com/wiki/IsDebuggerPresent

* Mastering Malware analysis 

* practical Malware analysis  












