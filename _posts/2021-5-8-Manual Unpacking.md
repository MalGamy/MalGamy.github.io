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



Malware is sometimes packed so it is important to unpacking the malware to resume the process of analysis, packing is very a common technique used by malware author to handle the
process of reverse engineering and work of malware analyst and detect by anti-virus and firewalls, the process of unpacking is producing another executable from malware.
let us do our manual unpacking on Maze ransomware with a common technique that is called Tail jump
<!-- more -->
# Tail jump 

