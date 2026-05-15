---
layout: default
title: "Red Team Engage - Initial Access"
permalink: /solo-purple-teaming/red-team-engage-initial-access/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Engage - Initial Access</h1>
</section>
<section class="spt-content">
<h3 id="1-review-of-level-1-attack-path"><strong>1. Review of Level 1
Attack Path</strong></h3>
<p>In Level 1, our attack sequence will follow these stages:</p>
<ol type="1">
<li><strong>Initial Access</strong>
<ul>
<li>Use the <strong>Click-Fix technique</strong> (TTP) via the Windows
Run dialogue.</li>
<li>Execute a PowerShell command that <strong>reflectively
loads</strong> our Stager (Apollo agent) into memory.</li>
</ul></li>
<li><strong>Enumeration</strong>
<ul>
<li>Run our <strong>Service Scan utility</strong> to bypass service
enumeration detections.</li>
<li>The Service Scan is implemented as a custom .NET assembly.</li>
<li>Register the assembly and use <strong>execute-assembly</strong> to
run it in memory.</li>
</ul></li>
<li><strong>Privilege Escalation</strong>
<ul>
<li>Use enumeration results to identify a vulnerable service
binary.</li>
<li>Overwrite the binary and escalate privileges.</li>
</ul></li>
<li><strong>Lateral Movement</strong>
<ul>
<li>Move to the Domain Controller (DC).</li>
<li>Establish a foothold on the DC.</li>
</ul></li>
</ol>
<hr />
<h3 id="2-setting-up-the-new-operation"><strong>2. Setting Up the New
Operation</strong></h3>
<ol type="1">
<li><strong>Close Out the Previous Operation</strong>
<ul>
<li>In Mythic, open the <strong>ecoin attack path level 0</strong>
operation.</li>
<li>Click <strong>Configure/Edit</strong> → toggle <strong>Operation to
Complete</strong> → click <strong>Update</strong>.</li>
<li>Refresh the page and confirm the operation is marked green
(completed).</li>
</ul></li>
<li><strong>Create a New Operation</strong>
<ul>
<li>Name it: <code>ecoin attack path level 1</code>.</li>
<li>Click <strong>Submit</strong>.</li>
<li>Set it as the <strong>current operation</strong>.</li>
<li>Verify the top banner shows
<code>ecoin attack path level 1</code>.</li>
</ul></li>
</ol>
<hr />
<h3 id="3-generating-a-payload"><strong>3. Generating a
Payload</strong></h3>
<ol type="1">
<li><p>In Mythic, click <strong>Payloads</strong> →
<strong>Actions</strong> → <strong>Generate New
Payload</strong>.</p></li>
<li><p>Select <strong>Windows</strong> as the target platform.</p></li>
<li><p>Keep all defaults and click <strong>Next</strong>.</p></li>
<li><p><strong>Commands</strong>: Either move all over now or register
as needed later.</p></li>
<li><p><strong>Transport</strong>: Select <strong>HTTP</strong> and set
the callback address:</p>
http://192.168.100.101
</li>
<li><p>Name the binary: <code>Apollo.exe</code>.</p></li>
<li><p>Click <strong>Create Payload</strong>.</p></li>
<li><p>Download the generated payload.</p></li>
</ol>
<hr />
<h3 id="4-transferring-the-payload-to-the-attack-host"><strong>4.
Transferring the Payload to the Attack Host</strong></h3>
<p>If you generated the payload <strong>on another machine</strong>:</p>
<ol type="1">
<li><p>Open a terminal and navigate to the payload’s folder (e.g.,
<code>Downloads</code>).</p></li>
<li><p>Use <code>scp</code> to transfer to your Kali attacker host:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> Apollo.exe jigglow@192.168.100.101:~/Downloads</span></code></pre></div></li>
<li><p>On your local machine, delete the payload for OPSEC.</p></li>
</ol>
<p>If you generated it <strong>directly on Kali</strong>, this transfer
step is unnecessary.</p>
<hr />
<h3 id="5-preparing-the-payload-for-staging"><strong>5. Preparing the
Payload for Staging</strong></h3>
<ol type="1">
<li><p>SSH into your Kali attacker host:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ssh</span> jigglow@192.168.100.101</span></code></pre></div></li>
<li><p>Navigate to the payload location:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> ~/Downloads</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span> <span class="at">-al</span></span></code></pre></div>
<ul>
<li>Confirm <code>Apollo.exe</code> was modified today.</li>
</ul></li>
<li><p>Start an HTTP server to stage the payload:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> <span class="at">-m</span> http.server 80</span></code></pre></div></li>
</ol>
<hr />
<h3 id="6-executing-the-initial-access"><strong>6. Executing the Initial
Access</strong></h3>
<ol type="1">
<li>On the target (soon-to-be breached host), open the Windows
<strong>Run</strong> dialogue (<code>Win + R</code>).</li>
<li>Your last-tested PowerShell command should already be present
(stored in a registry key).</li>
<li>Click <strong>OK</strong> to execute the command.</li>
<li>In Mythic, go to <strong>Callbacks</strong>.
<ul>
<li>Verify you have a <strong>new callback</strong> indicating
successful initial access.</li>
</ul></li>
</ol>
<hr />
<h3 id="7-verifying-undetected-execution"><strong>7. Verifying
Undetected Execution</strong></h3>
<ol type="1">
<li>In <strong>Wazuh</strong>, open the <strong>Solo Purple Teaming
Dashboard</strong>.</li>
<li>Refresh and check for detections:
<ul>
<li><strong>No events</strong> should be present.</li>
<li>This confirms our initial access was
<strong>undetected</strong>.</li>
</ul></li>
</ol>
<hr />
<h3 id="8-next-steps"><strong>8. Next Steps</strong></h3>
<p>In the following phase, you will:</p>
<ul>
<li>Perform <strong>enumeration</strong> using Service Scan via
<strong>execute-assembly</strong>.</li>
<li>Identify a vulnerable service binary.</li>
<li>Overwrite the binary to escalate privileges.</li>
<li>Obtain a high-integrity callback.</li>
</ul>
<hr />
<p>✅ <strong>End of Level 1 Engage Phase – Initial Access
Setup</strong></p>
</section>
</div>
