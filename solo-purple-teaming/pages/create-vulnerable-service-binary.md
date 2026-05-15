---
layout: default
title: "Create Vulnerable Service Binary"
permalink: /solo-purple-teaming/create-vulnerable-service-binary/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Create Vulnerable Service Binary</h1>
</section>
<section class="spt-content">
<h2 id="1-lab-context"><strong>1. Lab Context</strong></h2>
<p><img src="/_assets/create-vulnerable-service-binary/image.png"
alt="image.png" /></p>
<ul>
<li><strong>Domains</strong>:
<ul>
<li><strong>E Corp</strong> – parent domain</li>
<li><strong>E Coin</strong> – child domain of E Corp</li>
</ul></li>
<li><strong>Assumed Breach Host</strong>:
<ul>
<li>Hostname: <code>WRK-RLIN</code></li>
<li>IP: <code>10.0.2.100</code></li>
</ul></li>
<li><strong>Lab Networking</strong>:
<ul>
<li>Three edge devices (E Corp, E Coin, Attack LAN)</li>
</ul></li>
</ul>
<hr />
<h3 id="2-objective"><strong>2. Objective</strong></h3>
<p>You will:</p>
<ol type="1">
<li>Create a <strong>service account</strong>.</li>
<li>Create a <strong>Group Policy</strong> to give that account admin
privileges and log-on rights.</li>
<li>Write a <strong>Windows service binary</strong> (placeholder) that
we can later replace with a malicious payload.</li>
<li>Configure the service permissions so another user can start/stop
it.</li>
</ol>
<hr />
<h3 id="3-remote-administration-setup"><strong>3. Remote Administration
Setup</strong></h3>
<ul>
<li><strong>Why</strong>: RSAT tools allow remote Active Directory and
Group Policy management.</li>
<li><strong>Install RSAT Tools</strong> via PowerShell:</li>
</ul>
<div class="sourceCode" id="cb1"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a>Add-WindowsCapability <span class="op">-</span>Online <span class="op">-</span>Name RSAT<span class="op">:</span>ActiveDirectory<span class="op">.</span><span class="fu">DS</span><span class="op">-</span>LDS<span class="op">.</span><span class="fu">Tools</span></span></code></pre></div>
<ul>
<li>Launch <strong>MMC</strong> as Administrator.</li>
<li>Add <strong>Active Directory Users and Computers</strong>
snap-in.</li>
</ul>
<hr />
<h3 id="4-create-the-service-account"><strong>4. Create the Service
Account</strong></h3>
<ol type="1">
<li>In <strong>Active Directory Users and Computers</strong>,
right-click <code>Users</code> → <strong>New → User</strong>.</li>
<li><strong>Name</strong>:
<ul>
<li>First: <code>ecoin</code></li>
<li>Last: <code>sync</code></li>
<li>Username: <code>svc_ecoin_sync</code></li>
</ul></li>
<li><strong>Password Settings</strong>:
<ul>
<li>Uncheck: <em>User must change password at next logon</em></li>
<li>Check: <em>Password never expires</em></li>
</ul></li>
<li>Verify account creation in the <code>Users</code> container.</li>
</ol>
<hr />
<h3 id="5-grant-admin-privileges-via-group-policy"><strong>5. Grant
Admin Privileges via Group Policy</strong></h3>
<ul>
<li>Install GPO RSAT tools:</li>
</ul>
<div class="sourceCode" id="cb2"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>Add-WindowsCapability <span class="op">-</span>Online <span class="op">-</span>Name RSAT<span class="op">:</span>GroupPolicy<span class="op">.</span><span class="fu">Management</span><span class="op">.</span><span class="fu">Tools</span></span></code></pre></div>
<ul>
<li>Add <strong>Group Policy Management</strong> snap-in to MMC.</li>
<li>Edit <strong>Default Domain Policy</strong>:
<ol type="1">
<li><p>Navigate:
<code>Computer Configuration → Preferences → Control Panel Settings → Local Users and Groups</code>.</p></li>
<li><p>Add <strong>New → Local Group</strong>:
<code>Administrators</code>.</p></li>
<li><p>Add the service account:</p>
Ecoin\svc_ecoin_sync
</li>
<li><p>Apply and close.</p></li>
</ol></li>
<li><strong>Force GPO Update</strong> on WRK-RLIN:</li>
</ul>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="ex">gpupdate</span> /force</span></code></pre></div>
<ul>
<li>Verify membership:</li>
</ul>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="ex">net</span> localgroup administrators</span></code></pre></div>
<p>Confirm <code>Ecoin\svc_ecoin_sync</code> is listed.</p>
<hr />
<h3 id="6-allow-log-on-as-a-service"><strong>6. Allow "Log on as a
Service"</strong></h3>
<ol type="1">
<li>Edit <strong>Default Domain Policy</strong> again.</li>
<li>Navigate:
<code>Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment</code>.</li>
<li>Add <code>Ecoin\svc_ecoin_sync</code> to <strong>Log on as a
service</strong>.</li>
<li>Force GPO update:</li>
</ol>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="ex">gpupdate</span> /force</span></code></pre></div>
<hr />
<h3 id="7-create-the-vulnerable-service-binary"><strong>7. Create the
Vulnerable Service Binary</strong></h3>
<ol type="1">
<li>Open <strong>Visual Studio / Visual Studio Code</strong> on WRK-RLIN
(or any dev machine).</li>
<li>Create a <strong>New Project</strong>:
<ul>
<li>Type: <strong>Windows Service (.NET Framework)</strong></li>
<li>Name: <code>EcoinSync</code></li>
</ul></li>
<li>Leave default <code>OnStart</code> and <code>OnStop</code> methods
in <code>Service1.cs</code>.</li>
<li>Change build configuration to:
<ul>
<li><strong>Release</strong></li>
<li><strong>x64 Architecture</strong></li>
</ul></li>
<li>Build solution → locate <code>.exe</code> in output folder → copy to
Desktop.</li>
<li><strong>Note</strong>: Running it directly will fail — Windows
service binaries must be started via Service Control Manager.</li>
</ol>
<hr />
<h3 id="8-create-the-service"><strong>8. Create the
Service</strong></h3>
<p>In an <strong>elevated Command Prompt</strong>:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> create <span class="st">"EcoinSync"</span> binPath= <span class="st">"C:\Users\rlin\Desktop\EcoinSync.exe"</span> start= demand obj= <span class="st">"Ecoin\svc_ecoin_sync"</span> password= <span class="st">"PasswordHere"</span></span></code></pre></div>
<ul>
<li>Verify:</li>
</ul>
<div class="sourceCode" id="cb8"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> qc EcoinSync</span></code></pre></div>
<ul>
<li>Start/Stop the service:</li>
</ul>
<div class="sourceCode" id="cb9"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> start EcoinSync</span>
<span id="cb9-2"><a href="#cb9-2" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> stop EcoinSync</span></code></pre></div>
<hr />
<h3 id="9-delegate-startstop-rights-to-another-user"><strong>9. Delegate
Start/Stop Rights to Another User</strong></h3>
<p>We want <code>Ecoin\rlin</code> to start/stop the service.</p>
<ol type="1">
<li><strong>Get Current SDDL</strong>:</li>
</ol>
<div class="sourceCode" id="cb10"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> sdshow EcoinSync</span></code></pre></div>
<ol type="1">
<li><strong>Find SID for rlin</strong> in PowerShell:</li>
</ol>
<div class="sourceCode" id="cb11"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a><span class="op">(</span><span class="fu">New-Object</span> System<span class="op">.</span><span class="fu">Security</span><span class="op">.</span><span class="fu">Principal</span><span class="op">.</span><span class="fu">NTAccount</span><span class="op">(</span><span class="st">"Ecoin\rlin"</span><span class="op">)).</span><span class="fu">Translate</span><span class="op">([</span>System<span class="op">.</span><span class="fu">Security</span><span class="op">.</span><span class="fu">Principal</span><span class="op">.</span><span class="fu">SecurityIdentifier</span><span class="op">]).</span><span class="fu">Value</span></span></code></pre></div>
<ol type="1">
<li><p><strong>Build ACE</strong>:</p>
<ul>
<li><p><code>RP</code> = Start permission</p></li>
<li><p><code>WP</code> = Stop permission</p>
<p>Format: <code>(A;;RPWP;;;SID_HERE)</code></p></li>
</ul></li>
<li><p><strong>Set New SDDL</strong>:</p></li>
</ol>
<div class="sourceCode" id="cb12"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb12-1"><a href="#cb12-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> sdset EcoinSync <span class="st">"SDDL_PART1(A;;RPWP;;;SID_HERE)SDDL_PART2"</span></span></code></pre></div>
<hr />
<h3 id="10-test-delegation"><strong>10. Test Delegation</strong></h3>
<ul>
<li><strong>As Admin</strong>:</li>
</ul>
<div class="sourceCode" id="cb13"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb13-1"><a href="#cb13-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> start EcoinSync</span></code></pre></div>
<ul>
<li><strong>As rlin</strong> (non-admin):</li>
</ul>
<div class="sourceCode" id="cb14"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb14-1"><a href="#cb14-1" aria-hidden="true" tabindex="-1"></a><span class="ex">sc</span> stop EcoinSync</span></code></pre></div>
<p>Confirm it works.</p>
<hr />
<h3 id="11-lab-completion-checklist"><strong>11. Lab Completion
Checklist</strong></h3>
<p>✅ Service account <code>svc_ecoin_sync</code> created.</p>
<p>✅ Added to Local Admins via GPO.</p>
<p>✅ Granted "Log on as a Service" rights.</p>
<p>✅ Placeholder Windows service binary created.</p>
<p>✅ Service created with SC pointing to binary.</p>
<p>✅ Delegated start/stop permissions to a non-admin user.</p>
<hr />
<p>In the <strong>next phase</strong>, you’ll replace this placeholder
binary with a malicious payload to escalate privileges from a standard
domain account to local administrator.</p>
</section>
</div>
