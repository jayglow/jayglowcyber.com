---
layout: default
title: "Establish Foothold in ECOIN"
permalink: /solo-purple-teaming/establish-foothold-in-ecoin/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Establish Foothold in ECOIN</h1>
</section>
<section class="spt-content">
<h2 id="1-understanding-initial-access"><strong>1. Understanding Initial
Access</strong></h2>
<p>In this lab, we will simulate gaining an initial foothold on the
<strong>assumed breach host</strong> within the Ecoin network.</p>
<p><img src="/_assets/establish-foothold-in-ecoin/image.png"
alt="image.png" /></p>
<p><strong>Initial Access</strong> is the stage in the attack lifecycle
where an adversary first gains entry into a target environment. This can
occur through several methods:</p>
<ul>
<li><strong>Phishing</strong> – Convincing a user to click a malicious
link or open a malicious attachment.</li>
<li><strong>Valid accounts</strong> – Logging in directly using stolen
or leaked credentials.</li>
<li><strong>Exploiting public-facing web apps</strong> – Leveraging
unpatched vulnerabilities.</li>
<li><strong>Infected USB drives</strong> – Delivered through social
engineering or dropped in public places.</li>
<li><strong>Supply chain compromise</strong> – Modifying legitimate
software updates to deliver malicious code.</li>
</ul>
<p>For this <strong>Level Zero</strong> scenario, we will
<strong>simulate</strong> that a user (Rachel Lynn) has been tricked
into downloading and executing our malicious payload—either from an
email attachment or a malicious website.</p>
<hr />
<h2 id="2-solo-purple-teaming-context"><strong>2. Solo Purple Teaming
Context</strong></h2>
<p>As a solo purple teamer, it’s important to understand the
<strong>Lockheed Martin Cyber Kill Chain</strong>.</p>
<p>When focusing on <strong>Initial Access</strong>, you’re typically
working through the first five stages:</p>
<p><strong>Reconnaissance → Weaponization → Delivery → Exploitation →
Installation</strong></p>
<p><img src="/_assets/establish-foothold-in-ecoin/image%201.png"
alt="image.png" /></p>
<p><strong>Your goal</strong> (both as attacker and defender) is to:</p>
<ul>
<li>Detect delivery attempts.</li>
<li>Block exploitation where possible.</li>
<li>Alert on suspicious installation activity.</li>
</ul>
<p>Practicing these stages in a lab helps you improve detection and
response throughout the attack chain.</p>
<hr />
<h2 id="3-lab-setup-and-scenario"><strong>3. Lab Setup and
Scenario</strong></h2>
<p>We will use <strong>Microsoft Remote Desktop</strong> to log into the
<strong>assumed breach host</strong> (WRK-R-LIN) as <strong>Rachel
Lynn</strong>.</p>
<p>Rachel Lynn will:</p>
<ol type="1">
<li>Visit a malicious website under our control.</li>
<li>Download the <strong>Goodbye AMSI</strong> payload.</li>
<li>Execute it.</li>
</ol>
<p><strong>Important:</strong> Before running the payload,
<strong>disconnect from the Internet</strong> to prevent it from being
automatically submitted to antivirus vendors. This preserves the
payload’s effectiveness for testing.</p>
<hr />
<h2 id="4-downloading-the-payload"><strong>4. Downloading the
Payload</strong></h2>
<ol type="1">
<li><p>On the assumed breach host, open a web browser.</p></li>
<li><p>Navigate to:</p>
http://192.168.100.101:8000

<ul>
<li>This IP is the <strong>WAN interface of our Attack LAN</strong>,
forwarded to the Kali host running our staging server.</li>
</ul></li>
<li><p>Locate the file <strong>Goodbye_AMSI</strong> in the directory
listing.</p></li>
<li><p>Download the file to the local system.</p></li>
</ol>
<hr />
<h2 id="5-executing-the-payload"><strong>5. Executing the
Payload</strong></h2>
<ol type="1">
<li>Navigate to the downloaded file location.</li>
<li>Double-click <strong>Goodbye_AMSI.exe</strong>.</li>
<li>You may see <strong>SmartScreen is unavailable</strong> (because of
no Internet connection).
<ul>
<li>This is expected—click <strong>Run</strong>.</li>
</ul></li>
<li>For this Level Zero lab, a console window will appear.
<ul>
<li>In a real-world scenario, you would configure this to be hidden for
stealth.</li>
</ul></li>
</ol>
<hr />
<h2 id="6-verifying-callback-in-mythic"><strong>6. Verifying Callback in
Mythic</strong></h2>
<ol type="1">
<li><p>In Mythic, go to the <strong>Callbacks</strong> tab.</p></li>
<li><p>Confirm you see a new callback from:</p>
<ul>
<li><strong>Username:</strong> <code>ecoin\rlyn</code></li>
<li><strong>IP Address:</strong> <code>10.0.2.100</code> (the static IP
of WRK-R-LIN in the Ecoin LAN)</li>
</ul></li>
<li><p>Click the callback entry to open a <strong>Callback
Tab</strong>.</p></li>
<li><p>Rename the tab to something descriptive, e.g.:</p>
Assumed Breach Host – RLIN
</li>
</ol>
<hr />
<h2 id="7-testing-agent-interactivity"><strong>7. Testing Agent
Interactivity</strong></h2>
<ol type="1">
<li><p>With the callback tab open, try running the <code>whoami</code>
command:</p>
<ul>
<li>If the command isn’t loaded yet, Mythic will compile and load it
first.</li>
</ul></li>
<li><p>Once available, execute <code>whoami</code>.</p></li>
<li><p>You should see:</p>
ecoin\rlyn

<p>for both <strong>Local Identity</strong> and <strong>Impersonation
Identity</strong>.</p></li>
</ol>
<p>This confirms we have an <strong>interactive foothold</strong> inside
the Ecoin domain.</p>
<hr />
<h2 id="8-summary--next-steps"><strong>8. Summary &amp; Next
Steps</strong></h2>
<p>At this point, you should have:</p>
<ul>
<li>Simulated a successful <strong>Initial Access</strong> via malicious
download.</li>
<li>Verified an <strong>active Mythic callback</strong> from the assumed
breach host.</li>
<li>Tested basic <strong>agent interactivity</strong> with
<code>whoami</code>.</li>
</ul>
<p><strong>Before moving on:</strong></p>
<ul>
<li>Ensure the callback is stable and responding.</li>
<li>Confirm the IP and user match the expected target in your lab
diagram.</li>
</ul>
<p><strong>Next lecture:</strong> We will perform <strong>host and
domain enumeration</strong> to generate telemetry and begin building
detections for the Ecoin environment.</p>
</section>
</div>
