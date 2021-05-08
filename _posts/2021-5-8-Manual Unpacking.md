---
title:  
layout: single
comments: true
share: true
related: true
author_profile: true
permalink: "/:title/"
tags:
- malware analysis 
- unpacking 
- Maze ransomware 
categories:
- Articles
date: '2021-5-8 10-00-00 +0000'
toc: true
toc_label: Table of Contents
toc_sticky: true
---
<p align="center">
<img src="https://user-images.githubusercontent.com/74544712/117522272-ac054c80-afb2-11eb-8a6c-86e3ca327724.png" width="100%">
</p>

# Introducation

Malware is sometimes packed so it is important to unpacking the malware to resume the process of analysis, packing is very a common technique used by malware author to handle the process of reverse engineering and work of malware analyst and detect by anti-virus and firewalls, the process of unpacking is producing another executable from malware.

let us do our manual unpacking on Maze ransomware with a common technique called Tail jump
<!-- more -->

# Sample

We can donaload sample from [here](https://app.any.run/tasks/56248422-b327-4226-8a79-3155e24b999d/)

# Tools

1. IDa pro 
2. x32dbg
3. OllyDumpEx
4. Scylla

# Tail jump 

The tail is the instruction that you can find after the stub code and used to reach to the OEP 
1. jump instruction
2. ret or call instruction
we can identify the tail jump in IDA Pro and x32dbg and appears as the last instruction

# Demo 

In this Demo, I will show how to identify tial jump to reach the OEP, dump the unpacking malware and fix the unpacking malware 
1. I will load the sample in IDA pro to identify the tail jump
2. we identify the jump and see the address that malware jump to it after executing stub code to write unpacking malware in this place to execute it without any detection
3. I will copy an address of the tail jump to go to X36dbg
<video width="650" height="750" controls>
  <source type="video/mp4" src="https://user-images.githubusercontent.com/74544712/117524672-9c8c0080-afbe-11eb-9721-8317ad5ef543.mp4">
</video>

7.set breakpoint on this address and  press ```f9``` and press step over ```f8``` to reach the OEP 
5. I dump the process by using OllyDumpEx
6. I open the Scylla to fix import table as you can see in the video
7. Now you can load unpacking malware in IDA pro, see the huge functions and huge strings so you can analyze the malware






