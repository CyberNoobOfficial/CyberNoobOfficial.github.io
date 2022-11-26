---
layout: post
title: Powershell Networking Is Not That Hard
published: true
---


[> The .NET Framework consists of classes that arrange into namespaces. The classes in a particular namespace are usually arranged in a particular theme such as security, XML, input and output, directory services, protocols, and other things.](https://medium.com/r/?url=https%3A%2F%2Fdevblogs.microsoft.com%2Fscripting%2Fuse-net-framework-classes-to-explore-windows-powershell-processes%2F)

Today’s namespace is Net which provides a simple programming interface for many protocols. We will examine some basic classes under this namespace such as DNS, WebRequest, WebResponse, Sockets, and HTTP-specific classes.

## DNS

> DNS - Is a static class that retrieves information about a specific host from the Internet Domain Name System (DNS).[https://medium.com/r/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fdotnet%2Fapi%2Fsystem.net.dns%3Fview%3Dnet-5.0](https://medium.com/r/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fdotnet%2Fapi%2Fsystem.net.dns%3Fview%3Dnet-5.0)

To call a class inside the System Namespace, you need to put its name inside square brackets as such -

```
PS C:\> [System.Net.Dns]
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     False    Dns                                      System.Object
```
