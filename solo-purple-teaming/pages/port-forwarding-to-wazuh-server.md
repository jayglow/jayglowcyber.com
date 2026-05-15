---
layout: default
title: "Port Forwarding to Wazuh Server"
permalink: /solo-purple-teaming/port-forwarding-to-wazuh-server/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Port Forwarding to Wazuh Server</h1>
</section>
<section class="spt-content">
<h3 id="objective"><strong>Objective</strong></h3>
<p>Set up port forwarding on the <strong>AllSafe Edge</strong> device to
allow a Wazuh Agent installed on the <strong>Assume Breach Host
(wrk-rlin)</strong> to communicate with the <strong>Wazuh
Manager</strong>.</p>
<hr />
<h3 id="1-lab-network-context"><strong>1. Lab Network
Context</strong></h3>
<p><img src="/assets/images/solo-purple-teaming/port-forwarding-to-wazuh-server/image.png"
alt="image.png" /></p>
<ul>
<li><strong>Agent Host</strong>: <code>wrk-r-lin</code> →
<code>10.0.2.100</code> (Ecoin LAN)</li>
<li><strong>Wazuh Server</strong>: <code>10.0.4.2</code> (AllSafe
LAN)</li>
<li><strong>Network Flow Requirement</strong>:
<ol type="1">
<li>Traffic from the agent must pass through the <strong>Ecoin
Edge</strong> device.</li>
<li>Then, it enters through the <strong>AllSafe Edge</strong>
device.</li>
<li>Finally, it is forwarded to the Wazuh server.</li>
</ol></li>
</ul>
<hr />
<h3 id="2-access-the-allsafe-edge-device"><strong>2. Access the AllSafe
Edge Device</strong></h3>
<ol type="1">
<li><p>Log in to the <strong>Wazuh server</strong>.</p></li>
<li><p>From there, open a web browser and log into the <strong>AllSafe
Edge</strong> device (<code>LAN IP: 10.0.4.3</code>).</p></li>
<li><p>Navigate to:</p>
<p><strong>Firewall → NAT Rules</strong>.</p></li>
</ol>
<hr />
<h3 id="3-required-ports-for-wazuh"><strong>3. Required Ports for
Wazuh</strong></h3>
<p>We need to forward <strong>three specific ports</strong>:</p>
<table>
<thead>
<tr class="header">
<th>Port</th>
<th>Protocol</th>
<th>Purpose</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>1514</td>
<td>TCP/UDP</td>
<td>Wazuh Agent traffic</td>
</tr>
<tr class="even">
<td>1515</td>
<td>TCP</td>
<td>Wazuh Remote Install</td>
</tr>
<tr class="odd">
<td>55000</td>
<td>TCP</td>
<td>Wazuh REST API</td>
</tr>
</tbody>
</table>
<hr />
<h3 id="4-creating-nat-rules"><strong>4. Creating NAT
Rules</strong></h3>
<p><img src="/assets/images/solo-purple-teaming/port-forwarding-to-wazuh-server/image%201.png"
alt="image.png" /></p>
<h3 id="rule-1--port-1514"><strong>Rule 1 – Port 1514</strong></h3>
<ol type="1">
<li>Click <strong>Add</strong>.</li>
<li><strong>Protocol</strong>: TCP/UDP.</li>
<li><strong>Destination Port</strong>: <code>1514</code>.</li>
<li><strong>Redirect Target IP</strong>: <code>10.0.4.2</code> (Wazuh
server).</li>
<li><strong>Redirect Target Port</strong>: <code>1514</code>.</li>
<li>Save and Apply.</li>
<li><strong>Verify</strong>: Confirm <code>1514</code> is mapped to
<code>10.0.4.2:1514</code>.</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/port-forwarding-to-wazuh-server/image%202.png"
alt="image.png" /></p>
<hr />
<h3 id="rule-2--port-1515"><strong>Rule 2 – Port 1515</strong></h3>
<ol type="1">
<li>Click <strong>Add</strong>.</li>
<li><strong>Protocol</strong>: TCP.</li>
<li><strong>Destination Port</strong>: <code>1515</code>.</li>
<li><strong>Redirect Target IP</strong>: <code>10.0.4.2</code>.</li>
<li><strong>Redirect Target Port</strong>: <code>1515</code>.</li>
<li>Save and Apply.</li>
<li><strong>Verify</strong>: Confirm <code>1515</code> is mapped to
<code>10.0.4.2:1515</code>.</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/port-forwarding-to-wazuh-server/image%203.png"
alt="image.png" /></p>
<hr />
<h3 id="rule-3--port-55000"><strong>Rule 3 – Port 55000</strong></h3>
<ol type="1">
<li>Click <strong>Add</strong>.</li>
<li><strong>Protocol</strong>: TCP.</li>
<li><strong>Destination Port</strong>: <code>55000</code>.</li>
<li><strong>Redirect Target IP</strong>: <code>10.0.4.2</code>.</li>
<li><strong>Redirect Target Port</strong>: <code>55000</code>.</li>
<li>Save and Apply.</li>
<li><strong>Verify</strong>: Confirm <code>55000</code> is mapped to
<code>10.0.4.2:55000</code>.</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/port-forwarding-to-wazuh-server/image%204.png"
alt="image.png" /></p>
<hr />
<h3 id="5-final-verification"><strong>5. Final
Verification</strong></h3>
<ul>
<li>In the <strong>Firewall NAT Rules</strong> list, you should see all
three port forwarding rules pointing to the Wazuh server
(<code>10.0.4.2</code>) with the correct ports.</li>
<li>Ensure each rule is enabled and applied.</li>
</ul>
<p><img src="/assets/images/solo-purple-teaming/port-forwarding-to-wazuh-server/image%205.png"
alt="image.png" /></p>
<hr />
<h3 id="6-next-steps"><strong>6. Next Steps</strong></h3>
<p>With port forwarding configured, the Wazuh Agent on the Assume Breach
Host will be able to communicate with the Wazuh Manager.</p>
<p>In the next phase, we will <strong>install the Wazuh Agent</strong>
on <code>wrk-r-lin</code> and verify connectivity.</p>
</section>
</div>
