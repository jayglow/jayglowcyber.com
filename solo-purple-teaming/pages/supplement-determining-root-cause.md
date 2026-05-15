---
layout: default
title: "Supplement Determining Root Cause"
permalink: /solo-purple-teaming/supplement-determining-root-cause/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Supplement Determining Root Cause</h1>
</section>
<section class="spt-content">
<h1 id="supplement-determining-root-cause">Supplement: Determining Root
Cause</h1>
<p>Understanding how to determine root cause is a foundational skill
across numerous cybersecurity disciplines, including—but not limited
to—Incident Response, Reverse Engineering, Malware Analysis, Threat
Hunting, and Red Teaming. Root cause analysis is the structured process
of uncovering the underlying reason or chain of events that led to a
specific security outcome or anomaly.</p>
<p>This process relies heavily on the practitioner’s ability to
interpret evidence within the context of prior knowledge, including
known behaviors, threat patterns, and baseline activity. Baselining—the
practice of defining and understanding what constitutes normal behavior
in a given environment—plays a critical role in this process. When a
deviation from the baseline is observed, it serves as a signal that
prompts further investigation.</p>
<p>By comparing current activity to established norms and leveraging
accumulated knowledge, cybersecurity professionals can more effectively
formulate hypotheses about the cause of an incident. They then follow a
methodical approach to test and validate these hypotheses. This
investigative mindset not only sharpens technical analysis but also
enhances the accuracy, speed, and relevance of both defensive and
offensive operations in complex environments.</p>
<h1 id="identifying-the-deviation-from-the-baseline">Identifying the
Deviation from the Baseline</h1>
<p>When we ran our reverse shell PowerShell payload with realtime
protection enabled, we noticed a deviation - that is a different
outcome, from the baseline we established in the previous lecture.</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image.png"
alt="image.png" /></p>
<p>Determining the root cause of this error is relatively
straightforward. Applying a common system administration
technique—examining recent changes—we can hypothesize that Windows
Defender Real-Time Protection is blocking our payload. This hypothesis
is supported by the <code>FullyQualifiedErrorId</code>, which is set
to <code>ScriptContainedMaliciousContent</code>, a strong indicator that
the script has been flagged as malicious.</p>
<h1 id="proving-our-hypothesis">Proving Our Hypothesis</h1>
<p>One effective way to validate our hypothesis is through debugging.
Debugging is the process of analyzing a program's behavior by stepping
through its execution in a controlled manner. This allows us to observe
the program’s internal state, logic flow, and interactions with system
components in real time.</p>
<p>In this case, we will attach a debugger to a PowerShell process to
gain deeper insight into how
the <code>ScriptContainedMaliciousContent</code> error is triggered. By
tracing the execution path and monitoring relevant function calls, we
can reverse engineer the underlying detection logic used by Windows
Defender. This approach helps us not only confirm whether the error is
being generated due to a specific code pattern or behavior, but also
enhances our overall understanding of how defensive mechanisms are
implemented at the system level.</p>
<p>Open a PowerShell instance</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%201.png"
alt="image.png" /></p>
<p>Open x64dbg</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%202.png"
alt="image.png" /></p>
<p>Attach to the PowerShell process</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%203.png"
alt="image.png" /></p>
<p>When we attach a debugger to a PowerShell process, Windows Defender
blocks the activity.</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%204.png"
alt="image.png" /></p>
<h1 id="use-psrunspaces-to-work-around-windows-defender">Use PSRunspaces
to Work Around Windows Defender</h1>
<p>A PowerShell runspace is the execution environment in which
PowerShell commands, scripts, and functions are parsed, compiled, and
executed. It encapsulates the runtime context, including variables,
session state, execution policies, and loaded modules. Runspaces are the
foundation of PowerShell’s execution model and can be created and
managed programmatically, allowing for advanced scenarios such as
multithreading, custom hosting, and automation within .NET applications.
Understanding runspaces is essential for tasks such as debugging, custom
script hosting, and performing more complex operations that require
isolation or concurrency within a PowerShell-based workflow.</p>
<h3 id="key-components">Key Components:</h3>
<p><strong>Engine State</strong></p>
<ul>
<li>Holds session-specific commands, providers, variables, and
functions.</li>
<li>Represents the current "state" of the PowerShell environment.</li>
</ul>
<p><strong>Runtime</strong></p>
<ul>
<li>Manages execution context.</li>
<li>Controls the flow of data and command execution.</li>
</ul>
<p><strong>Pipeline</strong></p>
<ul>
<li>Enables object-based communication between commands.</li>
<li>Core to PowerShell’s functionality.</li>
</ul>
<h3
id="open-visual-studio-code-and-create-a-console-appnet-framework">Open
Visual Studio Code and Create a Console App(.NET Framework)</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%205.png"
alt="image.png" /></p>
<p>Browse to and add a reference to C:\Program Files (x86)\Reference
Assemblies\Microsoft\WindowsPowerShel\3.0\System.Management.Automation.dll</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%206.png"
alt="image.png" /></p>
<p>Write a program to create a PSRunspace and run the command
“whoami”</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">;</span></span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Collections</span><span class="op">.</span><span class="fu">ObjectModel</span><span class="op">;</span></span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Diagnostics</span><span class="op">;</span></span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Management</span><span class="op">.</span><span class="fu">Automation</span><span class="op">;</span></span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> System<span class="op">.</span><span class="fu">Runtime</span><span class="op">.</span><span class="fu">InteropServices</span><span class="op">;</span></span>
<span id="cb1-6"><a href="#cb1-6" aria-hidden="true" tabindex="-1"></a><span class="kw">using</span> PowerShell <span class="op">=</span> System<span class="op">.</span><span class="fu">Management</span><span class="op">.</span><span class="fu">Automation</span><span class="op">.</span><span class="fu">PowerShell</span><span class="op">;</span></span>
<span id="cb1-7"><a href="#cb1-7" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb1-8"><a href="#cb1-8" aria-hidden="true" tabindex="-1"></a><span class="kw">namespace</span> AttachDebuggerTest</span>
<span id="cb1-9"><a href="#cb1-9" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb1-10"><a href="#cb1-10" aria-hidden="true" tabindex="-1"></a>    <span class="kw">internal</span> <span class="kw">class</span> Program</span>
<span id="cb1-11"><a href="#cb1-11" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb1-12"><a href="#cb1-12" aria-hidden="true" tabindex="-1"></a>        <span class="kw">static</span> <span class="dt">void</span> <span class="fu">Main</span><span class="op">(</span><span class="dt">string</span><span class="op">[]</span> args<span class="op">)</span></span>
<span id="cb1-13"><a href="#cb1-13" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb1-14"><a href="#cb1-14" aria-hidden="true" tabindex="-1"></a>            Console<span class="op">.</span><span class="fu">ReadLine</span><span class="op">();</span></span>
<span id="cb1-15"><a href="#cb1-15" aria-hidden="true" tabindex="-1"></a>            PowerShell ps <span class="op">=</span> PowerShell<span class="op">.</span><span class="fu">Create</span><span class="op">();</span></span>
<span id="cb1-16"><a href="#cb1-16" aria-hidden="true" tabindex="-1"></a>            <span class="dt">string</span> script <span class="op">=</span> <span class="st">"whoami"</span><span class="op">;</span></span>
<span id="cb1-17"><a href="#cb1-17" aria-hidden="true" tabindex="-1"></a>            ps<span class="op">.</span><span class="fu">AddScript</span><span class="op">(</span>script<span class="op">);</span></span>
<span id="cb1-18"><a href="#cb1-18" aria-hidden="true" tabindex="-1"></a>            Collection<span class="op"><</span>PSObject<span class="op">></span> results <span class="op">=</span> ps<span class="op">.</span><span class="fu">Invoke</span><span class="op">();</span></span>
<span id="cb1-19"><a href="#cb1-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb1-20"><a href="#cb1-20" aria-hidden="true" tabindex="-1"></a>            <span class="kw">foreach</span> <span class="op">(</span>PSObject obj <span class="kw">in</span> results<span class="op">)</span></span>
<span id="cb1-21"><a href="#cb1-21" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb1-22"><a href="#cb1-22" aria-hidden="true" tabindex="-1"></a>                Console<span class="op">.</span><span class="fu">WriteLine</span><span class="op">(</span>obj<span class="op">.</span><span class="fu">ToString</span><span class="op">());</span></span>
<span id="cb1-23"><a href="#cb1-23" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb1-24"><a href="#cb1-24" aria-hidden="true" tabindex="-1"></a>            Console<span class="op">.</span><span class="fu">ReadLine</span><span class="op">();</span></span>
<span id="cb1-25"><a href="#cb1-25" aria-hidden="true" tabindex="-1"></a>            </span>
<span id="cb1-26"><a href="#cb1-26" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb1-27"><a href="#cb1-27" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span>
<span id="cb1-28"><a href="#cb1-28" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<p>Compile for x64 and run:</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%207.png"
alt="image.png" /></p>
<p>Attach to the process with x64dbg</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%208.png"
alt="image.png" /></p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%209.png"
alt="image.png" /></p>
<h2 id="reverse-engineering-powershell">Reverse Engineering
PowerShell</h2>
<p>Download the PowerShell Project from GitHub on your Reverse
Engineering VM: <a
href="https://github.com/PowerShell/PowerShell">https://github.com/PowerShell/PowerShell</a></p>
<h3
id="1-open-in-the-solution-file-with-visualstudio-and-search-the-project-for-scriptcontainedmaliciouscontent">1.
Open in the solution file with VisualStudio and search the project for
<code>ScriptContainedMaliciousContent</code></h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2010.png"
alt="image.png" /></p>
<p>You will see 4 results:</p>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2011.png"
alt="image.png" /></p>
<h3
id="3-click-on-the-first-instance-in-compiledscriptblockcs-and-analyze-the-code-to-determine-what-is-going-on">3.
Click on the first instance in CompiledScriptBlock.cs and analyze the
code to determine what is going on</h3>
<div class="sourceCode" id="cb2"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="co">// Call the AMSI API to determine if the script block has malicious content</span></span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="dt">var</span> amsiResult <span class="op">=</span> AmsiUtils<span class="op">.</span><span class="fu">ScanContent</span><span class="op">(</span>scriptExtent<span class="op">.</span><span class="fu">Text</span><span class="op">,</span> scriptFile<span class="op">);</span></span>
<span id="cb2-3"><a href="#cb2-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb2-4"><a href="#cb2-4" aria-hidden="true" tabindex="-1"></a><span class="kw">if</span> <span class="op">(</span>amsiResult <span class="op">==</span> AmsiUtils<span class="op">.</span><span class="fu">AmsiNativeMethods</span><span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_DETECTED</span><span class="op">)</span></span>
<span id="cb2-5"><a href="#cb2-5" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb2-6"><a href="#cb2-6" aria-hidden="true" tabindex="-1"></a>    <span class="dt">var</span> parseError <span class="op">=</span> <span class="kw">new</span> <span class="fu">ParseError</span><span class="op">(</span></span>
<span id="cb2-7"><a href="#cb2-7" aria-hidden="true" tabindex="-1"></a>        scriptExtent<span class="op">,</span></span>
<span id="cb2-8"><a href="#cb2-8" aria-hidden="true" tabindex="-1"></a>        <span class="st">"ScriptContainedMaliciousContent"</span><span class="op">,</span></span>
<span id="cb2-9"><a href="#cb2-9" aria-hidden="true" tabindex="-1"></a>        ParserStrings<span class="op">.</span><span class="fu">ScriptContainedMaliciousContent</span><span class="op">);</span></span>
<span id="cb2-10"><a href="#cb2-10" aria-hidden="true" tabindex="-1"></a>    <span class="kw">throw</span> <span class="kw">new</span> <span class="fu">ParseException</span><span class="op">(</span><span class="kw">new</span><span class="op">[]</span> <span class="op">{</span> parseError <span class="op">});</span></span>
<span id="cb2-11"><a href="#cb2-11" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<p>This code checks whether a PowerShell script block contains malicious
content using the <strong>Antimalware Scan Interface (AMSI)</strong>. If
malicious content is detected, it generates a <strong>parse
error</strong> and halts further processing of the script.</p>
<p>This line tells us where to go next</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="co">// Call the AMSI API to determine if the script block has malicious content</span></span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a><span class="dt">var</span> amsiResult <span class="op">=</span> AmsiUtils<span class="op">.</span><span class="fu">ScanContent</span><span class="op">(</span>scriptExtent<span class="op">.</span><span class="fu">Text</span><span class="op">,</span> scriptFile<span class="op">);</span></span></code></pre></div>
<p>amsiResult which is used as a flag to determine if the content was
malicious or not, is a result of calling AmsiUtils.ScanContent.</p>
<h3
id="4-jump-to-the-definition-of-scancontent-and-analyze-the-code-to-determine-the-function-of-scancontent">4.
Jump to the definition of ScanContent and analyze the code to determine
the function of ScanContent</h3>
<div class="sourceCode" id="cb4"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="co">/// </span><span class="kw"><summary></span></span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="co">/// Scans a string buffer for malware using the Antimalware Scan Interface (AMSI).</span></span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a><span class="co">/// Caller is responsible for calling AmsiCloseSession when a "session" (script)</span></span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a><span class="co">/// is complete, and for calling AmsiUninitialize when the runspace is being torn down.</span></span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a><span class="co">/// </span><span class="kw"></summary></span></span>
<span id="cb4-6"><a href="#cb4-6" aria-hidden="true" tabindex="-1"></a><span class="co">/// </span><span class="kw"><param</span><span class="ot"> name=</span><span class="dt">"content"</span><span class="kw">></span><span class="co">The string to be scanned.</span><span class="kw"></param></span></span>
<span id="cb4-7"><a href="#cb4-7" aria-hidden="true" tabindex="-1"></a><span class="co">/// </span><span class="kw"><param</span><span class="ot"> name=</span><span class="dt">"sourceMetadata"</span><span class="kw">></span><span class="co">Information about the source (filename, etc.).</span><span class="kw"></param></span></span>
<span id="cb4-8"><a href="#cb4-8" aria-hidden="true" tabindex="-1"></a><span class="co">/// </span><span class="kw"><returns></span><span class="co">AMSI_RESULT_DETECTED if malware was detected in the sample.</span><span class="kw"></returns></span></span>
<span id="cb4-9"><a href="#cb4-9" aria-hidden="true" tabindex="-1"></a><span class="kw">internal</span> <span class="kw">static</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span> <span class="fu">ScanContent</span><span class="op">(</span><span class="dt">string</span> content<span class="op">,</span> <span class="dt">string</span> sourceMetadata<span class="op">)</span></span>
<span id="cb4-10"><a href="#cb4-10" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb4-11"><a href="#cb4-11" aria-hidden="true" tabindex="-1"></a>        <span class="kw">#if</span> UNIX</span>
<span id="cb4-12"><a href="#cb4-12" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_NOT_DETECTED</span><span class="op">;</span></span>
<span id="cb4-13"><a href="#cb4-13" aria-hidden="true" tabindex="-1"></a>        <span class="kw">#else</span></span>
<span id="cb4-14"><a href="#cb4-14" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> <span class="fu">WinScanContent</span><span class="op">(</span>content<span class="op">,</span> sourceMetadata<span class="op">,</span> warmUp<span class="op">:</span> <span class="kw">false</span><span class="op">);</span></span>
<span id="cb4-15"><a href="#cb4-15" aria-hidden="true" tabindex="-1"></a>        <span class="kw">#endif</span></span>
<span id="cb4-16"><a href="#cb4-16" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<h3 id="5-jump-to-the-definition-of-winscancontent">5. Jump to the
definition of WinScanContent</h3>
<div class="sourceCode" id="cb5"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="kw">internal</span> <span class="kw">static</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span> <span class="fu">WinScanContent</span><span class="op">(</span></span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a>    <span class="dt">string</span> content<span class="op">,</span></span>
<span id="cb5-3"><a href="#cb5-3" aria-hidden="true" tabindex="-1"></a>    <span class="dt">string</span> sourceMetadata<span class="op">,</span></span>
<span id="cb5-4"><a href="#cb5-4" aria-hidden="true" tabindex="-1"></a>    <span class="dt">bool</span> warmUp<span class="op">)</span></span>
<span id="cb5-5"><a href="#cb5-5" aria-hidden="true" tabindex="-1"></a><span class="op">{</span></span>
<span id="cb5-6"><a href="#cb5-6" aria-hidden="true" tabindex="-1"></a>    <span class="kw">if</span> <span class="op">(</span><span class="dt">string</span><span class="op">.</span><span class="fu">IsNullOrEmpty</span><span class="op">(</span>sourceMetadata<span class="op">))</span></span>
<span id="cb5-7"><a href="#cb5-7" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb5-8"><a href="#cb5-8" aria-hidden="true" tabindex="-1"></a>        sourceMetadata <span class="op">=</span> <span class="dt">string</span><span class="op">.</span><span class="fu">Empty</span><span class="op">;</span></span>
<span id="cb5-9"><a href="#cb5-9" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span>
<span id="cb5-10"><a href="#cb5-10" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-11"><a href="#cb5-11" aria-hidden="true" tabindex="-1"></a>    <span class="dt">const</span> <span class="dt">string</span> EICAR_STRING <span class="op">=</span> <span class="st">"X5O!P%@AP[4</span><span class="sc">\\</span><span class="st">PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*"</span><span class="op">;</span></span>
<span id="cb5-12"><a href="#cb5-12" aria-hidden="true" tabindex="-1"></a>    <span class="kw">if</span> <span class="op">(</span>InternalTestHooks<span class="op">.</span><span class="fu">UseDebugAmsiImplementation</span><span class="op">)</span></span>
<span id="cb5-13"><a href="#cb5-13" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb5-14"><a href="#cb5-14" aria-hidden="true" tabindex="-1"></a>        <span class="kw">if</span> <span class="op">(</span>content<span class="op">.</span><span class="fu">Contains</span><span class="op">(</span>EICAR_STRING<span class="op">,</span> StringComparison<span class="op">.</span><span class="fu">Ordinal</span><span class="op">))</span></span>
<span id="cb5-15"><a href="#cb5-15" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb5-16"><a href="#cb5-16" aria-hidden="true" tabindex="-1"></a>            <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_DETECTED</span><span class="op">;</span></span>
<span id="cb5-17"><a href="#cb5-17" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb5-18"><a href="#cb5-18" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span>
<span id="cb5-19"><a href="#cb5-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-20"><a href="#cb5-20" aria-hidden="true" tabindex="-1"></a>    <span class="co">// If we had a previous initialization failure, just return the neutral result.</span></span>
<span id="cb5-21"><a href="#cb5-21" aria-hidden="true" tabindex="-1"></a>    <span class="kw">if</span> <span class="op">(</span>s_amsiInitFailed<span class="op">)</span></span>
<span id="cb5-22"><a href="#cb5-22" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb5-23"><a href="#cb5-23" aria-hidden="true" tabindex="-1"></a>        PSEtwLog<span class="op">.</span><span class="fu">LogAmsiUtilStateEvent</span><span class="op">(</span><span class="st">"ScanContent-InitFail"</span><span class="op">,</span> $<span class="st">"{s_amsiContext}-{s_amsiSession}"</span><span class="op">);</span></span>
<span id="cb5-24"><a href="#cb5-24" aria-hidden="true" tabindex="-1"></a>        <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_NOT_DETECTED</span><span class="op">;</span></span>
<span id="cb5-25"><a href="#cb5-25" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span>
<span id="cb5-26"><a href="#cb5-26" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-27"><a href="#cb5-27" aria-hidden="true" tabindex="-1"></a>    <span class="kw">lock</span> <span class="op">(</span>s_amsiLockObject<span class="op">)</span></span>
<span id="cb5-28"><a href="#cb5-28" aria-hidden="true" tabindex="-1"></a>    <span class="op">{</span></span>
<span id="cb5-29"><a href="#cb5-29" aria-hidden="true" tabindex="-1"></a>        <span class="kw">if</span> <span class="op">(</span>s_amsiInitFailed<span class="op">)</span></span>
<span id="cb5-30"><a href="#cb5-30" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb5-31"><a href="#cb5-31" aria-hidden="true" tabindex="-1"></a>            PSEtwLog<span class="op">.</span><span class="fu">LogAmsiUtilStateEvent</span><span class="op">(</span><span class="st">"ScanContent-InitFail"</span><span class="op">,</span> $<span class="st">"{s_amsiContext}-{s_amsiSession}"</span><span class="op">);</span></span>
<span id="cb5-32"><a href="#cb5-32" aria-hidden="true" tabindex="-1"></a>            <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_NOT_DETECTED</span><span class="op">;</span></span>
<span id="cb5-33"><a href="#cb5-33" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb5-34"><a href="#cb5-34" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-35"><a href="#cb5-35" aria-hidden="true" tabindex="-1"></a>        <span class="kw">try</span></span>
<span id="cb5-36"><a href="#cb5-36" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb5-37"><a href="#cb5-37" aria-hidden="true" tabindex="-1"></a>            <span class="kw">if</span> <span class="op">(!</span><span class="fu">CheckAmsiInit</span><span class="op">())</span></span>
<span id="cb5-38"><a href="#cb5-38" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb5-39"><a href="#cb5-39" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_NOT_DETECTED</span><span class="op">;</span></span>
<span id="cb5-40"><a href="#cb5-40" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb5-41"><a href="#cb5-41" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-42"><a href="#cb5-42" aria-hidden="true" tabindex="-1"></a>            <span class="kw">if</span> <span class="op">(</span>warmUp<span class="op">)</span></span>
<span id="cb5-43"><a href="#cb5-43" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb5-44"><a href="#cb5-44" aria-hidden="true" tabindex="-1"></a>                <span class="co">// We are warming up the AMSI component in console startup, and that means we initialize AMSI</span></span>
<span id="cb5-45"><a href="#cb5-45" aria-hidden="true" tabindex="-1"></a>                <span class="co">// and create a AMSI session, but don't really scan anything.</span></span>
<span id="cb5-46"><a href="#cb5-46" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_NOT_DETECTED</span><span class="op">;</span></span>
<span id="cb5-47"><a href="#cb5-47" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb5-48"><a href="#cb5-48" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-49"><a href="#cb5-49" aria-hidden="true" tabindex="-1"></a>            AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span> result <span class="op">=</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_CLEAN</span><span class="op">;</span></span>
<span id="cb5-50"><a href="#cb5-50" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-51"><a href="#cb5-51" aria-hidden="true" tabindex="-1"></a>            <span class="co">// Run AMSI content scan</span></span>
<span id="cb5-52"><a href="#cb5-52" aria-hidden="true" tabindex="-1"></a>            <span class="dt">int</span> hr<span class="op">;</span></span>
<span id="cb5-53"><a href="#cb5-53" aria-hidden="true" tabindex="-1"></a>            <span class="kw">unsafe</span></span>
<span id="cb5-54"><a href="#cb5-54" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb5-55"><a href="#cb5-55" aria-hidden="true" tabindex="-1"></a>                <span class="kw">fixed</span> <span class="op">(</span><span class="dt">char</span><span class="op">*</span> buffer <span class="op">=</span> content<span class="op">)</span></span>
<span id="cb5-56"><a href="#cb5-56" aria-hidden="true" tabindex="-1"></a>                <span class="op">{</span></span>
<span id="cb5-57"><a href="#cb5-57" aria-hidden="true" tabindex="-1"></a>                    <span class="dt">var</span> buffPtr <span class="op">=</span> <span class="kw">new</span> <span class="fu">IntPtr</span><span class="op">(</span>buffer<span class="op">);</span></span>
<span id="cb5-58"><a href="#cb5-58" aria-hidden="true" tabindex="-1"></a>                    hr <span class="op">=</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AmsiScanBuffer</span><span class="op">(</span></span>
<span id="cb5-59"><a href="#cb5-59" aria-hidden="true" tabindex="-1"></a>                        s_amsiContext<span class="op">,</span></span>
<span id="cb5-60"><a href="#cb5-60" aria-hidden="true" tabindex="-1"></a>                        buffPtr<span class="op">,</span></span>
<span id="cb5-61"><a href="#cb5-61" aria-hidden="true" tabindex="-1"></a>                        <span class="op">(</span><span class="dt">uint</span><span class="op">)(</span>content<span class="op">.</span><span class="fu">Length</span> <span class="op">*</span> <span class="kw">sizeof</span><span class="op">(</span><span class="dt">char</span><span class="op">)),</span></span>
<span id="cb5-62"><a href="#cb5-62" aria-hidden="true" tabindex="-1"></a>                        sourceMetadata<span class="op">,</span></span>
<span id="cb5-63"><a href="#cb5-63" aria-hidden="true" tabindex="-1"></a>                        s_amsiSession<span class="op">,</span></span>
<span id="cb5-64"><a href="#cb5-64" aria-hidden="true" tabindex="-1"></a>                        <span class="kw">ref</span> result<span class="op">);</span></span>
<span id="cb5-65"><a href="#cb5-65" aria-hidden="true" tabindex="-1"></a>                <span class="op">}</span></span>
<span id="cb5-66"><a href="#cb5-66" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb5-67"><a href="#cb5-67" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-68"><a href="#cb5-68" aria-hidden="true" tabindex="-1"></a>            <span class="kw">if</span> <span class="op">(!</span>Utils<span class="op">.</span><span class="fu">Succeeded</span><span class="op">(</span>hr<span class="op">))</span></span>
<span id="cb5-69"><a href="#cb5-69" aria-hidden="true" tabindex="-1"></a>            <span class="op">{</span></span>
<span id="cb5-70"><a href="#cb5-70" aria-hidden="true" tabindex="-1"></a>                <span class="co">// If we got a failure, just return the neutral result ("AMSI_RESULT_NOT_DETECTED")</span></span>
<span id="cb5-71"><a href="#cb5-71" aria-hidden="true" tabindex="-1"></a>                PSEtwLog<span class="op">.</span><span class="fu">LogAmsiUtilStateEvent</span><span class="op">(</span>$<span class="st">"AmsiScanBuffer-{hr}"</span><span class="op">,</span> $<span class="st">"{s_amsiContext}-{s_amsiSession}"</span><span class="op">);</span></span>
<span id="cb5-72"><a href="#cb5-72" aria-hidden="true" tabindex="-1"></a>                <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_NOT_DETECTED</span><span class="op">;</span></span>
<span id="cb5-73"><a href="#cb5-73" aria-hidden="true" tabindex="-1"></a>            <span class="op">}</span></span>
<span id="cb5-74"><a href="#cb5-74" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb5-75"><a href="#cb5-75" aria-hidden="true" tabindex="-1"></a>            <span class="kw">return</span> result<span class="op">;</span></span>
<span id="cb5-76"><a href="#cb5-76" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb5-77"><a href="#cb5-77" aria-hidden="true" tabindex="-1"></a>        <span class="kw">catch</span> <span class="op">(</span>DllNotFoundException<span class="op">)</span></span>
<span id="cb5-78"><a href="#cb5-78" aria-hidden="true" tabindex="-1"></a>        <span class="op">{</span></span>
<span id="cb5-79"><a href="#cb5-79" aria-hidden="true" tabindex="-1"></a>            PSEtwLog<span class="op">.</span><span class="fu">LogAmsiUtilStateEvent</span><span class="op">(</span><span class="st">"DllNotFoundException"</span><span class="op">,</span> $<span class="st">"{s_amsiContext}-{s_amsiSession}"</span><span class="op">);</span></span>
<span id="cb5-80"><a href="#cb5-80" aria-hidden="true" tabindex="-1"></a>            <span class="kw">return</span> AmsiNativeMethods<span class="op">.</span><span class="fu">AMSI_RESULT</span><span class="op">.</span><span class="fu">AMSI_RESULT_NOT_DETECTED</span><span class="op">;</span></span>
<span id="cb5-81"><a href="#cb5-81" aria-hidden="true" tabindex="-1"></a>        <span class="op">}</span></span>
<span id="cb5-82"><a href="#cb5-82" aria-hidden="true" tabindex="-1"></a>    <span class="op">}</span></span>
<span id="cb5-83"><a href="#cb5-83" aria-hidden="true" tabindex="-1"></a><span class="op">}</span></span></code></pre></div>
<p>This method prepares the script content, ensures AMSI is properly
initialized, and then calls the native AMSI API to scan the buffer. The
scan result is either:</p>
<ul>
<li><code>AMSI_RESULT_DETECTED</code> for malicious content, or</li>
<li><code>AMSI_RESULT_NOT_DETECTED</code> / <code>AMSI_RESULT_CLEAN</code> if
the content is safe.</li>
</ul>
<h3 id="5-follow-the-definition-of-amsiscanbuffer">5. Follow the
definition of AmsiScanBuffer</h3>
<div class="sourceCode" id="cb6"><pre
class="sourceCode csharp"><code class="sourceCode cs"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="co">/// Return Type: HRESULT->LONG->int</span></span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="co">///amsiContext: HAMSICONTEXT->HAMSICONTEXT__*</span></span>
<span id="cb6-3"><a href="#cb6-3" aria-hidden="true" tabindex="-1"></a><span class="co">///buffer: PVOID->void*</span></span>
<span id="cb6-4"><a href="#cb6-4" aria-hidden="true" tabindex="-1"></a><span class="co">///length: ULONG->unsigned int</span></span>
<span id="cb6-5"><a href="#cb6-5" aria-hidden="true" tabindex="-1"></a><span class="co">///contentName: LPCWSTR->WCHAR*</span></span>
<span id="cb6-6"><a href="#cb6-6" aria-hidden="true" tabindex="-1"></a><span class="co">///amsiSession: HAMSISESSION->HAMSISESSION__*</span></span>
<span id="cb6-7"><a href="#cb6-7" aria-hidden="true" tabindex="-1"></a><span class="co">///result: AMSI_RESULT*</span></span>
<span id="cb6-8"><a href="#cb6-8" aria-hidden="true" tabindex="-1"></a><span class="op">[</span><span class="fu">DefaultDllImportSearchPaths</span><span class="op">(</span>DllImportSearchPath<span class="op">.</span><span class="fu">System32</span><span class="op">)]</span></span>
<span id="cb6-9"><a href="#cb6-9" aria-hidden="true" tabindex="-1"></a><span class="op">[</span><span class="fu">DllImport</span><span class="op">(</span><span class="st">"amsi.dll"</span><span class="op">,</span> EntryPoint <span class="op">=</span> <span class="st">"AmsiScanBuffer"</span><span class="op">,</span> CallingConvention <span class="op">=</span> CallingConvention<span class="op">.</span><span class="fu">StdCall</span><span class="op">)]</span></span>
<span id="cb6-10"><a href="#cb6-10" aria-hidden="true" tabindex="-1"></a><span class="kw">internal</span> <span class="kw">static</span> <span class="kw">extern</span> <span class="dt">int</span> <span class="fu">AmsiScanBuffer</span><span class="op">(</span></span>
<span id="cb6-11"><a href="#cb6-11" aria-hidden="true" tabindex="-1"></a>System<span class="op">.</span><span class="fu">IntPtr</span> amsiContext<span class="op">,</span></span>
<span id="cb6-12"><a href="#cb6-12" aria-hidden="true" tabindex="-1"></a>System<span class="op">.</span><span class="fu">IntPtr</span> buffer<span class="op">,</span></span>
<span id="cb6-13"><a href="#cb6-13" aria-hidden="true" tabindex="-1"></a><span class="dt">uint</span> length<span class="op">,</span></span>
<span id="cb6-14"><a href="#cb6-14" aria-hidden="true" tabindex="-1"></a><span class="op">[</span>In<span class="op">][</span><span class="fu">MarshalAs</span><span class="op">(</span>UnmanagedType<span class="op">.</span><span class="fu">LPWStr</span><span class="op">)]</span> <span class="dt">string</span> contentName<span class="op">,</span></span>
<span id="cb6-15"><a href="#cb6-15" aria-hidden="true" tabindex="-1"></a>System<span class="op">.</span><span class="fu">IntPtr</span> amsiSession<span class="op">,</span></span>
<span id="cb6-16"><a href="#cb6-16" aria-hidden="true" tabindex="-1"></a><span class="kw">ref</span> AMSI_RESULT result<span class="op">);</span></span></code></pre></div>
<p>This native function takes the memory buffer for the script, its
length, a metadata string (e.g., file name), and outputs an AMSI scan
result. It's the bridge between PowerShell and the Windows Defender
antimalware engine.</p>
<h3 id="6-relaunch-attachdebuggertest-in-x64dbg">6. Relaunch
AttachDebuggerTest in x64dbg</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2012.png"
alt="image.png" /></p>
<h3
id="7-make-sure-x64dbg-is-in-a-running-state-and-then-press-enter-in-the-terminal-to-run-the-powershell-script-in-the-psrunspace">7.
Make sure x64dbg is in a running state and then press enter in the
terminal to run the PowerShell script in the PSRunspace</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2013.png"
alt="image.png" /></p>
<h3
id="8-run-the-application-to-run-the-whoami-via-the-psrunspace-within-attachdebuggertest">8.
Run the application to run the “whoami” via the PSRunspace within
AttachDebuggerTest</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2014.png"
alt="image.png" /></p>
<h3
id="9-via-the-symbols-tab-find-amsiscanbuffer-and-set-a-breakpoint">9.
Via the Symbols tab, find AmsiScanBuffer and set a breakpoint</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2015.png"
alt="image.png" /></p>
<h3
id="10-modify-attachdebuggertest-to-run-invoke-mimikatz-and-rebuild-solution">10.
Modify AttachDebuggerTest to run “Invoke-Mimikatz” and rebuild
solution</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2016.png"
alt="image.png" /></p>
<h3 id="11-relaunch-attachdebuggertest-in-x64dbg">11. Relaunch
AttachDebuggerTest in x64dbg</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2012.png"
alt="image.png" /></p>
<h3
id="11-step-through-the-program-until-you-hit-the-breakpoint-for-amsiscancontent-and-verify-invoke-mimikatz-is-being-passed-as-a-parameter">11.
Step through the program until you hit the breakpoint for
AmsiScanContent and verify “Invoke-Mimikatz” is being passed as a
parameter</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2017.png"
alt="image.png" /></p>
<h3
id="12-step-through-the-program-until-it-terminates-and-verify-the-parse-error-for-malicious-content-blocked-occurs">12.
Step through the program until it terminates and verify the parse error
for malicious content blocked occurs</h3>
<p><img src="/assets/images/solo-purple-teaming/supplement-determining-root-cause/image%2018.png"
alt="image.png" /></p>
</section>
</div>
