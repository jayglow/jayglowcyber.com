---
layout: default
title: "Supplement What is AMSI"
permalink: /solo-purple-teaming/supplement-what-is-amsi/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Supplement What is AMSI</h1>
</section>
<section class="spt-content">
<h1 id="supplement-what-is-amsi">Supplement: What is AMSI?</h1>
<p><img src="/assets/images/solo-purple-teaming/supplement-what-is-amsi/image.png"
alt="image.png" /></p>
<p><strong>Before bypassing AMSI, we need to understand what it is—and
whether the operational risk of patching it is justified.</strong></p>
<p>The <strong>Anti-Malware Scan Interface (AMSI)</strong> is a security
feature introduced by Microsoft to help detect malicious content at
runtime. It allows applications like PowerShell, VBScript, JavaScript,
and Microsoft Office to pass scripts and other dynamic content to
antivirus software—including Microsoft Defender—for real-time
inspection.</p>
<blockquote>
<p>“AMSI is an open interface that allows your applications and services
to integrate with any antimalware product that’s present on a
machine.”</p>
<p>— Microsoft Docs, Windows AMSI documentation</p>
</blockquote>
<p>Where it really shines, unfortunately, for us, is spotting fileless
malware. Even if we never touch disk, AMSI can still inspect what's
being executed in memory and flag our code before it runs. So bypassing
AMSI is often a necessary step if we want to stay stealthy and maintain
execution without tipping off defenders.</p>
<p>AMSI is <strong>specifically designed to catch fileless malware and
in-memory threats</strong>—the exact techniques we often rely on during
red team engagements, including:</p>
<ul>
<li>Obfuscated PowerShell payloads</li>
<li>Office macro malware</li>
<li>Inline JavaScript/VBScript</li>
<li>Dynamically generated or encoded shellcode</li>
</ul>
<blockquote>
<p>“AMSI provides a deeper level of inspection for malicious software
that employs obfuscation and evasion techniques on Windows' built-in
scripting hosts. By integrating AMSI, Microsoft Defender for Endpoint
offers extra layers of protection against advanced threats.”</p>
<p>[<a
href="https://learn.microsoft.com/en-us/defender-endpoint/amsi-on-mdav](https://learn.microsoft.com/en-us/defender-endpoint/amsi-on-mdav)">https://learn.microsoft.com/en-us/defender-endpoint/amsi-on-mdav](https://learn.microsoft.com/en-us/defender-endpoint/amsi-on-mdav)</a></p>
</blockquote>
<h2 id="quick-refresher-on-powershell-sunspaces">Quick Refresher on
PowerShell Sunspaces</h2>
<p><a
href="/solo-purple-teaming/6dyrlibzgaaaabjru5erkjggg/"></a></p>
<p><a
href="/solo-purple-teaming/6dyrlibzgaaaabjru5erkjggg/"></a></p>
<p><img src="/assets/images/solo-purple-teaming/supplement-what-is-amsi/image%201.png"
alt="image.png" /></p>
<p>Before diving into AMSI, it’s important to
understand <strong>PowerShell runspaces</strong>:</p>
<ul>
<li>A <strong>runspace</strong> is an <strong>isolated PowerShell
execution environment</strong> hosted within a .NET (e.g., C#)
application.</li>
<li>Think of it as a <strong>headless PowerShell
instance</strong>—no <code>powershell.exe</code> process needed.</li>
<li>Runspaces maintain their <strong>own state, variables, and execution
context</strong>, making them ideal for embedding PowerShell
functionality directly in code.</li>
<li>This technique is frequently used for <strong>stealthy
execution</strong>, as it avoids launching the PowerShell
console—<strong>bypassing traditional detection methods</strong> like
command-line monitoring.</li>
</ul>
<hr />
<h3 id="mag-why-it-matters">🔍 Why It Matters</h3>
<ul>
<li><strong>No <code>powershell.exe</code></strong> → evades
process-based detection.</li>
<li><strong>In-memory execution</strong> → avoids script logging to
disk.</li>
<li><strong>Fully programmable</strong> → allows flexible and dynamic
command execution from within C#.</li>
</ul>
<h3 id="risks-of-apollo-payload--amsi-detection">Risks of Apollo Payload
&amp; AMSI Detection</h3>
<ul>
<li><strong>Apollo</strong> is a <strong>C#/.NET binary</strong>, which
means it interacts closely with Windows features like AMSI.</li>
<li><strong>Risks</strong>:
<ul>
<li>When <strong>loaded reflectively</strong>, AMSI can
scan <strong>memory content</strong> for malicious patterns.</li>
<li>If Apollo leverages <strong>PowerShell functionality</strong>, it
increases exposure to AMSI scanning.</li>
<li>AMSI performs <strong>real-time inspection</strong> of scripts and
command content passed to scripting engines (like PowerShell).</li>
</ul></li>
<li><strong>Why It’s Dangerous</strong>:
<ul>
<li>Even if no file touches disk, <strong>in-memory
operations</strong> can be scanned and flagged.</li>
<li>AMSI integrates with <strong>Windows Defender and 3rd party
AV</strong>, increasing detection chances.</li>
</ul></li>
<li><strong>Mitigation Strategies</strong>:
<ul>
<li>Use <strong>AMSI bypass techniques</strong> (e.g., patching the AMSI
context in memory).</li>
<li><strong>Encrypt or obfuscate</strong> suspicious strings.</li>
<li>Avoid invoking PowerShell or split payload logic across multiple
layers to reduce detectability.</li>
</ul></li>
</ul>
<p><img src="/assets/images/solo-purple-teaming/supplement-what-is-amsi/image%202.png"
alt="image.png" /></p>
<ul>
<li><strong>Apollo is a C#/.NET binary</strong>, which means it’s
subject to inspection by <strong>AMSI (Antimalware Scan
Interface)</strong>.</li>
<li><strong>Risks</strong>:
<ul>
<li>If loaded <strong>reflectively</strong>, AMSI can scan its memory
content.</li>
<li>Using <strong>PowerShell functionality</strong> increases detection
likelihood.</li>
</ul></li>
<li><strong>Why it's dangerous</strong>: AMSI scans memory <strong>in
real time</strong> for suspicious patterns</li>
<li><strong>Mitigation</strong>: Consider using <strong>AMSI bypass
techniques</strong> to reduce the chance of detection during
execution.</li>
</ul>
<h3 id="amsi-patching-techniques">AMSI Patching Techniques</h3>
<ul>
<li><strong>Goal</strong>: Disable or weaken AMSI scanning to help
payloads avoid detection.</li>
<li><strong>Common Techniques</strong>:
<ul>
<li>Patch <code>AmsiScanBuffer</code> directly
in <code>amsi.dll</code>.</li>
<li>Patch methods in the <strong>AMSI native methods
class</strong> (used for .NET interop).</li>
<li>Target <code>WinScanContent</code> or <code>ScanContent</code> — key
functions in AMSI’s scanning pipeline.</li>
</ul></li>
</ul>
</section>
</div>
