---
layout: default
title: "Sysmon FileCreate Event ID 11"
permalink: /solo-purple-teaming/sysmon-filecreate-event-id-11/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sysmon FileCreate Event ID 11</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3 id="1-introduction-to-event-id-11"><strong>1. Introduction to Event
ID 11</strong></h3>
<ul>
<li><strong>What it is:</strong> Sysmon Event ID 11 logs <strong>file
creation</strong> operations when a file is created or overwritten.</li>
<li><strong>Why it’s important:</strong>
<ul>
<li>Helps monitor <em>auto-start</em> locations (e.g., Startup
folder).</li>
<li>Captures files in <strong>temporary</strong> and
<strong>Downloads</strong> directories, which are common malware drop
locations during initial infection.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-sysmon-filter-conditions"><strong>2. Sysmon Filter
Conditions</strong></h3>
<p>Sysmon allows filtering events based on specific
<strong>conditions</strong> applied to event fields.</p>
<p>Key condition types:</p>
<table>
<thead>
<tr class="header">
<th><strong>Condition Type</strong></th>
<th><strong>Meaning</strong></th>
<th><strong>Example</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>is</code></td>
<td>Exact match</td>
<td>Image is <code>C:\Windows\System32\cmd.exe</code></td>
</tr>
<tr class="even">
<td><code>is not</code></td>
<td>Exact mismatch</td>
<td>Image is not <code>cmd.exe</code></td>
</tr>
<tr class="odd">
<td><code>contains</code></td>
<td>Field contains a given substring</td>
<td>Target file contains <code>Downloads</code></td>
</tr>
<tr class="even">
<td><code>contains any</code></td>
<td>Field contains any of multiple substrings</td>
<td><code>cmd.exe</code>, <code>powershell.exe</code></td>
</tr>
<tr class="odd">
<td><code>contains all</code></td>
<td>Field contains all listed substrings</td>
<td>—</td>
</tr>
<tr class="even">
<td><code>begin with</code></td>
<td>Field starts with a specified string</td>
<td>Path begins with <code>C:\Windows\System32\drivers</code></td>
</tr>
<tr class="odd">
<td><code>end with</code></td>
<td>Field ends with a specified string</td>
<td>—</td>
</tr>
<tr class="even">
<td><code>match</code></td>
<td>Match using a regular expression</td>
<td>—</td>
</tr>
</tbody>
</table>
<hr />
<h3 id="3-modifying-sysmon-config-for-event-id-11"><strong>3. Modifying
Sysmon Config for Event ID 11</strong></h3>
<p><strong>Goal:</strong> Capture files created in high-risk
directories.</p>
<ol type="1">
<li>Locate <strong>Event ID 11</strong> section in your Sysmon
configuration.</li>
<li>Change it to <code>include</code> rules.</li>
<li>Add a <code>&lt;TargetFilename&gt;</code> filter:
<ul>
<li><strong>Condition:</strong> <code>contains</code></li>
<li><strong>Value:</strong> <code>Downloads</code></li>
</ul></li>
<li>Duplicate and adjust the filter for:
<ul>
<li><code>AppData</code> (condition: <code>contains</code>)</li>
<li><code>C:\Windows\System32\drivers</code> (condition:
<code>begin with</code>)</li>
<li><code>C:\Windows\SysWOW64\drivers</code> (condition:
<code>begin with</code>)</li>
</ul></li>
</ol>
<hr />
<h3 id="4-applying-the-configuration"><strong>4. Applying the
Configuration</strong></h3>
<ol type="1">
<li><p>Save your modified Sysmon config file
(<code>sysmonconfig.xml</code>).</p></li>
<li><p>From an <strong>Administrator Command Prompt</strong>, run:</p>
<pre><code>r
CopyEdit
sysmon -c sysmonconfig.xml
</code></pre></li>
<li><p>Ensure configuration validation and update succeed.</p></li>
</ol>
<hr />
<h3 id="5-testing-the-filter"><strong>5. Testing the
Filter</strong></h3>
<p><strong>Objective:</strong> Verify Event ID 11 logs a file creation
in the Downloads folder.</p>
<ol type="1">
<li><strong>Open Event Viewer</strong>
<ul>
<li>Path:
<code>Applications and Services Logs &gt; Microsoft &gt; Windows &gt; Sysmon &gt; Operational</code></li>
</ul></li>
<li><strong>Filter logs</strong> for Event ID <code>11</code>.</li>
<li><strong>Clear the log</strong> to capture only new events.</li>
<li><strong>Trigger a test event</strong>:
<ul>
<li>Download a file from the attack host’s web staging directory.</li>
<li>Example: Access <code>http://192.168.100.101:8000</code> and
download <code>goodbye_AMSI_1.exe</code> into
<code>Downloads</code>.</li>
<li>The file may trigger SmartScreen scanning before saving.</li>
</ul></li>
</ol>
<hr />
<h3 id="6-reviewing-the-event"><strong>6. Reviewing the
Event</strong></h3>
<ul>
<li>In <strong>Event Viewer</strong>, confirm:
<ul>
<li>File creation entry for <code>goodbye_AMSI_1.exe</code></li>
<li>Presence of <strong>Zone.Identifier</strong> (Mark of the Web),
indicating the file came from the internet.</li>
</ul></li>
</ul>
<hr />
<h3 id="7-verifying-in-wazuh"><strong>7. Verifying in
Wazuh</strong></h3>
<ol type="1">
<li>In Wazuh’s dashboard, go to <strong>Explore &gt;
Discover</strong>.</li>
<li>Change the index pattern to <code>archives</code>.</li>
<li>Query for:
<ul>
<li>Event ID = <code>11</code></li>
<li><code>data.win.TargetFilename</code> contains <code>AMSI</code></li>
</ul></li>
<li>Confirm event details:
<ul>
<li>File path</li>
<li>Mark of the Web attribute</li>
</ul></li>
</ol>
<hr />
<h3 id="8-additional-notes"><strong>8. Additional Notes</strong></h3>
<ul>
<li><strong>Mark of the Web</strong> is a valuable indicator for files
downloaded from the internet.</li>
<li>Research <strong>Sysmon Event ID 15</strong> to detect
<strong>Alternate Data Streams</strong> and Mark of the Web.</li>
<li>Before continuing, ensure your Wazuh manager is <strong>logging file
creations in the Downloads folder</strong>.</li>
</ul>
<hr />
<h3 id="next-step"><strong>Next Step</strong></h3>
<p>In the next lecture, you will explore <strong>PowerShell script block
logging</strong> to detect malicious script execution related to initial
access payloads.</p>
</section>
</div>
