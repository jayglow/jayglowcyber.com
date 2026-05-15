---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Writing A Service Enumeration To</h1>
</section>
<section class="spt-content">
<h1
id="red-team-layout---writing-a-service-enumeration-tool---part-1">Red
Team Layout - Writing A Service Enumeration Tool - Part 1</h1>
<p>Owner: Mike Sterrett</p>
<h3 id="objective"><strong>Objective</strong></h3>
<p>In this phase, we will begin building a custom C# utility called
<strong>Service Scan</strong> that enumerates all services on a Windows
endpoint. The goal is to:</p>
<ol type="1">
<li>Identify services our current user can <strong>start or
stop</strong>.</li>
<li>Identify services where we can <strong>write to the associated
binary</strong>.</li>
</ol>
<p>These checks are critical for detecting <strong>privilege escalation
opportunities</strong> via service abuse.</p>
<hr />
<h3 id="key-concepts"><strong>Key Concepts</strong></h3>
<h3 id="why-services-matter-in-privilege-escalation"><strong>Why
Services Matter in Privilege Escalation</strong></h3>
<ul>
<li>Many Windows services run with <strong>elevated
privileges</strong>.</li>
<li>If an attacker can:
<ul>
<li><p>Modify the service binary</p></li>
<li><p>Or control its start/stop behavior</p>
<p>→ They can escalate privileges and execute malicious code with higher
permissions.</p></li>
</ul></li>
</ul>
<h3 id="technology-used"><strong>Technology Used</strong></h3>
<ul>
<li><strong>C#</strong> with the <strong>.NET Framework</strong></li>
<li><strong>ServiceController</strong> class for service
interaction</li>
<li><strong>Windows Management Instrumentation (WMI)</strong> for
advanced permission checks</li>
<li><strong>Apollo Execute-Assembly</strong> (later phase) for
<strong>in-memory execution</strong> to evade disk-based detections</li>
</ul>
<hr />
<h3 id="step-1--understanding-the-servicecontroller-class"><strong>Step
1 – Understanding the ServiceController Class</strong></h3>
<p>The <code>ServiceController</code> class allows:</p>
<ul>
<li>Enumerating existing services</li>
<li>Querying properties like:
<ul>
<li><strong>DisplayName</strong> – Friendly name of the service</li>
<li><strong>ServiceType</strong> – Type of service (e.g., Win32
service)</li>
<li><strong>StartType</strong> – How the service starts (Automatic,
Manual, Disabled)</li>
<li><strong>Status</strong> – Current state (Running, Stopped,
Paused)</li>
</ul></li>
<li>Using <strong>GetServices()</strong> to retrieve all
<strong>non-driver services</strong> on a machine</li>
</ul>
<hr />
<h3 id="step-2--preparing-the-development-environment"><strong>Step 2 –
Preparing the Development Environment</strong></h3>
<ol type="1">
<li><strong>Remote into Reverse Engineering VM</strong>
<ul>
<li>Connect via Remote Desktop.</li>
</ul></li>
<li><strong>Open Visual Studio Code</strong></li>
<li><strong>Create a New Project</strong>
<ul>
<li>Type: <code>Console App (.NET Framework)</code></li>
<li>Name: <code>ServiceScanDemo</code></li>
</ul></li>
</ol>
<hr />
<h3 id="step-3--adding-required-references"><strong>Step 3 – Adding
Required References</strong></h3>
<p>The <code>ServiceController</code> class requires a reference to
<strong>System.ServiceProcess.dll</strong>.</p>
<p><strong>In Visual Studio Code:</strong></p>
<ol type="1">
<li><p>Remove unused <code>using</code> statements.</p></li>
<li><p>Add:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">ServiceProcess</span><span class="op">;</span></span></code></pre></div></li>
<li><p>If IntelliSense shows it’s unavailable:</p>
<ul>
<li><strong>Right-click</strong> the project → <strong>Add
Reference</strong></li>
<li>Go to <strong>.NET</strong> → Find
<strong>System.ServiceProcess</strong></li>
<li>Check the box → Click <strong>OK</strong></li>
</ul></li>
<li><p>The error disappears, meaning the reference is active.</p></li>
</ol>
<hr />
<h3 id="step-4--writing-the-enumeration-code"><strong>Step 4 – Writing
the Enumeration Code</strong></h3>
<ol type="1">
<li><p><strong>Retrieve All Services</strong></p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>ServiceController<span class="op">[]</span> services <span class="op">=</span> ServiceController<span class="op">.</span><span class="fu">GetServices</span><span class="op">();</span></span></code></pre></div></li>
<li><p><strong>Loop Through Services</strong></p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="kw">foreach</span> <span class="op">(</span>ServiceController service <span class="kw">in</span> services<span class="op">)</span></span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb3-3"><a href="#cb3-3" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">&quot;Service: {service.DisplayName}&quot;</span><span class="op">);</span></span>
<span id="cb3-4"><a href="#cb3-4" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">&quot;Start Type: {service.StartType}&quot;</span><span class="op">);</span></span>
<span id="cb3-5"><a href="#cb3-5" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>$<span class="st">&quot;Status: {service.Status}&quot;</span><span class="op">);</span></span>
<span id="cb3-6"><a href="#cb3-6" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">();</span></span>
<span id="cb3-7"><a href="#cb3-7" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div></li>
</ol>
<hr />
<h3 id="step-5--running-the-initial-version"><strong>Step 5 – Running
the Initial Version</strong></h3>
<ul>
<li>Press <strong>Run</strong> in Visual Studio Code.</li>
<li>Output:
<ul>
<li>Iterates through all services on the host</li>
<li>Displays:
<ul>
<li>Service Display Name</li>
<li>Start Type</li>
<li>Status</li>
</ul></li>
</ul></li>
<li>A blank line separates each service entry for readability.</li>
</ul>
<hr />
<h3 id="step-6--next-steps"><strong>Step 6 – Next Steps</strong></h3>
<p>Before moving on:</p>
<ol type="1">
<li><strong>Verify</strong> that your code:
<ul>
<li>Successfully enumerates all services</li>
<li>Prints the desired properties</li>
</ul></li>
<li><strong>Review</strong>:
<ul>
<li>The Microsoft documentation for
<strong>ServiceController</strong></li>
<li>The research docs for:
<ul>
<li><code>ServiceController</code></li>
<li><code>ManagementObjectSearcher</code> (used later for deeper WMI
queries)</li>
</ul></li>
</ul></li>
<li>Prepare for the next lecture where:
<ul>
<li>We’ll <strong>expand</strong> the Service Scan to check
permissions</li>
<li>Then use <strong>Apollo’s execute-assembly</strong> to run it
<strong>in-memory</strong> for stealth execution</li>
</ul></li>
</ol>
<hr />
<p>✅ <strong>By the end of this lecture:</strong></p>
<p>You now have a functional <strong>service enumeration
utility</strong> in C# that prints detailed metadata about all services.
This is the foundation for privilege escalation checks we’ll add
next.</p>
</section>
</div>
