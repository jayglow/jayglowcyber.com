---
layout: default
title: "Walkthrough Extracting Payload"
permalink: /solo-purple-teaming/walkthrough-extracting-payload/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Walkthrough Extracting Payload</h1>
</section>
<section class="spt-content">

# Walkthrough: Extracting Payload

Owner: Josh

# IOC Diagram

![image.png](Walkthrough%20Extracting%20Payload/image.png)

We have determined that when we run our malicious PowerShell command via the run dialog, Explorer.exe spawns a PowerShell process that reflectively loads apollo.exe. When we use execute_assembly, a sacrificial process is spawned determined by the setting of spawns_x64 and a CLR loader is injected that reflectively loads the .NET assembly passed as an argument. In this walkthrough,  we set out to prove that SMB is used as the transport for IPC because we have determined that the Apollo agent used PipeSecurity which requires the system process to broker authentication activities. 

## Why does the System process connect to the named pipe created by the sacrificial process instead of PowerShell?

**Apollo enforces security on its named pipes by setting explicit access control rules using the Windows PipeSecurity class. It defines who can create, read, or write to the pipe by adding access rules for the current user, everyone, and network users. These rules are applied when the named pipe is created, ensuring that Windows enforces permissions at the system level. This enforcement happens inside the SMB driver, which checks client tokens against these rules during connection. Because named pipe connections with security enforcement are brokered through the SMB protocol, the Windows System process is responsible for managing these connections. It acts as the intermediary that handles authentication, permission checks, and communication routing. This approach enables secure communication, supports impersonation, and prevents unauthorized access to Apollo’s inter-process communication channels.**

## If we review the code within the constructor of the AsyncNamedPiperSever, used for IPC by execute_assembly, we can see an instance of the PipeSecurity:

![image.png](Walkthrough%20Extracting%20Payload/image%201.png)

## To prove our theory, open Wireshark on the assumed breach host and select the loopback interface to start capturing traffic

![image.png](Walkthrough%20Extracting%20Payload/image%202.png)

## Filter SMB || SMB2

![image.png](Walkthrough%20Extracting%20Payload/image%203.png)

## Use execute_assembly:

![image.png](Walkthrough%20Extracting%20Payload/image%204.png)

## Audit the captured traffic and look for data being written to the pipe:

![image.png](Walkthrough%20Extracting%20Payload/image%205.png)

## Follow TCP Stream:

![image.png](Walkthrough%20Extracting%20Payload/image%206.png)

## Find chunk 1 of 7 and copy the base64 data:

![image.png](Walkthrough%20Extracting%20Payload/image%207.png)

## Paste the base64 data into CyberChef and use From Base64 to get a byte data output in decimal

![image.png](Walkthrough%20Extracting%20Payload/image%208.png)

## Copy the decimal data into another CyberChef input and use the From Decimal Recipe. Notice the MZ magic bytes and the DOS stub:

![image.png](Walkthrough%20Extracting%20Payload/image%209.png)

## Repeat steps for all 7 chunks of data

</section>
</div>
