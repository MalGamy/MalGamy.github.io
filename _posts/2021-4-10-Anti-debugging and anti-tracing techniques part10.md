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

https://player.vimeo.com/video/<https://user-images.githubusercontent.com/74544712/117498127-aa6b6280-af79-11eb-9d46-b8596f16f9bb.mp4>


https://user-images.githubusercontent.com/74544712/117498127-aa6b6280-af79-11eb-9d46-b8596f16f9bb.mp4

