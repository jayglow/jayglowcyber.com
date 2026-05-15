---
layout: default
title: "Sending Syslog Events From ECOIN Edge"
permalink: /solo-purple-teaming/sending-syslog-events-from-ecoin-edge/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sending Syslog Events From ECOIN Edge</h1>
</section>
<section class="spt-content">
<h3 id="1-recap-of-the-lab-setup"><strong>1. Recap of the Lab
Setup</strong></h3>
<ul>
<li><strong>AllSafe LAN</strong>: Where the <strong>Wazuh
server</strong> resides.</li>
<li><strong>Ecoin Edge Device</strong>: The focus for this
configuration, forwarding firewall events using <strong>Syslog</strong>
to the Wazuh server.</li>
<li><strong>Purpose of Syslog in Purple Teaming</strong>:
<ul>
<li>Standardized protocol for sending log data from devices to a central
location.</li>
<li>Includes source and severity for easy anomaly detection.</li>
<li>Allows real-time testing of offensive techniques and refining
detection strategies.</li>
</ul></li>
</ul>
<p><img src="/_assets/sending-syslog-events-from-ecoin-edge/image.png"
alt="image.png" /></p>
<hr />
<h3 id="2-steps-overview"><strong>2. Steps Overview</strong></h3>
<p>We will:</p>
<ol type="1">
<li><strong>Set port forwarding</strong> on AllSafe Edge for Syslog (UDP
514) → Wazuh server.</li>
<li><strong>Configure Syslog forwarding</strong> on Ecoin Edge (pfSense)
to send firewall events to the Wazuh server.</li>
<li><strong>Configure Wazuh manager</strong> to accept incoming Syslog
events.</li>
</ol>
<hr />
<h3 id="3-step-1--port-forwarding-on-allsafe-edge"><strong>3. Step 1 –
Port Forwarding on AllSafe Edge</strong></h3>
<ol type="1">
<li><p>Log into <strong>AllSafe Edge</strong>.</p></li>
<li><p>Go to <strong>Firewall → NAT</strong> and click <strong>Add New
Rule</strong>.</p></li>
<li><p><strong>Interface</strong>: WAN</p>
<p><strong>Protocol</strong>: UDP</p>
<p><strong>Destination Port</strong>: Syslog (514)</p>
<p><strong>Redirect Target IP</strong>: <code>10.0.4.2</code> (Wazuh
Manager)</p>
<p><strong>Redirect Target Port</strong>: Syslog (514)</p></li>
<li><p>Add a description for clarity.</p></li>
<li><p>Save and <strong>Apply Changes</strong>.</p></li>
</ol>
<hr />
<h3 id="4-step-2--syslog-forwarding-on-ecoin-edge-pfsense"><strong>4.
Step 2 – Syslog Forwarding on Ecoin Edge (pfSense)</strong></h3>
<h3 id="a-install-syslog-ng"><strong>A. Install syslog-ng</strong></h3>
<ol type="1">
<li>Log into <strong>Ecoin Edge</strong>.</li>
<li>Go to <strong>System → Package Manager → Available
Packages</strong>.</li>
<li>Search for <code>syslog-ng</code>.</li>
<li>Install <strong>syslog-ng</strong> (v1.16 at time of setup).</li>
<li>Confirm installation.</li>
</ol>
<h3 id="b-enable-syslog-ng"><strong>B. Enable syslog-ng</strong></h3>
<ol type="1">
<li>Go to <strong>Services → syslog-ng</strong>.</li>
<li>Check <strong>Enable syslog-ng</strong>.</li>
<li>Select interfaces to monitor: <strong>WAN</strong> and
<strong>Loopback</strong> (use Ctrl-click to select multiple).</li>
<li>Protocol: <strong>UDP</strong>.</li>
<li>Port: <code>5140</code> (default here; will route later).</li>
<li>Save.</li>
</ol>
<h3 id="c-create-a-destination"><strong>C. Create a
Destination</strong></h3>
<ol type="1">
<li><p>In <strong>syslog-ng</strong>, go to <strong>Advanced →
Add</strong>.</p></li>
<li><p>Name: <code>Wazuh</code>.</p></li>
<li><p>Type: Destination.</p></li>
<li><p>Parameters:</p>
udp("192.168.100.103" port(514) localip("10.0.2.3"))

