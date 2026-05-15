---
layout: default
title: "Level Zero Attack Path Review"
permalink: /solo-purple-teaming/level-zero-attack-path-review/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Level Zero Attack Path Review</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h2 id="1-understanding-the-scenario"><strong>1. Understanding the
Scenario</strong></h2>
<p>Before diving into the technical execution, it’s critical to define
the simulation parameters for both the <strong>target
environment</strong> and the <strong>attacker profile</strong>.</p>
<h3 id="target-environment-low-maturity"><strong>Target Environment (Low
Maturity)</strong></h3>
<ul>
<li><strong>Security Awareness:</strong> No formal user training or
awareness program.</li>
<li><strong>Telemetry:</strong> Limited to non-existent logging and
endpoint visibility.</li>
<li><strong>Preventative Controls:</strong> Minimal or poorly
configured.</li>
<li><strong>Patch Management:</strong> Neglected, leaving systems
vulnerable.</li>
<li><strong>Business Priorities:</strong> Heavy focus on availability;
security is an afterthought.</li>
</ul>
<p><strong>Implication:</strong> This environment is more susceptible to
basic attack techniques, making it ideal for measuring quick
improvements through purple teaming.</p>
<h3 id="attacker-profile-low-skill-threat-actor"><strong>Attacker
Profile (Low Skill Threat Actor)</strong></h3>
<ul>
<li><strong>TTP Knowledge:</strong> Little to no understanding of
tactics, techniques, and procedures.</li>
<li><strong>Operational Security:</strong> None — actions are noisy and
easily detected by competent defenders.</li>
<li><strong>Approach:</strong> Relies on straightforward,
unsophisticated attack steps.</li>
</ul>
<p><strong>Opportunity for Purple Teaming:</strong></p>
<p>This simulation allows a single operator to play both roles —
attacker and defender — to:</p>
<ul>
<li>Simulate realistic threats.</li>
<li>Measure detection and prevention effectiveness.</li>
<li>Identify immediate, high-value improvements.</li>
</ul>
<hr />
<h2 id="2-attack-path-phases"><strong>2. Attack Path
Phases</strong></h2>
<h3 id="phase-1-initial-access"><strong>Phase 1: Initial
Access</strong></h3>
<ul>
<li>Simulate a user downloading and executing malware on the
<strong>assumed breach host</strong>.</li>
<li><strong>Primary Goal:</strong> Bypass Microsoft Defender to
establish an initial foothold.</li>
</ul>
<hr />
<h3 id="phase-2-enumeration"><strong>Phase 2: Enumeration</strong></h3>
<ul>
<li>Conduct <strong>situational awareness</strong> to map the target
environment.</li>
<li>Identify:
<ul>
<li>Logged-in users.</li>
<li>Privileges and group memberships.</li>
<li>Active network connections.</li>
<li>Possible escalation paths.</li>
</ul></li>
</ul>
<hr />
<h3 id="phase-3-privilege-escalation"><strong>Phase 3: Privilege
Escalation</strong></h3>
<ul>
<li>Search for <strong>modifiable service binaries</strong> as an
escalation vector.</li>
<li>Tools &amp; Commands:
<ul>
<li><code>sc</code> → List services and associated executable
paths.</li>
<li><code>icacls</code> → Check file permissions for modification
rights.</li>
</ul></li>
<li><strong>Objective:</strong> Replace a legitimate service binary with
a malicious one to gain elevated privileges.</li>
</ul>
<hr />
<h2 id="3-transition-to-mythic-c2-operations"><strong>3. Transition to
Mythic C2 Operations</strong></h2>
<p>Before performing the hands-on attack, familiarize yourself with the
Mythic Command and Control (C2) framework.</p>
<h3 id="mythic-ui-preparation"><strong>Mythic UI
Preparation</strong></h3>
<p>In upcoming lectures, we will:</p>
<ol type="1">
<li><strong>Mythic UI Basics</strong> – Overview of the interface.</li>
<li><strong>User Settings</strong> – Customizing preferences.</li>
<li><strong>Operation Creation</strong> – Set up a dedicated
<strong>Level Zero Operation</strong>.</li>
<li><strong>Payloads &amp; Listeners</strong> – Understand the
relationship and configuration.</li>
<li><strong>Payload Generation</strong> – Create the payload for our
initial access stage.</li>
</ol>
<hr />
<h2 id="4-execution-plan"><strong>4. Execution Plan</strong></h2>
<p>Once the Mythic setup is complete:</p>
<ol type="1">
<li>Deploy the generated payload on the <strong>assumed breach
host</strong>.</li>
<li>Gain initial access.</li>
<li>Begin enumeration and privilege escalation testing.</li>
<li>Record detection and prevention gaps for follow-up defense
improvements.</li>
</ol>
</section>
</div>
