---
layout: default
title: "Bypassing Signature-Based Detections"
permalink: /solo-purple-teaming/bypassing-signature-based-detections/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Bypassing Signature-Based Detections</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1 id="dart-objective">🎯 Objective</h1>
<p>Learn how to modify and test a payload using
<strong>ThreatCheck</strong> to evade <strong>signature-based
detection</strong> by Windows Defender. The goal is to get a
<strong>customized Apollo agent</strong> past Defender without
detection.</p>
<hr />
<h2 id="open_book-part-1-introduction-to-signature-based-detection">📖
<strong>Part 1: Introduction to Signature-Based Detection</strong></h2>
<ul>
<li>Antivirus engines often use <strong>signatures</strong> to detect
known threats (e.g., specific byte patterns, hashes).</li>
<li>If you upload a payload to VirusTotal, it may become <strong>flagged
and distributed</strong>, making it ineffective.</li>
<li>Tools like <strong>ThreatCheck</strong> automate payload testing
against Defender without sending it to public AV engines.</li>
</ul>
<hr />
<h2 id="gear-part-2-lab-setup-and-precautions">⚙️ <strong>Part 2: Lab
Setup and Precautions</strong></h2>
<h3 id="lock-before-going-online">🔒 Before Going Online</h3>
<ol type="1">
<li><p><strong>Disable the following in Windows Defender:</strong></p>
<ul>
<li><strong>Cloud-delivered protection</strong></li>
<li><strong>Automatic sample submission</strong></li>
</ul></li>
<li><p><strong>Create an exclusion folder</strong>:</p>
<ul>
<li>Go to <strong>Windows Security &gt; Virus &amp; threat protection
&gt; Manage settings &gt; Exclusions</strong>.</li>
<li>Click <strong>"Add or remove exclusions"</strong> → Add a
<strong>folder</strong> named <code>research</code>.</li>
<li>This will be your safe zone for payloads.</li>
</ul>
<p><img src="Bypassing%20Signature-Based%20Detections/image.png"
alt="image.png" /></p></li>
</ol>
<hr />
<h2 id="arrow_down-part-3-download-required-projects-from-github">⬇️
<strong>Part 3: Download Required Projects from GitHub</strong></h2>
<h3 id="-tools-needed">🛠 Tools Needed</h3>
<ul>
<li><p><strong>ThreatCheck</strong> by RastaMouse</p>
<p>GitHub:
<code>https://github.com/rasta-mouse/ThreatCheck</code></p></li>
</ul>
<p><img src="Bypassing%20Signature-Based%20Detections/image%201.png"
alt="image.png" /></p>
<ul>
<li><p><strong>Apollo Agent</strong></p>
<p>GitHub: Search for <code>MythicAgents/apollo</code></p></li>
</ul>
<p><img src="Bypassing%20Signature-Based%20Detections/image%202.png"
alt="image.png" /></p>
<blockquote>
<p>Recommendation: Review all code before running it. Take a snapshot of
your VM before downloading.</p>
</blockquote>
<hr />
<h2 id="package-part-4-extract-and-build-projects">📦 <strong>Part 4:
Extract and Build Projects</strong></h2>
<h3 id="a-build-threatcheck">A. Build ThreatCheck</h3>
<ol type="1">
<li><strong>Extract <code>ThreatCheck-master.zip</code></strong> into
your <code>research</code> folder.</li>
<li>Open the <code>.sln</code> (solution) file in <strong>Visual
Studio</strong>.</li>
</ol>
<p><img src="Bypassing%20Signature-Based%20Detections/image%203.png"
alt="image.png" /></p>
<ol type="1">
<li>Go to
<code>Tools &gt; NuGet Package Manager &gt; Manage NuGet Packages for Solution</code>
and install:
<ul>
<li><code>CommandLineParser</code></li>
</ul></li>
<li><strong>Set configuration</strong> to <code>Release | x64</code> and
build the project.</li>
</ol>
<p><img src="Bypassing%20Signature-Based%20Detections/image%204.png"
alt="image.png" /></p>
<hr />
<h3 id="b-build-apollo-agent">B. Build Apollo Agent</h3>
<ol type="1">
<li><p>Extract <code>Apollo-master.zip</code>.</p></li>
<li><p>Navigate to:</p>
<pre><code>Apollo-master &gt; Payload_Type &gt; Apollo &gt; Apollo
</code></pre></li>
</ol>
<p><img src="Bypassing%20Signature-Based%20Detections/image%205.png"
alt="image.png" /></p>
<ol type="1">
<li>Open the Apollo solution in Visual Studio.</li>
<li>Set configuration to <code>Release | x64</code>.</li>
</ol>
<p><img src="Bypassing%20Signature-Based%20Detections/image%206.png"
alt="image.png" /></p>
<ol type="1">
<li><p>Fix any missing namespaces:</p>
<ul>
<li>If there's an error like missing <code>Task</code>,</li>
</ul>
<p><img src="Bypassing%20Signature-Based%20Detections/image%207.png"
alt="image.png" /></p>
<ul>
<li><p>navigate to:</p>
<pre><code>Apollo &gt; Management &gt; Task.cs
</code></pre>
<p>and correct the namespace.</p></li>
</ul>
<p><img src="Bypassing%20Signature-Based%20Detections/image%208.png"
alt="image.png" /></p></li>
<li><p>Rebuild the solution. It should build all 22 projects.</p></li>
<li><p>Locate the output:</p></li>
</ol>
<pre><code>Apollo &gt; bin &gt; x64 &gt; Release &gt; net451 &gt; Apollo.exe
</code></pre>
<p><img src="Bypassing%20Signature-Based%20Detections/image%209.png"
alt="image.png" /></p>
<hr />
<h2
id="arrows_counterclockwise-part-5-prepare-for-threatcheck-testing">🔄
<strong>Part 5: Prepare for ThreatCheck Testing</strong></h2>
<ol type="1">
<li>Disconnect your VM from the Internet.</li>
<li>Create a <strong>testing</strong> folder inside your
<code>research</code> directory.</li>
<li>Copy the <strong>Apollo agent binary</strong> and the following from
the <code>ThreatCheck</code> build:
<ul>
<li><code>ThreatCheck.exe</code></li>
<li><code>CommandLine.dll</code> (dependency)</li>
<li><code>System.Management.Automation.dll</code> (dependency)</li>
</ul></li>
<li>Paste them into the <code>testing</code> folder.</li>
</ol>
<p><img src="Bypassing%20Signature-Based%20Detections/image%2010.png"
alt="image.png" /></p>
<hr />
<h2 id="test_tube-part-6-run-threatcheck">🧪 <strong>Part 6: Run
ThreatCheck</strong></h2>
<ol type="1">
<li><p>Open <strong>Command Prompt</strong>.</p></li>
<li><p>Navigate to the testing folder:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> <span class="dt">\p</span>ath<span class="dt">\t</span>o<span class="dt">\r</span>esearch<span class="dt">\t</span>esting</span></code></pre></div></li>
<li><p>Run ThreatCheck:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="ex">ThreatCheck.exe</span> <span class="at">-f</span> Apollo.exe</span></code></pre></div></li>
<li><p>Observe:</p>
<ul>
<li>Windows Defender will likely <strong>flag</strong> the file.</li>
<li>Output will show <strong>detected strings</strong> like
<code>mythic</code>.</li>
</ul>
<p><img src="Bypassing%20Signature-Based%20Detections/image%2011.png"
alt="image.png" /></p></li>
</ol>
<hr />
<h2 id="dna-part-7-modify-payload-to-evade-detection">🧬 <strong>Part 7:
Modify Payload to Evade Detection</strong></h2>
<ol type="1">
<li>Go back to <strong>Apollo project</strong> in Visual Studio.</li>
<li>Use <strong>Find and Replace</strong>:
<ul>
<li>Press <code>Ctrl + Shift + F</code>.</li>
<li>Find: <code>Mythic</code> (case-sensitive)</li>
<li>Replace with: <code>My</code></li>
<li>Replace all (should be ~509 occurrences).</li>
<li>Repeat for lowercase <code>mythic</code> → <code>my</code> (~73
occurrences).</li>
</ul></li>
</ol>
<p><img src="Bypassing%20Signature-Based%20Detections/image%2012.png"
alt="image.png" /></p>
<ol type="1">
<li>Rebuild the Apollo solution.</li>
</ol>
<hr />
<h2 id="repeat-part-8-re-test-modified-payload">🔁 <strong>Part 8:
Re-Test Modified Payload</strong></h2>
<ol type="1">
<li><p>Copy the <strong>new Apollo.exe</strong> from:</p>
<pre><code>bin &gt; x64 &gt; Release &gt; net451
</code></pre>
<p>into your <code>testing</code> folder, replacing the old
one.</p></li>
<li><p>Run ThreatCheck again:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="ex">ThreatCheck.exe</span> <span class="at">-f</span> Apollo.exe</span></code></pre></div></li>
<li><p><strong>Success!</strong> If no threats are found, the payload
has <strong>bypassed Defender</strong>.</p></li>
</ol>
<p><img src="Bypassing%20Signature-Based%20Detections/image%2013.png"
alt="image.png" /></p>
<ol type="1">
<li>Optional: Paste it onto the desktop to verify it's not flagged.</li>
</ol>
<hr />
<h2 id="white_check_mark-part-9-required-outcome">✅ <strong>Part 9:
Required Outcome</strong></h2>
<p>To proceed:</p>
<ul>
<li>Your <strong>customized Apollo agent</strong> must successfully
bypass Windows Defender.</li>
<li>Make sure:
<ul>
<li><strong>Automatic sample submission remains OFF</strong></li>
<li>You <strong>do not upload</strong> the payload to public services
like VirusTotal</li>
</ul></li>
</ul>
<hr />
<h2 id="rocket-next-steps">🚀 <strong>Next Steps</strong></h2>
<p>In the next lecture:</p>
<ul>
<li>You will <strong>replace</strong> the Apollo agent code in your
<strong>Mythic C2 installation</strong> with the modified version.</li>
</ul>
<blockquote>
<p>⚠️ Note: You must complete the bypass yourself. This walkthrough
provides guidance, but the actual evasion is a required hands-on task
for this course.</p>
</blockquote>
</section>
</div>
