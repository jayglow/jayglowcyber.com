---
layout: default
title: "Pyramid of Pain"
permalink: /solo-purple-teaming/pyramid-of-pain/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Pyramid of Pain</h1>
</section>
<section class="spt-content">
<h3 id="overview"><strong>Overview</strong></h3>
<p>In this session, we explore the <strong>Pyramid of Pain</strong> — a
strategic model developed by <strong>David Bianco</strong> to help
security professionals understand which indicators of compromise (IOCs)
are most valuable for defenders and most difficult for attackers to
change.</p>
<p><img src="/assets/images/solo-purple-teaming/pyramid-of-pain/image.png" alt="image.png" /></p>
<p>The pyramid organizes IOCs from <strong>easiest to hardest for
adversaries to alter</strong>, and from <strong>least to most valuable
for defenders</strong>.</p>
<hr />
<h2 id="1-base-of-the-pyramid--hash-values"><strong>1. Base of the
Pyramid – Hash Values</strong></h2>
<ul>
<li><strong>Definition</strong>: Unique fingerprints of files, often
generated via hashing algorithms like MD5, SHA1, or SHA256.</li>
<li><strong>Detection</strong>:
<ul>
<li>Commonly used in <strong>Antivirus</strong>, <strong>EDR
tools</strong>, and <strong>YARA scans</strong>.</li>
<li>Effective for identifying <strong>known malware</strong>.</li>
</ul></li>
<li><strong>Attacker Evasion</strong>:
<ul>
<li>Extremely easy for attackers to bypass.</li>
<li>A <strong>single byte change</strong> in a file generates a
<strong>completely new hash</strong>.</li>
</ul></li>
<li><strong>Defensive Value</strong>: Low against skilled adversaries;
useful for known threats but not resilient.</li>
</ul>
<hr />
<h2 id="2-second-level--ip-addresses"><strong>2. Second Level – IP
Addresses</strong></h2>
<ul>
<li><strong>Role in Detection</strong>:
<ul>
<li>Used to block or monitor connections to <strong>malicious
infrastructure</strong>.</li>
<li>Often appears in threat intelligence feeds.</li>
</ul></li>
<li><strong>Attacker Evasion</strong>:
<ul>
<li>Attackers can rotate IPs quickly using:
<ul>
<li>VPS hosting</li>
<li>Bulletproof hosting services</li>
<li>Compromised legitimate servers</li>
</ul></li>
</ul></li>
<li><strong>Defensive Value</strong>: Moderate short-term effectiveness,
but poor for long-term blocking due to rapid IP changes.</li>
</ul>
<hr />
<h2 id="3-third-level--domains"><strong>3. Third Level –
Domains</strong></h2>
<ul>
<li><strong>Importance</strong>:
<ul>
<li>Central to <strong>phishing campaigns</strong>, <strong>malware
delivery</strong>, and <strong>C2 communications</strong>.</li>
</ul></li>
<li><strong>Attacker Evasion</strong>:
<ul>
<li>Can register new domains easily, but this costs
<strong>time</strong> and <strong>money</strong>.</li>
</ul></li>
<li><strong>Defensive Enhancements</strong>:
<ul>
<li><strong>Domain age checks</strong> – flag newly registered
sites.</li>
<li><strong>Registrar and hosting provider analysis</strong> – identify
suspicious service usage.</li>
<li><strong>Traffic/Ranking analysis</strong> – separate legitimate
domains from those created for attacks.</li>
</ul></li>
<li><strong>Defensive Value</strong>: Higher than IPs due to added
cost/delay for attackers to replace.</li>
</ul>
<hr />
<h2 id="4-fourth-level--network-host-artifacts"><strong>4. Fourth Level
– Network Host Artifacts</strong></h2>
<ul>
<li><strong>Definition</strong>: Evidence left behind on a compromised
system such as:
<ul>
<li>File paths</li>
<li>Registry keys</li>
<li>Mutex names</li>
</ul></li>
<li><strong>Attacker Evasion</strong>:
<ul>
<li>Changing these may <strong>break malware
functionality</strong>.</li>
</ul></li>
<li><strong>Defensive Techniques</strong>:
<ul>
<li><strong>Deep malware analysis</strong> required.</li>
<li>Monitor for common paths, registry entries, or artifacts tied to
specific tools.</li>
</ul></li>
<li><strong>Impact</strong>: Detecting these disrupts attacker
operations significantly.</li>
</ul>
<hr />
<h2 id="5-fifth-level--tools"><strong>5. Fifth Level –
Tools</strong></h2>
<ul>
<li><strong>Description</strong>:
<ul>
<li>Software adversaries use for exploitation, persistence, or data
exfiltration.</li>
<li>Can be <strong>open-source</strong> or
<strong>custom-built</strong>.</li>
</ul></li>
<li><strong>Attacker Evasion</strong>:
<ul>
<li>Replacing tools means extra <strong>development time</strong>,
<strong>cost</strong>, and <strong>risk</strong>.</li>
</ul></li>
<li><strong>Defensive Advantage</strong>:
<ul>
<li>Early detection of known tools can signal sophisticated
intrusions.</li>
<li>Forces attackers to abandon or rebuild preferred toolkits.</li>
</ul></li>
</ul>
<hr />
<h2
id="6-top-of-the-pyramid--tactics-techniques-and-procedures-ttps"><strong>6.
Top of the Pyramid – Tactics, Techniques, and Procedures
(TTPs)</strong></h2>
<ul>
<li><strong>Definition</strong>:
<ul>
<li><strong>Tactics</strong> – the high-level objectives (e.g.,
persistence, privilege escalation).</li>
<li><strong>Techniques</strong> – the general methods (e.g., credential
dumping).</li>
<li><strong>Procedures</strong> – the specific implementations.</li>
</ul></li>
<li><strong>Attacker Evasion</strong>:
<ul>
<li>Changing TTPs often requires <strong>retraining teams</strong> and
<strong>redesigning attack playbooks</strong>.</li>
</ul></li>
<li><strong>Defensive Value</strong>:
<ul>
<li>Highest in the pyramid.</li>
<li>Detection at this level causes <strong>maximum disruption</strong>
and <strong>long-term impact</strong>.</li>
<li>More resilient than indicators tied to specific infrastructure or
files.</li>
</ul></li>
</ul>
<hr />
<h2 id="key-takeaways"><strong>Key Takeaways</strong></h2>
<ul>
<li>The <strong>higher</strong> you detect on the pyramid, the
<strong>more pain</strong> you inflict on adversaries.</li>
<li>Lower levels (hashes, IPs) are quick to change — good for short-term
but weak in persistence.</li>
<li>Higher levels (artifacts, tools, TTPs) are <strong>hard to
change</strong> and <strong>disrupt attacker operations</strong> more
effectively.</li>
<li>Effective defenders combine <strong>multiple levels</strong> for
balanced detection.</li>
</ul>
<hr />
<h2 id="next-steps"><strong>Next Steps</strong></h2>
<p>In the next session, we’ll cover <strong>balancing accuracy vs.
coverage</strong> — understanding how to build detection strategies that
avoid excessive false positives while maintaining broad visibility.</p>
</section>
</div>
