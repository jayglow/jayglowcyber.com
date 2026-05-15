---
layout: default
title: "Setting Up Edge Appliances"
permalink: /solo-purple-teaming/setting-up-edge-appliances/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up Edge Appliances</h1>
</section>
<section class="spt-content">
<h1 id="wrench-setting-up-pfsense-lan-edge-devices-in-proxmox">🔧
Setting Up pfSense LAN Edge Devices in Proxmox</h1>
<h2 id="dart-objective">🎯 Objective</h2>
<p>This walkthrough guides you through setting up two pfSense edge
routers—one for the <strong>Attack LAN</strong> and one for the
<strong>Target LAN (Ecoin)</strong>—using <strong>Proxmox</strong> VMs.
Each pfSense VM will serve as a LAN gateway with <strong>static
IPs</strong>, proper interface assignments, and Internet connectivity
verification.</p>
<p><img src="/assets/images/solo-purple-teaming/setting-up-edge-appliances/image.png"
alt="image.png" /></p>
<hr />
<h2 id="desktop_computer-lab-requirements">🖥️ Lab Requirements</h2>
<ul>
<li>Proxmox environment (PVE)</li>
<li>pfSense ISO installer (from pfsense.org)</li>
<li>Two pfSense VMs with:
<ul>
<li><strong>2 GB RAM</strong></li>
<li><strong>32 GB disk</strong></li>
<li><strong>2 network adapters</strong> (WAN &amp; LAN via
<code>vmbr0</code>)</li>
</ul></li>
<li>Internet access through your home gateway (e.g.,
<code>192.168.100.1</code>)</li>
</ul>
<hr />
<h2 id="package-step-1-download-and-upload-pfsense-iso">📦 Step 1:
Download and Upload pfSense ISO</h2>
<ol type="1">
<li>Visit: <a
href="https://www.pfsense.org/download/">https://www.pfsense.org/download/</a></li>
<li>Select the <strong>Netgate Installer</strong>.</li>
<li>Upload the ISO to your <strong>Proxmox local storage</strong> via
the Proxmox web interface.</li>
</ol>
<hr />
<h2 id="bricks-step-2-create-the-first-pfsense-vm">🧱 Step 2: Create the
First pfSense VM</h2>
<ol type="1">
<li>In Proxmox, <strong>right-click</strong> your PVE node and select
<strong>Create VM</strong>.</li>
<li>Name the VM (e.g., <code>pfSense-edge-template</code>) and click
<strong>Next</strong>.</li>
<li>Under OS:
<ul>
<li>Select the <strong>uploaded pfSense ISO</strong>.</li>
</ul></li>
<li>Under System:
<ul>
<li>Leave defaults.</li>
</ul></li>
<li>Under Hard Disk:
<ul>
<li>Size: <strong>32 GB</strong> (you can reduce to 25 GB if
needed)</li>
</ul></li>
<li>Under CPU:
<ul>
<li><strong>1 socket, 1 core</strong></li>
</ul></li>
<li>Under Memory:
<ul>
<li><strong>2048 MB</strong> (can go as low as 1024 MB if resource
constrained)</li>
</ul></li>
<li>Under Network:
<ul>
<li><strong>Model</strong>: VirtIO (paravirtualized)</li>
<li><strong>Bridge</strong>: <code>vmbr0</code></li>
<li><strong>Firewall</strong>: Unchecked</li>
</ul></li>
<li>Click <strong>Finish</strong>.</li>
</ol>
<hr />
<h2 id="heavy_plus_sign-step-3-add-second-network-adapter">➕ Step 3:
Add Second Network Adapter</h2>
<ol type="1">
<li>Select the new VM.</li>
<li>Go to <strong>Hardware → Add → Network Device</strong>.</li>
<li>Set:
<ul>
<li><strong>Model</strong>: VirtIO</li>
<li><strong>Bridge</strong>: <code>vmbr0</code></li>
<li><strong>Firewall</strong>: Unchecked</li>
</ul></li>
<li>Click <strong>Add</strong>.</li>
</ol>
<hr />
<h2 id="minidisc-step-4-install-pfsense">💽 Step 4: Install pfSense</h2>
<ol type="1">
<li>Start the VM and open <strong>Console</strong>.</li>
<li>Follow these steps:
<ul>
<li>Accept the <strong>EULA</strong>.</li>
<li>Choose <strong>Install pfSense</strong>.</li>
<li>Use default <strong>keymap</strong>.</li>
<li>Proceed with <strong>Auto (UFS)</strong> installation (ZFS
optional).</li>
<li>Select the 32 GB disk.</li>
<li>Confirm installation.</li>
<li>Choose <strong>Install CE (Community Edition)</strong>.</li>
<li>Keep all defaults</li>
<li>Reboot after installation.</li>
</ul></li>
</ol>
<hr />
<h2 id="bar_chart-step-5-assign-interfaces">📊 Step 5: Assign
Interfaces</h2>
<p>When prompted:</p>
<ul>
<li><strong>WAN</strong>: <code>vtNet0</code></li>
<li><strong>LAN</strong>: <code>vtNet1</code></li>
<li>Confirm with <code>Y</code>.</li>
</ul>
<hr />
<h2 id="test_tube-step-6-convert-to-template-and-clone-vms">🧪 Step 6:
Convert to Template and Clone VMs</h2>
<ol type="1">
<li><strong>Shutdown</strong> the VM (<code>Option 6 → Y</code>).</li>
<li>Convert it to a <strong>template</strong>:
<ul>
<li>Right-click VM → Convert to Template</li>
<li>Rename to <code>pfSense-template</code> under
<strong>Options</strong></li>
</ul></li>
<li>Clone it twice:
<ul>
<li>Clone 1 → Name: <code>attack-edge</code> (full clone)</li>
<li>Clone 2 → Name: <code>ecoin-edge</code> (full clone)</li>
</ul></li>
<li>After cloning:
<ul>
<li><strong>Eject ISO disk</strong> from each clone under
<strong>Hardware</strong></li>
</ul></li>
</ol>
<hr />
<h2 id="globe_with_meridians-step-7-configure-attack-edge-ips">🌐 Step
7: Configure <code>attack-edge</code> IPs</h2>
<h3 id="boot-attack-edge-then">Boot <code>attack-edge</code>, then:</h3>
<ol type="1">
<li>Press <code>2</code> to set interface IPs.</li>
</ol>
<h3 id="wan-vtnet0">WAN (vtNet0):</h3>
<ul>
<li>Use <strong>Static IP</strong>: <code>192.168.100.101</code></li>
<li>Subnet: <code>24</code></li>
<li>Upstream gateway: <code>192.168.100.1</code></li>
<li>No DHCP / No IPv6 / No HTTP</li>
</ul>
<h3 id="lan-vtnet1">LAN (vtNet1):</h3>
<ul>
<li>Static IP: <code>10.0.3.1</code></li>
<li>Subnet: <code>24</code></li>
<li>No DHCP / No IPv6 / No HTTP</li>
</ul>
<ol type="1">
<li>Press <code>7</code> to ping: <code>google.com</code>
<ul>
<li>You should see replies.</li>
</ul></li>
</ol>
<hr />
<h2 id="globe_with_meridians-step-8-configure-ecoin-edge-ips">🌐 Step 8:
Configure <code>ecoin-edge</code> IPs</h2>
<h3 id="boot-ecoin-edge-then">Boot <code>ecoin-edge</code>, then:</h3>
<ol type="1">
<li>Press <code>2</code> to set interface IPs.</li>
</ol>
<h3 id="wan-vtnet0-1">WAN (vtNet0):</h3>
<ul>
<li>Static IP: <code>192.168.100.102</code></li>
<li>Subnet: <code>24</code></li>
<li>Gateway: <code>192.168.100.1</code></li>
<li>No DHCP / No IPv6 / No HTTP</li>
</ul>
<h3 id="lan-vtnet1-1">LAN (vtNet1):</h3>
<ul>
<li>Static IP: <code>10.0.2.3</code></li>
<li>Subnet: <code>24</code></li>
<li>No DHCP / No IPv6 / No HTTP</li>
</ul>
<ol type="1">
<li>Press <code>7</code> to ping: <code>google.com</code>
<ul>
<li>You should get successful replies.</li>
</ul></li>
</ol>
<hr />
<h2 id="white_check_mark-final-checklist-before-proceeding">✅ Final
Checklist Before Proceeding</h2>
<table>
<thead>
<tr class="header">
<th>Task</th>
<th>Completed?</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>pfSense ISO downloaded and uploaded to Proxmox</td>
<td>☐</td>
</tr>
<tr class="even">
<td>Base pfSense VM created and configured</td>
<td>☐</td>
</tr>
<tr class="odd">
<td>Converted to template</td>
<td>☐</td>
</tr>
<tr class="even">
<td>Cloned <code>attack-edge</code> and <code>ecoin-edge</code> VMs</td>
<td>☐</td>
</tr>
<tr class="odd">
<td>Static IPs configured on WAN and LAN interfaces</td>
<td>☐</td>
</tr>
<tr class="even">
<td>Verified Internet access with <code>ping google.com</code></td>
<td>☐</td>
</tr>
</tbody>
</table>
<hr />
<h2 id="compass-whats-next">🧭 What’s Next?</h2>
<p>In the next lecture, you’ll:</p>
<ul>
<li>Move your <strong>Kali host</strong> and <strong>Reverse Engineering
VM</strong> into the <strong>Attack LAN</strong></li>
<li>Assign <strong>static IPs</strong> within the
<code>10.0.3.0/24</code> subnet</li>
</ul>
<p>Make sure your edge devices are <strong>fully configured and
functional</strong> before moving on.</p>
</section>
</div>
