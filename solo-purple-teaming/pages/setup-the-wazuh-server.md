---
layout: default
title: "Setup the Wazuh Server"
permalink: /solo-purple-teaming/setup-the-wazuh-server/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setup the Wazuh Server</h1>
</section>
<section class="spt-content">
<h3 id="1-objective"><strong>1. Objective</strong></h3>
<p>In this lecture, we will:</p>
<ul>
<li>Create a new server VM.</li>
<li>Install <strong>Wazuh</strong> on it.</li>
<li>Configure it to work within the <strong>AllSafe LAN</strong>.</li>
<li>Prepare it for future lectures where we’ll use Wazuh to monitor
endpoints.</li>
</ul>
<hr />
<h3 id="2-lab-overview"><strong>2. Lab Overview</strong></h3>
<p>Our lab diagram now includes a <strong>new LAN</strong> called
<strong>AllSafe</strong>.</p>
<p>The Wazuh server will be installed in this LAN and connected via our
pfSense Edge device.</p>
<p><img src="/_assets/setup-the-wazuh-server/image.png"
alt="image.png" /></p>
<hr />
<h3 id="3-creating-the-wazuh-server-vm"><strong>3. Creating the Wazuh
Server VM</strong></h3>
<p><strong>Steps:</strong></p>
<ol type="1">
<li><strong>Select Host in Proxmox</strong>
<ul>
<li>In the example, <code>PVE4</code> is used (in a four-host
cluster).</li>
<li>You can choose any available host in your environment.</li>
</ul></li>
<li><strong>Create a New VM</strong>
<ul>
<li>Give it a name (e.g., <code>wazuh-demo</code>).</li>
<li>Select the ISO: <strong>Ubuntu 24.04 LTS</strong> (other Linux
distributions will work as well).</li>
<li>Check the <strong>QEMU agent</strong> option (optional but
useful).</li>
</ul></li>
<li><strong>Assign Resources</strong> (based on Wazuh’s recommendations
for 100–125 agents):
<ul>
<li><strong>Disk</strong>: 100 GB</li>
<li><strong>CPUs</strong>: 4</li>
<li><strong>RAM</strong>: 8 GB (8192 MB)</li>
</ul></li>
<li><strong>Disable the Proxmox Firewall</strong> for this VM (optional
for lab use).</li>
<li><strong>Finish VM Creation</strong> and <strong>Start</strong>
it.</li>
</ol>
<hr />
<h3 id="4-installing-ubuntu"><strong>4. Installing Ubuntu</strong></h3>
<ol type="1">
<li>Launch the VM console.</li>
<li>Select <strong>Try or Install Ubuntu</strong>.</li>
<li>Go through the setup wizard:
<ul>
<li>Language, time zone, and keyboard layout.</li>
<li><strong>Normal Installation</strong> (for browser and
utilities).</li>
<li>Erase disk and install Ubuntu.</li>
<li>Create a user account and password.</li>
</ul></li>
<li>Wait for the installation to complete, then <strong>restart</strong>
the VM.</li>
<li><strong>Remove the installation ISO</strong>:
<ul>
<li>Hardware → CD/DVD Drive → <strong>Do Not Use Media</strong>.</li>
</ul></li>
<li>Log in to Ubuntu.</li>
</ol>
<hr />
<h3 id="5-installing-wazuh"><strong>5. Installing Wazuh</strong></h3>
<ol type="1">
<li><p>Open a browser and navigate to the <strong>Wazuh Quickstart
Installation</strong> documentation.</p></li>
<li><p>Review system requirements — our VM meets them.</p></li>
<li><p>Copy the installation command from the documentation.</p></li>
<li><p>Open a terminal, paste the command, and run it.</p>
<p><em>(You will be prompted for your password.)</em></p></li>
<li><p>Wait for the installation to complete — this takes several
minutes.</p></li>
</ol>
<hr />
<h3 id="6-saving-admin-credentials"><strong>6. Saving Admin
Credentials</strong></h3>
<ul>
<li>After installation, Wazuh provides an <strong>admin
password</strong>.</li>
<li><strong>Save this password securely</strong> — it is difficult to
recover if lost.</li>
</ul>
<hr />
<h3 id="7-first-login-to-wazuh-web-ui"><strong>7. First Login to Wazuh
Web UI</strong></h3>
<ol type="1">
<li><p>In a browser, go to:</p>
https://localhost
</li>
<li><p>Accept the <strong>self-signed certificate</strong>
warning.</p></li>
<li><p>Log in with:</p>
<ul>
<li>Username: <code>admin</code></li>
<li>Password: (the one provided during install)</li>
</ul></li>
</ol>
<hr />
<h3 id="8-creating-a-secondary-admin-account"><strong>8. Creating a
Secondary Admin Account</strong></h3>
<ol type="1">
<li><p>Navigate to:</p>
<p><strong>Management → Security → Internal Users</strong>.</p></li>
<li><p>Create a new user account with a memorable password.</p></li>
<li><p>Assign <strong>admin privileges</strong> to this account.</p>
<p><em>(This ensures you can still log in if the original password is
lost.)</em></p></li>
</ol>
<hr />
<h3 id="9-assigning-a-static-ip-allsafe-lan"><strong>9. Assigning a
Static IP (AllSafe LAN)</strong></h3>
<ol type="1">
<li>Open <strong>Network Settings</strong> in Ubuntu.</li>
<li>Edit the active wired connection.</li>
<li>Under <strong>IPv4 Settings</strong>:
<ul>
<li>Change to <strong>Manual</strong>.</li>
<li>Address: <code>10.0.4.2</code></li>
<li>Netmask: <code>/24</code></li>
<li>Gateway: <code>10.0.4.3</code> (LAN interface on AllSafe
pfSense)</li>
<li>DNS: <code>192.168.10.1</code> (router interface)</li>
</ul></li>
<li>Save and reconnect.</li>
<li>Verify connectivity:
<ul>
<li><code>ping google.com</code> (external test)</li>
<li><code>ip a</code> (check assigned IP)</li>
</ul></li>
</ol>
<hr />
<h3 id="10-testing-pfsense-access"><strong>10. Testing pfSense
Access</strong></h3>
<ol type="1">
<li><p>In a browser, go to:</p>
https://10.0.4.3
</li>
<li><p>Accept certificate warning.</p></li>
<li><p>Log in (default: <code>admin</code> / <code>pfsense</code>) and
change the admin password.</p></li>
</ol>
<hr />
<h3 id="11-setting-up-port-forwarding-for-wazuh-access"><strong>11.
Setting Up Port Forwarding for Wazuh Access</strong></h3>
<ol type="1">
<li>In pfSense:
<ul>
<li><strong>Firewall → NAT → Port Forward → Add</strong>
<ul>
<li>Protocol: TCP</li>
<li>Destination Port: HTTPS (443)</li>
<li>Redirect IP: <code>10.0.4.2</code> (Wazuh server)</li>
<li>Redirect Port: HTTPS</li>
<li>Description: <code>Wazuh Dashboard</code></li>
</ul></li>
<li>Save and Apply Changes.</li>
</ul></li>
<li><strong>Allow Private Networks</strong>:
<ul>
<li>Interfaces → WAN</li>
<li>Uncheck <strong>Block private networks</strong> and <strong>Block
bogon networks</strong>.</li>
<li>Save and Apply.</li>
</ul></li>
</ol>
<hr />
<h3 id="12-accessing-wazuh-from-outside"><strong>12. Accessing Wazuh
from Outside</strong></h3>
<ol type="1">
<li><p>In a browser on another machine, go to:</p>
https://192.168.100.103

