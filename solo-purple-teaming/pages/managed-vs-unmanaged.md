---
layout: default
title: "Managed VS Unmanaged"
permalink: /solo-purple-teaming/managed-vs-unmanaged/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Managed VS Unmanaged</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1 id="introduction">Introduction</h1>
<p>Let’s start by understanding the difference between <strong>managed
and unmanaged code</strong>, and why this matters for red teamers.</p>
<hr />
<h3 id="white_check_mark-managed-code--controlled-by-the-clr">✅
<strong>Managed Code – Controlled by the CLR</strong></h3>
<p><strong>Managed code</strong> runs under the supervision of the
<strong>Common Language Runtime</strong> (CLR). Think of the CLR as a
safety net — it handles memory allocation, garbage collection, exception
handling, and even enforces security policies.</p>
<p>This managed environment is designed to prevent crashes and protect
memory from corruption.</p>
<p>From an attacker’s point of view, this structure is a
<strong>double-edged sword</strong>:</p>
<ul>
<li><strong>On the downside</strong>, traditional low-level exploits
like arbitrary memory corruption are harder to pull off.</li>
<li><strong>On the upside</strong>, .NET-heavy environments open new
doors — you can abuse trusted .NET assemblies, inject into CLR
processes, and live off the land using built-in .NET tools.</li>
</ul>
<p>Understanding how tightly managed code operates helps us know
<strong>where we're restricted</strong> — and more importantly,
<strong>where we might exploit or blend in</strong>.</p>
<hr />
<h3 id="white_check_mark-unmanaged-code--native--powerful">✅
<strong>Unmanaged Code – Native &amp; Powerful</strong></h3>
<p>In contrast, <strong>unmanaged code</strong> runs natively —
<strong>no CLR, no guardrails</strong>.</p>
<p>This gives us two important advantages as attackers:</p>
<ol type="1">
<li><strong>Direct access to memory and system resources</strong>.</li>
<li>The code is often written in <strong>C or C++</strong>, which are
extremely fast but also vulnerable.</li>
</ol>
<p>Since unmanaged code manages its <strong>own memory</strong>, we get
a bigger attack surface:</p>
<ul>
<li>Buffer overflows</li>
<li>Use-after-free vulnerabilities</li>
<li>Dangling pointers</li>
</ul>
<p>So, when we deal with unmanaged code, both the
<strong>opportunity</strong> and the <strong>risk</strong> are much
higher.</p>
<hr />
<h3 id="dart-why-this-matters-for-red-teaming">🎯 <strong>Why This
Matters for Red Teaming</strong></h3>
<p>Knowing whether you're working in a managed or unmanaged environment
is <strong>not just theory</strong> — it shapes your entire
engagement:</p>
<ul>
<li><strong>Unmanaged code</strong> is great for raw exploits: shellcode
injection, memory corruption, and initial access.</li>
<li><strong>Managed code</strong>, however, becomes useful <strong>after
you’ve gained access</strong> — you can abuse exposed .NET APIs, move
laterally, or use living-off-the-land techniques.</li>
</ul>
<p>But here’s the catch: .NET tools need the <strong>CLR to be
loaded</strong>. And that’s something defenders can
<strong>detect</strong>.</p>
<p>Modern blue teams are alert to processes that unexpectedly load the
CLR. So, for <strong>stealthy and long-lasting operations</strong>, you
must know when you're dealing with managed code, and how to
<strong>weaponize both</strong> environments.</p>
<hr />
<h3 id="brain-clr-as-an-attack-opportunity">🧠 <strong>CLR as an Attack
Opportunity</strong></h3>
<p>Once the CLR is active in a process, it’s <strong>not just a
defensive obstacle</strong> — it's a <strong>valuable
resource</strong>:</p>
<ul>
<li>You can <strong>inspect loaded .NET assemblies</strong> in memory to
discover features and find weak points.</li>
<li>You can <strong>load and run your own .NET payloads</strong> using
reflective execution — no need to drop files to disk.</li>
<li>You can <strong>enumerate application domains and
assemblies</strong>, essentially mapping out the managed code landscape
of the process.</li>
<li>Advanced red teamers can even <strong>hook managed methods at
runtime</strong>, allowing them to tamper with or bypass security tools
like EDRs.</li>
</ul>
<p>Bottom line: If the CLR is running, it gives you a <strong>whole new
toolkit</strong> to work with.</p>
<hr />
<h3 id="test_tube-lab-preview--enumerating-assemblies-in-c">🧪
<strong>Lab Preview – Enumerating Assemblies in C#</strong></h3>
<p>Let’s jump into a quick demo to solidify this.</p>
<p>We’ll use <strong>Visual Studio</strong> inside our <strong>Windows
11 reverse engineering lab VM</strong> to enumerate loaded assemblies in
a running .NET process.</p>
<h3 id="wrench-step-by-step">🔧 Step-by-Step</h3>
<ol type="1">
<li><strong>Open Visual Studio</strong></li>
<li>Create a new <strong>Console App (.NET Framework)</strong>
project</li>
<li>Name it something like <code>GoodbyeAMSI</code></li>
<li>Choose <strong>.NET Framework 4.7.2</strong></li>
</ol>
<p><img src="Managed%20VS%20Unmanaged/image.png" alt="image.png" /></p>
<p>Once the project is set up:</p>
<ol type="1">
<li>Remove the default <code>using</code> statements we don’t need</li>
<li>Add the following snippet to <code>Main()</code>:</li>
</ol>
<div class="sourceCode" id="cb1"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="kw">static</span> <span class="dt">void</span> <span class="fu">Main</span><span class="op">(</span><span class="dt">string</span><span class="op">[]</span> args<span class="op">)</span></span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>    <span class="kw">foreach</span> <span class="op">(</span><span class="dt">var</span> assembly <span class="kw">in</span> AppDomain<span class="op">.</span><span class="fu">CurrentDomain</span><span class="op">.</span><span class="fu">GetAssemblies</span><span class="op">())</span></span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a>        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>assembly<span class="op">.</span><span class="fu">FullName</span><span class="op">);</span></span>
<span id="cb1-6"><a href="#cb1-6" aria-hidden="true" tabindex="-1"></a>        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">();</span> <span class="co">// add a few new lines for spacing</span></span>
<span id="cb1-7"><a href="#cb1-7" aria-hidden="true" tabindex="-1"></a>        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">();</span></span>
<span id="cb1-8"><a href="#cb1-8" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span></code></pre></div>
<p><img src="Managed%20VS%20Unmanaged/image%201.png"
alt="image.png" /></p>
<p>When we run this program, it will list all the assemblies currently
loaded in the application domain. Since we haven’t included any extra
dependencies, you’ll likely only see:</p>
<ul>
<li><code>mscorlib</code></li>
<li>Your own <code>GoodbyeAMSI</code> assembly</li>
</ul>
<p><img src="Managed%20VS%20Unmanaged/image%202.png"
alt="image.png" /></p>
<p>This demonstrates how .NET reflects its own internal structure — and
that’s exactly what we’ll <strong>leverage next</strong>.</p>
<hr />
<h3 id="rocket-whats-next-amsi-bypass">🚀 <strong>What’s Next? AMSI
Bypass</strong></h3>
<p>In the next lesson, we’ll use this knowledge to <strong>bypass
AMSI</strong> (Anti-Malware Scan Interface) — a key defensive control in
Windows.</p>
<p>We’ll do this by:</p>
<ul>
<li>Enumerating loaded assemblies</li>
<li>Hooking into them using C#</li>
<li>Disabling AMSI within a CLR process</li>
</ul>
<p>This marks a pivot from understanding CLR… to <strong>weaponizing
it</strong>.</p>
<hr />
<h3 id="pushpin-summary">📌 Summary</h3>
<ul>
<li>Managed code = runs under CLR, safer, but can be abused via .NET
features</li>
<li>Unmanaged code = native execution, high-risk/high-reward, prime for
exploits</li>
<li>Red teamers can exploit both environments — but need to know
<strong>how and when</strong></li>
<li>CLR gives you internal visibility and attack surface inside the
memory of managed processes</li>
<li>We’ll use this in the next lab to bypass AMSI</li>
</ul>
</section>
</div>
