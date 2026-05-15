---
layout: default
title: "Build Custom Correlation Rule"
permalink: /solo-purple-teaming/build-custom-correlation-rule/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Build Custom Correlation Rule</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3 id="1-understanding-the-purpose-of-rule-correlation"><strong>1.
Understanding the Purpose of Rule Correlation</strong></h3>
<ul>
<li><strong>Goal</strong>: Combine two base detection rules into a
single, higher-fidelity alert.</li>
<li><strong>Why?</strong>
<ul>
<li>A single event (e.g., unsigned binary from Downloads
<strong>or</strong> a network connection to port 80) might not be
suspicious on its own.</li>
<li>But if both occur within a short time window (e.g., 2 seconds), it
may indicate <strong>Command and Control (C2)</strong> activity.</li>
</ul></li>
<li><strong>Benefit</strong>:
<ul>
<li>Reduces noise and false positives.</li>
<li>Surfaces more meaningful alerts.</li>
<li>Especially useful for solo purple teamers wanting high-fidelity
detections.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-how-wazuh-correlation-works"><strong>2. How Wazuh Correlation
Works</strong></h3>
<ul>
<li><strong>Wazuh’s built-in correlation is</strong>:
<ul>
<li><strong>Time-based &amp; coincidence-based</strong> — it looks for
events that occur within a certain time window from the same agent.</li>
</ul></li>
<li><strong>Limitations</strong>:
<ul>
<li>Does <strong>not</strong> verify if events came from the same
process (no matching on <code>ProcessGuid</code> or other fields).</li>
<li>Does <strong>not</strong> match on hostname, IP, or username unless
explicitly included.</li>
<li>Does <strong>not</strong> perform field-level joins — only looks for
events within the same timeframe.</li>
</ul></li>
<li><strong>Implication</strong>:
<ul>
<li>Good for simple behavioral stacking.</li>
<li>Not enough for detecting highly advanced or stealthy activity —
those require more sophisticated, field-aware correlation.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-defining-the-correlation-detection-logic"><strong>3. Defining
the Correlation Detection Logic</strong></h3>
<ul>
<li><p><strong>We want to trigger an alert when</strong>:</p>
<ul>
<li><p><strong>SID 100100</strong> (Unsigned binary from Downloads)</p>
<p><strong>AND</strong></p></li>
<li><p><strong>SID 100101</strong> (Network connection to ports 80 or
443)</p>
<p>occur within <strong>2 seconds</strong> from the same agent.</p></li>
</ul></li>
<li><p><strong>Outcome</strong>: Possible C2 activity alert.</p></li>
</ul>
<hr />
<h3 id="4-writing-the-correlation-rule"><strong>4. Writing the
Correlation Rule</strong></h3>
<ul>
<li><p><strong>Structure</strong>:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">group</span><span class="ot"> name=</span><span class="st">&quot;solo_purple_teaming_c2,windows,sysmon&quot;</span>&gt;</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>  &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100200&quot;</span><span class="ot"> level=</span><span class="st">&quot;13&quot;</span>&gt;</span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">if_match_group</span>&gt;solo_purple_teaming_c2&lt;/<span class="kw">if_match_group</span>&gt;</span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">frequency</span>&gt;2&lt;/<span class="kw">frequency</span>&gt;</span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">timeframe</span>&gt;2&lt;/<span class="kw">timeframe</span>&gt;</span>
<span id="cb1-6"><a href="#cb1-6" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;</span>
<span id="cb1-7"><a href="#cb1-7" aria-hidden="true" tabindex="-1"></a>      Correlation: 100100 and 100101 occurred within 2 seconds – Possible C2 activity</span>
<span id="cb1-8"><a href="#cb1-8" aria-hidden="true" tabindex="-1"></a>    &lt;/<span class="kw">description</span>&gt;</span>
<span id="cb1-9"><a href="#cb1-9" aria-hidden="true" tabindex="-1"></a>  &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb1-10"><a href="#cb1-10" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">group</span>&gt;</span></code></pre></div></li>
<li><p><strong>Key attributes</strong>:</p>
<ul>
<li><code>group name</code>: Groups related rules together.</li>
<li><code>rule id</code>: Jump to <code>100200</code> for correlation
rules.</li>
<li><code>level</code>: Set to <strong>13</strong> (Medium
severity).</li>
<li><code>frequency</code>: <code>2</code> means both events must occur
within timeframe.</li>
<li><code>timeframe</code>: <code>2</code> seconds.</li>
<li><code>if_match_group</code>: Matches the <strong>group</strong> that
contains both base rules.</li>
<li><code>description</code>: Clearly states what’s being detected.</li>
</ul></li>
</ul>
<hr />
<h3 id="5-implementing-in-wazuh-manager"><strong>5. Implementing in
Wazuh Manager</strong></h3>
<ol type="1">
<li><strong>Open Wazuh Manager</strong> → Navigate to <strong>Server
Management → Rules</strong>.</li>
<li><strong>Find your custom rule file</strong> (e.g.,
<code>solo_purple_teaming.xml</code>).</li>
<li><strong>Edit the file</strong> and paste your new correlation
<code>&lt;group&gt;</code> and <code>&lt;rule&gt;</code> block.</li>
<li><strong>Check syntax</strong>:
<ul>
<li>Fix special quote characters if pasted from other sources.</li>
<li>Ensure XML is properly closed.</li>
</ul></li>
<li><strong>Save changes</strong>.</li>
<li><strong>Restart Wazuh Manager</strong> → Wait for it to fully
reload.</li>
</ol>
<hr />
<h3 id="6-testing-the-correlation-rule"><strong>6. Testing the
Correlation Rule</strong></h3>
<ol type="1">
<li><strong>Run your initial access payload</strong> that triggers both
base rules.</li>
<li><strong>Confirm payload executes</strong> (e.g., unsigned binary
runs and makes network connection).</li>
<li><strong>Go to Wazuh → Discover → Search for Rule ID
<code>100200</code></strong>.</li>
<li>If no alert appears:
<ul>
<li>Wait briefly for event ingestion.</li>
<li>Check that the <code>if_match_group</code> exactly matches your base
rules’ group name.</li>
<li>Re-run the payload if needed.</li>
</ul></li>
<li><strong>When successful</strong>:
<ul>
<li>The correlation alert will fire.</li>
<li>This confirms Wazuh linked the two base rules within the defined
timeframe.</li>
</ul></li>
</ol>
<hr />
<h3 id="7-key-takeaways"><strong>7. Key Takeaways</strong></h3>
<ul>
<li><strong>Correlation reduces noise</strong> but relies on time-based
coincidence, not deep relational logic.</li>
<li><strong>For higher accuracy</strong>, combine Wazuh correlation
with:
<ul>
<li>Process-level matching (via Sysmon fields).</li>
<li>External enrichment or advanced analytics.</li>
</ul></li>
<li><strong>Lab checkpoint</strong>:
<ul>
<li>Before moving on, ensure you have a <strong>custom correlation
alert</strong> firing for unsigned binary + network connection
combination.</li>
</ul></li>
</ul>
<hr />
<p>In the <strong>next lecture</strong>, you’ll explore the
<strong>pitfalls of time-based correlation</strong> and begin designing
an <strong>advanced alerting engine</strong> that goes beyond Wazuh’s
built-in capabilities.</p>
</section>
</div>