<p><em>(WAN interface of AllSafe edge device)</em></p></li>
<li><p>Accept certificate warning.</p></li>
<li><p>Log in to the Wazuh Web UI.</p></li>
</ol>
<hr />
<h3 id="13-verification-before-moving-on"><strong>13. Verification
Before Moving On</strong></h3>
<p>Before the next lecture, ensure:</p>
<ul>
<li>Wazuh server is <strong>installed and running</strong>.</li>
<li>You can <strong>ping internal and external addresses</strong>.</li>
<li>pfSense web configurator is accessible.</li>
<li>Wazuh dashboard is reachable from your host machine.</li>
</ul>
<hr />
<h3 id="14-next-steps"><strong>14. Next Steps</strong></h3>
<p>In the following lectures, we will:</p>
<ul>
<li>Set up port forwarding for Wazuh agent traffic.</li>
<li>Install the Wazuh agent on <code>wrk-r-lin</code>.</li>
<li>Forward telemetry to the Wazuh server.</li>
<li>Take a <strong>crash course on Wazuh</strong>:
<ul>
<li>Building dashboards</li>
<li>Setting up detections</li>
<li>Analyzing telemetry</li>
</ul></li>
<li>Deploy <strong>Sysmon</strong> and forward its event data to
Wazuh.</li>
</ul>
</section>
</div>
