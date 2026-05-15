---
layout: default
title: "Testing Apollo on Assumed Breach Host"
permalink: /solo-purple-teaming/testing-apollo-on-assumed-breach-host/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Testing Apollo on Assumed Breach Host</h1>
</section>
<section class="spt-content">
<h1 id="introduction">Introduction</h1>
<h3 id="1-network-layout-recap"><strong>1. Network Layout
Recap</strong></h3>
<p>Before generating the payload, understand your lab network
layout:</p>
<p><img src="/assets/images/solo-purple-teaming/testing-apollo-on-assumed-breach-host/image.png"
alt="image.png" /></p>
<ul>
<li><strong>Attack LAN</strong>
<ul>
<li><strong>Hosts:</strong> Kali host, Reverse Engineering host</li>
<li><strong>LAN interface (Attack Edge):</strong>
<code>10.0.3.1</code></li>
<li><strong>WAN IP (Attack Edge):</strong>
<code>192.168.100.101</code></li>
</ul></li>
<li><strong>Ecoin LAN</strong>
<ul>
<li><strong>Host:</strong> Assumed Breach Host
(<code>work-r-lin</code>)</li>
<li><strong>LAN interface (Ecoin Edge):</strong>
<code>10.0.2.3</code></li>
<li><strong>WAN IP (Ecoin Edge):</strong>
<code>192.168.100.102</code></li>
<li><strong>Assumed Breach Host IP:</strong>
<code>10.0.2.100</code></li>
</ul></li>
</ul>
<p>⚠ <strong>Key Point:</strong> The Ecoin LAN cannot see internal
Attack LAN addresses.</p>
<p>Traffic from Ecoin → Attack LAN must go through firewall port
forwarding rules.</p>
<hr />
<h3
id="2-verify-security-settings-on-the-assumed-breach-host"><strong>2.
Verify Security Settings on the Assumed Breach Host</strong></h3>
<ol type="1">
<li>Confirm <strong>you are not connected to the Internet</strong>.</li>
<li>Disable the following in <strong>Windows Security</strong>:
<ul>
<li><strong>Cloud-delivered protection</strong></li>
<li><strong>Automatic sample submission</strong></li>
</ul></li>
<li>Keep <strong>Real-time protection ON</strong> – we are testing a
Defender bypass.</li>
</ol>
<hr />
<h3 id="3-check-firewall--port-forwarding-rules"><strong>3. Check
Firewall &amp; Port Forwarding Rules</strong></h3>
<p><img
src="/assets/images/solo-purple-teaming/testing-apollo-on-assumed-breach-host/image%201.png"
alt="image.png" /></p>
<p>On the <strong>Attack LAN pfSense</strong>:</p>
<ul>
<li>Create port forwarding rules for:
<ul>
<li><strong>80</strong> (HTTP – Mythic C2 callback)</li>
<li><strong>8000</strong> (Python Simple HTTP Server – payload
delivery)</li>
<li><strong>7443</strong> (Mythic Web UI)</li>
<li><strong>22</strong> (SSH – optional)</li>
</ul></li>
<li><strong>Critical for this test:</strong> ports <strong>80</strong>
and <strong>8000</strong> must be working.</li>
</ul>
<hr />
<h3 id="4-generate-apollo-payload-in-mythic"><strong>4. Generate Apollo
Payload in Mythic</strong></h3>
<ol type="1">
<li><p>On the <strong>Kali host</strong>, log in to Mythic.</p></li>
<li><p>Go to <strong>Payloads → Actions → Generate New
Payload</strong>.</p></li>
<li><p>Select:</p>
<ul>
<li><strong>Type:</strong> Windows</li>
<li><strong>Agent:</strong> Apollo</li>
<li><strong>Format:</strong> Executable (WinXe)</li>
</ul></li>
<li><p>Click <strong>Next</strong> – no additional options, bare minimum
payload.</p></li>
<li><p><strong>Listener:</strong> HTTP</p></li>
<li><p><strong>Callback Host:</strong> Use the <strong>Attack Edge WAN
IP</strong> → <code>192.168.100.101</code></p>
<ul>
<li>This IP will forward traffic to Kali on port 80 where Mythic
listens.</li>
</ul></li>
<li><p>Click <strong>Next</strong> and name the payload:</p>
<p><code>Apollo_breach_test</code></p></li>
<li><p>Click <strong>Create Payload</strong>.</p></li>
<li><p>Once generated, <strong>download</strong> it to the Kali
Downloads folder.</p>
<ul>
<li>Delete any older versions.</li>
<li>Rename the new file for clarity.</li>
</ul></li>
</ol>
<hr />
<h3 id="5-host-the-payload-with-python-http-server"><strong>5. Host the
Payload with Python HTTP Server</strong></h3>
<ol type="1">
<li><p>In Kali, stop any running server with
<code>CTRL + C</code>.</p></li>
<li><p>Start a new server:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> <span class="at">-m</span> http.server 8000</span></code></pre></div></li>
<li><p>This listens on <strong>port 8000</strong>, which is forwarded
from the Attack Edge WAN to Kali.</p></li>
</ol>
<hr />
<h3 id="6-download-payload-on-the-assumed-breach-host"><strong>6.
Download Payload on the Assumed Breach Host</strong></h3>
<ol type="1">
<li><p>Open a browser on the <strong>Assumed Breach
Host</strong>.</p></li>
<li><p>Enter:</p>
http://192.168.100.101:8000
</li>
<li><p>You should see the payload list.</p></li>
<li><p>Download the new <code>Apollo_breach_test</code> executable.</p>
<ul>
<li>Ignore SmartScreen warning (no Internet → SmartScreen not
reachable).</li>
<li>Keep the file.</li>
</ul></li>
</ol>
<hr />
<h3 id="7-execute-the-payload"><strong>7. Execute the
Payload</strong></h3>
<ol type="1">
<li>Open the downloaded file location.</li>
<li>Run <code>Apollo_breach_test.exe</code>.</li>
<li>Switch to Mythic:
<ul>
<li>You should see a <strong>new callback</strong> from the assumed
breach host.</li>
</ul></li>
<li>Hide any old callbacks from previous tests.</li>
</ol>
<hr />
<h3 id="8-end-the-callback"><strong>8. End the Callback</strong></h3>
<ol type="1">
<li>In Mythic, select the new callback.</li>
<li>Send the <strong>exit</strong> command:
<ul>
<li><code>exit</code> → terminate the agent.</li>
</ul></li>
<li>On the breach host, the process should terminate.</li>
</ol>
<hr />
<h3 id="9-clean-up"><strong>9. Clean Up</strong></h3>
<ol type="1">
<li>Move the payload to the Trash.</li>
<li>Empty the Trash – never leave payloads on test machines to avoid
accidental execution or detection.</li>
</ol>
<hr />
<h3 id="10-ready-for-next-steps"><strong>10. Ready for Next
Steps</strong></h3>
<p>✅ At this point, you should have:</p>
<ul>
<li>A working Apollo payload on the assumed breach host.</li>
<li>Successful Mythic C2 callback with Defender running.</li>
<li>Verified port forwarding for payload delivery &amp; C2 traffic.</li>
</ul>
<hr />
<h3 id="coming-next"><strong>Coming Next</strong></h3>
<p>In the <strong>next lecture</strong>, you’ll expand the lab:</p>
<ul>
<li>Add <strong>two domain controllers</strong>:
<ul>
<li><strong>ECorp</strong> (Parent Domain)</li>
<li><strong>ECoin</strong> (Child Domain)</li>
</ul></li>
<li>Create <strong>domain trust</strong> for cross-domain
enumeration.</li>
<li>Add another edge device for E Corp LAN.</li>
<li>Make the Ecoin DC <strong>dual-homed</strong>:
<ul>
<li>One interface in ECorp LAN</li>
<li>One interface in ECoin LAN</li>
</ul></li>
<li>Prepare for <strong>Attack Path Level 0</strong> – Solo Purple
Teaming with advanced red/blue tactics.</li>
</ul>
</section>
</div>
