---
layout: default
title: "Prepping the Apollo Agent"
permalink: /solo-purple-teaming/prepping-the-apollo-agent/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Prepping the Apollo Agent</h1>
</section>
<section class="spt-content">
<h1
id="test_tube-preparing-the-apollo-agent-for-reflection-based-loading">🧪
Preparing the Apollo Agent for Reflection-Based Loading</h1>
<h2 id="compass-overview">🧭 Overview</h2>
<p>In this exercise, you'll modify the <strong>Apollo agent</strong> to
include an <code>Execute()</code> method that can be called via
reflection. Then, you'll generate a new payload, host it, and test it
using the <strong>Goodbye AMSI</strong> stager. Finally, you’ll verify
its execution and analyze issues related to Windows Defender blocking
the agent.</p>
<hr />
<h2
id="white_check_mark-step-1-modify-apollo-agent-to-add-execute-method">✅
Step 1: Modify Apollo Agent to Add <code>Execute()</code> Method</h2>
<ol type="1">
<li><p><strong>SSH into your Kali host.</strong></p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ssh</span> username@your-kali-host-ip</span></code></pre></div></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image.png"
alt="image.png" /></p>
<ol type="1">
<li><p><strong>Navigate to the installed Apollo agent
directory.</strong></p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/mythic/mythic/installed_services/Apollo/agent_code/Apollo</span></code></pre></div></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%201.png"
alt="image.png" /></p>
<ol type="1">
<li><p><strong>Open the <code>Program.cs</code> file for editing using
<code>sudo</code>.</strong></p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="ex">CopyEdit</span></span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> nano Program.cs</span></code></pre></div></li>
<li><p><strong>Note the namespace and class:</strong></p>
<ul>
<li>Namespace: <code>Apollo</code></li>
<li>Class: <code>Program</code></li>
</ul>
<p><img src="/_assets/prepping-the-apollo-agent/image%202.png"
alt="image.png" /></p></li>
<li><p><strong>Above the <code>Main</code> method, insert the following
code:</strong></p></li>
</ol>
<div class="sourceCode" id="cb4"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="kw">public</span> <span class="kw">static</span> <span class="dt">void</span> <span class="fu">Execute</span><span class="op">()</span></span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a>    <span class="dt">string</span><span class="op">[]</span> args <span class="op">=</span> <span class="op">{};</span></span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a>    <span class="fu">Main</span><span class="op">(</span>args<span class="op">);</span></span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<p><img src="/_assets/prepping-the-apollo-agent/image%203.png"
alt="image.png" /></p>
<ol type="1">
<li><strong>Save and exit the file:</strong>
<ul>
<li>Press <code>Ctrl + X</code>, then <code>Y</code>, then
<code>Enter</code>.</li>
</ul></li>
</ol>
<hr />
<h2 id="white_check_mark-step-2-generate-a-new-apollo-payload">✅ Step
2: Generate a New Apollo Payload</h2>
<ol type="1">
<li><strong>Go to the Mythic Web UI.</strong></li>
<li><strong>Navigate to the <code>Payloads</code> section.</strong></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%204.png"
alt="image.png" /></p>
<ol type="1">
<li>Click <strong>Actions > Generate Payload</strong>.</li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%205.png"
alt="image.png" /></p>
<ol type="1">
<li><strong>Select:</strong>
<ul>
<li><strong>OS:</strong> Windows</li>
<li><strong>Build Type:</strong> WinExe (default)</li>
<li>Click <strong>Next</strong></li>
</ul></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%206.png"
alt="image.png" /></p>
<ol type="1">
<li><strong>Choose C2 Profile:</strong>
<ul>
<li>Select <code>http</code></li>
<li>Update the host to match your Kali box IP address (e.g.,
<code>http://10.0.3.2</code>)</li>
<li>Click <strong>Next</strong></li>
</ul></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%207.png"
alt="image.png" /></p>
<ol type="1">
<li><strong>Payload Name:</strong> Leave as <code>apollo.exe</code> or
rename if needed.</li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%208.png"
alt="image.png" /></p>
<ol type="1">
<li><strong>Click Generate.</strong></li>
</ol>
<p>Wait for the build to complete.</p>
<p><img src="/_assets/prepping-the-apollo-agent/image%209.png"
alt="image.png" /></p>
<hr />
<h2
id="white_check_mark-step-3-download-and-transfer-the-payload-to-kali">✅
Step 3: Download and Transfer the Payload to Kali</h2>
<ol type="1">
<li><strong>Download the payload to your local machine (e.g.,
macOS).</strong></li>
<li><strong>Use <code>scp</code> to transfer it to your Kali
box:</strong></li>
</ol>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> ~/Downloads/apollo.exe kali@10.0.3.2:/home/kali/Downloads/</span></code></pre></div>
<p><img src="/_assets/prepping-the-apollo-agent/image%2010.png"
alt="image.png" /></p>
<hr />
<h2
id="white_check_mark-step-4-host-payload-using-python-http-server">✅
Step 4: Host Payload Using Python HTTP Server</h2>
<ol type="1">
<li><strong>SSH into your Kali box again (if needed).</strong></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%2011.png"
alt="image.png" /></p>
<ol type="1">
<li><p><strong>Navigate to the Downloads directory.</strong></p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> ~/Downloads</span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span></span></code></pre></div>
<p><img src="/_assets/prepping-the-apollo-agent/image%2012.png"
alt="image.png" /></p>
<p>Confirm <code>apollo.exe</code> is present.</p></li>
<li><p><strong>Start a Python HTTP server:</strong></p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> <span class="at">-m</span> http.server 8000</span></code></pre></div></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%2013.png"
alt="image.png" /></p>
<p>Now the payload is being hosted at:</p>
<p><code>http://10.0.3.2:8000/apollo.exe</code></p>
<hr />
<h2
id="white_check_mark-step-5-update-and-build-goodbye-amsi-for-reflection">✅
Step 5: Update and Build Goodbye AMSI for Reflection</h2>
<ol type="1">
<li><p><strong>On your reverse engineering Windows host, open the
Goodbye AMSI source code.</strong></p></li>
<li><p><strong>Modify the file to:</strong></p>
<ul>
<li>Change the payload to <code>apollo.exe</code></li>
</ul>
<p><img src="/_assets/prepping-the-apollo-agent/image%2014.png"
alt="image.png" /></p>
<ul>
<li>Update the type name to <code>Apollo.Program</code></li>
</ul></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%2015.png"
alt="image.png" /></p>
<ol type="1">
<li><strong>Save the changes and rebuild the solution (Build > Build
Solution).</strong></li>
</ol>
<hr />
<h2 id="white_check_mark-step-6-execute-the-test">✅ Step 6: Execute the
Test</h2>
<ol type="1">
<li><p><strong>Open a command prompt on your reverse engineering
box.</strong></p></li>
<li><p><strong>Navigate to the Goodbye AMSI executable
location.</strong></p></li>
<li><p><strong>Run it:</strong></p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="ex">GoodbyeAMSI.exe</span></span></code></pre></div></li>
</ol>
<hr />
<h2 id="no_entry_sign-troubleshooting-no-callback">🚫 Troubleshooting:
No Callback?</h2>
<p>If you don’t receive a callback in Mythic:</p>
<ol type="1">
<li><p><strong>Check Windows Defender logs:</strong></p>
<ul>
<li>Go to <strong>Windows Security > Virus &amp; threat protection
> Protection history</strong></li>
<li>Look for a detection around the same time as your test</li>
<li>If you see something like "Mythic Agent Detected", it’s likely being
blocked by real-time protection</li>
</ul>
<p><img src="/_assets/prepping-the-apollo-agent/image%2016.png"
alt="image.png" /></p></li>
<li><p><strong>Disable Defender temporarily:</strong></p>
<ul>
<li>Go to <strong>Virus &amp; threat protection > Manage
settings</strong></li>
<li>Turn <strong>Real-time protection</strong> OFF</li>
</ul></li>
<li><p><strong>Retry Goodbye AMSI execution.</strong></p></li>
<li><p><strong>Check Mythic UI for successful
callback.</strong></p></li>
</ol>
<p><img src="/_assets/prepping-the-apollo-agent/image%2017.png"
alt="image.png" /></p>
<hr />
<h2 id="tada-success-criteria">🎉 Success Criteria</h2>
<p>You have successfully completed the process if:</p>
<ul>
<li>Apollo agent was modified with an <code>Execute()</code> method</li>
<li>You generated and hosted the payload</li>
<li>You modified and built the Goodbye AMSI stager</li>
<li>Upon execution, you saw a callback in Mythic UI</li>
</ul>
<hr />
<h2 id="brain-challenge-before-next-lecture">🧠 Challenge Before Next
Lecture</h2>
<p>Before moving forward:</p>
<blockquote>
<p>✅ Ensure your modified Apollo agent can be reflectively loaded via
the Goodbye AMSI stager.</p>
</blockquote>
<hr />
<h2 id="soon-next-steps">🔜 Next Steps</h2>
<p>In the next lecture, you’ll dive into how to bypass <strong>Windows
Defender</strong> more effectively by modifying the agent further to
evade real-time protection.</p>
</section>
</div>
