---
layout: default
title: "Building Advanced Correlation Engine - Part 1"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-1/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 1</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3
id="1-understanding-the-problem-correlation-by-coincidence"><strong>1.
Understanding the Problem: Correlation by Coincidence</strong></h3>
<ul>
<li><strong>Definition</strong>: Correlation by coincidence happens when
events are tied together just because they occur close in time or share
superficial similarities.</li>
<li><strong>Example</strong>: Detecting a process start + network
connection within 2 seconds could flag suspicious behavior — but also
normal system updates.</li>
<li><strong>Issue</strong>:
<ul>
<li>Leads to false positives.</li>
<li>Causes alert fatigue.</li>
<li>Misses real threats.</li>
</ul></li>
</ul>
<p><strong>Key takeaway</strong>: Build correlation based on
<em>meaningful relationships</em> between events, not just time
proximity.</p>
<hr />
<h3 id="2-correlation-by-relationships"><strong>2. Correlation by
Relationships</strong></h3>
<ul>
<li><strong>Better methods</strong>:
<ul>
<li><strong>Process GUID correlation</strong> – Link events from the
same process to reconstruct execution flows.</li>
<li><strong>Parent-child process relationships</strong> – e.g.,
<code>cmd.exe</code> spawned from <code>winword.exe</code> → possible
malicious macro.</li>
<li><strong>File hash correlation</strong> – Tie together events
involving the same file.</li>
<li><strong>User-based correlation</strong> – Track a single account
across multiple hosts or activities.</li>
</ul></li>
</ul>
<p><strong>Example</strong>:</p>
<p>If the same process GUID triggered:</p>
<ul>
<li><p><strong>Rule 100100</strong> – Unsigned binary loaded from
Downloads.</p></li>
<li><p><strong>Rule 100101</strong> – Network connection to suspicious
port.</p>
<p>…within 2 seconds → High-fidelity detection.</p></li>
</ul>
<hr />
<h3 id="3-advanced-time-based-and-user-based-correlation"><strong>3.
Advanced Time-Based and User-Based Correlation</strong></h3>
<ul>
<li><strong>Time-based</strong>:
<ul>
<li>Group repeated failed logins → successful compromise.</li>
<li>Detect rapid lateral movement.</li>
</ul></li>
<li><strong>User-based</strong>:
<ul>
<li>Service accounts doing unusual actions.</li>
<li>Non-admin suddenly using admin tools like PsExec.</li>
</ul></li>
</ul>
<hr />
<h3 id="4-advanced-correlation-engine-ace-architecture"><strong>4.
Advanced Correlation Engine (ACE) Architecture</strong></h3>
<ul>
<li><strong>Purpose</strong>: Extend Wazuh’s built-in detection
capabilities.</li>
<li><strong>How it works</strong>:
<ol type="1">
<li>Monitors <code>alerts.json</code> in real-time.</li>
<li>Extracts key fields: <code>rule_id</code>, <code>timestamp</code>,
<code>process_guid</code>.</li>
<li>Buffers alerts in memory grouped by process GUID.</li>
<li>Correlates if <strong>Rule A (100100)</strong> and <strong>Rule B
(100101)</strong> fire for the same process within a time window.</li>
<li>Generates a correlated alert → feeds it back into Wazuh.</li>
</ol></li>
</ul>
<hr />
<h3 id="5-setting-up-the-python-script"><strong>5. Setting Up the Python
Script</strong></h3>
<p><strong>Location</strong>:
<code>/opt/wazuh_tools/wazuh_ace.py</code></p>
<p><strong>Imports</strong>:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="im">import</span> json</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="im">import</span> time</span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a><span class="im">from</span> collections <span class="im">import</span> defaultdict, deque</span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a><span class="im">from</span> dateutil <span class="im">import</span> parser <span class="im">as</span> dt_parser</span></code></pre></div>
<ul>
<li><code>json</code> → parse Wazuh alert data.</li>
<li><code>time</code> → sleep &amp; timing.</li>
<li><code>defaultdict</code> + <code>deque</code> → store recent
events.</li>
<li><code>dt_parser</code> → convert timestamps into comparable
objects.</li>
</ul>
<p><strong>Constants</strong>:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>ALERTS_FILE <span class="op">=</span> <span class="st">&quot;/var/ossec/logs/alerts/alerts.json&quot;</span></span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a>RULE_A <span class="op">=</span> <span class="dv">100100</span></span>
<span id="cb2-3"><a href="#cb2-3" aria-hidden="true" tabindex="-1"></a>RULE_B <span class="op">=</span> <span class="dv">100101</span></span>
<span id="cb2-4"><a href="#cb2-4" aria-hidden="true" tabindex="-1"></a>TIME_WINDOW <span class="op">=</span> <span class="dv">2</span>  <span class="co"># seconds</span></span></code></pre></div>
<p><strong>Event Buffer</strong>:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>event_buffer <span class="op">=</span> defaultdict(<span class="kw">lambda</span>: deque(maxlen<span class="op">=</span><span class="dv">50</span>))</span></code></pre></div>
<p>Stores tuples of <code>(timestamp, rule_id)</code> for each process
GUID.</p>
<hr />
<h3 id="6-parsing-alerts"><strong>6. Parsing Alerts</strong></h3>
<p>Function: <code>parse_alert(line)</code></p>
<ol type="1">
<li>Parse JSON from each log line.</li>
<li>Extract:
<ul>
<li><code>rule_id</code></li>
<li><code>process_guid</code> (<code>ProcessGuid</code> in
<code>win.eventdata</code>)</li>
<li><code>timestamp</code></li>
</ul></li>
<li>Return <code>(rule_id, process_guid, timestamp)</code> or
<code>(None, None, None)</code> if invalid.</li>
<li>Use <code>try/except</code> to handle malformed JSON without
crashing.</li>
</ol>
<hr />
<h3 id="7-main-correlation-function"><strong>7. Main Correlation
Function</strong></h3>
<p>Function: <code>correlate_events()</code></p>
<ol type="1">
<li>Open <code>alerts.json</code> in <strong>read mode</strong>.</li>
<li>Seek to the <strong>end of file</strong>.</li>
<li>Loop indefinitely (<code>while True</code>):
<ul>
<li>Read a new line.</li>
<li>If no new line → <code>time.sleep(0.5)</code> and retry.</li>
<li>Parse the alert.</li>
<li>If <code>rule_id</code> or <code>guid</code> missing → skip.</li>
</ul></li>
<li>Append <code>(timestamp, rule_id)</code> to the buffer for that
GUID.</li>
<li>Retrieve all events for that GUID → filter those within
<code>TIME_WINDOW</code>.</li>
<li>If both <strong>RULE_A</strong> and <strong>RULE_B</strong> exist in
recent events → print correlated alert.</li>
</ol>
<hr />
<h3 id="8-running-the-script"><strong>8. Running the
Script</strong></h3>
<p>In <code>__main__</code> block:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="cf">if</span> <span class="va">__name__</span> <span class="op">==</span> <span class="st">&quot;__main__&quot;</span>:</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a>    <span class="bu">print</span>(<span class="ss">f&quot;Monitoring </span><span class="sc">{</span>ALERTS_FILE<span class="sc">}</span><span class="ss"> for correlations...&quot;</span>)</span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a>    correlate_events()</span></code></pre></div>
<p>Make executable:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> chmod +x wazuh_ace.py</span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> wazuh_ace.py</span></code></pre></div>
<hr />
<h3 id="9-debugging--troubleshooting"><strong>9. Debugging &amp;
Troubleshooting</strong></h3>
<ul>
<li><strong>Issue</strong>: Script not detecting correlation.</li>
<li><strong>Method</strong>: Use print statements to check:
<ul>
<li>If alerts are being parsed.</li>
<li>If <code>rule_id</code> and <code>guid</code> are captured.</li>
</ul></li>
<li><strong>Bug found</strong>: Time window too strict (<code>2s</code>
with <code>0.5s</code> sleep missed some matches).</li>
<li><strong>Fix</strong>: Increase <code>TIME_WINDOW</code> to
<code>5s</code> for better matching.</li>
</ul>
<hr />
<h3 id="10-final-testing"><strong>10. Final Testing</strong></h3>
<ul>
<li>Re-run simulated attack payload on assumed breached host.</li>
<li>Confirm correlated alerts appear:</li>
</ul>
<pre><code>[ALERT] Correlated possible C2 activity: Rule 100100 &amp; Rule 100101 seen for GUID &lt;process_guid&gt; within 5 seconds at &lt;timestamp&gt;
</code></pre>
<ul>
<li>Remove debug print statements once working.</li>
</ul>
<hr />
<h3 id="key-lessons-for-students"><strong>Key Lessons for
Students</strong></h3>
<ul>
<li>Avoid <strong>coincidence-based</strong> correlation; use meaningful
event relationships.</li>
<li><strong>Process GUID correlation</strong> is powerful for
reconstructing attack chains.</li>
<li><strong>Small time windows</strong> risk missing events; balance
precision vs. detection success.</li>
<li><strong>Print debugging</strong> is simple but effective in script
troubleshooting.</li>
<li>Feeding correlated results back into Wazuh enhances detection
capabilities.</li>
</ul>
</section>
</div>
