---
title: Manual Unpacking 
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

# Introducation

Malware is sometimes packed so it is important to unpacking the malware to resume the process of analysis, packing is very a common technique used by malware author to handle the process of reverse engineering and work of malware analyst and detect by anti-virus and firewalls, the process of unpacking is producing another executable from malware.
<br>
<p align="center">
<img src="https://user-images.githubusercontent.com/74544712/117522272-ac054c80-afb2-11eb-8a6c-86e3ca327724.png" width="80%">
</p>
<br>
let us do our manual unpacking on Maze ransomware with a common technique which called Tail jump
<!-- more -->

# Sample

We can donaload sample from [here](https://app.any.run/tasks/56248422-b327-4226-8a79-3155e24b999d/)

# Tools

1. Detect it Easy (DiE)
2. x32dbg
 
# Tail jump 







<video width="320" height="240" controls>
  <source type="video/mp4" src="https://user-images.githubusercontent.com/74544712/117524672-9c8c0080-afbe-11eb-9721-8317ad5ef543.mp4">
</video>




