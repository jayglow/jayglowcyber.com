---
layout: default
title: "Configure Firewall Rules"
permalink: /solo-purple-teaming/configure-firewall-rules/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Configure Firewall Rules</h1>
</section>
<section class="spt-content">
<h1 id="introduction">Introduction</h1>
<p>In this exercise, you’ll configure <strong>port forwarding
rules</strong> on the <strong>Attack LAN pfSense edge device</strong> so
that external systems can reach key services running on your Kali host
(Mythic C2, SSH, etc.).</p>
<hr />
<h2 id="1-ports-well-configure"><strong>1. Ports We’ll
Configure</strong></h2>
<p>We’ll forward the following ports to the Kali host:</p>
<table>
<thead>
<tr class="header">
<th><strong>Port</strong></th>
<th><strong>Purpose</strong></th>
<th><strong>Forward To</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>80</strong></td>
<td>Mythic C2 listener</td>
<td>Kali host</td>
</tr>
<tr class="even">
<td><strong>22</strong></td>
<td>SSH</td>
<td>Kali host</td>
</tr>
<tr class="odd">
<td><strong>7443</strong></td>
<td>Mythic Web UI</td>
<td>Kali host</td>
</tr>
<tr class="even">
<td><strong>8000</strong></td>
<td>Python HTTP server for payload downloads</td>
<td>Kali host</td>
</tr>
<tr class="odd">
<td><em>(Optional)</em> <strong>3389</strong></td>
<td>RDP to Windows RE host</td>
<td>Windows RE host</td>
</tr>
</tbody>
</table>
<hr />
<h2 id="2-identify-the-edge-device-ips"><strong>2. Identify the Edge
Device IPs</strong></h2>
<ul>
<li><strong>WAN IP</strong> (external-facing, used by outside hosts):
<code>192.168.100.101</code></li>
<li><strong>LAN IP</strong> (internal-facing, gateway for Attack LAN):
<code>10.0.3.1</code></li>
</ul>
<hr />
<h2 id="3-access-the-pfsense-web-configurator"><strong>3. Access the
pfSense Web Configurator</strong></h2>
<ol type="1">
<li><p>From your Kali Linux VM, open a browser and navigate to:</p>
https://10.0.3.1
</li>
<li><p>If this is your first login, <strong>change the default
password</strong>.</p></li>
<li><p>If you ever lose access, you can reset the password from the
pfSense console:</p>
<ul>
<li><strong>Select Option 3</strong> to reset to default
credentials.</li>
</ul></li>
</ol>
<hr />
<h2 id="4-allow-private-ip-forwarding"><strong>4. Allow Private IP
Forwarding</strong></h2>
<p>Since we’re simulating the internet inside our lab, we need the WAN
interface to accept private IPs.</p>
<ol type="1">
<li><p>In pfSense, go to:</p>
Firewall → Rules → WAN
</li>
<li><p>Click the <strong>gear icon</strong> on the WAN interface
rule.</p></li>
<li><p>Scroll down and <strong>uncheck</strong>:</p>
<ul>
<li><em>Block private networks and loopback addresses</em></li>
</ul></li>
<li><p><strong>Save</strong> and <strong>Apply
Changes</strong>.</p></li>
</ol>
<hr />
<h2 id="5-create-the-ssh-port-forward-rule-port-22--kali"><strong>5.
Create the SSH Port Forward Rule (Port 22 → Kali)</strong></h2>
<ol type="1">
<li><p>Go to:</p>
Firewall → NAT → Port Forward
</li>
<li><p>Click <strong>Add</strong>.</p></li>
<li><p>Configure:</p>
<ul>
<li><strong>Interface:</strong> WAN</li>
<li><strong>Protocol:</strong> TCP</li>
<li><strong>Destination Port Range:</strong> SSH (22)</li>
<li><strong>Redirect Target IP:</strong> <code>10.0.3.2</code> (Kali
host)</li>
<li><strong>Redirect Target Port:</strong> SSH (22)</li>
<li><strong>Description:</strong> <code>SSH to Kali host</code></li>
</ul></li>
<li><p><strong>Save</strong> and <strong>Apply
Changes</strong>.</p></li>
</ol>
<hr />
<h2 id="6-test-ssh-access"><strong>6. Test SSH Access</strong></h2>
<ol type="1">
<li><p>On Kali, start SSH:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> service ssh start</span></code></pre></div></li>
<li><p>From an <strong>external host</strong> (e.g., your Mac), run:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ssh</span> jglo@192.168.100.101</span></code></pre></div>
<ul>
<li>Replace <code>jglo</code> with your Kali username.</li>
</ul></li>
<li><p>When prompted, enter your password.</p></li>
<li><p>Verify you’re on Kali:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">hostname</span></span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="ex">ip</span> a <span class="kw">|</span> <span class="fu">grep</span> INET</span></code></pre></div>
<ul>
<li>You should see <code>10.0.3.2</code> among the IPs.</li>
</ul></li>
</ol>
<hr />
<h2 id="7-create-the-mythic-web-ui-rule-port-7443--kali"><strong>7.
Create the Mythic Web UI Rule (Port 7443 → Kali)</strong></h2>
<ol type="1">
<li><p>In <strong>pfSense</strong>:</p>
Firewall → NAT → Port Forward → Add
</li>
<li><p>Configure:</p>
<ul>
<li><strong>Interface:</strong> WAN</li>
<li><strong>Protocol:</strong> TCP</li>
<li><strong>Destination Port Range:</strong> Custom → 7443</li>
<li><strong>Redirect Target IP:</strong> <code>10.0.3.2</code> (Kali
host)</li>
<li><strong>Redirect Target Port:</strong> 7443</li>
<li><strong>Description:</strong> <code>Mythic UI</code></li>
</ul></li>
<li><p><strong>Save</strong> and <strong>Apply
Changes</strong>.</p></li>
</ol>
<hr />
<h2 id="8-test-mythic-web-ui-access"><strong>8. Test Mythic Web UI
Access</strong></h2>
<ol type="1">
<li><p>Ensure Mythic is running on Kali.</p></li>
<li><p>From an <strong>external host</strong>, go to:</p>
https://192.168.100.101:7443
</li>
<li><p>You should see the Mythic login page.</p></li>
</ol>
<hr />
<h2 id="9-other-recommended-port-forwards"><strong>9. Other Recommended
Port Forwards</strong></h2>
<p>Repeat the above steps for:</p>
<table>
<thead>
<tr class="header">
<th><strong>Port</strong></th>
<th><strong>Forward To</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>8000 (HTTP payload server)</td>
<td><code>10.0.3.2</code> (Kali)</td>
</tr>
<tr class="even">
<td>3389 (RDP)</td>
<td>Windows RE host IP</td>
</tr>
</tbody>
</table>
<hr />
<h2 id="10-challenge-for-students"><strong>10. Challenge for
Students</strong></h2>
<p>Before moving on:</p>
<ul>
<li>Configure and <strong>test</strong> all necessary port
forwards.</li>
<li>At minimum, ensure:
<ul>
<li>SSH (22) works.</li>
<li>Mythic Web UI (7443) works.</li>
<li>Payload HTTP server (8000) works.</li>
<li><em>(Optional)</em> RDP (3389) works.</li>
</ul></li>
</ul>
<p>Once complete, you’ll be ready to <strong>set up the breach
point</strong> on the Ecoin network for <strong>Attack Level
0</strong>.</p>
</section>
</div>
