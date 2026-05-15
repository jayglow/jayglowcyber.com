---
layout: default
title: "Attack Path Level 1"
permalink: /solo-purple-teaming/attack-path-level-1/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Attack Path Level 1</h1>
</section>
<section class="spt-content">
<h3 id="overview"><strong>Overview</strong></h3>
<p>Attack Path Level 1 builds directly on the detections and defenses
you created during Level 0.</p>
<p>The objective here isn’t just to “break in again” — it’s to
<strong>bypass</strong> those very defenses, sharpen adversarial
thinking, and improve both offensive and defensive skills through
iterative testing.</p>
<hr />
<h3 id="1-initial-access--clickfix-technique"><strong>1. Initial Access
– ClickFix Technique</strong></h3>
<ul>
<li><strong>Purpose</strong>: Test whether you can still gain execution
despite previous detections for suspicious image loads.</li>
<li><strong>Method</strong>:
<ul>
<li>Use the <strong>Windows Run dialog (<code>Win + R</code>)</strong>
to launch payloads.</li>
<li>This “ClickFix” technique is subtle and avoids some process creation
traces seen in normal execution paths.</li>
</ul></li>
<li><strong>Challenge</strong>:
<ul>
<li>Bypass both <strong>Windows Defender</strong> and your
<strong>custom blue team rules</strong> for unsigned binaries in
<code>\Temp</code> and <code>\Users</code> directories.</li>
</ul></li>
<li><strong>New Twist</strong>:
<ul>
<li>Develop evasions for <strong>reflection-based assembly
loading</strong> to directly target your earlier PowerShell reflection
detection rules.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-enumeration--advanced-recon"><strong>2. Enumeration – Advanced
Recon</strong></h3>
<ul>
<li><strong>Purpose</strong>: See if your updated enumeration tactics
can slip past the recon detections from Level 0.</li>
<li><strong>Method</strong>:
<ul>
<li>Perform host and network reconnaissance with adjusted commands,
obfuscation, or timing.</li>
</ul></li>
<li><strong>Blue Team Test</strong>:
<ul>
<li>Validate if your detections are catching behavioral patterns or just
specific commands.</li>
</ul></li>
<li><strong>Goal</strong>: Force adaptation — your enumeration must
evade previous detection logic while still collecting useful intel.</li>
</ul>
<hr />
<h3 id="3-privilege-escalation--modifiable-service-binary"><strong>3.
Privilege Escalation – Modifiable Service Binary</strong></h3>
<ul>
<li><strong>Purpose</strong>: Go beyond just privilege escalation —
bypass detections that flag service enumeration and abuse.</li>
<li><strong>Method</strong>:
<ul>
<li>Identify a service binary you can modify (replace or alter to
execute your payload).</li>
<li>Avoid triggering service-related detection rules from Level 0.</li>
</ul></li>
<li><strong>Key Learning</strong>:
<ul>
<li>This tests whether your detections are robust against <em>slightly
altered tactics</em> or if they rely too heavily on exact matches.</li>
</ul></li>
</ul>
<hr />
<h3 id="4-lateral-movement--targeting-the-domain-controller"><strong>4.
Lateral Movement – Targeting the Domain Controller</strong></h3>
<ul>
<li><strong>Purpose</strong>: Take on a <strong>high-value
objective</strong> while maintaining stealth.</li>
<li><strong>Method</strong>:
<ul>
<li>Attempt to pivot and access the <strong>Domain Controller
(DC)</strong>.</li>
<li>Use lateral movement techniques that differ from Level 0
attempts.</li>
</ul></li>
<li><strong>Blue Team Test</strong>:
<ul>
<li>Measure whether any traces are logged and caught by your rules.</li>
<li>Success here means achieving stealth against <em>your own tuned
detections</em>.</li>
</ul></li>
</ul>
<hr />
<h3 id="5-mindset--takeaways"><strong>5. Mindset &amp;
Takeaways</strong></h3>
<ul>
<li><strong>Iterative Learning</strong>: Each red team action feeds back
into improving blue team logic, and vice versa.</li>
<li><strong>Bypass Is Not the Goal Alone</strong>: The real value lies
in identifying gaps, understanding detection evasion, and preparing for
<strong>real-world adversaries</strong>.</li>
<li><strong>Continuous Improvement</strong>: Solo purple teaming is a
cycle — every level increases complexity, and every bypass teaches you
how to close the gap.</li>
</ul>
<hr />
<p><strong>Next Step:</strong></p>
<p>In the following lab, you’ll start the <strong>red team
layout</strong> for Attack Path Level 1, focusing first on using the
<strong>Run box technique</strong> to bypass unsigned image load
detections from the <code>Downloads</code> folder.</p>
</section>
</div>
