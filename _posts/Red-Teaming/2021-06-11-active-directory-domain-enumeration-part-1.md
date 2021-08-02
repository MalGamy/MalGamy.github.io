---
title:  "Active Directory Domain Enumeration Part-1 With Powerview"
classes: wide
header:
  teaser: /img/redteampng.png
ribbon: red
description: "enumerating the Domain,users, groups, Domain controller ,computers and local groups"
categories:
  - Red-Teaming
toc: true
---

Enumeration is the process of extracting information from the Active Directory like enumerating the users, groups, some interesting fields and resources.

## Active Directory
 * Directory Service created by Microsoft
 * Used to manage Domains in a Windows Environment
 * Centralized Management of users and computers
 * Handles all authentication and authorization 

used in
 * Enterprise environments use this to manage Windows based networks
 * Commonly used for users work stations and Windows servers
 * Centralized Management 


## Install PowerView
  PowerView is a PowerShell tool to gain network situational awareness on Windows domains. It contains a set of pure-PowerShell replacements for various windows "net *"           commands, which utilize PowerShell AD hooks and underlying Win32 API functions to perform useful Windows domain functionality.
  Several functions for the enumeration and abuse of domain trusts also exist
  Download script
 - https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerView/powerview.ps1

```
. ./powerview.ps1
```

<img src="/img/adpart1/1.png" alt="Getting-gz" width="800" height="110"> 

we notice that Powerview detected 

disable protection
```
Set-MpPreference -DisableRealtimeMonitoring $true
```


## Domain
Domains are a hierarchical way of organizing users and computers that work together on the same network

Get Current Domain
```
 Get-Domain
```

<img src="/img/adpart1/2.PNG" alt="Getting-gz" width="600" height="150"> 

Enumerate Other Domains
```
Get-Domain -Domain <DomainName>
```

<img src="/img/adpart1/3.PNG" alt="Getting-gz" width="600" height="150"> 

Get Domain SID
```
Get-DomainSID
```

<img src="/img/adpart1/4.PNG" alt="Getting-gz" width="600" height="100"> 

## Domain Policy
A domain security policy :
      is a security policy that is specifically applied to a given domain or set of computers or drives in a given system. System administrators use a domain security policy to       set security protocols for part of a network, including password protocols, access levels and much more
Get Domain Policy
```
Get-DomainPolicy
```

<img src="/img/adpart1/5.PNG" alt="Getting-gz" width="800" height="300"> 


policy configurations of the Domain about system access
```
(Get-DomainPolicy)."SystemAccess"
```

<img src="/img/adpart1/6.PNG" alt="Getting-gz" width="600" height="200"> 



policy configurations of the Domain about  kerberos
```
 (Get-DomainPolicy)."kerberospolicy"
```

<img src="/img/adpart1/7.PNG" alt="Getting-gz" width="600" height="200"> 

## Domain Controllers
A domain controller is a server that responds to authentication requests and verifies users on computer networks, keeps all of that data organized and secured
```
 Get-DomainController 
 ```
 
<img src="/img/adpart1/8.PNG" alt="Getting-gz" width="800" height="300"> 
 
```
Get-DomainController -Domain <DomainName>
```
  
  <img src="/img/adpart1/9.PNG" alt="Getting-gz" width="800" height="200"> 
  
  
##  Domain Users
A domain user is one whose username and password are stored on a domain controller rather than the computer the user is logging into. When you log in as a domain user, the computer asks the domain controller what privileges are assigned to you. When the computer receives an appropriate response from the domain controller, it logs you in with the proper permissions and restrictions.

Get Domain User 
```
Get-DomainUser 
```
 
<img src="/img/adpart1/10.PNG" alt="Getting-gz" width="800" height="200"> 

```
Get-DomainUser | select cn
```

<img src="/img/adpart1/11.PNG" alt="Getting-gz" width="600" height="100"> 

list of all properities for user
```
Get-DomainUser -Identity <username>
```

