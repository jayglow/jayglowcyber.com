---
layout: default
title: "Generate Payload"
permalink: /solo-purple-teaming/generate-payload/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Generate Payload</h1>
</section>
<section class="spt-content">
<h2 id="objective"><strong>Objective</strong></h2>
<p>In this exercise, we will:</p>
<ol type="1">
<li>Configure the <strong>reflective loader</strong> to retrieve the
Apollo payload from the correct IP address.</li>
<li>Generate an <strong>Apollo agent payload</strong> in Mythic
configured to call back over HTTP.</li>
<li>Stage all files for initial access in the lab.</li>
</ol>
<hr />
<h2 id="part-1--configuring-the-reflective-loader"><strong>Part 1 –
Configuring the Reflective Loader</strong></h2>
<ol type="1">
<li><p><strong>Understand the Setup</strong></p>
<ul>
<li><p>Our project is called <strong>Goodbye AMSI</strong>, used to
reflectively load the Apollo agent.</p></li>
<li><p>In previous tests, we reached the Kali host using:</p>
10.0.3.2:8000

<p>Both the reverse engineering box and Kali host were on the
<strong>Attack LAN</strong>.</p></li>
<li><p>Now, we must use the <strong>WAN interface</strong> so the
payload can reach the Mythic server from <strong>inside the Ecoin
network</strong>.</p></li>
<li><p>WAN interface address:</p>
192.168.100.101:8000
</li>
</ul></li>
<li><p><strong>Edit the Loader</strong></p>
<ul>
<li><p>In the reflective loader source code, replace the old IP/port
with:</p>
192.168.100.101:8000
</li>
<li><p>Save the file (<strong>Ctrl + S</strong>).</p></li>
</ul></li>
<li><p><strong>Build the Solution</strong></p>
<ul>
<li><p>Compile the project in <strong>x64 Debug</strong> mode.</p></li>
<li><p>Output file:</p>
goodbyeAMSI.exe
</li>
</ul></li>
<li><p><strong>Locate and Transfer the File</strong></p>
<ul>
<li><p>Navigate to:</p>
/source/repos/GoodbyeAMSI/x64/Debug/
</li>
<li><p>Transfer the executable to the Kali host:</p>
<ul>
<li><p>If SSH isn’t running on Kali, start it:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> service ssh start</span></code></pre></div></li>
<li><p>Use SCP from the Reverse Engineering box to copy the file:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> goodbyeAMSI.exe user@10.0.3.2:/home/user/Downloads</span></code></pre></div></li>
<li><p>Enter your password when prompted.</p></li>
</ul></li>
</ul></li>
</ol>
<hr />
<h2 id="part-2--generating-the-apollo-payload-in-mythic"><strong>Part 2
– Generating the Apollo Payload in Mythic</strong></h2>
<ol type="1">
<li><p><strong>Open Mythic and Start Payload Creation</strong></p>
<ul>
<li>In Mythic’s UI, click the <strong>Payloads</strong> icon
(hazard/radioactive symbol).</li>
<li>Select <strong>Actions → Generate New Payload</strong>.</li>
</ul></li>
<li><p><strong>Payload Configuration</strong></p>
<ul>
<li><p><strong>Platform:</strong> Windows</p></li>
<li><p><strong>Agent:</strong> Apollo</p></li>
<li><p><strong>Format:</strong> Windows Binary</p></li>
<li><p><strong>Commands:</strong> Add all available commands (→
button).</p></li>
<li><p><strong>C2 Profile:</strong> HTTP</p>
<ul>
<li>Host: <code>192.168.100.101</code> (WAN interface of Attack LAN edge
device)</li>
</ul></li>
<li><p><strong>Description:</strong></p>
Assumed breach payload – Attack Level Zero
</li>
</ul></li>
<li><p><strong>Generate and Download</strong></p>
<ul>
<li>Click <strong>Create Payload</strong>.</li>
<li>Once generated, download it to your current machine (Mac outside the
lab in this example).</li>
</ul></li>
<li><p><strong>Rename and Transfer</strong></p>
<ul>
<li><p>Rename the payload:</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="fu">mv</span> apollo2.exe apollo.exe</span></code></pre></div></li>
<li><p>Use SCP to transfer the file into Kali’s Downloads directory:</p>
<div class="sourceCode" id="cb10"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> apollo.exe user@192.168.100.101:/home/user/Downloads</span></code></pre></div></li>
<li><p>Enter your password when prompted.</p></li>
</ul></li>
</ol>
<hr />
<h2 id="part-3--staging-the-payload-for-delivery"><strong>Part 3 –
Staging the Payload for Delivery</strong></h2>
<ol type="1">
<li><p><strong>Verify Files on Kali</strong></p>
<ul>
<li><p>SSH into the Kali host:</p>
<div class="sourceCode" id="cb11"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ssh</span> user@192.168.100.101</span></code></pre></div></li>
<li><p>Go to the Downloads directory and confirm:</p>
<div class="sourceCode" id="cb12"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb12-1"><a href="#cb12-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span></span></code></pre></div>
<p>You should see:</p>
goodbyeAMSI.exe
apollo.exe
</li>
</ul></li>
<li><p><strong>Start a Python HTTP Server</strong></p>
<ul>
<li><p>In the Downloads directory, run:</p>
<div class="sourceCode" id="cb14"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb14-1"><a href="#cb14-1" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> <span class="at">-m</span> http.server 8000</span></code></pre></div></li>
<li><p>If the server is already running, you’ll see a message indicating
the port is in use.</p></li>
</ul></li>
</ol>
<hr />
<h2 id="before-moving-on"><strong>Before Moving On</strong></h2>
<p>✅ Ensure you have:</p>
<ul>
<li><code>goodbyeAMSI.exe</code> staged in Kali Downloads.</li>
<li><code>apollo.exe</code> payload staged in Kali Downloads.</li>
<li>Python HTTP server running on port 8000 to serve the payload during
initial access.</li>
</ul>
<hr />
<h2 id="next-step"><strong>Next Step</strong></h2>
<p>In the next lecture, we will:</p>
<ul>
<li>Launch the staged payload.</li>
<li>Establish an initial foothold inside the <strong>ecoin
domain</strong>.</li>
</ul>
</section>
</div>
