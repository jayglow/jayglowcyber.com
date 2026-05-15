---
layout: default
title: "Setting Up ECORP Edge Appliance"
permalink: /solo-purple-teaming/setting-up-ecorp-edge-appliance/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up ECORP Edge Appliance</h1>
</section>
<section class="spt-content">
<h1 id="overview"><strong>Overview</strong></h1>
<p>In this lecture, we will add the <strong>E Corp edge device</strong>
to our lab environment for <strong>Attack Level Zero</strong>.</p>
<p>This will allow traffic to route from the E Corp network → through
the E Coin network → into our simulated Internet → and finally, out to
the real Internet.</p>
<p><img src="/_assets/setting-up-ecorp-edge-appliance/image.png"
alt="image.png" /></p>
<h2 id="lab-design"><strong>Lab Design</strong></h2>
<ul>
<li><strong>E Corp</strong> will be the <strong>parent
domain</strong>.</li>
<li><strong>E Coin</strong> will be the <strong>child
domain</strong>.</li>
<li>We are adding an <strong>edge device</strong> between E Corp and E
Coin.</li>
</ul>
<p><strong>E Corp Edge Device IP Plan:</strong></p>
<ul>
<li><strong>LAN IP:</strong> <code>10.0.1.3</code></li>
<li><strong>WAN IP:</strong> <code>10.0.2.4</code></li>
<li><strong>Upstream Gateway:</strong> LAN interface of the E Coin Edge
(<code>10.0.2.3</code>)</li>
</ul>
<hr />
<h2 id="step-1--clone-and-name-the-new-pfsense-device"><strong>Step 1 –
Clone and Name the New pfSense Device</strong></h2>
<ol type="1">
<li><p>In <strong>Proxmox</strong>, we cloned the pfSense
template.</p></li>
<li><p>Named it <strong>ECorp Edge</strong>.</p></li>
<li><p>By default, the WAN IP may be assigned via DHCP from your home
router</p>
<p>(e.g., <code>192.168.100.106</code> in this example — yours may
differ).</p></li>
</ol>
<hr />
<h2 id="step-2--assign-network-interfaces"><strong>Step 2 – Assign
Network Interfaces</strong></h2>
<p>If you forgot to assign a LAN interface during creation:</p>
<ol type="1">
<li>At the pfSense console menu, select <strong>Option 1</strong>
(Assign Interfaces).</li>
<li>Do <strong>not</strong> set up VLANs → select
<strong>No</strong>.</li>
<li>Assign:
<ul>
<li><strong>WAN:</strong> <code>vtnet0</code></li>
<li><strong>LAN:</strong> <code>vtnet1</code></li>
</ul></li>
<li>Confirm changes.</li>
</ol>
<hr />
<h2 id="step-3--configure-the-wan-interface"><strong>Step 3 – Configure
the WAN Interface</strong></h2>
<ol type="1">
<li><p>From the menu, select <strong>Option 2</strong> (Set Interface IP
Address).</p></li>
<li><p>Choose <strong>1 (WAN)</strong>.</p></li>
<li><p>DHCP: <strong>No</strong>.</p></li>
<li><p>Set IP: <code>10.0.2.4</code></p>
<p>Subnet: <code>/24</code></p></li>
<li><p>Upstream Gateway: <code>10.0.2.3</code> (LAN of E Coin
Edge).</p></li>
<li><p>Default Gateway: <strong>Yes</strong>.</p></li>
<li><p>IPv6: <strong>No</strong>.</p></li>
<li><p>Enable DHCP: <strong>No</strong>.</p></li>
<li><p>Revert to HTTP: <strong>No</strong>.</p></li>
</ol>
<hr />
<h2 id="step-4--configure-the-lan-interface"><strong>Step 4 – Configure
the LAN Interface</strong></h2>
<ol type="1">
<li><p>From the menu, select <strong>Option 2</strong> again.</p></li>
<li><p>Choose <strong>2 (LAN)</strong>.</p></li>
<li><p>DHCP: <strong>No</strong>.</p></li>
<li><p>Set IP: <code>10.0.1.3</code></p>
<p>Subnet: <code>/24</code></p></li>
<li><p>Upstream Gateway: <em>(leave blank — LAN doesn’t need
one)</em>.</p></li>
<li><p>IPv6: <strong>No</strong>.</p></li>
<li><p>Enable DHCP: <strong>No</strong>.</p></li>
<li><p>Revert to HTTP: <strong>No</strong>.</p></li>
</ol>
<hr />
<h2 id="step-5--temporary-host-reconfiguration"><strong>Step 5 –
Temporary Host Reconfiguration</strong></h2>
<p>Since no host exists on the new E Corp LAN yet:</p>
<ol type="1">
<li>On your <strong>Windows 11 (R-LIN)</strong> assumed breach host:
<ul>
<li>Open <strong>Network &amp; Internet → Ethernet → IPv4
Settings</strong>.</li>
<li>Set:
<ul>
<li>IP: <code>10.0.1.100</code></li>
<li>Gateway: <code>10.0.1.3</code></li>
<li>Preferred DNS: <em>(leave as is for now)</em>.</li>
</ul></li>
<li>Save settings.</li>
</ul></li>
</ol>
<hr />
<h2 id="step-6--access-the-pfsense-webconfigurator"><strong>Step 6 –
Access the pfSense WebConfigurator</strong></h2>
<ol type="1">
<li><p>In a browser, go to:</p>
<p><code>https://10.0.1.3</code></p></li>
<li><p>Accept the certificate warning → Continue.</p></li>
<li><p>Login with:</p>
<ul>
<li><strong>Username:</strong> <code>admin</code></li>
<li><strong>Password:</strong> <code>pfsense</code></li>
</ul></li>
<li><p>Change the admin password to something secure.</p></li>
</ol>
<hr />
<h2 id="step-7--allow-private-network-routing"><strong>Step 7 – Allow
Private Network Routing</strong></h2>
<p>Because we’re simulating the Internet with private IP ranges:</p>
<ol type="1">
<li>Go to <strong>Interfaces → WAN</strong>.</li>
<li>Scroll down and <strong>uncheck</strong>:
<ul>
<li><em>Block private networks and loopback addresses</em>.</li>
</ul></li>
<li><strong>Save</strong> and <strong>Apply Changes</strong>.</li>
</ol>
<hr />
<h2 id="step-8--connectivity-testing"><strong>Step 8 – Connectivity
Testing</strong></h2>
<p>From the Windows host, test each hop:</p>
<ol type="1">
<li><strong>E Corp LAN Interface</strong> →
<code>ping 10.0.1.3</code></li>
<li><strong>E Corp WAN Interface</strong> →
<code>ping 10.0.2.4</code></li>
<li><strong>E Coin LAN Interface</strong> (Upstream Gateway) →
<code>ping 10.0.2.3</code></li>
<li><strong>E Coin WAN Interface</strong> →
<code>ping 192.168.100.102</code></li>
<li><strong>Home Router LAN</strong> →
<code>ping 192.168.100.1</code></li>
<li><strong>Public Internet Test</strong>:
<ul>
<li>Temporarily connect lab to Internet.</li>
<li><code>ping google.com</code> (DNS resolution via home router).</li>
</ul></li>
</ol>
<p>If all tests pass, you have full routing from E Corp → E Coin →
simulated Internet → real Internet.</p>
<hr />
<h2 id="step-9--before-moving-on"><strong>Step 9 – Before Moving
On</strong></h2>
<p>Before the next lecture:</p>
<ul>
<li>Ensure the E Corp Edge appliance is <strong>fully
configured</strong>.</li>
<li>You can <strong>successfully ping</strong>:
<ul>
<li>E Corp WAN</li>
<li>E Coin LAN</li>
<li>E Coin WAN</li>
<li>Home Router LAN</li>
<li><code>google.com</code></li>
</ul></li>
</ul>
<hr />
<h2 id="next-lecture-preview"><strong>Next Lecture Preview</strong></h2>
<p>We will configure the <strong>E Corp Domain Controller</strong> with
the IP:</p>
CopyEdit
10.0.1.2

</section>
</div>
