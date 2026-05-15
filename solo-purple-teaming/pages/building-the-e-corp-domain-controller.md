---
layout: default
title: "Building the E Corp Domain Controller"
permalink: /solo-purple-teaming/building-the-e-corp-domain-controller/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building the E Corp Domain Controller</h1>
</section>
<section class="spt-content">
<h3 id="overview"><strong>Overview</strong></h3>
<p><img src="/_assets/building-the-e-corp-domain-controller/image.png"
alt="image.png" /></p>
<p>In this lab, we will build the <strong>ECorp Domain
Controller</strong> (Ecorp-DC-1) and configure it with:</p>
<ul>
<li><strong>Static IP</strong>: <code>10.0.1.2</code></li>
<li><strong>Default Gateway</strong>: <code>10.0.1.3</code> (LAN
interface of the E Corp Edge device)</li>
</ul>
<p>We’ll use a Windows Server installation (2019 in this example) and
Proxmox for virtualization. The process includes creating a template VM,
cloning it for the E Corp DC, configuring network settings, and
promoting it to a domain controller.</p>
<hr />
<h2 id="step-1--create-a-windows-server-template"><strong>Step 1 –
Create a Windows Server Template</strong></h2>
<ol type="1">
<li><strong>Log into Proxmox</strong>
<ul>
<li>You may be using a different virtualization platform; steps will be
similar.</li>
</ul></li>
<li><strong>Create a New VM</strong>
<ul>
<li>Click <strong>Create VM</strong> (example done on host
<code>PVE3</code>).</li>
<li><strong>Name</strong>: <code>DC-template</code></li>
<li><strong>ISO Image</strong>: Select Windows Server (2019 used here,
but 2022 or 2025 are fine).</li>
<li>Add <strong>VirtIO drivers</strong> ISO for storage and network
support.</li>
</ul></li>
<li><strong>System Settings</strong>
<ul>
<li>Enable <strong>QEMU Agent</strong>.</li>
<li>Storage: Local LVM Thin (100 GB disk).</li>
<li>CPU: 4 cores.</li>
<li>Memory: 4096 MB (4 GB).</li>
<li>Network Adapter: Select <strong>Intel</strong> (avoid extra driver
installs), <strong>Firewall disabled</strong>.</li>
</ul></li>
<li><strong>Install Windows Server</strong>
<ul>
<li>Start VM and press any key to boot from the ISO.</li>
<li>Language: English → <strong>Next</strong> → <strong>Install
Now</strong>.</li>
<li>Edition: Datacenter (Desktop Experience).</li>
<li>Accept terms → Custom Install.</li>
<li>Load VirtIO storage driver (<code>amd64\2k19</code>).</li>
<li>Install Windows normally.</li>
</ul></li>
<li><strong>Initial Setup</strong>
<ul>
<li>Set Administrator password.</li>
<li>Log in to verify installation.</li>
<li>Power down VM.</li>
<li>Right-click VM → <strong>Convert to Template</strong>.</li>
</ul></li>
</ol>
<hr />
<h2 id="step-2--clone-template-for-e-corp-dc"><strong>Step 2 – Clone
Template for E Corp DC</strong></h2>
<ol type="1">
<li><strong>Clone Template</strong>
<ul>
<li>Right-click the template → <strong>Clone</strong>.</li>
<li>Name: <code>ecorp-dc-1</code>.</li>
<li>Type: <strong>Full Clone</strong>.</li>
<li>Wait for cloning to finish.</li>
</ul></li>
<li><strong>Start the VM</strong>
<ul>
<li>Log in to Windows Server.</li>
</ul></li>
</ol>
<hr />
<h2 id="step-3--configure-system-settings"><strong>Step 3 – Configure
System Settings</strong></h2>
<ol type="1">
<li><strong>Rename Computer</strong>
<ul>
<li>Go to <strong>System Properties</strong> → <strong>Change
Settings</strong>.</li>
<li>Name: <code>ecorp-dc-1</code>.</li>
<li>Restart when prompted.</li>
</ul></li>
<li><strong>Configure Network</strong>
<ul>
<li><p>Go to <strong>Network &amp; Internet Settings</strong> →
<strong>Change adapter options</strong>.</p></li>
<li><p>Right-click <strong>Ethernet</strong> → Properties →
<strong>IPv4</strong>:</p>
<ul>
<li><p>IP Address: <code>10.0.1.2</code></p></li>
<li><p>Subnet Mask: <code>255.255.255.0</code></p></li>
<li><p>Default Gateway: <code>10.0.1.3</code></p></li>
<li><p>Preferred DNS: <code>192.168.100.1</code> (LAN interface on home
router)</p>
<p><em>Optional Alternate DNS: <code>8.8.8.8</code></em></p></li>
</ul></li>
<li><p>Disable IPv6.</p></li>
<li><p>Test connectivity:</p>
<ul>
<li><code>ping 10.0.1.3</code> (E Corp Edge LAN)</li>
<li><code>ping 10.0.2.4</code> (E Corp Edge WAN)</li>
<li><code>ping 10.0.2.3</code> (Ecoin Edge LAN)</li>
<li><code>ping 192.168.100.102</code> (Ecoin Edge WAN)</li>
<li><code>ping 192.168.100.1</code> (Home Router)</li>
</ul></li>
<li><p>Temporarily connect lab to the internet →
<code>ping google.com</code> (verify DNS).</p></li>
</ul></li>
</ol>
<hr />
<h2
id="step-4--install-active-directory-domain-services-ad-ds"><strong>Step
4 – Install Active Directory Domain Services (AD DS)</strong></h2>
<ol type="1">
<li><strong>Open Server Manager</strong>
<ul>
<li>Click <strong>Manage</strong> → <strong>Add Roles and
Features</strong>.</li>
<li><strong>Role-based or feature-based installation</strong>.</li>
<li>Select this server → <strong>Next</strong>.</li>
<li>Check <strong>Active Directory Domain Services</strong> → Add
required features → <strong>Next</strong>.</li>
<li>Confirm and <strong>Install</strong>.</li>
</ul></li>
<li><strong>Promote to Domain Controller</strong>
<ul>
<li>Click the <strong>Post-deployment Configuration</strong>
notification.</li>
<li>Choose <strong>Add a new forest</strong>.</li>
<li>Root domain name: <code>ecorp.lab</code>.</li>
<li>Functional level: Windows Server 2016.</li>
<li>Keep defaults for DNS and GC; set <strong>Directory Services Restore
Mode password</strong>.</li>
<li>NetBIOS name: <code>ECORP</code>.</li>
<li>Review → Install → Allow server to reboot.</li>
</ul></li>
</ol>
<hr />
<h2 id="step-5--verify-domain-controller"><strong>Step 5 – Verify Domain
Controller</strong></h2>
<ol type="1">
<li><strong>Log in to Domain</strong>
<ul>
<li>Use credentials: <code>ECORP\Administrator</code>.</li>
</ul></li>
<li><strong>Test DNS &amp; Connectivity</strong>
<ul>
<li>Open Command Prompt:
<ul>
<li><code>ping google.com</code> → should return replies.</li>
</ul></li>
</ul></li>
</ol>
<hr />
<h2 id="final-checklist-before-moving-on"><strong>Final Checklist Before
Moving On</strong></h2>
<p>✅ <code>ecorp-dc-1</code> has the correct hostname and IP
(<code>10.0.1.2</code>).</p>
<p>✅ Gateway and DNS are properly configured.</p>
<p>✅ Can ping internal lab interfaces and external domains.</p>
<p>✅ Domain <code>ecorp.lab</code> is fully functional.</p>
<hr />
<p><strong>Next Lecture Preview</strong>: We will clone the template
again to create the <strong>Ecoin Domain Controller</strong>, then
configure it as a <strong>child domain</strong> under the E Corp parent
domain.</p>
</section>
</div>
