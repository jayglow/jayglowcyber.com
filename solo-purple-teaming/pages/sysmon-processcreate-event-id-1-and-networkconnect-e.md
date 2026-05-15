---
layout: default
title: "Sysmon ProcessCreate Event ID 1 & NetworkConnect E"
permalink: /solo-purple-teaming/sysmon-processcreate-event-id-1-and-networkconnect-e/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sysmon ProcessCreate Event ID 1 & NetworkConnect E</h1>
</section>
<section class="spt-content">
<h1
id="sysmon-processcreate-event-id-1--networkconnect-event-id-3">Sysmon
ProcessCreate Event ID 1 &amp; NetworkConnect Event ID 3</h1>
<h3 id="1-understanding-the-focus-events"><strong>1. Understanding the
Focus Events</strong></h3>
<p><strong>Event ID 1 – Process Creation</strong></p>
<ul>
<li>Logs extended info about new processes.</li>
<li>Includes <strong>full command line</strong>, process GUID, file
hash, and hash type.</li>
<li>Useful for spotting commands run during <strong>host
enumeration</strong>.</li>
</ul>
<p><strong>Event ID 3 – Network Connection</strong></p>
<ul>
<li>Logs <strong>TCP and UDP</strong> connections.</li>
<li>Disabled by default in Sysmon.</li>
<li>Includes <strong>source/destination IP, ports, hostnames</strong>,
and links to the process via process GUID and PID.</li>
</ul>
<hr />
<h2 id="2-exploring-telemetry-in-wazuh"><strong>2. Exploring Telemetry
in Wazuh</strong></h2>
<ol type="1">
<li><strong>Login to Wazuh Manager Dashboard</strong>
<ul>
<li>Review the <strong>Agent Summary</strong> (active vs. disconnected
agents).</li>
<li>Check <strong>24-hour alerts summary</strong> (critical, high,
medium, low).</li>
<li>Note other available dashboards:
<ul>
<li><strong>Endpoint Security</strong> – Configuration assessment,
malware detection, file integrity.</li>
<li><strong>Threat Intelligence</strong> – Threat hunting, vulnerability
detection, MITRE ATT&amp;CK mapping.</li>
<li><strong>Security Operations</strong> – SOC-focused data.</li>
<li><strong>Cloud Security</strong> – Cloud-specific alerts.</li>
</ul></li>
</ul></li>
<li><strong>Go to Explore → Discover</strong>
<ul>
<li>Select the <strong>index pattern</strong>:
<code>wazuh-archives-*</code>.</li>
<li>Confirm <strong>timestamp</strong> is the time field.</li>
<li>Refresh field list to decode new Sysmon fields after adding
events.</li>
</ul></li>
<li><strong>Adjust the Time Range</strong>
<ul>
<li>Use <strong>relative time</strong> (e.g., last 12 hours).</li>
<li>Click <strong>Update</strong> to refresh search results.</li>
</ul></li>
</ol>
<hr />
<h2 id="3-searching-for-sysmon-event-id-1"><strong>3. Searching for
Sysmon Event ID 1</strong></h2>
<ol type="1">
<li><p><strong>In the search bar</strong>, run:</p>
data.win.system.eventID:1
</li>
<li><p><strong>Expected Result</strong> – None found → <strong>Telemetry
gap identified</strong>.</p></li>
</ol>
<hr />
<h2 id="4-enabling-sysmon-event-id-1-process-creation"><strong>4.
Enabling Sysmon Event ID 1 (Process Creation)</strong></h2>
<ol type="1">
<li><p><strong>RDP into the Assumed Breach Host</strong>.</p></li>
<li><p>Open
<strong><code>base_sysmonconfig.xml</code></strong>.</p></li>
<li><p>Locate the <code><Include></code> filter for
<code>powershell.exe</code>.</p></li>
<li><p>Replace with an <strong>empty
<code><Exclude></code></strong> tag:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">Exclude</span> /></span></code></pre></div>
<ul>
<li>This logs <strong>all process creation events</strong>.</li>
</ul></li>
<li><p><strong>Save the configuration</strong>.</p></li>
<li><p><strong>Update Sysmon config via PowerShell (Admin)</strong>:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cd</span> Downloads</span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a><span class="op">.</span>\Sysmon64<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>c <span class="op">.</span>\base_sysmonconfig<span class="op">.</span><span class="fu">xml</span></span></code></pre></div>
<ul>
<li>Look for confirmation: schema validated, config updated.</li>
</ul></li>
</ol>
<hr />
<h2 id="5-verifying-event-id-1-logging-in-wazuh"><strong>5. Verifying
Event ID 1 Logging in Wazuh</strong></h2>
<ol type="1">
<li>Return to <strong>Wazuh → Discover</strong>.</li>
<li>Re-run the query for <strong>event ID 1</strong>.</li>
<li>Confirm process creation events are now present.</li>
<li>Expand an event and verify:
<ul>
<li><code>agent.id</code>, <code>agent.ip</code>,
<code>agent.name</code></li>
<li><strong><code>data.win.eventdata.CommandLine</code></strong>
contains executed commands.</li>
</ul></li>
</ol>
<hr />
<h2 id="6-generating-a-test-event"><strong>6. Generating a Test
Event</strong></h2>
<ol type="1">
<li><p><strong>In Mythic</strong>, establish a new callback:</p>
<ul>
<li><p>On Kali attack host:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> Downloads</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> <span class="at">-m</span> http.server 8000</span></code></pre></div></li>
<li><p>On assumed breach host:</p>
<ul>
<li>Run <code>goodbye_AMSI.exe</code> to load Apollo agent.</li>
</ul></li>
</ul></li>
<li><p><strong>In Mythic</strong>, run:</p>
shell net localgroup administrators

