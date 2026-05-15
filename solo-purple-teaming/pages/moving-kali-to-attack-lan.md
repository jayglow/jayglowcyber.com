---
layout: default
title: "Moving Kali To Attack LAN"
permalink: /solo-purple-teaming/moving-kali-to-attack-lan/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Moving Kali To Attack LAN</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1 id="introduction">Introduction</h1>
<p>In this lesson, you will reconfigure your Kali host so it connects to
the <strong>Attack LAN</strong> with a static IP. This allows your Kali
box to communicate with your pfSense attack LAN gateway and, if needed,
access the Internet.</p>
<hr />
<h3 id="1-objective"><strong>1. Objective</strong></h3>
<ul>
<li>Assign Kali a <strong>static IP</strong>: <code>10.0.3.2</code></li>
<li>Use <strong>gateway</strong>: <code>10.0.3.1</code> (LAN interface
of the Attack LAN pfSense appliance)</li>
<li>Verify connectivity by pinging through multiple hops, ending with an
Internet check.</li>
</ul>
<hr />
<h3 id="2-open-network-settings"><strong>2. Open Network
Settings</strong></h3>
<ol type="1">
<li><strong>Log in</strong> to your Kali host in Proxmox.</li>
<li><strong>Right-click</strong> the network icon in the system
tray.</li>
<li>Select <strong>Edit Connections</strong>.</li>
</ol>
<hr />
<h3 id="3-check-existing-connection"><strong>3. Check Existing
Connection</strong></h3>
<ol type="1">
<li><strong>Double-click</strong> your current
<code>Wired connection 1</code>.</li>
<li>Go to the <strong>IPv4 Settings</strong> tab.</li>
<li>Notice it is set to <strong>Automatic (DHCP)</strong>, which
currently pulls an IP from your <strong>home LAN</strong>.</li>
<li>We will keep this profile but <strong>add a new one</strong> for the
lab.</li>
</ol>
<hr />
<h3 id="4-create-a-new-network-profile"><strong>4. Create a New Network
Profile</strong></h3>
<ol type="1">
<li>In the <strong>Edit Connections</strong> window, click the
<strong>+</strong> button.</li>
<li>Choose <strong>Ethernet</strong> and click
<strong>Create</strong>.</li>
<li>Name it something descriptive (e.g.,
<code>SuperCyberLab</code>).</li>
<li>Under <strong>IPv4 Settings</strong>:
<ul>
<li>Change <strong>Method</strong> to <strong>Manual</strong>.</li>
<li>Click <strong>Add</strong> and enter:
<ul>
<li><strong>Address</strong>: <code>10.0.3.2</code></li>
<li><strong>Netmask</strong>: <code>255.255.255.0</code> (/24)</li>
<li><strong>Gateway</strong>: <code>10.0.3.1</code></li>
</ul></li>
</ul></li>
<li>Click <strong>Save</strong>.</li>
</ol>
<p><em>(Optional: You can disable IPv6 for this profile by setting
<strong>IPv6 Method</strong> to "Disable.")</em></p>
<hr />
<h3 id="5-switch-to-the-new-profile"><strong>5. Switch to the New
Profile</strong></h3>
<ol type="1">
<li><strong>Right-click</strong> the network icon again.</li>
<li>Select the <strong>SuperCyberLab</strong> profile.</li>
<li>Your Kali host is now connected to the Attack LAN.</li>
</ol>
<hr />
<h3 id="6-verify-the-ip-address"><strong>6. Verify the IP
Address</strong></h3>
<ol type="1">
<li><p>Open a terminal.</p></li>
<li><p>Run:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="ex">ifconfig</span> eth0</span></code></pre></div></li>
<li><p>Confirm the IP is <code>10.0.3.2</code>.</p></li>
</ol>
<hr />
<h3 id="7-test-connectivity"><strong>7. Test Connectivity</strong></h3>
<p>Ping each hop to ensure routing is working:</p>
<table>
<thead>
<tr class="header">
<th>Test Target</th>
<th>Command</th>
<th>Expected Result</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Attack LAN pfSense LAN interface</strong></td>
<td><code>ping 10.0.3.1</code></td>
<td>Replies received</td>
</tr>
<tr class="even">
<td><strong>pfSense WAN interface</strong></td>
<td><code>ping 192.168.100.101</code></td>
<td>Replies received</td>
</tr>
<tr class="odd">
<td><strong>Home router LAN gateway</strong></td>
<td><code>ping 192.168.100.1</code></td>
<td>Replies received</td>
</tr>
<tr class="even">
<td><strong>Google.com (Internet)</strong></td>
<td><code>ping google.com</code></td>
<td>Replies received</td>
</tr>
</tbody>
</table>
<hr />
<h3 id="8-fixing-dns-resolution-issues"><strong>8. Fixing DNS Resolution
Issues</strong></h3>
<p>If <code>ping google.com</code> fails with a <strong>temporary name
resolution error</strong>:</p>
<ol type="1">
<li><p>Go back to <strong>Edit Connections</strong>.</p></li>
<li><p>Select <strong>SuperCyberLab</strong> and click
<strong>Edit</strong>.</p></li>
<li><p>Under <strong>IPv4 Settings</strong>, add your DNS server
(example: your home router gateway <code>192.168.100.1</code>).</p></li>
<li><p>Save and close.</p></li>
<li><p>Retry:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ping</span> google.com</span></code></pre></div></li>
<li><p>You should now get replies.</p></li>
</ol>
<blockquote>
<p>Note: In the final lab setup, DNS may be handled by your domain
controller in the simulated enterprise environment.</p>
</blockquote>
<hr />
<h3 id="9-homework--lab-task"><strong>9. Homework / Lab
Task</strong></h3>
<p>Before moving on:</p>
<ul>
<li>Reconfigure your <strong>Windows host</strong> to have a static IP
in the Attack LAN subnet.</li>
<li>Set its default gateway to the <strong>pfSense Attack LAN
IP</strong>.</li>
<li>Verify you can:
<ol type="1">
<li>Ping the pfSense LAN interface (<code>10.0.3.1</code>)</li>
<li>Ping the pfSense WAN interface (<code>192.168.100.101</code>)</li>
<li>Ping your home router (<code>192.168.100.1</code>)</li>
<li>Ping <code>google.com</code></li>
</ol></li>
</ul>
<hr />
<h3 id="10-whats-next"><strong>10. What’s Next</strong></h3>
<p>In the next lecture, you will:</p>
<ul>
<li>Configure pfSense firewall rules to forward:
<ul>
<li><strong>HTTP (80)</strong> → Kali for Mythic C2 listener</li>
<li><strong>8080</strong> → Kali for payload delivery</li>
<li><strong>7443</strong> → Kali for Mythic Web UI access</li>
<li><strong>22</strong> → Kali for SSH access</li>
</ul></li>
</ul>
<p>This will make external access to your Kali C2 setup much easier.</p>
</section>
</div>
