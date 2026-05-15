---
layout: default
title: "Configuring Wazuh for Defender and Sysmon"
permalink: /solo-purple-teaming/configuring-wazuh-for-defender-and-sysmon/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Configuring Wazuh for Defender and Sysmon</h1>
</section>
<section class="spt-content">
<h3 id="1-recap-of-lab-environment"><strong>1. Recap of Lab
Environment</strong></h3>
<ul>
<li><strong>AllSafe LAN</strong>: Wazuh server resides here.</li>
<li><strong>Assumed Breach Host</strong>:
<ul>
<li>Wazuh agent installed and forwarding events to AllSafe via port
forwarding on the AllSafe edge device.</li>
<li>Sysmon installed and verified to be logging events.</li>
</ul></li>
</ul>
<p><img
src="/assets/images/solo-purple-teaming/configuring-wazuh-for-defender-and-sysmon/image.png"
alt="image.png" /></p>
<p>Goal: Configure Wazuh agent to forward <strong>Sysmon</strong> and
<strong>Windows Defender</strong> logs to the Wazuh server.</p>
<hr />
<h3 id="2-key-concepts-before-configuration"><strong>2. Key Concepts
Before Configuration</strong></h3>
<h3 id="a-agent-groups"><strong>a. Agent Groups</strong></h3>
<ul>
<li>Logical collections of agents for easier management.</li>
<li>Group assets by:
<ul>
<li><strong>Role</strong> (e.g., workstations, domain controllers)</li>
<li><strong>Location</strong></li>
<li><strong>Threat exposure</strong></li>
</ul></li>
<li><strong>Benefits</strong>:
<ul>
<li>Apply settings, rules, and policies to an entire group at once.</li>
<li>Isolate policies for different asset types.</li>
<li>Improve scalability and manageability.</li>
</ul></li>
</ul>
<h3 id="b-configuration-methods"><strong>b. Configuration
Methods</strong></h3>
<ul>
<li><strong>Agent-based</strong>: Configurations live on the endpoint
(max flexibility, harder to manage at scale).</li>
<li><strong>Centralized</strong>: Configurations pushed from Wazuh
Manager to agents (consistent, scalable, lower error risk).</li>
<li><strong>Recommendation</strong>: Small/varied environments →
Agent-based; Large/standardized → Centralized.</li>
</ul>
<h3 id="c-configuration-precedence"><strong>c. Configuration
Precedence</strong></h3>
<ul>
<li><strong>Local</strong>: <code>ossec.conf</code> (on the agent).</li>
<li><strong>Central</strong>: <code>agent.conf</code> (on the manager) —
<strong>overrides</strong> local settings.</li>
</ul>
<h3 id="d-logall-setting"><strong>d. <code>logall</code>
Setting</strong></h3>
<ul>
<li><strong>Default</strong>: Disabled — only sends events matching
detection rules.</li>
<li><strong><code>log_all</code> or <code>log_all_JSON</code></strong>:
Sends all collected logs to the manager.
<ul>
<li>Required for creating searchable indices in Wazuh Dashboard.</li>
<li>Use <code>log_all_JSON</code> for full JSON-structured archives
(preferred for indexing).</li>
</ul></li>
</ul>
<hr />
<h3 id="3-lab-configuration-steps"><strong>3. Lab Configuration
Steps</strong></h3>
<h3 id="step-1-create-a-windows-agent-group"><strong>Step 1: Create a
Windows Agent Group</strong></h3>
<ol type="1">
<li>Log into <strong>Wazuh Console</strong> → Hamburger menu →
<strong>Agent Management</strong> → <strong>Groups</strong>.</li>
<li>Click <strong>Add New Group</strong> → Name: <code>Windows</code> →
Save.</li>
<li>Move the assumed breach host to this group:
<ul>
<li>Dashboard → Overview → <strong>Active Agents</strong>.</li>
<li>Click the three dots (…) next to the agent → <strong>Edit
Groups</strong>.</li>
<li>Remove from default, add to <strong>Windows</strong> → Save.</li>
</ul></li>
</ol>
<hr />
<h3
id="step-2-enable-sysmon--windows-defender-log-collection"><strong>Step
2: Enable Sysmon &amp; Windows Defender Log Collection</strong></h3>
<ol type="1">
<li><strong>Edit the Windows Group Configuration</strong>:
<ul>
<li>Hamburger menu → <strong>Agent Management</strong> →
<strong>Groups</strong>.</li>
<li>Click the pencil next to <code>Windows</code>.</li>
</ul></li>
<li>Add these entries:</li>
</ol>
<div class="sourceCode" id="cb1"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">localfile</span>></span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">log_format</span>>eventchannel</<span class="kw">log_format</span>></span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">location</span>>Microsoft-Windows-Windows Defender/Operational</<span class="kw">location</span>></span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a></<span class="kw">localfile</span>></span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb1-6"><a href="#cb1-6" aria-hidden="true" tabindex="-1"></a><<span class="kw">localfile</span>></span>
<span id="cb1-7"><a href="#cb1-7" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">log_format</span>>eventchannel</<span class="kw">log_format</span>></span>
<span id="cb1-8"><a href="#cb1-8" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">location</span>>Microsoft-Windows-Sysmon/Operational</<span class="kw">location</span>></span>
<span id="cb1-9"><a href="#cb1-9" aria-hidden="true" tabindex="-1"></a></<span class="kw">localfile</span>></span></code></pre></div>
<ol type="1">
<li>Save — confirm “File successfully edited” message.</li>
</ol>
<hr />
<h3 id="step-3-enable-full-log-collection-on-wazuh-manager"><strong>Step
3: Enable Full Log Collection on Wazuh Manager</strong></h3>
<ol type="1">
<li><p>SSH into <strong>Wazuh Manager</strong>:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /var/ossec/etc</span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi ossec.conf</span></code></pre></div></li>
<li><p>Locate:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">logall_json</span>>no</<span class="kw">logall_json</span>></span></code></pre></div></li>
<li><p>Change to:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">logall_json</span>>yes</<span class="kw">logall_json</span>></span></code></pre></div></li>
<li><p>Save &amp; exit → Restart Wazuh Manager:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart wazuh-manager</span></code></pre></div></li>
</ol>
<hr />
<h3 id="step-4-enable-archives-in-filebeat"><strong>Step 4: Enable
Archives in Filebeat</strong></h3>
<ol type="1">
<li><p>Navigate:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /etc/filebeat</span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi filebeat.yml</span></code></pre></div></li>
<li><p>Locate:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode yaml"><code class="sourceCode yaml"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="fu">archives</span><span class="kw">:</span></span>
<span id="cb7-2"><a href="#cb7-2" aria-hidden="true" tabindex="-1"></a><span class="at">  </span><span class="fu">enabled</span><span class="kw">:</span><span class="at"> </span><span class="ch">false</span></span></code></pre></div></li>
<li><p>Change to:</p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode yaml"><code class="sourceCode yaml"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="fu">archives</span><span class="kw">:</span></span>
<span id="cb8-2"><a href="#cb8-2" aria-hidden="true" tabindex="-1"></a><span class="at">  </span><span class="fu">enabled</span><span class="kw">:</span><span class="at"> </span><span class="ch">true</span></span></code></pre></div></li>
<li><p>Restart Filebeat:</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart filebeat</span>
<span id="cb9-2"><a href="#cb9-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl status filebeat</span></code></pre></div></li>
</ol>
<hr />
<h3
id="step-5-create-an-archives-index-pattern-in-wazuh-dashboard"><strong>Step
5: Create an Archives Index Pattern in Wazuh Dashboard</strong></h3>
<ol type="1">
<li><p>Wazuh Dashboard → <strong>Dashboard Management</strong> →
<strong>Index Patterns</strong>.</p></li>
<li><p>Click <strong>Create Index Pattern</strong> → Name:</p>
winwazuh-archives-*
</li>
<li><p>Select <strong><code>timestamp</code></strong> as the time field
→ <strong>Create Index Pattern</strong>.</p></li>
</ol>
<hr />
<h3 id="step-6-verify-event-ingestion"><strong>Step 6: Verify Event
Ingestion</strong></h3>
<ol type="1">
<li>Go to Hamburger menu → <strong>Explore</strong> →
<strong>Discover</strong>.</li>
<li>Select <code>wazuh-archives-*</code> index pattern.</li>
<li>Confirm <strong>Sysmon</strong> and <strong>Windows
Defender</strong> events are visible.</li>
</ol>
<hr />
<h3 id="step-7-trigger-a-windows-defender-alert-test"><strong>Step 7:
Trigger a Windows Defender Alert (Test)</strong></h3>
<ol type="1">
<li><p>SSH to <strong>Kali server</strong>.</p></li>
<li><p>Copy Mimikatz to the web-served downloads directory:</p>
<div class="sourceCode" id="cb11"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> cp /opt/mythic/mythic/installed_services/Apollo/agent_code/mimikatz/x64/mimikatz.exe ~/downloads/</span></code></pre></div></li>
<li><p>On the <strong>Assumed Breach Host</strong>, open browser →
Download Mimikatz from:</p>
http://<Kali_IP>:8000/mimikatz.exe
</li>
<li><p>Windows Defender should trigger an alert.</p></li>
<li><p>Check Wazuh Dashboard → <strong>Overview</strong>:</p>
<ul>
<li>Confirm <strong>high-severity alert</strong> from Windows
Defender.</li>
<li>Verify alert details and rule match.</li>
</ul></li>
</ol>
<hr />
<h3 id="8-checklist-before-moving-on"><strong>8. Checklist Before Moving
On</strong></h3>
<p>✅ Windows &amp; Sysmon logs enabled in <code>agent.conf</code>.</p>
<p>✅ <code>log_all_JSON</code> set to <strong>yes</strong> in
<code>ossec.conf</code>.</p>
<p>✅ Archives enabled in <strong>Filebeat</strong>.</p>
<p>✅ Archives index created in Wazuh Dashboard.</p>
<p>✅ Verified Defender &amp; Sysmon logs are ingesting.</p>
<hr />
<h3 id="next-lecture"><strong>Next Lecture</strong></h3>
<p>We will configure <strong>Wazuh to consume Syslog events</strong> and
set up <strong>Ecoin Edge</strong> to forward those events to Wazuh.</p>
</section>
</div>
