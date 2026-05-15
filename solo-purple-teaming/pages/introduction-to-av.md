---
layout: default
title: "Introduction to AV"
permalink: /solo-purple-teaming/introduction-to-av/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Introduction to AV</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1
id="understanding-antivirus-software-before-bypassing-windows-defender-with-apollo">Understanding
Antivirus Software (Before Bypassing Windows Defender with Apollo)</h1>
<h2 id="dart-lecture-objectives">🎯 Lecture Objectives</h2>
<p>By the end of this walkthrough, you will:</p>
<ul>
<li>Understand how modern antivirus (AV) software functions</li>
<li>Learn the differences between detection techniques (signature,
heuristic, behavior-based)</li>
<li>Recognize the strengths and limitations of AV</li>
<li>Prepare for bypass techniques demonstrated in upcoming sessions</li>
</ul>
<hr />
<h2 id="shield-what-is-antivirus-software">🛡️ What Is Antivirus
Software?</h2>
<p>Antivirus (AV) software is designed to <strong>detect, prevent, and
remove malicious software (malware)</strong>. But it’s more than just a
basic scanner.</p>
<p>AV solutions operate on multiple layers:</p>
<ul>
<li><strong>File system monitoring</strong> for infected or suspicious
files</li>
<li><strong>Memory monitoring</strong> for runtime anomalies</li>
<li><strong>Network traffic inspection</strong> to catch threats before
arrival</li>
</ul>
<p>Think of AV as the gatekeeper — protecting the operating system from
attackers trying to gain a foothold.</p>
<hr />
<h2 id="wrench-core-functions-of-antivirus">🔧 Core Functions of
Antivirus</h2>
<ol type="1">
<li><strong>File Scanning</strong>
<ul>
<li>On-demand or scheduled scans for known threats</li>
</ul></li>
<li><strong>Real-Time Monitoring</strong>
<ul>
<li>Actively scans as events happen (files opened, apps executed)</li>
</ul></li>
<li><strong>Malware Detection &amp; Removal</strong>
<ul>
<li>Identifies, quarantines, and neutralizes threats</li>
</ul></li>
<li><strong>Quarantine</strong>
<ul>
<li>Isolates suspicious files for further analysis</li>
</ul></li>
<li><strong>Automatic Updates</strong>
<ul>
<li>Regularly pulls latest threat intel to remain effective</li>
</ul></li>
</ol>
<hr />
<h2 id="dna-signature-based-detection">🧬 Signature-Based Detection</h2>
<ul>
<li><strong>What it is</strong>: Scans files against a <strong>database
of known malware "signatures"</strong> — unique identifiers of malicious
code.</li>
<li><strong>Strengths</strong>:
<ul>
<li>Fast and accurate for <strong>known threats</strong></li>
</ul></li>
<li><strong>Weaknesses</strong>:
<ul>
<li><strong>Useless against unknown or polymorphic malware</strong></li>
<li>Requires <strong>frequent updates</strong></li>
</ul></li>
</ul>
<p>📌 <strong>Polymorphic Malware</strong>: Code that changes its
structure to evade signature detection.</p>
<hr />
<h2 id="mag-heuristic-based-detection">🔍 Heuristic-Based Detection</h2>
<ul>
<li><strong>Goes beyond signatures</strong> — looks for
<strong>suspicious characteristics</strong> in code.</li>
<li>Uses <strong>rules and scoring systems</strong> to evaluate
potential threats.</li>
</ul>
<h3 id="two-main-techniques">Two Main Techniques:</h3>
<ul>
<li><strong>Static Analysis</strong>: Inspects code without executing
it.</li>
<li><strong>Dynamic Analysis</strong>: Executes code in a
<strong>sandbox</strong> to observe behavior.</li>
</ul>
<p>🟡 <strong>Downside</strong>: Can result in <strong>false
positives</strong> (legitimate software flagged).</p>
<hr />
<h2 id="stopwatch-real-time-protection">⏱️ Real-Time Protection</h2>
<ul>
<li>Hooks directly into the operating system to monitor:
<ul>
<li>File access</li>
<li>Process execution</li>
<li>Registry changes</li>
</ul></li>
</ul>
<p>🔒 Especially valuable for <strong>zero-day threats</strong> —
attacks with <strong>no known signature</strong>.</p>
<p>Real-time protection:</p>
<ul>
<li><strong>Stops threats at execution</strong></li>
<li><strong>Blocks malicious behavior immediately</strong></li>
</ul>
<hr />
<h2 id="cloud-cloud-based-scanning">☁️ Cloud-Based Scanning</h2>
<ul>
<li>Sends files or behaviors to <strong>cloud-based threat
engines</strong> for deeper analysis.</li>
</ul>
<h3 id="benefits">Benefits:</h3>
<ol type="1">
<li><strong>Speed &amp; accuracy</strong> — faster scanning, better
decision-making</li>
<li><strong>Global telemetry</strong> — learns from attacks around the
world</li>
<li><strong>Reputation scoring</strong> — checks how common or
suspicious a file is</li>
</ol>
<p>Example: If a file is rare, unsigned, and downloaded from a sketchy
site, it may be flagged as high risk.</p>
<hr />
<h2 id="brain-behavior-based-detection">🧠 Behavior-Based Detection</h2>
<ul>
<li>Focuses on <strong>what a program does</strong>, not how it
looks.</li>
<li>Catches:
<ul>
<li><strong>Code injection</strong></li>
<li><strong>Memory manipulation</strong></li>
<li><strong>Process hollowing</strong></li>
<li><strong>Privilege escalation</strong></li>
</ul></li>
</ul>
<p>💡 This technique powers <strong>modern EDR (Endpoint Detection &amp;
Response)</strong> tools.</p>
<p>✅ <strong>Great at detecting obfuscated or zero-day
malware.</strong></p>
<hr />
<h2 id="warning-limitations-of-antivirus-software">⚠️ Limitations of
Antivirus Software</h2>
<ol type="1">
<li><strong>It’s not foolproof</strong>
<ul>
<li>Targeted or custom-built malware can evade detection</li>
</ul></li>
<li><strong>Performance hits</strong>
<ul>
<li>May slow down system during scans or heavy monitoring</li>
</ul></li>
<li><strong>False positives</strong>
<ul>
<li>Legitimate apps flagged as malware</li>
</ul></li>
<li><strong>False negatives</strong>
<ul>
<li>Real malware goes undetected</li>
</ul></li>
</ol>
<p>🛡️ <strong>Best Practice</strong>: AV should be just <strong>one
layer</strong> in a broader <strong>Defense-in-Depth</strong> strategy,
alongside:</p>
<ul>
<li>Endpoint protection</li>
<li>Network segmentation</li>
<li>User training</li>
</ul>
<hr />
<h2 id="arrows_counterclockwise-whats-next">🔄 What’s Next?</h2>
<p>In the next lecture:</p>
<ul>
<li>You'll learn how to <strong>use ThreatCheck to bypass Windows
Defender’s signature-based detections</strong>.</li>
<li>This hands-on demo illustrates <strong>how attackers avoid AV
detection</strong>.</li>
</ul>
<p>➡️ <strong>Future Lectures</strong>: Focus will shift to
<strong>defensive techniques</strong>, including:</p>
<ul>
<li>Mitigating AV weaknesses</li>
<li>Building multi-layered defenses</li>
<li>Leveraging defense-in-depth strategies for modern threat
landscapes</li>
</ul>
<hr />
<h2 id="compass-recap">🧭 Recap</h2>
<table>
<thead>
<tr class="header">
<th>Topic</th>
<th>Summary</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>AV Basics</td>
<td>Detects, prevents, removes malware via multiple layers</td>
</tr>
<tr class="even">
<td>Signature Detection</td>
<td>Fast but weak against unknown threats</td>
</tr>
<tr class="odd">
<td>Heuristic Detection</td>
<td>Smarter, but risk of false positives</td>
</tr>
<tr class="even">
<td>Real-Time Protection</td>
<td>Hooks OS functions to block threats instantly</td>
</tr>
<tr class="odd">
<td>Cloud-Based Scanning</td>
<td>Adds speed, reputation, and global intel</td>
</tr>
<tr class="even">
<td>Behavior-Based Detection</td>
<td>Identifies malware by actions, not appearance</td>
</tr>
<tr class="odd">
<td>AV Limitations</td>
<td>Can be bypassed, needs layered security to be effective</td>
</tr>
</tbody>
</table>
</section>
</div>
