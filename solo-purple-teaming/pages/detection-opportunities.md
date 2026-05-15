---
layout: default
title: "Detection Opportunities"
permalink: /solo-purple-teaming/detection-opportunities/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Detection Opportunities</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3 id="1-purpose-of-the-lecture">1. <strong>Purpose of the
Lecture</strong></h3>
<ul>
<li>This session is <strong>foundational</strong> for becoming highly
effective at <strong>red teaming</strong> or <strong>blue
teaming</strong>.</li>
<li>The concepts here help you excel in:
<ul>
<li><strong>Offense</strong> – as a Red Team Operator.</li>
<li><strong>Defense</strong> – as a Detection Engineer.</li>
</ul></li>
<li>In <strong>solo purple teaming</strong>, you must operate
<strong>self-sufficiently</strong> without relying on a separate team to
share intelligence.</li>
</ul>
<hr />
<h3 id="2-the-solo-purple-teaming-context">2. <strong>The Solo Purple
Teaming Context</strong></h3>
<ul>
<li>Since you control the simulation:
<ul>
<li>You <strong>know the attack path</strong>.</li>
<li>You know <strong>every command, payload, and technique</strong>
used.</li>
</ul></li>
<li>Tools like <strong>Mythic</strong> store <strong>full task
history</strong>, letting you:
<ul>
<li>Retrace each action.</li>
<li>Compare it against collected telemetry.</li>
</ul></li>
<li><strong>Primary task</strong>: Identify <strong>telemetry
gaps</strong> — where visibility breaks down.</li>
</ul>
<hr />
<h3 id="3-telemetry-verification-process">3. <strong>Telemetry
Verification Process</strong></h3>
<ul>
<li><strong>Check native Windows Event Logs</strong>:
<ul>
<li>May require <strong>enabling extra audit settings</strong>.</li>
</ul></li>
<li><strong>Review Sysmon configuration</strong>:
<ul>
<li>Ensure it is tuned for <strong>events you care about</strong>.</li>
</ul></li>
<li><strong>Evaluate EDR logs</strong>:
<ul>
<li>Verify if events are recorded and easily searchable.</li>
</ul></li>
<li><strong>When in doubt</strong>:
<ul>
<li>Research what events a technique <em>should</em> produce.</li>
<li>Compare with what is actually being collected.</li>
</ul></li>
<li><strong>Cycle for improvement</strong>:
<ol type="1">
<li>Simulate</li>
<li>Verify</li>
<li>Adjust</li>
</ol></li>
</ul>
<hr />
<h3 id="4-initial-access--attack-path-level-zero">4. <strong>Initial
Access – Attack Path Level Zero</strong></h3>
<p><strong>Scenario:</strong> C2 payload delivered as <strong>unsigned
executable</strong> downloaded from the internet.</p>
<h3 id="key-telemetry-indicators">Key Telemetry Indicators</h3>
<ul>
<li><strong>Mark of the Web (MOTW)</strong> present (downloaded from
internet).</li>
<li><strong>Unsigned binary</strong> execution.</li>
<li><strong>Outbound HTTP connections</strong> to C2
infrastructure.</li>
</ul>
<h3 id="relevant-sysmon-event-ids">Relevant Sysmon Event IDs</h3>
<ol type="1">
<li><strong>Event ID 7 – Image Load</strong>
<ul>
<li>Tracks unsigned executables loading from <strong>user
directories</strong> (e.g., Downloads).</li>
<li>Combine with file reputation services to reduce false
positives.</li>
</ul></li>
<li><strong>Event ID 15 – File Create Stream Hash</strong>
<ul>
<li>Detects first write of a file from the internet.</li>
<li>Strong indicator of malicious download.</li>
</ul></li>
<li><strong>Event ID 3 – Network Connect</strong>
<ul>
<li>Shows outbound network activity.</li>
<li>Detects suspicious HTTP connections to <strong>non-standard
destinations</strong>.</li>
</ul></li>
</ol>
<hr />
<h3 id="5-situational-awareness-post-compromise-reconnaissance">5.
<strong>Situational Awareness (Post-Compromise
Reconnaissance)</strong></h3>
<ul>
<li><strong>Attacker’s Goals</strong>:
<ul>
<li>Identify <strong>host</strong> and <strong>user
privileges</strong>.</li>
<li>Discover other users.</li>
<li>Check for exploitable software.</li>
<li>Plan <strong>lateral movement</strong>.</li>
</ul></li>
<li><strong>Example Recon Commands</strong>:
<ul>
<li><code>whoami</code></li>
<li><code>hostname</code></li>
<li><code>net user</code></li>
<li><code>tasklist</code></li>
</ul></li>
</ul>
<h3 id="defensive-detection">Defensive Detection</h3>
<ul>
<li><strong>Sysmon Event ID 1 – Process Creation</strong>
<ul>
<li>Captures <strong>full command-line arguments</strong>.</li>
<li>Reveals reconnaissance and enumeration tools.</li>
<li>Crucial for spotting <strong>post-compromise behavior</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="6-understanding-sysmon">6. <strong>Understanding
Sysmon</strong></h3>
<ul>
<li><strong>Definition</strong>: System Monitor from <strong>Microsoft
Sysinternals</strong>.</li>
<li><strong>Role</strong>:
<ul>
<li>Provides <strong>high-fidelity event data</strong> to Windows Event
Log.</li>
<li>Runs as a <strong>Windows service</strong> with a
<strong>driver</strong> for low-level monitoring.</li>
<li>Operates as a <strong>Protected Process Light (PPL)</strong> for
tamper resistance.</li>
</ul></li>
<li><strong>Limitations</strong>:
<ul>
<li>Not an EDR (no real-time blocking).</li>
</ul></li>
<li><strong>Strengths</strong>:
<ul>
<li>Free, actively maintained, highly configurable.</li>
<li>Ideal for <strong>threat hunting</strong>, <strong>detection
engineering</strong>, and <strong>incident response</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="7-sysmon-capabilities-useful-for-detection">7. <strong>Sysmon
Capabilities Useful for Detection</strong></h3>
<ul>
<li><strong>Process Creation Events</strong>:
<ul>
<li>Includes <strong>parent process</strong> for execution chain
analysis.</li>
</ul></li>
<li><strong>File Hashing</strong>:
<ul>
<li>SHA1, MD5, SHA256, and imphash.</li>
</ul></li>
<li><strong>Unique Process GUIDs</strong>:
<ul>
<li>Allows correlation even if PID is reused.</li>
</ul></li>
<li><strong>Session GUIDs</strong>:
<ul>
<li>Tracks actions in the same user session.</li>
</ul></li>
<li><strong>File &amp; Disk Events</strong>:
<ul>
<li>Detect creation, timestamp tampering.</li>
</ul></li>
<li><strong>Network Connection Logging</strong>:
<ul>
<li>Captures IPs, hostnames, ports, originating process.</li>
</ul></li>
<li><strong>Dynamic Rule Filtering</strong>:
<ul>
<li>Include/exclude events as needed.</li>
</ul></li>
<li><strong>Auto-Reload Config</strong>:
<ul>
<li>Quick iteration during tuning.</li>
</ul></li>
<li><strong>Early Boot Data Capture</strong>:
<ul>
<li>Detects low-level threats.</li>
</ul></li>
</ul>
<hr />
<h3 id="8-next-steps-in-the-lab">8. <strong>Next Steps in the
Lab</strong></h3>
<ul>
<li>Begin reviewing <strong>each Sysmon Event ID</strong> required for
<strong>high-fidelity detections</strong>.</li>
<li>Implement or enable these events in the <strong>lab
environment</strong>.</li>
<li>Validate that all critical telemetry is being collected for
<strong>Attack Path Level Zero</strong>.</li>
</ul>
<hr />
<p>✅ <strong>Outcome for Students</strong>: By the end of this
exercise, you’ll know exactly <strong>what telemetry to expect</strong>
for specific attack stages, how to <strong>verify its presence</strong>,
and how to <strong>tune Sysmon</strong> to close visibility gaps.</p>
</section>
</div>
