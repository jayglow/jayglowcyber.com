---
layout: default
title: "Sysmon ImageLoad Event ID 7"
permalink: /solo-purple-teaming/sysmon-imageload-event-id-7/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sysmon ImageLoad Event ID 7</h1>
</section>
<section class="spt-content">
<h3 id="1-purpose-of-the-exploration-phase"><strong>1. Purpose of the
Exploration Phase</strong></h3>
<p>Before enabling <strong>Sysmon Event ID 7</strong>, remember the
purpose of the <em>exploration phase</em> in the blue team
activities:</p>
<ul>
<li><strong>Identify telemetry gaps</strong> – Determine what’s missing
from our collected logs.</li>
<li><strong>Identify detection opportunities</strong> – Find where the
available telemetry can be leveraged to spot suspicious activity.</li>
</ul>
<blockquote>
<p>In this phase, you’ll often perform independent research to
understand what telemetry exists and how it can be used for
detections.</p>
<p>This course streamlines that process so you can focus on practical
implementation.</p>
</blockquote>
<hr />
<h3 id="2-researching-command-line-arguments-in-telemetry"><strong>2.
Researching Command Line Arguments in Telemetry</strong></h3>
<ul>
<li><p>To find ways of capturing command line arguments in Windows,
search for:</p>
capture command line arguments Windows Logging
</li>
<li><p>AI or search results may reveal multiple solutions:</p>
<ul>
<li><strong>Sysmon</strong> (our current choice)</li>
<li><strong>Group Policy</strong> – enabling command line arguments for
process creation events in the Windows Security log (Event ID
4688).</li>
</ul></li>
</ul>
<hr />
<h3 id="3-understanding-unsigned-image-loads"><strong>3. Understanding
Unsigned Image Loads</strong></h3>
<ul>
<li><strong>Why monitor unsigned image loads?</strong>
<ul>
<li>Malware or custom payloads are often unsigned, making this a strong
detection indicator.</li>
</ul></li>
<li>To check if a binary is signed:
<ol type="1">
<li><p>On your reverse engineering machine, use Sysinternals
<code>sigcheck</code>.</p></li>
<li><p>Example:</p>
sigcheck goodbyeamsi.exe
</li>
<li><p>If unsigned, it will show as <strong>untrusted
code</strong>.</p></li>
</ol></li>
</ul>
<hr />
<h3 id="4-researching-how-to-capture-unsigned-image-loads"><strong>4.
Researching How to Capture Unsigned Image Loads</strong></h3>
<ul>
<li><p>Search for:</p>
capture unsigned image load events windows
</li>
<li><p>Sysmon <strong>Event ID 7</strong> logs image loads by processes
and includes a <strong>Signed</strong> field:</p>
<ul>
<li><code>True</code> → Binary is signed</li>
<li><code>False</code> → Binary is unsigned</li>
</ul></li>
</ul>
<hr />
<h3 id="5-checking-for-existing-event-id-7-telemetry"><strong>5.
Checking for Existing Event ID 7 Telemetry</strong></h3>
<ul>
<li>In <strong>Wazuh</strong>:
<ol type="1">
<li>Navigate to <strong>Explore → Discover</strong>.</li>
<li>Filter for <code>Event ID: 7</code>.</li>
<li>If no results in the last 12 hours → proceed to enable it in
Sysmon.</li>
</ol></li>
</ul>
<hr />
<h3 id="6-updating-sysmon-configuration"><strong>6. Updating Sysmon
Configuration</strong></h3>
<ol type="1">
<li><p>On the <strong>Assume Breach Host</strong>:</p>
<ul>
<li>Open the Sysmon configuration.</li>
<li>Locate <strong>Event ID 7</strong> rules.</li>
<li>Change the <code><Include></code> rule to capture <strong>all
events where <code>Signed = false</code></strong>.</li>
</ul></li>
<li><p>Save the configuration.</p></li>
<li><p>In <strong>PowerShell (Admin)</strong>:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cd</span> Downloads</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a>sysmon <span class="op">-</span>c sysmonconfig<span class="op">.</span><span class="fu">xml</span></span></code></pre></div>
<ul>
<li>Ensure configuration is <strong>validated and updated</strong>.</li>
</ul></li>
</ol>
<hr />
<h3 id="7-verifying-in-event-viewer"><strong>7. Verifying in Event
Viewer</strong></h3>
<ul>
<li>Open <strong>Event Viewer</strong> → <strong>Applications and
Services Logs</strong> → <strong>Microsoft → Windows → Sysmon →
Operational</strong>.</li>
<li>Filter for <code>Event ID: 7</code>.</li>
<li>Confirm unsigned image load events appear.</li>
</ul>
<hr />
<h3 id="8-verifying-in-wazuh"><strong>8. Verifying in
Wazuh</strong></h3>
<ul>
<li><p>Switch <strong>Index Pattern</strong> to:</p>
int_wazuh-archives-*
</li>
<li><p>Search for <code>Event ID: 7</code>.</p></li>
<li><p>Confirm new entries are populating in the last few
minutes.</p></li>
</ul>
<hr />
<h3 id="9-testing-with-the-initial-access-payload"><strong>9. Testing
with the Initial Access Payload</strong></h3>
<ol type="1">
<li>On the <strong>Assume Breach Host</strong>:
<ul>
<li>Rerun <code>goodbyeamsi.exe</code>.</li>
</ul></li>
<li>In <strong>Mythic</strong>:
<ul>
<li>Verify a new callback is established.</li>
</ul></li>
<li>In <strong>Wazuh</strong>:
<ul>
<li>Search for <code>Event ID: 7</code> alerts related to
<code>goodbyeamsi.exe</code>.</li>
<li>Switch to **wazuh-archives-**index to see all raw events.</li>
</ul></li>
</ol>
<hr />
<h3 id="10-filtering-for-the-payloads-image-loads"><strong>10. Filtering
for the Payload’s Image Loads</strong></h3>
<ol type="1">
<li><p>Filter by:</p>
data.win.eventdata.ImageLoaded : *amsi*

<ul>
<li>This shows all image loads related to <code>goodbyeamsi.exe</code>
and its DLLs.</li>
</ul></li>
<li><p>Expand event details.</p></li>
<li><p>Check:</p>
data.win.eventdata.Signed : False

<ul>
<li>Confirms the binary is <strong>unsigned</strong>.</li>
</ul></li>
</ol>
<hr />
<h3 id="11-completion-check"><strong>11. Completion Check</strong></h3>
<p>✅ Before moving on:</p>
<ul>
<li>You can see unsigned image load events in <strong>Wazuh
Manager</strong>.</li>
<li>You have verified the detection works with your initial access
payload.</li>
</ul>
<hr />
<p><strong>Next Step:</strong> In the following lecture, you will enable
<strong>Sysmon Event ID 11</strong> (File Create events) to expand
telemetry coverage.</p>
</section>
</div>