<img src="/img/adpart1/12.PNG" alt="Getting-gz" width="800" height="200"> 

properties of a specific user
```
Get-DomainUser -Identity <username> -Properties DisplayName, MemberOf,objectsid,useraccountcontrol | Format-List
```

<img src="/img/adpart1/13.PNG" alt="Getting-gz" width="800" height="200"> 


user logged on a machine
 ```
 Get-NetLoggedon -ComputerName <computer-name>
 ```
 
 <img src="/img/adpart1/14.PNG" alt="Getting-gz" width="600" height="150"> 

## Domain Computers
Get alist of computers in the current domain
```
Get-NetComputer| select name
```

<img src="/img/adpart1/15.PNG" alt="Getting-gz" width="600" height="150"> 

```
Get-NetComputer -OperatingSystem "*Server 2016*" | select name ,operatingsystem |Format-List
```
<img src="/img/adpart1/16.PNG" alt="Getting-gz" width="800" height="200"> 


## Groups 
groups are a collection of Active Directory objects. The group can include users, computers, other groups, and other AD objects

Get all groups in the current domain
```
Get-NetGroup | select name
```

<img src="/img/adpart1/17.PNG" alt="Getting-gz" width="600" height="200">

Get all groups in the target domain
```
Get-NetGroup -Domain <targetdomain> | select name
```

<img src="/img/adpart1/18.PNG" alt="Getting-gz" width="600" height="200">


All data about the specific group
```
Get-NetGroup 'Domain Admins'
```

<img src="/img/adpart1/19.PNG" alt="Getting-gz" width="800" height="200">

 grep any group contain admin
 ``` 
 Get-NetGroup "*admin*"| select name 
 ```
 
 <img src="/img/adpart1/20.PNG" alt="Getting-gz" width="800" height="200">

## Local groups
Local groups on the local (or remote) machine.Requires local admin rights on the remote machine
Local Admin Rights:
Giving a user Local Admin Rights means giving them full control over the local computer.  (Please note that this DOES NOT give them any extra rights to anything on the network). A user with Local Admin Rights can do the following:
    Add and Remove Software,Printers,etc.
    Change computer settings like network configuration, power settings, etc.
```
Get-NetLocalGroup | Select-Object GroupName
```

<img src="/img/adpart1/21.PNG" alt="Getting-gz" width="700" height="150">

members of a specific local group on the local (or remote) machine. Also requires local admin rights on the remote machine
```
Get-NetLocalGroupMember -GroupName Administrators | Select-Object MemberName, IsGroup, IsDomain
```

<img src="/img/adpart1/22.PNG" alt="Getting-gz" width="800" height="200">


Get all members of the domain admin group
```
Get-NetGroupMember -MemberName "domain admins" -Recurse | select MemberName
```

<img src="/img/adpart1/23.PNG" alt="Getting-gz" width="800" height="200">


Get the group membership for a user
```
Get-NetGroup -UserName <"username">| select name
```

<img src="/img/adpart1/24.PNG" alt="Getting-gz" width="800" height="150">


List all the local group on a machine
```
Get-NetLocalGroup 
```

<img src="/img/adpart1/25.PNG" alt="Getting-gz" width="800" height="200">


List all the local group on a target machine
```
Get-NetLocalGroup -ComputerName <computername>
```

<img src="/img/adpart1/26.PNG" alt="Getting-gz" width="800" height="200">


Get activity logged user on a computer (need local admin right)
```
Get-NetLoggedon -ComputerName DomainAD.karim.net
 ```
 
<img src="/img/adpart1/27.PNG" alt="Getting-gz" width="600" height="150">

Get the last logged on the computer(need administrative right and remote register on the target)
```
Get-LastLoggedOn -ComputerName DomainAD.karim.net
```

<img src="/img/adpart1/28.PNG" alt="Getting-gz" width="700" height="150">

I finished part 1 today waite me in the next part.








