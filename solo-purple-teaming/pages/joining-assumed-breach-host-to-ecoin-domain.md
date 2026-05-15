---
layout: default
title: "Joining Assumed Breach Host to ECOIN Domain"
permalink: /solo-purple-teaming/joining-assumed-breach-host-to-ecoin-domain/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Joining Assumed Breach Host to ECOIN Domain</h1>
</section>
<section class="spt-content">
<h3 id="introduction"><strong>Introduction</strong></h3>
<p>In this exercise, you’ll join the assumed breach host
(<code>wrk-rlin</code>) to the <strong>Ecoin</strong> domain using a
newly created domain account. You’ll also troubleshoot common domain
join issues.</p>
<p><img
src="/assets/images/solo-purple-teaming/joining-assumed-breach-host-to-ecoin-domain/image.png"
alt="image.png" /></p>
<hr />
<h2 id="step-1--review-the-network-setup"><strong>Step 1 – Review the
Network Setup</strong></h2>
<p>Before starting, ensure you understand your lab layout:</p>
<ul>
<li><strong>Host:</strong> <code>wrk-rlin</code> (Windows)</li>
<li><strong>IP Address:</strong> <code>10.0.2.100</code></li>
<li><strong>Domain Controller (Ecoin):</strong>
<code>10.0.2.2</code></li>
<li><strong>Domain Name:</strong> <code>ecoin</code></li>
</ul>
<hr />
<h2 id="step-2--create-a-domain-user"><strong>Step 2 – Create a Domain
User</strong></h2>
<ol type="1">
<li><p>Log in to the <strong>Ecoin Domain Controller</strong> in
Proxmox.</p></li>
<li><p>Open <strong>Active Directory Users and Computers</strong>:</p>
<p><code>Tools → Active Directory Users and Computers</code></p></li>
<li><p>Right-click <strong>Users</strong> → select <strong>New →
User</strong>.</p></li>
<li><p>Enter:</p>
<ul>
<li><strong>First Name:</strong> Rachel</li>
<li><strong>Last Name:</strong> Lynn</li>
<li><strong>User logon name:</strong> <code>R.Lin</code> (which becomes
<code>ecoin\rlin</code>)</li>
</ul></li>
<li><p>Click <strong>Next</strong>.</p></li>
<li><p>Enter a password.</p>
<ul>
<li><strong>Uncheck:</strong> "User must change password at next
logon"</li>
<li><strong>Check:</strong> "Password never expires"</li>
</ul></li>
<li><p>Click <strong>Next</strong>, then
<strong>Finish</strong>.</p></li>
<li><p>Verify that <strong>Rachel Lynn</strong> now appears under the
<strong>Users</strong> container.</p></li>
</ol>
<hr />
<h2 id="step-3--prepare-the-assumed-breach-host"><strong>Step 3 –
Prepare the Assumed Breach Host</strong></h2>
<ol type="1">
<li>Log in to the <strong>wrk-rlin</strong> host.</li>
<li>Change the <strong>computer name</strong>:
<ul>
<li>Go to <strong>Control Panel → System → Change settings →
Change</strong>.</li>
<li>Enter new name: <code>ecoin-wrk-rlin</code>.</li>
<li>Click <strong>OK</strong> and restart the computer.</li>
</ul></li>
</ol>
<hr />
<h2 id="step-4--attempt-domain-join"><strong>Step 4 – Attempt Domain
Join</strong></h2>
<ol type="1">
<li>After restart, go back to:
<ul>
<li><strong>Control Panel → System → Change settings →
Change</strong></li>
</ul></li>
<li>Under <strong>Member of</strong>, select <strong>Domain</strong> and
enter: <code>ecoin</code>.</li>
<li>Click <strong>OK</strong>.</li>
<li>Enter credentials for <code>ecoin\rlin</code>.</li>
</ol>
<hr />
<h2 id="step-5--troubleshoot-domain-join-failure"><strong>Step 5 –
Troubleshoot Domain Join Failure</strong></h2>
<p>If the join fails with “Cannot find domain controller”:</p>
<ol type="1">
<li>Check DNS settings:
<ul>
<li>Go to <strong>Ethernet Adapter Properties → IPv4
Settings</strong>.</li>
<li>Change the <strong>Preferred DNS server</strong> to the
<strong>Domain Controller’s IP</strong> (<code>10.0.2.2</code>).</li>
</ul></li>
<li>Save and close.</li>
<li>Test connectivity:
<ul>
<li><p>Open Command Prompt.</p></li>
<li><p>Run:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ping</span> ecoin-dc1</span></code></pre></div></li>
<li><p>You should get replies from the domain controller.</p></li>
</ul></li>
</ol>
<hr />
<h2 id="step-6--successfully-join-the-domain"><strong>Step 6 –
Successfully Join the Domain</strong></h2>
<ol type="1">
<li>Repeat the domain join:
<ul>
<li><strong>Control Panel → System → Change settings →
Change</strong></li>
<li>Enter domain: <code>ecoin</code></li>
<li>Provide <code>ecoin\rlin</code> credentials.</li>
</ul></li>
<li>You should now see <strong>"Welcome to the Ecoin
domain"</strong>.</li>
<li>Restart the computer.</li>
</ol>
<hr />
<h2 id="step-7--log-in-as-the-new-domain-user"><strong>Step 7 – Log in
as the New Domain User</strong></h2>
<ol type="1">
<li>On the login screen, select <strong>Other User</strong>.</li>
<li>Enter:
<ul>
<li>Username: <code>ecoin\rlin</code></li>
<li>Password: (the one you set in Step 2)</li>
</ul></li>
<li>You should see <strong>"Welcome Rachel Lynn"</strong> as the profile
is created.</li>
</ol>
<hr />
<h2 id="step-8--security-note"><strong>Step 8 – Security
Note</strong></h2>
<ul>
<li><strong>Any authenticated user</strong> in the domain can add
computer objects to it.</li>
<li>From an attacker’s perspective:
<ul>
<li>If they have valid domain credentials, they could join a new host to
the domain.</li>
<li>This allows them to operate from a “bring-your-own-device” (BYOD)
machine, avoiding logs from compromised hosts.</li>
<li>This technique can help evade detection in a real attack
scenario.</li>
</ul></li>
</ul>
<hr />
<h2 id="step-9--final-verification"><strong>Step 9 – Final
Verification</strong></h2>
<ul>
<li>Ensure <code>wrk-rlin</code> is joined to the Ecoin domain.</li>
<li>Successfully log in with the <code>ecoin\rlin</code> account.</li>
</ul>
<hr />
<p>✅ <strong>Next Step in the Lab:</strong></p>
<p>We will configure a vulnerable service for enumeration during the
Attack Path Level 0 exercise.</p>
</section>
</div>
