---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-3/
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
id="red-team-layout---writing-a-service-enumeration-tool---part-3">Red
Team Layout - Writing A Service Enumeration Tool - Part 3</h1>
<p>Owner: Mike Sterrett</p>
<h3 id="1-lecture-objective"><strong>1. Lecture Objective</strong></h3>
<p>In this session, we continue developing our <strong>Service Scan
Utility</strong>.</p>
<p>Our focus: <strong>Check if the current user has permission to start
a service</strong> as we iterate through all services on a target
system.</p>
<hr />
<h2 id="2-understanding-security-descriptors-in-net"><strong>2.
Understanding Security Descriptors in .NET</strong></h2>
<p>Before coding, we need a solid understanding of the
<strong>RawSecurityDescriptor</strong> class in .NET, as it gives
low-level access to Windows object permissions.</p>
<ul>
<li><p><strong>DACL (Discretionary Access Control List)</strong></p>
<p>Defines which users or groups are allowed or denied access to the
object.</p></li>
<li><p><strong>Group Field</strong></p>
<p>Sets or retrieves the <strong>primary group</strong> associated with
the object.</p>
<p>Relevant in systems where <strong>group ownership</strong> impacts
access.</p></li>
<li><p><strong>Owner Field</strong></p>
<p>Stores the <strong>Security Identifier (SID)</strong> of the object
owner.</p>
<p>Ownership is critical because owners often have permission-modifying
rights.</p></li>
</ul>
<hr />
<h3 id="3-rawsecuritydescriptor-constructors"><strong>3.
RawSecurityDescriptor Constructors</strong></h3>
<p>.NET provides multiple ways to create a
<strong>RawSecurityDescriptor</strong>:</p>
<ol type="1">
<li><p><strong>Byte Array Constructor</strong></p>
<p>Initialize from raw binary data (often from unmanaged Windows API
calls).</p></li>
<li><p><strong>Detailed Constructor</strong></p>
<p>Provide explicit control flags, owner/group, and both system &amp;
discretionary ACLs.</p></li>
<li><p><strong>SDDL String Constructor</strong></p>
<p>Use <strong>Security Descriptor Definition Language</strong>
strings.</p>
<ul>
<li>Human-readable</li>
<li>Great for importing/exporting permissions</li>
<li><strong>We will use this</strong> for our utility.</li>
</ul></li>
</ol>
<hr />
<h3 id="4-understanding-service-permissions"><strong>4. Understanding
Service Permissions</strong></h3>
<p>Windows stores service permissions as a <strong>bitmask</strong> (an
integer where each bit is a permission flag).</p>
<ul>
<li><p><strong>SERVICE_START permission</strong> = <code>0x00010</code>
(binary <code>00010000</code>)</p></li>
<li><p>An ACE (<strong>Access Control Entry</strong>) can have
<strong>multiple permissions</strong> combined.</p></li>
<li><p>To check if <strong>SERVICE_START</strong> is granted:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="kw">if</span> <span class="op">((</span>accessMask <span class="op">&amp;</span> SERVICE_START<span class="op">)</span> <span class="op">==</span> SERVICE_START<span class="op">)</span></span></code></pre></div>
<p>A match means the permission is present.</p></li>
</ul>
<hr />
<h2 id="5-coding-the-canstartservice-function"><strong>5. Coding the
<code>CanStartService</code> Function</strong></h2>
<h3 id="step-1--create-the-function"><strong>Step 1 — Create the
Function</strong></h3>
<ul>
<li><p>Place it <strong>below</strong> the existing
<code>GetServiceSDDL</code> method.</p></li>
<li><p>Signature:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="kw">public</span> <span class="kw">static</span> <span class="dt">bool</span> <span class="fu">CanStartService</span><span class="op">(</span><span class="dt">string</span> sddl<span class="op">)</span></span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb2-3"><a href="#cb2-3" aria-hidden="true" tabindex="-1"></a>    <span class="kw">return</span> <span class="kw">false</span><span class="op">;</span> <span class="co">// placeholder</span></span>
<span id="cb2-4"><a href="#cb2-4" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div></li>
</ul>
<hr />
<h3 id="step-2--null-check"><strong>Step 2 — Null Check</strong></h3>
<p>Ensure the SDDL is not null before processing:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="kw">if</span> <span class="op">(</span>sddl <span class="op">!=</span> <span class="kw">null</span><span class="op">)</span></span></code></pre></div>
<hr />
<h3 id="step-3--get-current-user-identity"><strong>Step 3 — Get Current
User Identity</strong></h3>
<div class="sourceCode" id="cb4"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a>WindowsIdentity currentUser <span class="op">=</span> WindowsIdentity<span class="op">.</span><span class="fu">GetCurrent</span><span class="op">();</span></span></code></pre></div>
<p>This retrieves a <strong>WindowsIdentity</strong> object for the
currently logged-in user.</p>
<hr />
<h3 id="step-4--create-raw-security-descriptor-from-sddl"><strong>Step 4
— Create Raw Security Descriptor from SDDL</strong></h3>
<div class="sourceCode" id="cb5"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a>RawSecurityDescriptor rsd <span class="op">=</span> <span class="kw">new</span> <span class="fu">RawSecurityDescriptor</span><span class="op">(</span>sddl<span class="op">);</span></span></code></pre></div>
<hr />
<h3 id="step-5--iterate-through-the-dacl"><strong>Step 5 — Iterate
Through the DACL</strong></h3>
<div class="sourceCode" id="cb6"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="kw">foreach</span> <span class="op">(</span>CommonAce ace <span class="kw">in</span> rsd<span class="op">.</span><span class="fu">DiscretionaryAcl</span><span class="op">)</span></span></code></pre></div>
<p>We focus on <strong>AccessAllowed</strong> ACE types:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="kw">if</span> <span class="op">(</span>ace<span class="op">.</span><span class="fu">AceType</span> <span class="op">==</span> AceType<span class="op">.</span><span class="fu">AccessAllowed</span><span class="op">)</span></span></code></pre></div>
<hr />
<h3 id="step-6--check-user-or-group-match"><strong>Step 6 — Check User
or Group Match</strong></h3>
<p>We check if:</p>
<ul>
<li><p>The ACE’s <strong>SID</strong> matches the current user’s SID</p>
<p><strong>OR</strong></p></li>
<li><p>The ACE’s SID is in one of the user’s group memberships.</p></li>
</ul>
<div class="sourceCode" id="cb8"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="kw">if</span> <span class="op">(</span>currentUser<span class="op">.</span><span class="fu">User</span><span class="op">.</span><span class="fu">Equals</span><span class="op">(</span>ace<span class="op">.</span><span class="fu">SecurityIdentifier</span><span class="op">)</span> <span class="op">||</span></span>
<span id="cb8-2"><a href="#cb8-2" aria-hidden="true" tabindex="-1"></a>    currentUser<span class="op">.</span><span class="fu">Groups</span><span class="op">.</span><span class="fu">Contains</span><span class="op">(</span>ace<span class="op">.</span><span class="fu">SecurityIdentifier</span><span class="op">))</span></span>
<span id="cb8-3"><a href="#cb8-3" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb8-4"><a href="#cb8-4" aria-hidden="true" tabindex="-1"></a>    <span class="kw">return</span> <span class="kw">true</span><span class="op">;</span> <span class="co">// Permission granted</span></span>
<span id="cb8-5"><a href="#cb8-5" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<hr />
<h2 id="6-integrating-permission-check-into-service-scan"><strong>6.
Integrating Permission Check Into Service Scan</strong></h2>
<ul>
<li><p><strong>Move SDDL retrieval</strong> up in the loop.</p></li>
<li><p>Call:</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="kw">if</span> <span class="op">(</span><span class="fu">CanStartService</span><span class="op">(</span>sddl<span class="op">))</span></span>
<span id="cb9-2"><a href="#cb9-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb9-3"><a href="#cb9-3" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>serviceName<span class="op">);</span></span>
<span id="cb9-4"><a href="#cb9-4" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>sddl<span class="op">);</span></span>
<span id="cb9-5"><a href="#cb9-5" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span><span class="st">&quot;Current user can start this service.&quot;</span><span class="op">);</span></span>
<span id="cb9-6"><a href="#cb9-6" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div></li>
</ul>
<hr />
<h2 id="7-testing-in-the-lab"><strong>7. Testing in the
Lab</strong></h2>
<h3 id="step-1--run-in-reverse-engineering-vm"><strong>Step 1 — Run in
Reverse Engineering VM</strong></h3>
<ul>
<li>Execute the utility to see which services the current user can
start.</li>
</ul>
<h3 id="step-2--test-on-assume-breach-host"><strong>Step 2 — Test on
Assume Breach Host</strong></h3>
<ul>
<li>Use <code>scp</code> to copy the executable to the Kali host’s
downloads.</li>
<li>SSH in and start the staging server.</li>
<li>Download the service scan demo from the assume breach host’s
browser.</li>
<li>Run the tool and observe output.</li>
</ul>
<p><strong>Expected Result:</strong></p>
<p>The utility lists several services, including the
<strong>ecoin</strong> service, as startable by the current user.</p>
<hr />
<h2 id="8-student-task-before-next-lecture"><strong>8. Student Task
Before Next Lecture</strong></h2>
<ul>
<li>Iterate through all services with the Service Scan Utility.</li>
<li>Verify that the output correctly identifies services you can
start.</li>
<li>Read <strong>ReAttach research</strong> mentioned in the
walkthrough.</li>
<li>Prepare for the next session, where we’ll <strong>finish the custom
utility</strong> and learn to execute it in memory using
<strong>Apollo’s Execute-Assembly</strong>.</li>
</ul>
</section>
</div>
