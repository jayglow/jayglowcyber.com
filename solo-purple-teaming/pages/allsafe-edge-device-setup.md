---
layout: default
title: "AllSafe Edge Device Setup"
permalink: /solo-purple-teaming/allsafe-edge-device-setup/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>AllSafe Edge Device Setup</h1>
</section>
<section class="spt-content">
<h3 id="objective"><strong>Objective</strong></h3>
<p>In this lab, you will configure the <strong>AllSafe Edge
Device</strong> in pfSense with the correct WAN and LAN IP addresses,
verify connectivity, and prepare the environment for building the
<strong>Wazuh SIEM server</strong> in the next step.</p>
<hr />
<h2 id="1-review-the-network-diagram"><strong>1. Review the Network
Diagram</strong></h2>
<p><img src="/assets/images/solo-purple-teaming/allsafe-edge-device-setup/image.png"
alt="image.png" /></p>
<p>From the diagram:</p>
<ul>
<li><strong>WAN Interface IP:</strong> <code>192.168.100.103</code></li>
<li><strong>LAN Interface IP:</strong> <code>10.0.4.3</code></li>
</ul>
<p>The WAN connects to the home network, and the LAN connects to the
AllSafe internal network where the Wazuh server will be located.</p>
<hr />
<h2 id="2-start-the-allsafe-edge-device"><strong>2. Start the AllSafe
Edge Device</strong></h2>
<ol type="1">
<li>In <strong>Proxmox</strong> (or your virtual lab platform), clone
your <strong>pfSense template</strong>.</li>
<li>Power on the cloned VM for the AllSafe Edge device.</li>
<li>Open the <strong>Console</strong> for this VM.</li>
</ol>
<hr />
<h2 id="3-initial-pfsense-console-access"><strong>3. Initial pfSense
Console Access</strong></h2>
<p>When the pfSense console main menu appears, you’ll see that the WAN
interface has obtained a <strong>DHCP IPv4 address</strong> from your
home LAN (e.g., <code>192.168.100.106</code>).</p>
<p>We will replace this with the static WAN IP from the diagram.</p>
<hr />
<h2 id="4-configure-the-wan-interface"><strong>4. Configure the WAN
Interface</strong></h2>
<ol type="1">
<li><p>At the menu, type <code>2</code> to <strong>Set interface(s) IP
address</strong>.</p></li>
<li><p>Select the <strong>WAN</strong> interface.</p></li>
<li><p>When prompted for DHCP, type <code>n</code> (No).</p></li>
<li><p>Enter the WAN IP:</p>
192.168.100.103
</li>
<li><p>For the subnet bit count, enter:</p>
24

<p>(This corresponds to <code>255.255.255.0</code>.)</p></li>
<li><p>For the upstream gateway, enter:</p>
192.168.100.1

<p><em>(This is the LAN interface of your home router.)</em></p></li>
<li><p>When asked about IPv6 configuration, type <code>n</code>
(No).</p></li>
<li><p>Skip any additional IPs by pressing
<strong>Enter</strong>.</p></li>
<li><p>When asked to enable DHCP on this interface, type
<code>n</code>.</p></li>
<li><p>When prompted to revert to HTTP, type <code>n</code>.</p></li>
</ol>
<hr />
<h2 id="5-configure-the-lan-interface"><strong>5. Configure the LAN
Interface</strong></h2>
<ol type="1">
<li><p>From the interface configuration menu, select
<strong>LAN</strong>.</p></li>
<li><p>Type <code>n</code> for DHCP.</p></li>
<li><p>Enter the LAN IP:</p>
10.0.4.3
</li>
<li><p>For the subnet bit count, enter:</p>
24
</li>
<li><p>Leave the upstream gateway blank and press <strong>Enter</strong>
(LAN doesn’t require one).</p></li>
<li><p>Type <code>n</code> for IPv6.</p></li>
<li><p>Press <strong>Enter</strong> to skip additional IPs.</p></li>
<li><p>Type <code>n</code> for enabling DHCP on LAN.</p></li>
<li><p>Type <code>n</code> for reverting changes.</p></li>
</ol>
<hr />
<h2 id="6-verify-network-connectivity"><strong>6. Verify Network
Connectivity</strong></h2>
<ol type="1">
<li><p>From the main pfSense console menu, type <code>7</code> to
<strong>Ping host</strong>.</p></li>
<li><p>First, test connectivity to your home router:</p>
192.168.100.1

<ul>
<li>You should receive replies.</li>
</ul></li>
<li><p>Next, test Internet connectivity by pinging Google:</p>
google.com

<ul>
<li>A successful reply confirms both WAN and DNS are functioning.</li>
</ul></li>
</ol>
<hr />
<h2 id="7-lab-completion-check"><strong>7. Lab Completion
Check</strong></h2>
<p>Before moving on:</p>
<ul>
<li>Confirm the AllSafe Edge Device <strong>WAN</strong> IP is
<code>192.168.100.103</code>.</li>
<li>Confirm the <strong>LAN</strong> IP is <code>10.0.4.3</code>.</li>
<li>Verify you can <strong>ping the home router</strong> and
<strong>google.com</strong> from pfSense.</li>
</ul>
<hr />
<h2 id="next-steps"><strong>Next Steps</strong></h2>
<p>In the upcoming lecture, you will:</p>
<ul>
<li>Build and configure the <strong>Wazuh server</strong> inside the
AllSafe LAN.</li>
<li>Test full network connectivity between devices.</li>
<li>Install the Wazuh EDR agent on <code>wrk-r-lin</code> to forward
events to the SIEM.</li>
</ul>
</section>
</div>
