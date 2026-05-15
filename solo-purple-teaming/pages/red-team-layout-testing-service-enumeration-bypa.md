---
layout: default
title: "Red Team Layout - Testing Service Enumeration Bypa"
permalink: /solo-purple-teaming/red-team-layout-testing-service-enumeration-bypa/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Testing Service Enumeration Bypa</h1>
</section>
<section class="spt-content">
<h1 id="red-team-layout---testing-service-enumeration-bypass">Red Team
Layout - Testing Service Enumeration Bypass</h1>
<p>Owner: Mike Sterrett</p>
<h2 id="1-objective"><strong>1. Objective</strong></h2>
<p>In this lab, you’ll learn how to:</p>
<ul>
<li>Register and execute a .NET assembly (our custom
<code>service_scan_demo.exe</code>) in Apollo.</li>
<li>Understand how attackers stage tools in memory.</li>
<li>Experiment with injection techniques to evade Windows Defender.</li>
<li>Successfully run the service scan utility without detection.</li>
</ul>
<hr />
<h3 id="2-key-concepts"><strong>2. Key Concepts</strong></h3>
<h3 id="register-assembly"><strong>Register Assembly</strong></h3>
<ul>
<li><strong>Purpose:</strong> Loads a .NET assembly into the agent’s
memory for later execution.</li>
<li><strong>Important:</strong> <em>Does not execute the binary</em>—it
stages it.</li>
<li><strong>Why it matters:</strong> Mirrors attacker behavior where
tools are loaded once and executed when needed, minimizing
footprint.</li>
</ul>
<h3 id="execute-assembly"><strong>Execute Assembly</strong></h3>
<ul>
<li><strong>Purpose:</strong> Executes a previously registered .NET
assembly in memory.</li>
<li><strong>Process:</strong> Spawns a process (default:
<code>rundll32.exe</code>), injects the assembly, and runs it
filelessly.</li>
<li><strong>Footprint:</strong> Still leaves process-related telemetry
(child processes, command lines, suspicious binaries).</li>
</ul>
<hr />
<h3 id="3-dynamic-injection-techniques-in-apollo"><strong>3. Dynamic
Injection Techniques in Apollo</strong></h3>
<p>Apollo supports three injection methods:</p>
<ol type="1">
<li><strong>CreateRemoteThread</strong> – Classic and reliable, but
noisy.</li>
<li><strong>Early Bird QueueUserAPC</strong> – Stealthier; injects into
a suspended process before its main thread starts.</li>
<li><strong>Syscalls NTCreateThreadEx</strong> – Lower-level API; can
evade EDRs focused on higher-level calls.</li>
</ol>
<p><strong>Why experiment?</strong></p>
<p>Switching methods helps determine which are caught and which slip
past defenses. This builds both offensive and defensive awareness.</p>
<hr />
<h3 id="4-lab-steps"><strong>4. Lab Steps</strong></h3>
<h3 id="step-1--initial-access-via-run-box"><strong>Step 1 – Initial
Access via Run Box</strong></h3>
<ol type="1">
<li>Open <strong>Run</strong> (<code>Win + R</code>).</li>
<li>Paste and execute your PowerShell loader command.</li>
<li>Ensure your <strong>staging server</strong> is running so Apollo can
be fetched and executed.</li>
<li>Verify the callback appears in Mythic.</li>
</ol>
<hr />
<h3 id="step-2--register-the-service-scan-assembly"><strong>Step 2 –
Register the Service Scan Assembly</strong></h3>
<ol type="1">
<li><p>In Mythic, select your callback.</p></li>
<li><p>Run:</p>
<pre><code>register_assembly
</code></pre>
<p>This opens a file selector.</p></li>
<li><p>Browse to and select <code>service_scan_demo.exe</code>.</p></li>
<li><p>Task the agent to load it into memory.</p></li>
</ol>
<hr />
<h3 id="step-3--execute-the-assembly"><strong>Step 3 – Execute the
Assembly</strong></h3>
<ol type="1">
<li><p>Check usage:</p>
<pre><code>help execute_assembly
</code></pre></li>
<li><p>Run:</p>
<pre><code>execute_assembly service_scan_demo.exe
</code></pre></li>
<li><p><strong>Problem:</strong> No results returned—likely due to
<strong>Windows Defender detection</strong>.</p></li>
</ol>
<hr />
<h3 id="step-4--confirm-defender-detection"><strong>Step 4 – Confirm
Defender Detection</strong></h3>
<ol type="1">
<li>Re-run <code>execute_assembly</code> and monitor for Defender
alerts.</li>
<li>Verify Defender is catching and blocking the execution.</li>
</ol>
<hr />
<h3 id="step-5--test-alternate-injection-techniques"><strong>Step 5 –
Test Alternate Injection Techniques</strong></h3>
<ol type="1">
<li><p>Get available methods:</p>
<pre><code>get_injection_techniques
</code></pre></li>
<li><p>Try:</p>
<pre><code>set_injection_technique early_bird_queuuserapc
execute_assembly service_scan_demo.exe
</code></pre></li>
<li><p>If still detected, switch to:</p>
<pre><code>set_injection_technique syscall_x64.ntcreatethreadex
</code></pre></li>
<li><p>Run <code>execute_assembly</code> again.</p></li>
</ol>
<p><strong>Result:</strong> Still detected—so injection method alone is
insufficient.</p>
<hr />
<h3 id="step-6--change-spawn-to-process"><strong>Step 6 – Change
Spawn-To Process</strong></h3>
<ol type="1">
<li><p>Default spawn target: <code>rundll32.exe</code> (<em>commonly
detected by Defender</em>).</p></li>
<li><p>Change it:</p>
<pre><code>spawn_to_x64
</code></pre>
<p>Use the file picker to choose:</p>
<pre><code>C:\Windows\System32\smartscreen.exe
</code></pre></li>
<li><p>Task the agent.</p></li>
</ol>
<hr />
<h3 id="step-7--execute-with-new-spawn-to"><strong>Step 7 – Execute with
New Spawn-To</strong></h3>
<ol type="1">
<li><p>Run:</p>
<pre><code>execute_assembly service_scan_demo.exe
</code></pre></li>
<li><p><strong>Expected Outcome:</strong></p>
<ul>
<li>No Defender alert.</li>
<li>Results returned (e.g., <code>EcoinSync</code> service modifiable by
<code>rlin</code>).</li>
</ul></li>
</ol>
<hr />
<h3 id="step-8--validate-in-wazuh"><strong>Step 8 – Validate in
Wazuh</strong></h3>
<ol type="1">
<li>Refresh your Wazuh dashboard.</li>
<li>Confirm no detections triggered during execution.</li>
</ol>
<hr />
<h3 id="5-lessons-learned"><strong>5. Lessons Learned</strong></h3>
<ul>
<li><strong>Staging vs Execution:</strong> Always register first, then
execute.</li>
<li><strong>Defender Evasion:</strong> Injection technique changes alone
may fail—changing the spawn target can help.</li>
<li><strong>Detection Planning:</strong>
<ul>
<li>Execute Assembly uses named pipes and process injection.</li>
<li>Defenders should monitor for:
<ul>
<li><code>Pipe Create</code> and <code>Pipe Connect</code> events.</li>
<li><code>Process Access</code> events to the spawn target.</li>
<li>Unusual process-child relationships.</li>
</ul></li>
</ul></li>
</ul>
<hr />
<h3 id="6-next-steps"><strong>6. Next Steps</strong></h3>
<p>In the next lab, you will:</p>
<ul>
<li>Add the <code>SVC_EcoinSync</code> account to the <strong>Domain
Admins</strong> group (for lab purposes).</li>
<li>Perform lateral movement to the Domain Controller.</li>
<li>Execute the <strong>full Attack Path Level 1</strong> from start to
finish.</li>
</ul>
</section>
</div>
