---
title: Manual Unpacking part1 
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

# Introduction

Malware is sometimes packed so it is important to unpack the malware to resume The analysis process, packing is very a common technique used by malware author to handle the process of reverse engineering and work of malware analyst
packing helps malware to hide from anti-virus and Firewall and the process of unpacking produces another executable from malware.

let us do our manual unpacking on Maze ransomware with a common technique called Tail jump
<!-- more -->

# Sample

We can download sample from [here](https://app.any.run/tasks/56248422-b327-4226-8a79-3155e24b999d/)

# Tools

1. IDa pro 
2. x32dbg
3. OllyDumpEx
4. Scylla

# Tail jump 

The tail is the instruction that you can find after the stub code and used to reach to the OEP.
1. jump instruction.
2. ret or call instruction.

we can identify the tail jump in IDA Pro and x32dbg and appears as the last instruction.

# Demo 

In this Demo, I will show how to identify tail jump to reach the OEP, dump unpack malware and fix unpack malware 
* I will load the sample in IDA pro to identify the tail jump
* we identify the jump and see the address that malware jump to it after executing stub code to write unpacking malware in this place to execute it without any detection
* I will copy an address of the tail jump so i open x32dbg and press ```ctrl + G ``` to paste the address
* set breakpoint on this address and  press ```f9``` and press step over ```f8``` to reach the OEP 
* I dump the process by using OllyDumpEx
* I open the Scylla to fix import table as you can see in the video
* Now you can load unpack malware in IDA pro, see the huge functions and a lot of strings so you can analyze the malware







