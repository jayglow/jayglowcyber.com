---
layout: default
title: "Blue Team Explore - Hunting For Initial Access Exe"
permalink: /solo-purple-teaming/blue-team-explore-hunting-for-initial-access-exe/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Hunting For Initial Access Exe</h1>
</section>
<section class="spt-content">
<h1 id="blue-team-explore---hunting-for-initial-access-execution">Blue
Team Explore - Hunting For Initial Access Execution</h1>
<h2 id="1-recap-of-previous-lecture"><strong>1. Recap of Previous
Lecture</strong></h2>
<ul>
<li>In the last lecture, we examined the <strong>execute
assembly</strong> command:
<ul>
<li>Spawns a sacrificial process.</li>
<li>Injects a CLR loader.</li>
<li>Reflectively loads a .NET assembly to execute.</li>
</ul></li>
<li>In our attack path:
<ul>
<li><strong>PowerShell</strong> hosts our <strong>Apollo
agent</strong>.</li>
<li>PowerShell accessed <strong>smartscreen.exe</strong>, granting it
<strong>0x1fffff</strong> (full access rights).</li>
<li>SmartScreen created a <strong>unique named pipe</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-this-lectures-goal"><strong>2. This Lecture’s
Goal</strong></h3>
<ul>
<li>Step back in the kill chain to look for <strong>initial access
indicators</strong>.</li>
<li>Focus on:
<ol type="1">
<li>Relationship between <strong>Run Dialog</strong> and the malicious
PowerShell command.</li>
<li><strong>Registry events</strong> related to running the command from
Run Dialog.</li>
</ol></li>
</ul>
<hr />
<h3 id="3-updating-sysmon-configuration"><strong>3. Updating Sysmon
Configuration</strong></h3>
<ol type="1">
<li><p>Add a <strong>filter</strong> in <code>process create</code>
events:</p>
<ul>
<li>Only capture where <code>Image</code> ends with
<code>.exe</code>.</li>
</ul></li>
<li><p>Add an additional <strong>destination port (8000)</strong>:</p>
<ul>
<li>This captures connections to the <strong>staging server</strong>
that downloads Apollo.</li>
</ul></li>
<li><p>Save and apply config:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sysmon64</span> <span class="at">-c</span> base_sysmon_config.xml</span></code></pre></div></li>
<li><p>Verify configuration update.</p></li>
</ol>
<hr />
<h3 id="4-understanding-the-run-dialog-and-registry-storage"><strong>4.
Understanding the Run Dialog and Registry Storage</strong></h3>
<ul>
<li><p><strong>Run Dialog</strong> shows command history
(auto-populated).</p></li>
<li><p>Stores up to <strong>25 commands</strong> in:</p>
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU

<ul>
<li>Keys: <code>a</code> (first command), <code>b</code> (second),
etc.</li>
<li><code>MRUList</code> value defines display order.</li>
</ul></li>
</ul>
<hr />
<h3 id="5-lab-steps--observing--clearing-runmru"><strong>5. Lab Steps –
Observing &amp; Clearing RunMRU</strong></h3>
<ol type="1">
<li>Open <strong>Regedit</strong> and navigate to
<strong>RunMRU</strong>.</li>
<li>Note the malicious PowerShell command in key <code>a</code>.</li>
<li>Copy the command, delete all RunMRU entries.</li>
<li>Open <strong>Run Dialog</strong> – it’s empty now.</li>
<li>Paste attack command → execute → check <strong>Mythic</strong> for
callback.</li>
<li>If no callback, re-run payload and clear registry again.</li>
</ol>
<hr />
<h3 id="6-verifying-in-wazuh"><strong>6. Verifying in
Wazuh</strong></h3>
<ol type="1">
<li><p>Go to <strong>Explore → Discover</strong> in Wazuh.</p></li>
<li><p>Change <strong>index pattern</strong> to
<code>wazuh-archives</code>.</p></li>
<li><p>Search for:</p>
data.win.event.image:*powershell.exe AND data.win.system.eventID:3
</li>
<li><p>Add <strong>Destination IP</strong> column to verify connections
to <strong>attack infrastructure</strong>.</p></li>
<li><p>Optionally, add <strong>Destination Port</strong> column (expect
ports 80 &amp; 8000).</p></li>
</ol>
<hr />
<h3 id="7-pivoting-to-process-relationships"><strong>7. Pivoting to
Process Relationships</strong></h3>
<ul>
<li>Add <strong>Process GUID</strong> as a column → filter events by
GUID.</li>
<li>Check Event IDs linked to PowerShell:
<ul>
<li><strong>12</strong>: Registry create/delete.</li>
<li><strong>3</strong>: Network connect.</li>
<li><strong>7</strong>: Image load.</li>
<li><strong>13</strong>: Registry value modification.</li>
</ul></li>
<li>Identify <strong>parent process</strong> of PowerShell:
<ul>
<li>Filter <code>eventID:1</code> → parent is
<strong>explorer.exe</strong> (Run Dialog runs under Explorer
context).</li>
</ul></li>
</ul>
<hr />
<h3 id="8-tracing-parent-process-activity"><strong>8. Tracing Parent
Process Activity</strong></h3>
<ol type="1">
<li>Replace filter to search by <strong>parent process
GUID</strong>.</li>
<li>Find related registry events (<strong>12 &amp; 13</strong>).</li>
<li>Add <strong>Details</strong> column → shows registry value set.</li>
<li>Add <strong>Target Object</strong> column → shows registry
path.</li>
<li>Locate <strong>RunMRU</strong> key:
<ul>
<li>Example: <code>RunMRU\a</code> contains the malicious PowerShell
command.</li>
</ul></li>
</ol>
<hr />
<h3 id="9-mapping-to-ioc-diagram"><strong>9. Mapping to IOC
Diagram</strong></h3>
<ul>
<li>Update diagram:
<ul>
<li><strong>explorer.exe</strong> (clean) → launches
<strong>powershell.exe</strong> (malicious).</li>
<li><strong>powershell.exe</strong> → runs Apollo loader, connects to
C2.</li>
<li><strong>explorer.exe</strong> sets <strong>RunMRU</strong> registry
keys.</li>
<li>Initial access triggered by <strong>Run Dialog</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="10-student-checklist-before-moving-on"><strong>10. Student
Checklist Before Moving On</strong></h3>
<p>✅ See <strong>RunMRU</strong> key storing malicious PowerShell
command.</p>
<p>✅ Verify <strong>network connections</strong> to C2 (ports 80 &amp;
8000).</p>
<p>✅ Map IOC chain in diagram:</p>
<ul>
<li>Run Dialog → explorer.exe → powershell.exe → smartscreen.exe →
C2.</li>
</ul>
<hr />
<h3 id="next-step"><strong>Next Step</strong></h3>
<ul>
<li>In the <strong>next lecture</strong>, we’ll begin the <strong>Blue
Team Trace Phase</strong> and perform <strong>reverse
engineering</strong> of captured artifacts.</li>
</ul>
</section>
</div>