<ul>
<li><code>192.168.100.103</code>: WAN IP of AllSafe Edge.</li>
<li><code>10.0.2.3</code>: LAN IP of pfSense Ecoin Edge.</li>
</ul></li>
<li><p>Save.</p></li>
</ol>
<h3 id="d-create-a-log-forwarder"><strong>D. Create a Log
Forwarder</strong></h3>
<ol type="1">
<li>Add a new log route: Name it <code>Wazuh Route</code>.</li>
<li><strong>Source</strong>: Default.</li>
<li><strong>Destination</strong>: Wazuh.</li>
<li>Save.</li>
</ol>
<h3 id="e-enable-remote-logging"><strong>E. Enable Remote
Logging</strong></h3>
<ol type="1">
<li>Go to <strong>Status → System Logs → Settings</strong>.</li>
<li>Enable <strong>BSD RFC 3164</strong> format.</li>
<li>Check <strong>Enable sending log messages to remote
syslog</strong>.</li>
<li><strong>Source Address</strong>: LAN interface.</li>
<li><strong>Destination IP</strong>: <code>192.168.100.103</code>
(AllSafe Edge WAN).</li>
<li><strong>Port</strong>: 514.</li>
<li>Select log categories (initially send everything).</li>
<li>Save.</li>
</ol>
<hr />
<h3 id="5-step-3--configure-wazuh-manager-to-accept-syslog"><strong>5.
Step 3 – Configure Wazuh Manager to Accept Syslog</strong></h3>
<ol type="1">
<li><p>In Wazuh UI, go to <strong>Server Management → Settings → Edit
Configuration</strong>.</p></li>
<li><p>Add a <code><remote></code> block:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">remote</span>></span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">connection</span>>syslog</<span class="kw">connection</span>></span>
<span id="cb2-3"><a href="#cb2-3" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">port</span>>514</<span class="kw">port</span>></span>
<span id="cb2-4"><a href="#cb2-4" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">protocol</span>>udp</<span class="kw">protocol</span>></span>
<span id="cb2-5"><a href="#cb2-5" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">allowed-ips</span>>192.168.100.0/24</<span class="kw">allowed-ips</span>></span>
<span id="cb2-6"><a href="#cb2-6" aria-hidden="true" tabindex="-1"></a></<span class="kw">remote</span>></span></code></pre></div>
<ul>
<li>Allows all devices in <code>192.168.100.0/24</code> to send
Syslog.</li>
</ul></li>
<li><p>Save changes.</p></li>
<li><p>Restart the Wazuh Manager.</p></li>
</ol>
<hr />
<h3 id="6-step-4--verification"><strong>6. Step 4 –
Verification</strong></h3>
<h3 id="a-from-the-wazuh-server"><strong>A. From the Wazuh
Server</strong></h3>
<ol type="1">
<li><p>SSH into the Wazuh server.</p></li>
<li><p>Run:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="ex">tcpdump</span> <span class="at">-i</span> any port 514</span></code></pre></div></li>
<li><p>Confirm you see incoming Syslog events from Ecoin Edge.</p></li>
</ol>
<h3 id="b-from-wazuh-ui"><strong>B. From Wazuh UI</strong></h3>
<ol type="1">
<li>Go to <strong>Discover → Index: Wazuh Archives</strong>.</li>
<li>Search for <code>pfSense</code>.</li>
<li>Confirm Syslog entries from Ecoin Edge are present.</li>
</ol>
<p><img
src="/_assets/sending-syslog-events-from-ecoin-edge/image%201.png"
alt="image.png" /></p>
<hr />
<h3 id="7-step-5--optimize-log-forwarding"><strong>7. Step 5 – Optimize
Log Forwarding</strong></h3>
<ul>
<li>Initially, all logs were forwarded.</li>
<li>For efficiency, forward <strong>only firewall events</strong>:
<ol type="1">
<li>In pfSense: <strong>Status → System Logs → Settings</strong>.</li>
<li>Under remote forwarding, select <strong>Firewall Events</strong>
only.</li>
<li>Save.</li>
</ol></li>
</ul>
<hr />
<h3 id="8-final-checklist"><strong>8. Final Checklist</strong></h3>
<p>✅ AllSafe Edge forwards UDP 514 to Wazuh.</p>
<p>✅ Ecoin Edge sends firewall Syslog events via syslog-ng.</p>
<p>✅ Wazuh Manager receives and parses logs.</p>
<p>✅ Verified via <code>tcpdump</code> and Wazuh UI search.</p>
</section>
</div>
