---
layout: default
title: "Install Wazuh Agent"
permalink: /solo-purple-teaming/install-wazuh-agent/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Install Wazuh Agent</h1>
</section>
<section class="spt-content">
<h3 id="1-reviewing-the-network-setup">1. <strong>Reviewing the Network
Setup</strong></h3>
<p>Before installing the Wazuh agent, let’s recap the current lab
network configuration:</p>
<ul>
<li><strong>AllSafe LAN</strong>
<ul>
<li>Network: <code>10.0.4.0/24</code></li>
<li>Wazuh Server resides here.</li>
</ul></li>
<li><strong>Ecoin LAN</strong>
<ul>
<li>The <strong>assumed breach host</strong> (<code>wrk-r-lin</code>) is
here.</li>
<li>IP address: <code>10.0.2.100</code>.</li>
</ul></li>
<li><strong>Traffic Path</strong>
<ul>
<li>To reach the Wazuh server, traffic must:
<ol type="1">
<li>Egress from <strong>Ecoin</strong> through the <strong>Ecoin Edge
Device</strong>.</li>
<li>Ingress into <strong>AllSafe</strong> through the <strong>AllSafe
Edge Device</strong>.</li>
<li>Pass through port forwarding rules on the AllSafe edge to reach the
Wazuh server.</li>
</ol></li>
</ul></li>
</ul>
<p><img src="/assets/images/solo-purple-teaming/install-wazuh-agent/image.png" alt="image.png" /></p>
<hr />
<h2 id="2-accessing-the-wazuh-dashboard">2. <strong>Accessing the Wazuh
Dashboard</strong></h2>
<ol type="1">
<li>Log in to the <strong>Wazuh dashboard</strong>.</li>
<li>Locate and click <strong>"Deploy new agent"</strong> in the top-left
corner.</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/install-wazuh-agent/image%201.png"
alt="image.png" /></p>
<hr />
<h2 id="3-configuring-the-agent">3. <strong>Configuring the
Agent</strong></h2>
<ol type="1">
<li><p><strong>Select your operating system</strong> from the
list.</p></li>
<li><p><strong>Set the Wazuh server IP address</strong>:</p>
<ul>
<li><p><strong>Important</strong>: This is <strong>not</strong> the
Wazuh server's internal IP.</p></li>
<li><p>Use the <strong>WAN IP address</strong> of the AllSafe Edge
device:</p>
192.168.100.103
</li>
</ul></li>
<li><p>Leave all other settings at their defaults.</p></li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/install-wazuh-agent/image%202.png"
alt="image.png" /></p>
<ol type="1">
<li>Copy the <strong>PowerShell installation command</strong>
provided.</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/install-wazuh-agent/image%203.png"
alt="image.png" /></p>
<hr />
<h2 id="4-installing-the-agent-on-the-assumed-breach-host">4.
<strong>Installing the Agent on the Assumed Breach Host</strong></h2>
<ol type="1">
<li>Connect to the <strong>assumed breach host</strong>
(<code>wrk-rlin</code>) via <strong>Remote Desktop Protocol
(RDP)</strong>.</li>
<li>Open <strong>PowerShell as Administrator</strong>.</li>
<li>Paste the copied PowerShell command and press
<strong>Enter</strong>.</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/install-wazuh-agent/image%204.png"
alt="image.png" /></p>
<ul>
<li><p><strong>Note</strong>: Your lab must have <strong>Internet
access</strong> at this point, as the installation downloads the MSI
package from:</p>
packages.wazuh.com
</li>
</ul>
<hr />
<h2 id="5-starting-the-wazuh-service">5. <strong>Starting the Wazuh
Service</strong></h2>
<ol type="1">
<li><p>Once installation completes, locate the <strong>service start
command</strong>:</p>
net start WazuhSvc
</li>
<li><p>Run this command in the same PowerShell window.</p></li>
<li><p>The service should now be running.</p></li>
</ol>
<hr />
<h2 id="6-verifying-agent-check-in">6. <strong>Verifying Agent
Check-In</strong></h2>
<ol type="1">
<li><p>Return to the <strong>Wazuh dashboard</strong>.</p></li>
<li><p>Navigate to:</p>
Agent Management → Summary
</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/install-wazuh-agent/image%205.png"
alt="image.png" /></p>
<ol type="1">
<li>Verify that the <strong>Ecoin wrk-rlin</strong> host appears as
<strong>active</strong>.
<ul>
<li>Host IP: <code>10.0.2.100</code></li>
<li>Group: Default (can be changed later).</li>
</ul></li>
</ol>
<p>If you see the agent reporting in, your installation and port
forwarding setup are working correctly.</p>
<p><img src="/assets/images/solo-purple-teaming/install-wazuh-agent/image%206.png"
alt="image.png" /></p>
<hr />
<h2 id="7-lab-completion-check">7. <strong>Lab Completion
Check</strong></h2>
<p>✅ Wazuh agent is installed on the assumed breach host.</p>
<p>✅ Agent is checking in to the Wazuh manager.</p>
<hr />
<h2 id="8-next-steps">8. <strong>Next Steps</strong></h2>
<p>In the next lecture, you will:</p>
<ul>
<li>Install <strong>Sysmon</strong> on the assumed breach host.</li>
<li>Configure Wazuh to forward Sysmon events to the Wazuh manager for
monitoring.</li>
</ul>
</section>
</div>
