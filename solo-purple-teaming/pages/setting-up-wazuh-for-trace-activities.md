---
layout: default
title: "Setting Up Wazuh for Trace Activities"
permalink: /solo-purple-teaming/setting-up-wazuh-for-trace-activities/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up Wazuh for Trace Activities</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3 id="1-why-wazuh-is-used-in-this-course">1. <strong>Why Wazuh Is Used
in This Course</strong></h3>
<ul>
<li><strong>Purpose</strong>: Wazuh is chosen because it’s a
professional-grade <strong>open-source SIEM</strong> ideal for learning
and experimentation.</li>
<li><strong>Role in the course</strong>:
<ul>
<li>Provides a foundation for <strong>detection engineering</strong> and
<strong>threat hunting</strong>.</li>
<li>Not the sole focus, but central to blue team tracing labs.</li>
<li>Students may adapt the lab for a different SIEM if desired.</li>
</ul></li>
<li><strong>Core capabilities</strong>:
<ul>
<li>Log collection, correlation, and real-time alerting.</li>
<li>File integrity monitoring, vulnerability detection, and threat intel
integration.</li>
<li>Tight integration with <strong>Elastic Stack</strong> for
visualization and analysis.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-wazuh-rules-and-their-importance">2. <strong>Wazuh Rules and
Their Importance</strong></h3>
<ul>
<li><strong>Detection logic</strong>: Rules are written in XML and work
with decoders to parse logs.</li>
<li><strong>Components</strong>:
<ul>
<li><strong>Conditions</strong>: Keyword matches, thresholds,
patterns.</li>
<li><strong>Severity levels</strong>: Higher levels indicate more urgent
alerts.</li>
</ul></li>
<li><strong>Course focus</strong>:
<ul>
<li>We’ll work with <strong>levels 12–15</strong> (serious/actionable
alerts).</li>
<li>All <strong>custom rules</strong> start at <strong>level
12</strong>, adjusted based on severity and confidence.</li>
</ul></li>
<li><strong>Solo purple teaming value</strong>:
<ul>
<li>Translate red team actions directly into blue team detections.</li>
<li>Test and tune for high-fidelity alerts.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-saved-searches-for-blue-team-tracing">3. <strong>Saved
Searches for Blue Team Tracing</strong></h3>
<ul>
<li><strong>Definition</strong>: Pre-built queries in Wazuh/Kibana that
quickly filter specific events.</li>
<li><strong>Purpose</strong>:
<ul>
<li>Useful for dashboards, recurring investigations, and
monitoring.</li>
<li>Can feed alerts or hunting workflows.</li>
</ul></li>
<li><strong>Goal for this step</strong>:
<ul>
<li>Create a saved search to view alerts with <strong>rule.level &gt;
12</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="4-creating-the-saved-search">4. <strong>Creating the Saved
Search</strong></h3>
<p><strong>Steps</strong>:</p>
<ol type="1">
<li><p>In Wazuh Manager, click the <strong>hamburger menu</strong> →
<strong>Explore</strong> → <strong>Discover</strong>.</p></li>
<li><p><strong>Select index pattern</strong>:
<code>wazuh-alerts-*</code>.</p></li>
<li><p>In the search bar (DQL), enter:</p>
<pre><code>rule.level &gt; 12
</code></pre></li>
<li><p><strong>Add useful columns</strong>:</p>
<ul>
<li>Agent name</li>
<li>Rule description</li>
<li>Rule ID</li>
<li>Rule level</li>
<li>Rule groups</li>
</ul></li>
<li><p><strong>Reorder columns</strong> using the arrow buttons in the
column headers.</p></li>
<li><p>Review the results — e.g., <strong>33 alerts above level
12</strong>.</p></li>
<li><p><strong>Save search</strong>:</p>
<ul>
<li>Click <strong>Save</strong> → Title it
<code>Solo Purple Teaming - Alerts Above 12</code>.</li>
</ul></li>
</ol>
<hr />
<h3 id="5-identifying-and-tuning-a-noisy-rule">5. <strong>Identifying
and Tuning a Noisy Rule</strong></h3>
<ul>
<li>In the saved search, notice many alerts are <strong>false
positives</strong> from <strong>Rule ID: 92213</strong>.</li>
<li>Decision: Lower this rule’s severity to reduce noise before custom
rule building.</li>
</ul>
<hr />
<h3 id="6-overriding-the-rule">6. <strong>Overriding the
Rule</strong></h3>
<p><strong>Steps</strong>:</p>
<ol type="1">
<li><p>In Wazuh Manager UI:</p>
<ul>
<li>Go to <strong>Hamburger Menu</strong> → <strong>Server
Management</strong> → <strong>Rules</strong>.</li>
<li>Search for <code>92213</code>.</li>
<li>Open the rule file containing it.</li>
</ul></li>
<li><p><strong>Copy the rule</strong> into your local override file:</p>
<ul>
<li><p>SSH into the Wazuh Manager:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /var/ossec/etc/rules</span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi local_rules.xml</span></code></pre></div></li>
<li><p>Paste the <strong>group</strong> definition and the <strong>92213
rule</strong>.</p></li>
<li><p>Change:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">level</span>&gt;15&lt;/<span class="kw">level</span>&gt;</span></code></pre></div>
<p>to:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">level</span>&gt;12&lt;/<span class="kw">level</span>&gt;</span></code></pre></div></li>
<li><p>Save and exit: <code>Esc :wq</code>.</p></li>
</ul></li>
<li><p><strong>Restart Wazuh Manager</strong>:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart wazuh-manager</span></code></pre></div></li>
</ol>
<hr />
<h3 id="7-verifying-the-change">7. <strong>Verifying the
Change</strong></h3>
<ol type="1">
<li>Open <strong>Explore → Discover</strong> again.</li>
<li>Load the saved search.</li>
<li>Review alerts — noisy rule now shows level 12 instead of 15.</li>
<li>Confirm you have a cleaner baseline before writing custom
rules.</li>
</ol>
<hr />
<h3 id="8-why-this-matters">8. <strong>Why This Matters</strong></h3>
<ul>
<li>A <strong>clean baseline</strong> prevents false positives from
interfering with your purple teaming.</li>
<li>You can now <strong>focus on detecting</strong> your own simulated
attacker activities.</li>
<li>You’ll build <strong>custom rules</strong> that:
<ul>
<li>Match your offensive simulation.</li>
<li>Belong to clearly defined groups.</li>
<li>Maintain high detection fidelity.</li>
</ul></li>
</ul>
<hr />
<h3 id="9-next-steps">9. <strong>Next Steps</strong></h3>
<p>Before moving on:</p>
<ul>
<li>Ensure:
<ul>
<li>Wazuh is running.</li>
<li>Saved search for alerts above level 12 is available.</li>
<li>Rule 92213 has been tuned.</li>
</ul></li>
<li>Next lecture:
<ul>
<li>Build your <strong>first custom detection rule</strong> for attack
level 0 activities.</li>
<li>Later: Learn to <strong>bypass your own detections</strong> for red
team skill growth.</li>
</ul></li>
</ul>
</section>
</div>
