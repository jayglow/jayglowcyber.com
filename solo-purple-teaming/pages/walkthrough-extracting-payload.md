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
<h1 id="walkthrough-extracting-payload">Walkthrough: Extracting
Payload</h1>
<h1 id="ioc-diagram">IOC Diagram</h1>
<p><img src="/_assets/walkthrough-extracting-payload/image.png"
alt="image.png" /></p>
<p>We have determined that when we run our malicious PowerShell command
via the run dialog, Explorer.exe spawns a PowerShell process that
reflectively loads apollo.exe. When we use execute_assembly, a
sacrificial process is spawned determined by the setting of spawns_x64
and a CLR loader is injected that reflectively loads the .NET assembly
passed as an argument. In this walkthrough, we set out to prove that SMB
is used as the transport for IPC because we have determined that the
Apollo agent used PipeSecurity which requires the system process to
broker authentication activities.</p>
<h2
id="why-does-the-system-process-connect-to-the-named-pipe-created-by-the-sacrificial-process-instead-of-powershell">Why
does the System process connect to the named pipe created by the
sacrificial process instead of PowerShell?</h2>
<p><strong>Apollo enforces security on its named pipes by setting
explicit access control rules using the Windows PipeSecurity class. It
defines who can create, read, or write to the pipe by adding access
rules for the current user, everyone, and network users. These rules are
applied when the named pipe is created, ensuring that Windows enforces
permissions at the system level. This enforcement happens inside the SMB
driver, which checks client tokens against these rules during
connection. Because named pipe connections with security enforcement are
brokered through the SMB protocol, the Windows System process is
responsible for managing these connections. It acts as the intermediary
that handles authentication, permission checks, and communication
routing. This approach enables secure communication, supports
impersonation, and prevents unauthorized access to Apollo’s
inter-process communication channels.</strong></p>
<h2
id="if-we-review-the-code-within-the-constructor-of-the-asyncnamedpipersever-used-for-ipc-by-execute_assembly-we-can-see-an-instance-of-the-pipesecurity">If
we review the code within the constructor of the AsyncNamedPiperSever,
used for IPC by execute_assembly, we can see an instance of the
PipeSecurity:</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%201.png"
alt="image.png" /></p>
<h2
id="to-prove-our-theory-open-wireshark-on-the-assumed-breach-host-and-select-the-loopback-interface-to-start-capturing-traffic">To
prove our theory, open Wireshark on the assumed breach host and select
the loopback interface to start capturing traffic</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%202.png"
alt="image.png" /></p>
<h2 id="filter-smb--smb2">Filter SMB || SMB2</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%203.png"
alt="image.png" /></p>
<h2 id="use-execute_assembly">Use execute_assembly:</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%204.png"
alt="image.png" /></p>
<h2
id="audit-the-captured-traffic-and-look-for-data-being-written-to-the-pipe">Audit
the captured traffic and look for data being written to the pipe:</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%205.png"
alt="image.png" /></p>
<h2 id="follow-tcp-stream">Follow TCP Stream:</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%206.png"
alt="image.png" /></p>
<h2 id="find-chunk-1-of-7-and-copy-the-base64-data">Find chunk 1 of 7
and copy the base64 data:</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%207.png"
alt="image.png" /></p>
<h2
id="paste-the-base64-data-into-cyberchef-and-use-from-base64-to-get-a-byte-data-output-in-decimal">Paste
the base64 data into CyberChef and use From Base64 to get a byte data
output in decimal</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%208.png"
alt="image.png" /></p>
<h2
id="copy-the-decimal-data-into-another-cyberchef-input-and-use-the-from-decimal-recipe-notice-the-mz-magic-bytes-and-the-dos-stub">Copy
the decimal data into another CyberChef input and use the From Decimal
Recipe. Notice the MZ magic bytes and the DOS stub:</h2>
<p><img src="/_assets/walkthrough-extracting-payload/image%209.png"
alt="image.png" /></p>
<h2 id="repeat-steps-for-all-7-chunks-of-data">Repeat steps for all 7
chunks of data</h2>
</section>
</div>
