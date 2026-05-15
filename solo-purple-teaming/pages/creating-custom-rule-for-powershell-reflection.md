---
layout: default
title: "Creating Custom Rule for PowerShell Reflection"
permalink: /solo-purple-teaming/creating-custom-rule-for-powershell-reflection/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Creating Custom Rule for PowerShell Reflection</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3 id="objective"><strong>Objective</strong></h3>
<p>In this lab, you’ll create a <strong>custom Wazuh rule</strong> to
detect <strong>PowerShell reflection activity</strong> using Script
Block Logging data. This is part of building high-fidelity detections
for our initial access payloads.</p>
<hr />
<h3 id="1-rule-overview"><strong>1. Rule Overview</strong></h3>
<p>Our rule will:</p>
<ul>
<li>Have a <strong>Rule ID</strong>: <code>100103</code></li>
<li><strong>Level</strong>: <code>15</code> (high severity)</li>
<li>Match against the <strong>if_sid</strong> <code>91802</code>
(PowerShell Script Block Logging event).</li>
<li>Search for the keyword <strong>"reflection"</strong> in the event
data.</li>
<li>Include a descriptive message for easy identification.</li>
</ul>
<hr />
<h3 id="2-navigating-to-the-custom-rules-file"><strong>2. Navigating to
the Custom Rules File</strong></h3>
<ol type="1">
<li><p><strong>Log into the Wazuh Manager</strong>.</p></li>
<li><p>Go to <strong>Server Management</strong>.</p></li>
<li><p>Select <strong>Rules</strong>.</p></li>
<li><p>Locate and open your <strong>custom rules file</strong> — in this
case:</p>
<p><code>solo_purple_teaming_rules.xml</code></p></li>
<li><p>Find the <strong>PowerShell grouping</strong> section where you
want to add the new rule.</p></li>
</ol>
<hr />
<h3 id="3-creating-the-rule"><strong>3. Creating the Rule</strong></h3>
<p>Add a new <code>&lt;rule&gt;</code> block inside the PowerShell
group:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100103&quot;</span><span class="ot"> level=</span><span class="st">&quot;15&quot;</span>&gt;</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">if_sid</span>&gt;91802&lt;/<span class="kw">if_sid</span>&gt;</span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">match</span>&gt;reflection&lt;/<span class="kw">match</span>&gt;</span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;Detected PowerShell reflection&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">rule</span>&gt;</span></code></pre></div>
<p><strong>Key points:</strong></p>
<ul>
<li><code>id</code> must be unique and within your chosen custom rules
range.</li>
<li><code>level</code> indicates the severity (15 is very high).</li>
<li><code>if_sid</code> refers to the base event ID for Script Block
Logging.</li>
<li><code>match</code> searches the event data for our keyword.</li>
<li><code>description</code> is what you’ll see in alerts.</li>
</ul>
<hr />
<h3 id="4-saving-and-restarting-wazuh"><strong>4. Saving and Restarting
Wazuh</strong></h3>
<ul>
<li><p>Save the changes to your rules file.</p></li>
<li><p>Restart the Wazuh Manager to apply changes:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart wazuh-manager</span></code></pre></div></li>
</ul>
<hr />
<h3 id="5-testing-the-rule"><strong>5. Testing the Rule</strong></h3>
<ol type="1">
<li><strong>Run the Initial Access Payload</strong>
<ul>
<li>Double-click on <strong>Goodbye MZ</strong> to execute the
payload.</li>
</ul></li>
<li><strong>Search for the Alert in Wazuh</strong>
<ul>
<li><p>Go to <strong>Discover</strong> in Wazuh.</p></li>
<li><p>Search by Rule ID:</p>
<pre><code>rule.id:100103
</code></pre></li>
<li><p>Verify that the alert appears for the reflection event.</p></li>
</ul></li>
<li><strong>Inspect the Alert</strong>
<ul>
<li>Expand the event details.</li>
<li>Confirm that the <strong>description</strong> says: <em>Detected
PowerShell reflection</em>.</li>
</ul></li>
</ol>
<hr />
<h3 id="6-next-steps"><strong>6. Next Steps</strong></h3>
<ul>
<li>Ensure you have <strong>at least two custom PowerShell
rules</strong> before proceeding:
<ol type="1">
<li>One for <code>Invoke-WebRequest</code> (from the previous
lecture).</li>
<li>One for <strong>reflection</strong> (this lecture).</li>
</ol></li>
<li>In the next session, you’ll build a <strong>Solo Purple Teaming
Dashboard</strong> from scratch to visualize these detections.</li>
</ul>
</section>
</div>
