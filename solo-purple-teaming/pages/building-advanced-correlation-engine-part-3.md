---
layout: default
title: "Building Advanced Correlation Engine - Part 3"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-3/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 3</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h2 id="objective"><strong>Objective</strong></h2>
<p>In this lab, we will modify our <strong>Advanced Correlation
Engine</strong> so that correlation alerts are written to a log file in
<strong>JSON format</strong>.</p>
<p>This will allow us to:</p>
<ul>
<li>Monitor the alerts with the Wazuh Manager.</li>
<li>Create a <strong>custom decoder</strong>.</li>
<li>Parse these fields to generate custom log events.</li>
<li>Visualize correlation alerts in a dashboard.</li>
</ul>
<hr />
<h3 id="1-preparing-the-environment"><strong>1. Preparing the
Environment</strong></h3>
<ol type="1">
<li><p>Connect to the <strong>Wazuh Manager</strong> via SSH.</p></li>
<li><p>Navigate to the tools directory:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/wazuh-tools</span></code></pre></div></li>
<li><p>Open the <strong>Advanced Correlation Engine</strong> script for
editing:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi waza_ace.py</span></code></pre></div></li>
</ol>
<hr />
<h3 id="2-adding-json-log-output"><strong>2. Adding JSON Log
Output</strong></h3>
<p>We’ll modify the section of the script where the alert is matched and
printed to also write it to a file.</p>
<h3 id="21-open-the-file-for-writing"><strong>2.1 Open the file for
writing</strong></h3>
<ul>
<li><p>Use Python’s <code>with open()</code> to create or append to a
log file:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="cf">with</span> <span class="bu">open</span>(<span class="st">&quot;correlation.json&quot;</span>, <span class="st">&quot;a&quot;</span>) <span class="im">as</span> log_file:</span></code></pre></div></li>
<li><p>We are placing the log in the current directory for now
(shortcut), though <code>/var/log</code> would be the ideal
location.</p></li>
<li><p><strong>Note:</strong> This directory is protected — running the
script will require <code>sudo</code>.</p></li>
</ul>
<h3 id="22-write-the-json-object"><strong>2.2 Write the JSON
object</strong></h3>
<ul>
<li><p>Use <code>json.dump()</code> to write a dictionary of values:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a>json.dump({</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;timestamp&quot;</span>: ts.isoformat(),</span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;processGuid&quot;</span>: guid,</span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;ruleA&quot;</span>: rule_a,</span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;ruleB&quot;</span>: rule_b,</span>
<span id="cb4-6"><a href="#cb4-6" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;hostName&quot;</span>: host_name,</span>
<span id="cb4-7"><a href="#cb4-7" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;image&quot;</span>: image,</span>
<span id="cb4-8"><a href="#cb4-8" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;message&quot;</span>: <span class="ss">f&quot;Correlated possible C2 activity on host </span><span class="sc">{</span>host_name<span class="sc">}</span><span class="ss"> from image </span><span class="sc">{</span>image<span class="sc">}</span><span class="ss"> with process GUID </span><span class="sc">{</span>guid<span class="sc">}</span><span class="ss">&quot;</span></span>
<span id="cb4-9"><a href="#cb4-9" aria-hidden="true" tabindex="-1"></a>}, log_file)</span>
<span id="cb4-10"><a href="#cb4-10" aria-hidden="true" tabindex="-1"></a>log_file.write(<span class="st">&quot;</span><span class="ch">\n</span><span class="st">&quot;</span>)</span></code></pre></div></li>
<li><p><strong>Why these fields?</strong></p>
<ul>
<li><code>timestamp</code> – When the alert occurred.</li>
<li><code>processGuid</code> – Unique process identifier.</li>
<li><code>ruleA</code> and <code>ruleB</code> – Both base rules must
have fired.</li>
<li><code>hostName</code> – System involved.</li>
<li><code>image</code> – The process image file.</li>
<li><code>message</code> – Readable correlation description.</li>
</ul></li>
<li><p><strong>Note on fields:</strong></p>
<p>Destination IP and port are excluded for now because some alerts
(like image loads) don’t have them.</p>
<p>We’ll handle that in a future refactor.</p></li>
</ul>
<hr />
<h3 id="3-saving-and-running-the-script"><strong>3. Saving and Running
the Script</strong></h3>
<ol type="1">
<li><p>Save your changes and exit <code>vi</code>.</p></li>
<li><p>Run the script with sudo:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> python3 waza_ace.py</span></code></pre></div></li>
<li><p>The script will now listen for events and append correlation
alerts to <code>correlation.json</code>.</p></li>
</ol>
<hr />
<h3 id="4-testing-the-setup"><strong>4. Testing the Setup</strong></h3>
<ol type="1">
<li><p>Trigger a known correlation event by running the <strong>initial
access payload</strong> on the assumed-breached host.</p></li>
<li><p>Check Wazuh’s Discover/Overview tabs for the correlated rule
IDs.</p>
<ul>
<li>Remember: In a <strong>lab environment</strong>, there can be lag
before events appear.</li>
</ul></li>
<li><p>If no events show, press <strong>CTRL+C</strong> to stop the
script, then check the file:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cat</span> correlation.json</span></code></pre></div></li>
<li><p>You should see a JSON entry for the correlation event.</p></li>
</ol>
<hr />
<h3 id="5-verifying-json-format"><strong>5. Verifying JSON
Format</strong></h3>
<ul>
<li>Confirm that:
<ul>
<li>The log entry is valid JSON.</li>
<li>Special characters like <code>{}</code> inside strings are enclosed
in quotes.</li>
<li>Each event is on its own line (due to <code>\n</code> after
<code>json.dump()</code>).</li>
</ul></li>
</ul>
<hr />
<h3 id="6-possible-refactoring-for-future"><strong>6. Possible
Refactoring for Future</strong></h3>
<ul>
<li>Instead of passing tuples, store event data in a
<strong>dictionary</strong> and iterate over it to merge more fields
(e.g., destination IP, additional image loads) from both base
events.</li>
<li>This will allow for richer correlation data in the alert.</li>
</ul>
<hr />
<h3 id="7-next-steps"><strong>7. Next Steps</strong></h3>
<p>In the next lecture, we will:</p>
<ol type="1">
<li>Set up the correlation engine script as a <strong>service</strong>
so it runs automatically on server start.</li>
<li>Configure the Wazuh Manager to monitor the
<code>correlation.json</code> file.</li>
<li>Write a <strong>custom decoder</strong> to ingest these JSON alerts
into Wazuh.</li>
<li>Create a dashboard visualization for correlation alerts.</li>
</ol>
<hr />
<p>✅ <strong>Before continuing</strong>: Make sure your script is
correctly writing to <code>correlation.json</code> and that at least one
test event has been logged.</p>
</section>
</div>
