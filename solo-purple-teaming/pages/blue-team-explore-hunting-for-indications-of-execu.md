---
layout: default
title: "Blue Team Explore Hunting For Indications of execu"
permalink: /solo-purple-teaming/blue-team-explore-hunting-for-indications-of-execu/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore Hunting For Indications of execu</h1>
</section>
<section class="spt-content">
<h1
id="blue-team-explore-hunting-for-indications-of-execute_assembly">Blue
Team Explore: Hunting For Indications of execute_assembly</h1>
<p>Owner: Mike Sterrett</p>
<h2 id="introduction">Introduction</h2>
<p>In this exercise, we’ll explore telemetry to detect:</p>
<ul>
<li><strong>Process Injection</strong></li>
<li><strong>Inter-Process Communications (IPC)</strong></li>
<li><strong>C2 (Command &amp; Control) Communications</strong></li>
</ul>
<p>We’ll use Mythic, Wazuh, and PowerPoint for diagramming our
findings.</p>
<hr />
<h3 id="1-initial-setup"><strong>1. Initial Setup</strong></h3>
<ol type="1">
<li><strong>Log into the assumed breach host</strong> in the lab.</li>
<li><strong>Execute Initial Access Payload</strong>:
<ul>
<li>Open the <strong>Run</strong> dialog.</li>
<li>The last command should be pre-filled; click <strong>OK</strong> to
execute it.</li>
<li>Confirm you get a callback in Mythic.</li>
</ul></li>
<li><strong>Set Tab Description</strong>:
<ul>
<li>In Mythic, set the tab description to
<code>Blue Team Explore</code>.</li>
</ul></li>
<li><strong>Switch to Reverse Engineering Host</strong>:
<ul>
<li>Register the <strong>service_scan_demo.exe</strong> assembly in
Mythic using the modal dialog.</li>
<li>Task the agent to load it.</li>
</ul></li>
</ol>
<hr />
<h3 id="2-configure-injection-technique"><strong>2. Configure Injection
Technique</strong></h3>
<ol type="1">
<li>In Mythic, run:
<ul>
<li><code>get injection techniques</code></li>
<li>Set the technique to <code>syscallx64 NtCreateThreadx</code>.</li>
</ul></li>
<li>Set the spawn process to <code>smartscreen.exe</code> using the
modal dialog.</li>
<li>Execute <code>service_scan_demo.exe</code> and wait for
results.</li>
</ol>
<hr />
<h3 id="3-reviewing-execute-assembly-documentation"><strong>3. Reviewing
Execute Assembly Documentation</strong></h3>
<ol type="1">
<li>In Mythic:
<ul>
<li>View <strong>metadata</strong> for
<code>execute assembly</code>.</li>
<li>This confirms it:
<ul>
<li>Uses the .NET CLR loader.</li>
<li>Injects into a <strong>sacrificial process</strong> defined by
<code>spawn_to_x64</code>.</li>
<li>Indicates <strong>process injection</strong> into
<code>smartscreen.exe</code>.</li>
</ul></li>
</ul></li>
<li><strong>Conclusion</strong>: The original PowerShell process will
open a handle to <code>smartscreen.exe</code>.</li>
</ol>
<hr />
<h3 id="4-hunting-in-wazuh-for-process-injection"><strong>4. Hunting in
Wazuh for Process Injection</strong></h3>
<ol type="1">
<li><p>Go to <strong>Explore → Discover</strong> in Wazuh.</p></li>
<li><p>Change the index pattern to <code>wazuh-archives</code>.</p></li>
<li><p>Search for <strong>Sysmon Event ID 10</strong> (process access
events).</p></li>
<li><p>Use Boolean AND conditions:</p>
<ul>
<li><strong>Source Image</strong>: <code>powershell.exe</code></li>
<li><strong>Target Image</strong>: <code>smartscreen.exe</code></li>
</ul></li>
<li><p>Fix case sensitivity issues:</p>
<ul>
<li><code>eventdata</code> field is lowercase, but
<code>sourceImage</code> is camel case.</li>
</ul></li>
<li><p><strong>Key IOC Found</strong>:</p>
<p>PowerShell accessed SmartScreen with <strong>Granted Access =
0x1ffff</strong> (full process rights).</p></li>
</ol>
<hr />
<h3 id="5-diagramming-iocs"><strong>5. Diagramming IOCs</strong></h3>
<ul>
<li>Use <strong>PowerPoint</strong> for IOC diagrams.</li>
<li>Add:
<ul>
<li>PowerShell process details (image name, process GUID in red).</li>
<li>C2 communication icon.</li>
<li>Granted access details for process injection.</li>
</ul></li>
</ul>
<hr />
<h3 id="6-identifying-c2-communications"><strong>6. Identifying C2
Communications</strong></h3>
<ol type="1">
<li>Search by <strong>Process GUID</strong> for the PowerShell
process.</li>
<li>Identify network events:
<ul>
<li><strong>Event ID 3</strong> (Network Connect).</li>
<li>Destination IP: <code>192.168.100.101</code> (attack
infrastructure).</li>
<li>Destination Port: <code>80</code>.</li>
</ul></li>
<li>Add to diagram:
<ul>
<li>Beaconing process with port details.</li>
</ul></li>
</ol>
<hr />
<h3 id="7-investigating-target-process-smartscreenexe"><strong>7.
Investigating Target Process (smartscreen.exe)</strong></h3>
<ol type="1">
<li>Search for events linked to <strong>SmartScreen’s Process
GUID</strong>.</li>
<li>Identify:
<ul>
<li><strong>Pipe creation</strong> (Event ID 17) → Named Pipe with a
unique GUID.</li>
<li><strong>Pipe connection</strong> (Event ID 18) → Unexpectedly
connected by <code>System</code> process.</li>
</ul></li>
<li>Add pipe creation &amp; connection to diagram (mark pipe name in
red).</li>
</ol>
<hr />
<h3 id="8-loaded-modules-analysis"><strong>8. Loaded Modules
Analysis</strong></h3>
<ol type="1">
<li>Search for <strong>Image Load Events</strong> for SmartScreen
process.</li>
<li>Identify .NET DLLs:
<ul>
<li><code>System.Management.ni.dll</code></li>
<li><code>System.Core.dll</code></li>
<li><code>System.Xml.dll</code></li>
<li>Others.</li>
</ul></li>
<li><strong>Hypothesis</strong>: These may be loaded because our .NET
assembly was injected.</li>
</ol>
<hr />
<h3 id="9-key-takeaways"><strong>9. Key Takeaways</strong></h3>
<ul>
<li><strong>Process Injection IOC</strong>:
<ul>
<li>PowerShell accessing SmartScreen with <code>0x1ffff</code>
rights.</li>
</ul></li>
<li><strong>IPC IOC</strong>:
<ul>
<li>Named pipe creation &amp; connection events.</li>
</ul></li>
<li><strong>C2 IOC</strong>:
<ul>
<li>Outbound connections to attacker IP on port 80.</li>
</ul></li>
<li><strong>Possible Anomaly</strong>:
<ul>
<li>SmartScreen loading multiple .NET DLLs.</li>
</ul></li>
</ul>
<hr />
<h3 id="10-next-steps"><strong>10. Next Steps</strong></h3>
<ul>
<li>Before next lecture:
<ul>
<li>Complete your IOC diagram.</li>
<li>Map process injection, pipe events, and C2 communications.</li>
</ul></li>
<li><strong>Next Lab</strong>:
<ul>
<li>Enable Sysmon Event IDs 12, 13, and 14 (registry events).</li>
<li>Hunt for malicious registry changes linked to Attack Path Level
One.</li>
</ul></li>
</ul>
</section>
</div>
