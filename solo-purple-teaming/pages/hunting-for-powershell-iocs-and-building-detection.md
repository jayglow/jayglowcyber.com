---
layout: default
title: "Hunting For PowerShell IOCs and Building Detection"
permalink: /solo-purple-teaming/hunting-for-powershell-iocs-and-building-detection/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Hunting For PowerShell IOCs and Building Detection</h1>
</section>
<section class="spt-content">
<h1 id="hunting-for-powershell-iocs-and-building-detections">Hunting For
PowerShell IOCs and Building Detections</h1>
<p>Owner: Mike Sterrett</p>
<h2 id="1-understanding-the-goal"><strong>1. Understanding the
Goal</strong></h2>
<p>In this lab, we will:</p>
<ul>
<li>Hunt for evidence of malicious PowerShell activity from
<strong>Attack Path Level Zero</strong>.</li>
<li>Learn how <strong>PowerShell Script Block Logging</strong> works and
why it’s essential for defenders.</li>
<li>Use Wazuh to:
<ol type="1">
<li>Search for suspicious PowerShell activity.</li>
<li>Identify suspicious script content.</li>
<li>Create a <strong>custom Wazuh rule</strong> to detect it.</li>
<li>Test the detection by re-running our simulated attack.</li>
</ol></li>
</ul>
<hr />
<h3 id="2-why-script-block-logging-is-important"><strong>2. Why Script
Block Logging is Important</strong></h3>
<ul>
<li><strong>Script Block Logging</strong> captures <strong>the entire
contents</strong> of executed PowerShell scripts — even dynamically
generated code.</li>
<li>Unlike basic logging, it detects:
<ul>
<li>Encoded or hidden execution.</li>
<li>Remote download-and-execute commands.</li>
<li>Attempts to disable security controls.</li>
</ul></li>
<li>For <strong>detection engineering</strong>, this gives you
behavior-based detection capability instead of relying only on:
<ul>
<li>Command-line flags.</li>
<li>Process names.</li>
</ul></li>
<li>In <strong>solo purple teaming</strong>, you can:
<ul>
<li>Simulate malicious PowerShell activity.</li>
<li>Verify whether your rules catch it.</li>
<li>Refine detections based on real telemetry.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-hunting-for-malicious-powershell-in-wazuh"><strong>3. Hunting
for Malicious PowerShell in Wazuh</strong></h3>
<p><strong>Steps:</strong></p>
<ol type="1">
<li><p><strong>Log in to Wazuh Manager.</strong></p></li>
<li><p>Navigate to <strong>Explore → Discover</strong> (this gives
access to all events).</p></li>
<li><p>Change the <strong>index pattern</strong> to:</p>
<pre><code>wazuh-archives-*
</code></pre>
<p>(This ensures you’re searching all stored events.)</p></li>
<li><p><strong>Filter for PowerShell Script Block events</strong> by
Event ID:</p>
<pre><code>data.win.system.eventID:4104
</code></pre>
<ul>
<li>4104 = PowerShell Script Block Logging event.</li>
<li>Example result: <strong>38 hits</strong> in our lab
environment.</li>
<li>In production: expect <strong>thousands to millions</strong> of
results.</li>
</ul></li>
<li><p>Use <strong>keyword searching</strong> to find suspicious
commands.</p>
<p>Example:</p>
<pre><code>invoke
</code></pre>
<ul>
<li>Found multiple <strong>Invoke-WebRequest</strong> instances.</li>
<li>Expand a result → Check <code>scriptblocktext</code> field.</li>
<li>The script in our lab:
<ul>
<li>Downloads <code>Apollo.exe</code> using
<code>Invoke-WebRequest</code>.</li>
<li>Loads it reflectively using <code>.NET Reflection</code>.</li>
</ul></li>
</ul></li>
</ol>
<p><strong>Why it’s suspicious:</strong></p>
<ul>
<li><code>Invoke-WebRequest</code> + reflective loading are common
malicious behaviors.</li>
</ul>
<hr />
<h3 id="4-creating-a-custom-wazuh-rule"><strong>4. Creating a Custom
Wazuh Rule</strong></h3>
<p><strong>4.1. Find the correct SID for Script Block Logs</strong></p>
<ul>
<li>Go to <strong>Server Management → Rules</strong>.</li>
<li>Search for “PowerShell”.</li>
<li>Identify:
<ul>
<li><code>91801</code> = Group of Windows PowerShell operational channel
rules.</li>
<li><code>91802</code> = Group for <strong>PowerShell Script Block
Logging</strong> rules.
<ul>
<li>Confirmed by comment: <em>"PowerShell script block rules"</em>.</li>
</ul></li>
</ul></li>
<li>We will use <strong>SID 91802</strong>.</li>
</ul>
<p><strong>4.2. Edit the Solo Purple Teaming rules file</strong></p>
<ul>
<li><p>Search for:</p>
<pre><code>solo_purple_teaming.xml
</code></pre></li>
<li><p>Add a new rule block:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">group</span><span class="ot"> name=</span><span class="st">&quot;solo_purple_teaming,windows,powershell&quot;</span>&gt;</span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100102&quot;</span><span class="ot"> level=</span><span class="st">&quot;15&quot;</span>&gt;</span>
<span id="cb5-3"><a href="#cb5-3" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">if_sid</span>&gt;91802&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb5-4"><a href="#cb5-4" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">match</span>&gt;invoke web request&lt;/<span class="kw">match</span>&gt;</span>
<span id="cb5-5"><a href="#cb5-5" aria-hidden="true" tabindex="-1"></a>        &lt;<span class="kw">description</span>&gt;Detected malicious PowerShell activity: Invoke-WebRequest&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb5-6"><a href="#cb5-6" aria-hidden="true" tabindex="-1"></a>    &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb5-7"><a href="#cb5-7" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">group</span>&gt;</span></code></pre></div>
<p><strong>Notes:</strong></p>
<ul>
<li>Group name: includes <code>solo_purple_teaming</code>,
<code>windows</code>, and <code>powershell</code>.</li>
<li>Rule ID: <code>100102</code> (next in sequence).</li>
<li>Level: <code>15</code> = critical/high fidelity.</li>
<li><code>if_sid</code>: ensures we only match Script Block events.</li>
<li><code>match</code>: simple regex for
<code>invoke web request</code>.</li>
</ul></li>
</ul>
<p><strong>4.3. Save and restart Wazuh Manager</strong></p>
<hr />
<h3 id="5-testing-the-rule"><strong>5. Testing the Rule</strong></h3>
<p><strong>Option 1 – Using <code>rule-test</code></strong></p>
<ul>
<li><p>Run:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cat</span> archives.json <span class="kw">|</span> <span class="fu">grep</span> <span class="st">&quot;invoke web request&quot;</span> <span class="kw">|</span> <span class="fu">tail</span> <span class="at">-n</span> 1 <span class="op">&gt;</span> test_event.json</span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="ex">/var/ossec/bin/wazuh-logtest</span> <span class="op">&lt;</span> test_event.json</span></code></pre></div></li>
<li><p><em>Note:</em> <code>rule-test</code> may not always behave
exactly like production detection.</p></li>
</ul>
<p><strong>Option 2 – Live Attack Simulation</strong></p>
<ul>
<li>Re-run the <strong>initial access payload</strong>.</li>
<li>In Wazuh, check:
<ul>
<li><p><strong>Solo Purple Teaming dashboard</strong>.</p></li>
<li><p><strong>Time filter</strong>: last 5 minutes.</p></li>
<li><p>Look for <strong>critical event</strong> with:</p>
<pre><code>Detected malicious PowerShell activity: Invoke-WebRequest
</code></pre></li>
</ul></li>
</ul>
<hr />
<h3 id="6-troubleshooting-notes"><strong>6. Troubleshooting
Notes</strong></h3>
<ul>
<li>Sometimes correlation events may not trigger immediately — ensure:
<ul>
<li>Rules are active.</li>
<li>Script Block Logging is enabled.</li>
<li>Manager is fully restarted.</li>
</ul></li>
<li>In production: you must <strong>thoroughly test</strong> before
deployment.</li>
</ul>
<hr />
<h3 id="7-next-challenge"><strong>7. Next Challenge</strong></h3>
<ul>
<li>Build detections for:
<ul>
<li><strong>Service enumeration</strong> activities from Attack Path
Level Zero.</li>
<li>PowerShell <strong>reflection-based loading</strong>.</li>
</ul></li>
</ul>
<hr />
<p><strong>Key Takeaways:</strong></p>
<ul>
<li><strong>Script Block Logging</strong> is vital for detecting
malicious PowerShell.</li>
<li>Hunting is about <strong>behavior</strong> recognition, not just
known signatures.</li>
<li>In Wazuh:
<ul>
<li>Use SIDs to target the right log type.</li>
<li>Use regex matching to pinpoint suspicious commands.</li>
</ul></li>
<li>Solo purple teaming accelerates detection tuning by leveraging known
attack paths.</li>
</ul>
</section>
</div>