<p><em>(Correct typos as needed.)</em></p></li>
<li><p><strong>In Wazuh</strong>, search for:</p>
data.win.system.eventID:1 AND data.win.eventdata.CommandLine:*localgroup*
</li>
<li><p>Confirm matching events appear, including errors and correct
commands.</p></li>
</ol>
<hr />
<h2
id="7-searching-for-sysmon-event-id-3-network-connections"><strong>7.
Searching for Sysmon Event ID 3 (Network Connections)</strong></h2>
<ol type="1">
<li><p>Query:</p>
data.win.system.eventID:3
</li>
<li><p><strong>Expected Result</strong> – None found → <strong>Telemetry
gap identified</strong>.</p></li>
</ol>
<hr />
<h2 id="8-enabling-sysmon-event-id-3-network-connections"><strong>8.
Enabling Sysmon Event ID 3 (Network Connections)</strong></h2>
<ol type="1">
<li><p><strong>In Wireshark</strong>, verify beacon traffic:</p>
<ul>
<li>Filter: <code>http</code></li>
<li>Note beacon traffic to attack infrastructure (port 80).</li>
</ul></li>
<li><p><strong>Edit Sysmon Config</strong> to include specific
destination ports:</p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">NetworkConnect</span><span class="ot"> onmatch=</span><span class="st">"include"</span>></span>
<span id="cb8-2"><a href="#cb8-2" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">DestinationPort</span>>80</<span class="kw">DestinationPort</span>></span>
<span id="cb8-3"><a href="#cb8-3" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">DestinationPort</span>>135</<span class="kw">DestinationPort</span>></span>
<span id="cb8-4"><a href="#cb8-4" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">DestinationPort</span>>443</<span class="kw">DestinationPort</span>></span>
<span id="cb8-5"><a href="#cb8-5" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">DestinationPort</span>>445</<span class="kw">DestinationPort</span>></span>
<span id="cb8-6"><a href="#cb8-6" aria-hidden="true" tabindex="-1"></a></<span class="kw">NetworkConnect</span>></span></code></pre></div></li>
<li><p><strong>Save config</strong>.</p></li>
<li><p><strong>Update Sysmon config</strong>:</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="op">.</span>\Sysmon64<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>c <span class="op">.</span>\base_sysmonconfig<span class="op">.</span><span class="fu">xml</span></span></code></pre></div></li>
</ol>
<hr />
<h2 id="9-verifying-event-id-3-locally"><strong>9. Verifying Event ID 3
Locally</strong></h2>
<ol type="1">
<li>Open <strong>Event Viewer</strong> →
<code>Applications and Services Logs</code> → <code>Microsoft</code> →
<code>Windows</code> → <code>Sysmon</code> →
<code>Operational</code>.</li>
<li>Filter for <strong>Event ID 3</strong>.</li>
<li>Confirm connections for the selected ports.</li>
</ol>
<hr />
<h2 id="10-testing-and-viewing-in-wazuh"><strong>10. Testing and Viewing
in Wazuh</strong></h2>
<ol type="1">
<li><p>Start a <strong>new beacon</strong> with
<code>goodbye_AMSI.exe</code>.</p></li>
<li><p>In Wazuh, search for:</p>
data.win.system.eventID:3
</li>
<li><p>Confirm network connection logs appear.</p></li>
<li><p>Expand an event to view:</p>
<ul>
<li>Destination port.</li>
<li>Source/destination IP.</li>
<li>Associated process image.</li>
</ul></li>
</ol>
<hr />
<h2 id="11-conclusion--next-steps"><strong>11. Conclusion &amp; Next
Steps</strong></h2>
<ul>
<li><strong>Telemetry Gaps Identified</strong>:
<ul>
<li>Process creation events (ID 1) – <strong>Fixed</strong>.</li>
<li>Network connection events (ID 3) – <strong>Fixed</strong>.</li>
</ul></li>
<li><strong>Data Now Available</strong>:
<ul>
<li>Process command lines from executed commands.</li>
<li>Network connections from beacon traffic.</li>
</ul></li>
<li><strong>Next Lecture Preview</strong>:
<ul>
<li>Enable <strong>Sysmon Event ID 7</strong> to detect untrusted code
execution.</li>
<li>Combine with Event ID 3 for <strong>high-fidelity beaconing
detection</strong>.</li>
</ul></li>
</ul>
</section>
</div>
