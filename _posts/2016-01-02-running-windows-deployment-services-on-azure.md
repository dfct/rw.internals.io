---
layout: post
title: "Running Windows Deployment Services on Azure"
tags:
- winpe
- azure
- wds
- portmapper
---


This is another draft I had sitting around for ages. Far from complete as a piece of writing, but worth posting as is in case anyone else has a crazy plan for WDS and Azure.

- - - -

I decided one day that I wanted to set up Windows Deployment Services. I've done a lot of work with WIMs and Windows PE, but I hadn't played with Windows Deployment Services. Like many of my projects, I wanted to set up WDS on Azure.

Now WDS's [documentation](http://technet.microsoft.com/en-us/library/hh831764.aspx) pretty explicitly states that this is a bad plan:

> ! Warning  
><br/>
> WDS cannot be run on a virtual machine in Windows Azure.  

But it doesn't mention _why_. So it feels like there is wiggle room. And clearly in support of this, you can provision a Windows Server VM, install the WDS role, and add install images without a single error!

Though things mostly tank from there. :)

### How to Run WDS on Azure

The trick to--also frameable as "the problem with"--running WDS on Azure is reaching the server. Anything running on Azure is behind a lot of networking infrastructure, you need to explicitly open endpoints to reach the VM through. Furthermore, the networking pieces of WDS do not travel across the internet well. Residential ISPs block many of these ports as intentional firewalling, I would guess to combat malware & worms of old.

There is [WDS documentation](https://technet.microsoft.com/en-us/library/cc732918(v=ws.10)) describing which ports WDS, but in practice I found this list to be incomplete. Here's a more thorough list:

```
UDP 67 - DHCP & PXE BootP Server
UDP 68 - DHCP & PXE BootP Client 
UDP 69 - Trivial File Transfer Protocol (TFTP)
TCP 135 - RPC EndPoint Mapper
UDP 137 - NetBIOS Name Service (name registration & resolution)
UDP 138 - NetBIOS Datagram Service
TCP 139 - NetBIOS Session Service
TCP 445 - SMB File Sharing and Authentication
UDP 500 - Internet Security Association and Key Management Protocol (ISAKMP)
UDP 4011 - PXE Server ProxyDHCP port
UDP 4500 - IPSec NAT Traversal
TCP 5040 - Windows Deployment Services server RPC Listener Port
UDP 5041 - Multicast Session Initiation Port
UDP 64001 ∨
[.....] - Multicasting
UDP 65000 ∧
```

A typical WDS session may go like this:

1. Machine tries to network boot
2. Machine establishes a connection with DHCP server
3. DHCP server points machine to PXE server (WDS)
4. WDS delivers network boot program
5. Machine downloads a Windows PE image
6. Machine boots to PE and connects to WDS
7. Imaging can take place
See also: [http://networkboot.org/fundamentals/](http://networkboot.org/fundamentals/)

In my case, I was building out a USB boot drive with a PE environment on it. I modified the PE boot script to automatically try to connect to a WDS server at 'wds.internals.io'. To get things working despite the ports ISPs were blocking, I opened the following ports on Azure as one half of a redirection scheme: 22 public / 135 private, 80 public / 139 private, 443 public / 445 private. Then, using [my portmapper program](https://github.com/dfct/PortMapper) running in the WinPE environment on the client machine, I intercepted and remapped port 135 -> 22, port 139 -> 80, and port 445 -> 443. This circumvented the blocked ports, and WDS on Azure worked over the internets :)
