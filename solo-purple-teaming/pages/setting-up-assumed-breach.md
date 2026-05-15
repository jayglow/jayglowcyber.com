---
layout: default
title: "Setting Up Assumed Breach"
permalink: /solo-purple-teaming/setting-up-assumed-breach/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up Assumed Breach</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1 id="introduction">Introduction</h1>
<p>In this lecture, we’ll set up the <strong>Assumed Breach
Host</strong> for our lab environment. This host will be used to
simulate a compromised internal system within the <strong>Ecoin
LAN</strong>.</p>
<hr />
<h2 id="1-system-requirements"><strong>1. System
Requirements</strong></h2>
<p>We will use <strong>Windows 11</strong> with the following
specifications:</p>
<ul>
<li><strong>RAM:</strong> 4 GB</li>
<li><strong>CPU:</strong> 4 cores</li>
<li><strong>Disk Space:</strong> 100 GB</li>
</ul>
<p>📌 <em>Tip:</em> You can search for <strong>Windows 11 Enterprise
Evaluation Edition</strong> online to download the ISO for installation.
This provides an evaluation period suitable for lab work.</p>
<blockquote>
<p>⚠️ Since this is an advanced course, the basic Windows installation
steps are not covered here.</p>
</blockquote>
<hr />
<h2 id="2-network-configuration"><strong>2. Network
Configuration</strong></h2>
<p>The Windows 11 host will use a <strong>static IP</strong> in the
Ecoin LAN.</p>
<ul>
<li><strong>IP Address:</strong> <code>10.0.2.100</code></li>
<li><strong>Subnet Mask:</strong> <code>255.255.255.0</code> (/24)</li>
<li><strong>Default Gateway:</strong> <code>10.0.2.3</code> (LAN
interface of the ecoin edge device)</li>
<li><strong>Preferred DNS:</strong> <code>192.168.100.1</code> (LAN
interface of home router – for Internet DNS resolution)</li>
</ul>
<hr />
<h3 id="step-by-step"><strong>Step-by-Step</strong></h3>
<ol type="1">
<li><strong>Log in</strong> to the Windows 11 Assumed Breach Host as
<strong>Administrator</strong>.</li>
<li>Open <strong>Network &amp; Internet settings</strong> →
<strong>Ethernet</strong>.</li>
<li>Locate your Ethernet adapter and select <strong>Edit IP
settings</strong>.</li>
<li>Switch to <strong>Manual</strong> configuration.</li>
<li>Enter the following:
<ul>
<li><strong>IP Address:</strong> <code>10.0.2.100</code></li>
<li><strong>Subnet mask:</strong> <code>255.255.255.0</code></li>
<li><strong>Gateway:</strong> <code>10.0.2.3</code></li>
<li><strong>Preferred DNS:</strong> <code>192.168.100.1</code></li>
</ul></li>
<li>Save your changes.</li>
</ol>
<hr />
<h2 id="3-connectivity-testing"><strong>3. Connectivity
Testing</strong></h2>
<p>We will test the configuration from the Windows host.</p>
<ol type="1">
<li><p><strong>Open Command Prompt</strong>.</p></li>
<li><p><strong>Ping the LAN interface</strong> on the ecoin edge
device:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ping</span> 10.0.2.3</span></code></pre></div>
<p>You should get a reply.</p></li>
<li><p><strong>Ping the Attack Network WAN interface</strong>:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ping</span> 192.168.100.101</span></code></pre></div>
<p>If this fails, we need to adjust firewall settings on the ecoin edge
device.</p></li>
<li><p><strong>Ping Google</strong> to confirm Internet access:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ping</span> google.com</span></code></pre></div></li>
</ol>
<hr />
<h2 id="4-adjusting-pfsense-edge-device-firewall-settings"><strong>4.
Adjusting pfSense Edge Device Firewall Settings</strong></h2>
<p>If you cannot ping across networks, the edge device may be blocking
private network traffic.</p>
<ol type="1">
<li><p>Open a browser and go to the ecoin edge device LAN interface:</p>
<pre><code>https://10.0.2.3
</code></pre></li>
<li><p><strong>Accept the security warning</strong> (click
<em>Advanced</em> → <em>Proceed</em>).</p></li>
<li><p><strong>Log in</strong> to the pfSense web UI.</p></li>
<li><p>Navigate to:</p>
<p><strong>Firewall</strong> → <strong>Rules</strong></p></li>
<li><p>Locate the option for <strong>RFC 1918 networks</strong> (Block
private networks).</p></li>
<li><p>Click the gear icon to edit the rule and
<strong>uncheck</strong>:</p>
<ul>
<li><em>Block private networks and loopback addresses</em></li>
</ul></li>
<li><p>Click <strong>Save</strong> → <strong>Apply
Changes</strong>.</p></li>
<li><p>Re-test your ping to the Attack Network WAN interface.</p></li>
</ol>
<hr />
<h2 id="5-final-verification"><strong>5. Final
Verification</strong></h2>
<p>Make sure:</p>
<ul>
<li>The Assumed Breach Host is in the E<strong>coin LAN</strong>.</li>
<li>You can ping:
<ul>
<li>The E<strong>coin LAN gateway</strong> (<code>10.0.2.3</code>)</li>
<li>The <strong>Attack Network WAN interface</strong>
(<code>192.168.100.101</code>)</li>
<li>External domains like <strong>google.com</strong> (if Internet is
enabled for the lab)</li>
</ul></li>
</ul>
<hr />
<h2 id="6-lab-best-practice"><strong>6. Lab Best Practice</strong></h2>
<ul>
<li>Keep your lab <strong>disconnected from the Internet</strong> unless
Internet access is specifically required for a task.</li>
<li>Always verify gateway and cross-network connectivity before moving
to the next phase.</li>
</ul>
<hr />
<p>✅ <strong>You’re now ready for the next lecture.</strong> Your
Windows 11 Assumed Breach Host should be configured, connected to the
ecoin LAN, and tested for connectivity both within the lab and to the
Internet.</p>
</section>
</div>
