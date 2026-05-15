---
layout: default
title: "Privilege Escalation"
permalink: /solo-purple-teaming/privilege-escalation/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Privilege Escalation</h1>
</section>
<section class="spt-content">
<h3 id="1-understanding-privilege-escalation"><strong>1. Understanding
Privilege Escalation</strong></h3>
<ul>
<li><strong>Definition</strong>: Gaining higher permissions than
initially granted (e.g., from standard user to Administrator or
SYSTEM).</li>
<li><strong>Scenario</strong>: We start with <strong>medium integrity
level</strong> as <code>rlynn</code> (Rachel Lynn) and aim to
escalate.</li>
<li><strong>Purpose for Solo Purple Teaming</strong>:
<ul>
<li>Test detection of privilege escalation.</li>
<li>Bridge from initial access to more impactful actions.</li>
<li>Enable lateral movement and persistence.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-types-of-privilege-escalation"><strong>2. Types of Privilege
Escalation</strong></h3>
<ul>
<li><strong>Vertical Escalation</strong>:
<ul>
<li>From standard user → Administrator.</li>
<li>Primary goal: unlock broader control.</li>
</ul></li>
<li><strong>Horizontal Escalation</strong>:
<ul>
<li>Same privilege level, different accounts.</li>
<li>Useful for evasion or alternate data access.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-common-privilege-escalation-techniques"><strong>3. Common
Privilege Escalation Techniques</strong></h3>
<p>We focus on <strong>misconfigured services</strong> for this lab, but
others include:</p>
<ul>
<li><strong>Unquoted service paths</strong></li>
<li><strong>DLL hijacking/sideloading</strong></li>
<li><strong>Insecure token handling</strong> (e.g.,
<code>SeImpersonatePrivilege</code>)</li>
<li><strong>UAC bypass</strong></li>
<li><strong>Credential harvesting</strong> (<code>LSASS</code>, SAM,
DPAPI secrets)</li>
</ul>
<hr />
<h3 id="4-lab-goal"><strong>4. Lab Goal</strong></h3>
<p>We’ll:</p>
<ol type="1">
<li>Install the <strong>Mythic service_wrapper agent</strong>.</li>
<li>Attempt privilege escalation via a <strong>vulnerable
service</strong>.</li>
<li>Discover Defender detection.</li>
<li>Write our <strong>own custom service wrapper</strong> to evade
detection.</li>
</ol>
<hr />
<h3 id="5-installing-service-wrapper-in-mythic"><strong>5. Installing
Service Wrapper in Mythic</strong></h3>
<ol type="1">
<li><p>SSH into Kali host and run these commands:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/mythic/mythic</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic_cli install github https://github.com/MythicAgents/service_wrapper</span></code></pre></div></li>
<li><p>Update if prompted.</p></li>
<li><p>Disconnect from internet to avoid payload signature
submissions.</p></li>
</ol>
<hr />
<h3 id="6-generating-the-payload"><strong>6. Generating the
Payload</strong></h3>
<ol type="1">
<li><strong>In Mythic UI</strong> → Payloads → Generate new
payload.</li>
<li>Agent: <strong>Apollo</strong>, Type: <strong>Shellcode</strong>
(binary format).</li>
<li>Commands: Move all over → Next.</li>
<li>C2 Profile: <strong>HTTP</strong>.</li>
<li>Callback Host: <strong>WAN IP</strong> (e.g.,
<code>192.168.100.101</code>).</li>
<li>Output name: <code>apollo.bin</code>.</li>
</ol>
<hr />
<h3 id="7-creating-the-service-wrapper-payload"><strong>7. Creating the
Service Wrapper Payload</strong></h3>
<ol type="1">
<li>Payload Type: <strong>Windows</strong>, Framework:
<code>.NET 4.0</code>, Arch: <code>x64</code>.</li>
<li>Select the shellcode payload.</li>
<li>Name: <code>apollo_service_wrapped.exe</code>.</li>
<li>Download and stage the payload on the Kali host.</li>
</ol>
<hr />
<h3 id="8-detection-by-defender"><strong>8. Detection by
Defender</strong></h3>
<ul>
<li>Attempting to run <code>apollo_service_wrapped.exe</code> triggers
<strong>Windows Defender detection</strong>.</li>
<li><strong>Reason</strong>: Service wrapper uses suspicious API calls
(<code>VirtualProtect</code>, <code>VirtualUnlock</code>) linked to
shellcode injection.</li>
<li><strong>Next Step</strong>: Build a custom wrapper to avoid
detection.</li>
</ul>
<hr />
<h3 id="9-writing-a-custom-service-wrapper"><strong>9. Writing a Custom
Service Wrapper</strong></h3>
<p><strong>On Reverse Engineering VM:</strong></p>
<ol type="1">
<li><p>Open Visual Studio → New Project → Windows Service (.NET
Framework).</p></li>
<li><p>Name: <code>ecoin_sync</code>.</p></li>
<li><p><strong>In <code>Service1.cs</code></strong>:</p>
<ul>
<li>Add a background thread in <code>OnStart</code>.</li>
<li>Create <code>DoWork()</code> method to:
<ul>
<li>Launch <code>powershell.exe</code>.</li>
<li>Run <code>Apollo.exe</code> from Downloads.</li>
<li>Hide window, redirect output.</li>
<li>Keep service alive with loop.</li>
</ul></li>
</ul></li>
<li><p><strong>OnStop()</strong>:</p>
<ul>
<li>Abort the worker thread if running.</li>
</ul></li>
<li><p>Build solution:</p>
<ul>
<li>Platform: <code>x64</code>, Configuration:
<code>Release</code>.</li>
</ul></li>
<li><p>Stage to Downloads folder on Kali:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> ecoin_sync.exe user@staging_server:/downloads</span></code></pre></div></li>
</ol>
<hr />
<h3 id="10-deploying-the-custom-wrapper"><strong>10. Deploying the
Custom Wrapper</strong></h3>
<ol type="1">
<li>In Mythic, confirm <strong>PowerShell command</strong> is
available.</li>
<li>Use PowerShell <code>Invoke-WebRequest</code> to download
<code>Apollo.exe</code> and <code>ecoin_sync.exe</code> to the target’s
<code>Downloads</code> or <code>Desktop</code>.</li>
<li>Rename existing service binary to preserve it.</li>
<li>Replace with malicious <code>ecoin_sync.exe</code>.</li>
</ol>
<hr />
<h3 id="11-starting-the-service"><strong>11. Starting the
Service</strong></h3>
<ol type="1">
<li><p>Query service status:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="ex">shell</span> sc query ecoin_sync</span></code></pre></div></li>
<li><p>Start service:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="ex">shell</span> sc start ecoin_sync</span></code></pre></div></li>
<li><p><strong>Result</strong>: New Mythic callback appears as
<code>SVC_ecoin_sync</code> with <strong>high integrity</strong>
privileges.</p></li>
</ol>
<hr />
<h3 id="12-verification"><strong>12. Verification</strong></h3>
<ul>
<li>Check <strong>metadata</strong> in Mythic for
<code>Elevation Level = High</code>.</li>
<li>We now have <strong>Administrator-level access</strong>.</li>
</ul>
<hr />
<h3 id="key-takeaways"><strong>Key Takeaways</strong></h3>
<ul>
<li>Privilege escalation is critical for expanding attacker
capabilities.</li>
<li>Defender detections often focus on injection-related API calls.</li>
<li>Custom tools can bypass default detections.</li>
<li>Always test both offensive execution and defensive telemetry.</li>
</ul>
<hr />
<p><strong>Next Lecture Preview</strong>: We’ll set up telemetry
collection with Wazuh and begin building detections for privilege
escalation activity.</p>
</section>
</div>
