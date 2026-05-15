---
layout: default
title: "Building Detections for IWR and RunMRU"
permalink: /solo-purple-teaming/building-detections-for-iwr-and-runmru/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Detections for IWR and RunMRU</h1>
</section>
<section class="spt-content">
<p>Owner: Josh</p>
<h1 id="ioc-diagram">IOC Diagram</h1>
<p>We have determined that using the Run dialog to execute our malicious
PowerShell command invoke Explorer.exe which creates registry keys to
store a history of commands and the order of those commands.
Explorer.exe also spawns PowerShell.exe which executes our malicious
command to reflective load apollo.exe. When using execute_assembly, a
sacrificial process is spawned set by the spawn_x64 property, in our
case smart screen.exe, and injects a CLR loader that reflective loads
the assembly identified by the passed argument and executes. Named pipes
are used for IPC and since Apollo uses pipe security, the transport is
brokered through SMB involving the System process.</p>
<p><img src="Building%20Detections%20for%20IWR%20and%20RunMRU/image.png"
alt="image.png" /></p>
<h2 id="improving-invoke-webrequest-detection">Improving
Invoke-WebRequest Detection</h2>
<p>This detection uses keyword matching, which is inherently brittle and
easy for a skilled threat actor to evade. In a Solo Purple Teaming
workflow, however, even these simple detections have strategic value.
They’re quick wins that help you learn and practice both the blue and
red sides of Detection Engineering—understanding how defenders build
rules and how attackers work to bypass them. By introducing more
variables into the Detection Engineering stack, you force the adversary
to adapt, increasing their workload and the chances they’ll make an
OPSEC slip. Each added hurdle—no matter how small—becomes an opportunity
to observe, learn, and refine your defenses, creating a feedback loop
where detection drives adversary exposure, and adversary actions drive
detection improvements.</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100102&quot;</span><span class="ot"> level=</span><span class="st">&quot;15&quot;</span>&gt;</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;91802&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.scriptBlockText&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)iwr|Invoke-WebRequest&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Detected Malicious PowerShell - Invoke-WebRequest&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">rule</span>&gt;</span></code></pre></div>
<h2 id="creating-runmru-detection-for-powershell">Creating RunMRU
Detection for PowerShell</h2>
<p>This is another keyword-based detection, but it poses a slightly
greater challenge for threat actors to evade because
the <strong>PowerShell</strong> keyword is required when launching
PowerShell from the Run dialog. While adversaries can bypass it by
switching to alternative execution mechanisms, that’s exactly the goal
of Detection Engineering—forcing them to adapt and potentially make
mistakes.</p>
<p>The primary drawback is that if system administrators regularly use
the Run dialog to launch PowerShell, this detection can produce a high
volume of false positives. Fortunately, this is often solvable through
minor behavior changes, such as encouraging admins to use alternative
launch methods. In my experience, very few administrators execute
PowerShell commands directly from the Run dialog, so the impact is
typically minimal.</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100108&quot;</span><span class="ot"> level=</span><span class="st">&quot;15&quot;</span>&gt;</span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;61615&lt;/<span class="kw">if_sid</span>&gt; </span>
<span id="cb2-3"><a href="#cb2-3" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.targetObject&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)runmru&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb2-4"><a href="#cb2-4" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.details&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)powershell&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb2-5"><a href="#cb2-5" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Malicious RunMRU Key Details&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb2-6"><a href="#cb2-6" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">rule</span>&gt;</span></code></pre></div>
<h2 id="bonus-detection-for-suspicious-process-access">BONUS: Detection
for Suspicious Process Access</h2>
<p>This rule on its own will be noisy, but when correlated with other
detections, it can become a powerful tool for identifying malicious
activity. Consider pairing it with <strong>pipe creation
events</strong> from the target process and <strong>network connection
events</strong>from the source process. By linking these signals, you
transform high-volume, low-fidelity alerts into high-confidence
indicators of suspicious behavior.</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100109&quot;</span><span class="ot"> level=</span><span class="st">&quot;12”&gt;</span></span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a><span class="st">        </span><span class="er">&lt;</span><span class="st">if_sid&gt;61612</span><span class="er">&lt;</span><span class="st">/if_sid&gt; </span></span>
<span id="cb3-3"><a href="#cb3-3" aria-hidden="true" tabindex="-1"></a><span class="st">        </span><span class="er">&lt;</span><span class="st">field name=&quot;</span><span class="ot">win.eventdata.grantedAccess</span><span class="er">&quot;</span> <span class="er">type</span><span class="ot">=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)0x1fffff&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb3-4"><a href="#cb3-4" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Suspicious Process Access&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb3-5"><a href="#cb3-5" aria-hidden="true" tabindex="-1"></a><span class="er">&lt;</span>/rule&gt;</span></code></pre></div>
<h1
id="full-custom-solo-purple-teaming-configuration-file-with-out-bonus-detection">Full
Custom Solo Purple Teaming Configuration File With Out Bonus
Detection</h1>
<div class="sourceCode" id="cb4"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="co">&lt;!-- Modify it at your will. --&gt;</span></span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">group</span><span class="ot"> name=</span><span class="st">&quot;solo_purple_teaming_c2,windows,sysmon&quot;</span>&gt;</span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100100&quot;</span><span class="ot"> level=</span><span class="st">&quot;12&quot;</span>&gt;</span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;61609&lt;/<span class="kw">if_sid</span>&gt; </span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.ImageLoaded&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)[c-z]:\\\\Users\\\\.+|[c-z]:\\\\Windows\\\\Temp\\\\.+&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-6"><a href="#cb4-6" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.Signed&quot;</span>&gt;false&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-7"><a href="#cb4-7" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Unsigned image loaded from user or temp directory&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-8"><a href="#cb4-8" aria-hidden="true" tabindex="-1"></a>    &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-9"><a href="#cb4-9" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100101&quot;</span><span class="ot"> level=</span><span class="st">&quot;12&quot;</span>&gt;</span>
<span id="cb4-10"><a href="#cb4-10" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;61605&lt;/<span class="kw">if_sid</span>&gt; </span>
<span id="cb4-11"><a href="#cb4-11" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.DestinationPort&quot;</span>&gt;80|443&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-12"><a href="#cb4-12" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.Image&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)[c-z]:\\\\Users\\\\.+|[c-z]:\\\\Windows\\\\Temp\\\\.+&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-13"><a href="#cb4-13" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Network Connect to Common C2 Ports From Downloads or Users&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-14"><a href="#cb4-14" aria-hidden="true" tabindex="-1"></a>    &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-15"><a href="#cb4-15" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100108&quot;</span><span class="ot"> level=</span><span class="st">&quot;15&quot;</span>&gt;</span>
<span id="cb4-16"><a href="#cb4-16" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;61615&lt;/<span class="kw">if_sid</span>&gt; </span>
<span id="cb4-17"><a href="#cb4-17" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.targetObject&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)runmru&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-18"><a href="#cb4-18" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.details&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)powershell&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-19"><a href="#cb4-19" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Possible Malicious RunMRU Value Set&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-20"><a href="#cb4-20" aria-hidden="true" tabindex="-1"></a>    &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-21"><a href="#cb4-21" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">group</span>&gt;</span>
<span id="cb4-22"><a href="#cb4-22" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">group</span><span class="ot"> name=</span><span class="st">&quot;solo_purple_teaming_c2_correlation&quot;</span>&gt;</span>
<span id="cb4-23"><a href="#cb4-23" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100200&quot;</span><span class="ot"> level=</span><span class="st">&quot;13&quot;</span><span class="ot"> frequency=</span><span class="st">&quot;2&quot;</span><span class="ot"> timeframe=</span><span class="st">&quot;2&quot;</span>&gt;</span>
<span id="cb4-24"><a href="#cb4-24" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_matched_group</span>&gt;solo_purple_teaming_c2&lt;/<span class="kw">if_matched_group</span>&gt;</span>
<span id="cb4-25"><a href="#cb4-25" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;Correlation: 100100 and 100101 occurred within 2 seconds. Possible C2 Activity&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-26"><a href="#cb4-26" aria-hidden="true" tabindex="-1"></a>  &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-27"><a href="#cb4-27" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">group</span>&gt;</span>
<span id="cb4-28"><a href="#cb4-28" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">group</span><span class="ot"> name=</span><span class="st">&quot;solo_purple_teaming, windows, powershell&quot;</span>&gt;</span>
<span id="cb4-29"><a href="#cb4-29" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100103&quot;</span><span class="ot"> level=</span><span class="st">&quot;15&quot;</span>&gt;</span>
<span id="cb4-30"><a href="#cb4-30" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;91802&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb4-31"><a href="#cb4-31" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">match</span>&gt;Reflection&lt;/<span class="kw">match</span>&gt;</span>
<span id="cb4-32"><a href="#cb4-32" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Detected Malicious PowerShell Reflection&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-33"><a href="#cb4-33" aria-hidden="true" tabindex="-1"></a>    &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-34"><a href="#cb4-34" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100102&quot;</span><span class="ot"> level=</span><span class="st">&quot;15&quot;</span>&gt;</span>
<span id="cb4-35"><a href="#cb4-35" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;91802&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb4-36"><a href="#cb4-36" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.scriptBlockText&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;(?i)Invoke-Webrequest|iwr&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-37"><a href="#cb4-37" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Detected Malicious PowerShell - Invoke-WebRequest&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-38"><a href="#cb4-38" aria-hidden="true" tabindex="-1"></a>    &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-39"><a href="#cb4-39" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">group</span>&gt;</span>
<span id="cb4-40"><a href="#cb4-40" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">group</span><span class="ot"> name=</span><span class="st">&quot;solo_purple_teaming,windows,sysmon,suspicious_commands&quot;</span>&gt;</span>
<span id="cb4-41"><a href="#cb4-41" aria-hidden="true" tabindex="-1"></a>  &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100104&quot;</span><span class="ot"> level=</span><span class="st">&quot;14&quot;</span>&gt;</span>
<span id="cb4-42"><a href="#cb4-42" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">if_sid</span>&gt;92032&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb4-43"><a href="#cb4-43" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.commandLine&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;.*sc.*(qc|sdshow).*&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-44"><a href="#cb4-44" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">options</span>&gt;no_full_log&lt;/<span class="kw">options</span>&gt;</span>
<span id="cb4-45"><a href="#cb4-45" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;Service Enumeration activity spawned via cmd shell execution&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-46"><a href="#cb4-46" aria-hidden="true" tabindex="-1"></a>  &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-47"><a href="#cb4-47" aria-hidden="true" tabindex="-1"></a>  &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100107&quot;</span><span class="ot"> level=</span><span class="st">&quot;14&quot;</span>&gt;</span>
<span id="cb4-48"><a href="#cb4-48" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">if_sid</span>&gt;92032&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb4-49"><a href="#cb4-49" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.commandLine&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;.*wmic.*service.*get.*&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-50"><a href="#cb4-50" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">options</span>&gt;no_full_log&lt;/<span class="kw">options</span>&gt;</span>
<span id="cb4-51"><a href="#cb4-51" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;Service Enumeration activity spawned via cmd shell execution&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-52"><a href="#cb4-52" aria-hidden="true" tabindex="-1"></a>  &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-53"><a href="#cb4-53" aria-hidden="true" tabindex="-1"></a>  &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100105&quot;</span><span class="ot"> level=</span><span class="st">&quot;13&quot;</span>&gt;</span>
<span id="cb4-54"><a href="#cb4-54" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">if_sid</span>&gt;92032&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb4-55"><a href="#cb4-55" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.commandLine&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;.*net.*localgroup.*administrators.*&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-56"><a href="#cb4-56" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">options</span>&gt;no_full_log&lt;/<span class="kw">options</span>&gt;</span>
<span id="cb4-57"><a href="#cb4-57" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;Local group enumeration for administrators activity spawned via cmd shell execution&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-58"><a href="#cb4-58" aria-hidden="true" tabindex="-1"></a>  &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-59"><a href="#cb4-59" aria-hidden="true" tabindex="-1"></a>  &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100106&quot;</span><span class="ot"> level=</span><span class="st">&quot;13&quot;</span>&gt;</span>
<span id="cb4-60"><a href="#cb4-60" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">if_sid</span>&gt;92032&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb4-61"><a href="#cb4-61" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;win.eventdata.commandLine&quot;</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;.*whoami.*/priv.*&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb4-62"><a href="#cb4-62" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">options</span>&gt;no_full_log&lt;/<span class="kw">options</span>&gt;</span>
<span id="cb4-63"><a href="#cb4-63" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;User privilege enumeration using whoami spawned via cmd shell execution&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb4-64"><a href="#cb4-64" aria-hidden="true" tabindex="-1"></a>  &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb4-65"><a href="#cb4-65" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">group</span>&gt;</span></code></pre></div>
</section>
</div>
