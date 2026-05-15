---
layout: default
title: "Blue Team Explore - Enabling Sysmon Event IDs 10,"
permalink: /solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-10/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Enabling Sysmon Event IDs 10,</h1>
</section>
<section class="spt-content">
<h1 id="blue-team-explore---enabling-sysmon-event-ids-10-17--18">Blue
Team Explore - Enabling Sysmon Event IDs 10, 17 &amp; 18</h1>
<h2 id="objective"><strong>Objective</strong></h2>
<p>In this lab, you will enable Sysmon logging for:</p>
<ul>
<li><strong>Event ID 10</strong> – Process Access</li>
<li><strong>Event ID 17</strong> – Pipe Created</li>
<li><strong>Event ID 18</strong> – Pipe Connected</li>
</ul>
<p>You will configure Sysmon to capture all process access events with
full access rights, then verify event ingestion in Wazuh.</p>
<hr />
<h3 id="1-prepare-the-sysmon-configuration"><strong>1. Prepare the
Sysmon Configuration</strong></h3>
<ol type="1">
<li>Log into the <strong>Soon Breach</strong> host.</li>
<li>Navigate to the <strong>base Sysmon configuration</strong> file
located in your <strong>Downloads</strong> folder.</li>
<li>Locate the <strong>Event ID 10</strong> rule group.</li>
<li>Add an XML entry to match <strong>Granted Access</strong> with the
value <code>0x1fffff</code> (full process access).
<ul>
<li>Use the <code><grantedAccess></code> tag.</li>
<li>Set the <strong>condition</strong> to <code>"is"</code>.</li>
<li>Set the <strong>match value</strong> to <code>0x1fffff</code>.</li>
</ul></li>
</ol>
<hr />
<h3 id="2-apply-the-updated-sysmon-config"><strong>2. Apply the Updated
Sysmon Config</strong></h3>
<ol type="1">
<li><p>Open <strong>Command Prompt as Administrator</strong>.</p></li>
<li><p>Change directory to:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> C:<span class="dt">\U</span>sers<span class="dt">\r</span>lin<span class="dt">\D</span>ownloads</span></code></pre></div></li>
<li><p>Update Sysmon’s configuration:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sysmon64</span> <span class="at">-c</span> baseconfig.xml</span></code></pre></div></li>
<li><p>Confirm that the configuration file is validated and
updated.</p></li>
</ol>
<hr />
<h3 id="3-verify-event-id-10-in-wazuh"><strong>3. Verify Event ID 10 in
Wazuh</strong></h3>
<ol type="1">
<li><p>Open the <strong>Wazuh console</strong>.</p></li>
<li><p>Go to <strong>Discover</strong> under
<strong>Explore</strong>.</p></li>
<li><p>Switch your <strong>index pattern</strong> to
<strong>archives</strong>.</p></li>
<li><p>Search for:</p>
data.win.system.eventID: 10
</li>
<li><p>Confirm process access events are appearing from the
<code>ecoin-wrk-rlin</code> host.</p></li>
<li><p>Expand an event and verify that the <strong>Granted
Access</strong> field shows:</p>
0x1fffff
</li>
</ol>
<hr />
<h3 id="4-resolve-field-mapping-warnings"><strong>4. Resolve Field
Mapping Warnings</strong></h3>
<ol type="1">
<li>In Wazuh, navigate to <strong>Dashboard Management</strong> →
<strong>Index Patterns</strong>.</li>
<li>Select <strong>Wazuh archives</strong>.</li>
<li>Click <strong>Refresh field list</strong>.</li>
<li>Return to <strong>Discover</strong>, reapply your Event ID 10
filter, and confirm there are no warnings.</li>
</ol>
<hr />
<h3 id="5-enable-event-ids-17-and-18"><strong>5. Enable Event IDs 17 and
18</strong></h3>
<ol type="1">
<li><p>Return to your Sysmon config file on the <strong>Soon
Breach</strong> host.</p></li>
<li><p>Modify the relevant section by changing <strong>include</strong>
to <strong>exclude</strong> as required for your rule
structure.</p></li>
<li><p>Save the file.</p></li>
<li><p>In <strong>Command Prompt as Administrator</strong>, reapply the
config:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sysmon64</span> <span class="at">-c</span> baseconfig.xml</span></code></pre></div></li>
</ol>
<hr />
<h3 id="6-verify-events-17-and-18"><strong>6. Verify Events 17 and
18</strong></h3>
<ol type="1">
<li><p>In Wazuh Discover, filter for:</p>
data.win.system.eventID: 17

<p>Confirm <strong>Pipe Created</strong> events appear.</p></li>
<li><p>Then filter for:</p>
data.win.system.eventID: 18

<p>Confirm <strong>Pipe Connected</strong> events appear.</p></li>
</ol>
<hr />
<h3 id="7-final-verification"><strong>7. Final
Verification</strong></h3>
<p>Ensure all three event types are present in Wazuh:</p>
<ul>
<li><strong>Event ID 10</strong> – Process Access (Granted Access =
<code>0x1fffff</code>)</li>
<li><strong>Event ID 17</strong> – Pipe Created</li>
<li><strong>Event ID 18</strong> – Pipe Connected</li>
</ul>
<hr />
<p>✅ <strong>Before moving to the next lecture:</strong></p>
<p>Verify you can see <strong>Sysmon process access</strong>,
<strong>pipe connect</strong>, and <strong>pipe create</strong> events
in Wazuh.</p>
</section>
</div>
