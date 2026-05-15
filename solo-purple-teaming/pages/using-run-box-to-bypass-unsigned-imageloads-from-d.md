---
layout: default
title: "Using Run Box to Bypass UnSigned ImageLoads From D"
permalink: /solo-purple-teaming/using-run-box-to-bypass-unsigned-imageloads-from-d/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Using Run Box to Bypass UnSigned ImageLoads From D</h1>
</section>
<section class="spt-content">
<h1
id="using-run-box-to-bypass-unsigned-imageloads-from-downloads">Using
Run Box to Bypass UnSigned ImageLoads From Downloads</h1>
<h3 id="1-introduction-to-the-technique"><strong>1. Introduction to the
Technique</strong></h3>
<ul>
<li>We are replacing our initial access method with a <strong>Windows
Run Dialogue execution</strong>.</li>
<li>This approach <strong>reflectively loads the Apollo implant into
memory</strong>—no files are written to disk.</li>
<li>Advantages:
<ul>
<li>Avoids file-based detections (AV, EDR file scan hooks, on-access
scanners).</li>
<li>No phishing attachments for secure email gateways to scan.</li>
<li>Minimal user action—paste a one-liner into the run box and press
Enter.</li>
</ul></li>
<li>From a defender’s view:
<ul>
<li>Execution appears <strong>user-initiated</strong>.</li>
<li>No new files appear on disk.</li>
<li>Code lives only in <strong>live process memory</strong>.</li>
<li>Detection requires memory telemetry or <strong>advanced PowerShell
Script Block Logging</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-why-powershell"><strong>2. Why PowerShell?</strong></h3>
<ul>
<li>Cross-platform, built on .NET.</li>
<li>Can automate tasks, query system info, and manage settings.</li>
<li>Direct access to:
<ul>
<li>Windows APIs</li>
<li>.NET classes</li>
<li>WMI</li>
</ul></li>
<li>Commonly used by both administrators and red teamers.</li>
</ul>
<hr />
<h3 id="3-understanding-the-command-structure"><strong>3. Understanding
the Command Structure</strong></h3>
<p>We start with a <strong>basic stealthy PowerShell
command</strong>:</p>
<ul>
<li><code>w hidden</code> → launches PowerShell without a visible
window.</li>
<li><code>c</code> (short for <code>command</code>) → executes the
specified command/script block and exits.</li>
<li><code>&amp; { ... }</code> → <strong>call operator</strong> invoking
the enclosed script block.</li>
<li>Inside the block, you can place any payload (e.g.,
<code>Get-Process</code> for testing).</li>
</ul>
<hr />
<h3 id="4-setting-up-in-the-lab"><strong>4. Setting Up in the
Lab</strong></h3>
<ol type="1">
<li>Connect to your <strong>reverse engineering VM</strong> via
RDP.</li>
<li>Open the <strong>goodbye AMZ project</strong> (initial access
payload with Apollo reflective loading).</li>
<li>Copy the PowerShell script into <strong>Notepad++</strong>.</li>
</ol>
<hr />
<h3 id="5-converting-to-a-one-liner"><strong>5. Converting to a
One-Liner</strong></h3>
<ul>
<li><p>Start with:</p>
powershell -c "& { ... }"
</li>
<li><p>Reduce size to fit into the run dialogue:</p>
<ul>
<li>Replace double quotes with single quotes where possible.</li>
<li>Shorten variable names:
<ul>
<li><code>bytes</code> → <code>b</code></li>
<li><code>loadedAssembly</code> → <code>l</code></li>
<li><code>instance</code> → <code>i</code></li>
<li><code>result</code> → <code>r</code></li>
<li><code>method</code> → <code>m</code></li>
<li><code>type</code> → <code>t</code></li>
</ul></li>
<li>Remove unnecessary spaces.</li>
<li>Inline method names instead of storing in separate variables.</li>
<li>Remove unused or redundant lines.</li>
</ul></li>
</ul>
<hr />
<h3 id="6-testing-in-powershell"><strong>6. Testing in
PowerShell</strong></h3>
<ul>
<li>Paste the one-liner into a <strong>PowerShell window</strong>
first.</li>
<li>Run one line at a time if errors occur.</li>
<li>Common troubleshooting:
<ul>
<li>Check renamed variables (e.g., <code>instance</code> →
<code>i</code>).</li>
<li>Ensure method calls reference the shortened names correctly.</li>
</ul></li>
</ul>
<hr />
<h3 id="7-adding-window-hiding"><strong>7. Adding Window
Hiding</strong></h3>
<ul>
<li><p>Add <code>w hidden</code> to run PowerShell without a visible
window.</p></li>
<li><p>Example:</p>
powershell -w hidden -c "& { ... }"
</li>
</ul>
<hr />
<h3 id="8-run-box-length-limit"><strong>8. Run Box Length
Limit</strong></h3>
<ul>
<li>The run dialogue has a <strong>character limit</strong>.</li>
<li>If the payload is still too long:
<ul>
<li>Further shorten variable names.</li>
<li>Replace <code>Invoke-WebRequest</code> with its alias
<code>iwr</code>.
<ul>
<li>This also bypasses custom detection rules for
<code>Invoke-WebRequest</code>.</li>
</ul></li>
</ul></li>
</ul>
<hr />
<h3 id="9-final-testing"><strong>9. Final Testing</strong></h3>
<ul>
<li>Paste into the run dialogue (<code>Windows + R</code>).</li>
<li>Ensure you receive the Apollo <strong>callback</strong> in your C2
server.</li>
<li>If it fails, break it into lines again and debug.</li>
<li>Verify it bypasses Windows Defender and previous custom
detections.</li>
</ul>
<hr />
<h3 id="10-real-world-deployment-concept"><strong>10. Real-World
Deployment Concept</strong></h3>
<ul>
<li>Host the one-liner on a <strong>compromised webpage</strong>.</li>
<li>Use JavaScript or other means to <strong>hijack the
clipboard</strong> with the payload.</li>
<li>Give the user instructions to:
<ul>
<li>Press <code>Windows + R</code></li>
<li>Paste (<code>Ctrl + V</code>)</li>
<li>Press Enter</li>
</ul></li>
<li>This technique has proven surprisingly effective in real-world
scenarios.</li>
</ul>
<hr />
<h3 id="11-before-moving-on"><strong>11. Before Moving On</strong></h3>
<ul>
<li>Confirm you can:
<ul>
<li>Use the run dialogue to load Apollo into memory.</li>
<li>Bypass Windows Defender detections.</li>
</ul></li>
<li><strong>Next step:</strong> Study the attached research on
<strong>PowerShell obfuscation</strong>.
<ul>
<li>This will be necessary for bypassing reflection detection in the
upcoming lecture.</li>
</ul></li>
</ul>
</section>
</div>
