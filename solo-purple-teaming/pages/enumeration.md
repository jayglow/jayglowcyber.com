---
layout: default
title: "Enumeration"
permalink: /solo-purple-teaming/enumeration/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Enumeration</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h2 id="overview"><strong>Overview</strong></h2>
<p>In this phase, we move into the <strong>second stage</strong> of
Attack Path Level Zero — <strong>Enumeration</strong>.</p>
<p>After gaining <strong>initial access</strong> to a compromised
system, the goal is to gain <strong>situational awareness</strong>. This
involves discovering:</p>
<ul>
<li>What system we’re on</li>
<li>Who we are (username and privileges)</li>
<li>What else is running in the environment</li>
</ul>
<p>Enumeration gives us the context needed to decide whether to proceed
with:</p>
<ul>
<li><strong>Privilege escalation</strong></li>
<li><strong>Lateral movement</strong></li>
<li><strong>Target identification</strong></li>
</ul>
<p>For a <strong>solo purple teamer</strong>, this simulates the
reconnaissance an attacker would perform immediately after compromise,
and helps train you to think like an adversary.</p>
<hr />
<h2 id="step-1--starting-enumeration-in-mythic"><strong>Step 1 –
Starting Enumeration in Mythic</strong></h2>
<ol type="1">
<li><strong>Open Mythic UI</strong> and navigate to your active
callback.
<ul>
<li>This is your communication channel with the compromised system.</li>
</ul></li>
<li><strong>Check current user and privileges</strong>:
<ul>
<li><p>Task Name: <code>shell</code></p></li>
<li><p>Command:</p>
<pre><code>whoami /priv
</code></pre></li>
<li><p>This tells us:</p>
<ul>
<li>The logged-in user</li>
<li>The privileges associated with that account</li>
</ul></li>
<li><p>In this case, nothing overly interesting is found — but executing
these commands simulates attacker behavior and creates telemetry
artifacts for later detection.</p></li>
</ul></li>
</ol>
<hr />
<h2 id="step-2--identify-local-administrators"><strong>Step 2 – Identify
Local Administrators</strong></h2>
<ol type="1">
<li><strong>Find members of the local Administrators group</strong>:
<ul>
<li><p>Task Name: <code>shell</code></p></li>
<li><p>Command:</p>
<pre><code>net localgroup administrators
</code></pre></li>
<li><p>Review the results for unusual accounts.</p>
<ul>
<li>Here, we see <strong><code>svc_ecoin_sync</code></strong> — a
service account in the Administrators group.</li>
</ul></li>
</ul></li>
</ol>
<hr />
<h2 id="step-3--enumerate-services"><strong>Step 3 – Enumerate
Services</strong></h2>
<p>We want to see if that <code>svc_ecoin_sync</code> account is tied to
a service.</p>
<ol type="1">
<li><strong>List all services with start names</strong> using WMI:
<ul>
<li><p>Task Name: <code>shell</code></p></li>
<li><p>Command:</p>
<pre><code>wmic service get name,displayname,startname
</code></pre></li>
<li><p>Look for services running as
<strong><code>svc_ecoin_sync</code></strong>.</p></li>
<li><p>We find:</p>
<ul>
<li><strong>Service Name:</strong> <code>ecoin sync</code></li>
<li><strong>Display Name:</strong> <code>Ecoin Sync</code></li>
<li><strong>Runs as:</strong> <code>ecoin\svc_ecoin_sync</code> (local
admin privileges)</li>
</ul></li>
</ul></li>
</ol>
<hr />
<h2 id="step-4--investigate-the-service"><strong>Step 4 – Investigate
the Service</strong></h2>
<p>We need more details about the vulnerable service.</p>
<ol type="1">
<li><strong>Query service configuration</strong>:
<ul>
<li><p>Task Name: <code>shell</code></p></li>
<li><p>Command:</p>
<pre><code>sc qc &quot;ecoin sync&quot;
</code></pre></li>
<li><p>Review:</p>
<ul>
<li><p><strong>Binary Path:</strong></p>
<pre><code>C:\Users\rlyn\Desktop\ecoin_sync.exe
</code></pre></li>
<li><p>This binary is located in the current user’s desktop
directory.</p></li>
</ul></li>
</ul></li>
</ol>
<hr />
<h2 id="step-5--check-service-permissions"><strong>Step 5 – Check
Service Permissions</strong></h2>
<p>We want to see if the current user can modify or control this
service.</p>
<ol type="1">
<li><strong>View the service’s security descriptor</strong>:
<ul>
<li><p>Task Name: <code>shell</code></p></li>
<li><p>Command:</p>
<pre><code>sc sdshow &quot;ecoin sync&quot;
</code></pre></li>
<li><p>Look for <strong>Access Control Entries (ACEs)</strong> that
grant permissions to the current user.</p></li>
</ul></li>
<li><strong>Find the current user’s SID</strong> to match against ACEs:
<ul>
<li><p>Task Name: <code>shell</code></p></li>
<li><p>Command:</p>
<pre><code>wmic useraccount where name=&quot;%username%&quot; get name,sid
</code></pre></li>
<li><p>Compare the SID with entries from the <code>sc sdshow</code>
output.</p>
<ul>
<li>Here, the SID matches, confirming <strong><code>rlyn</code></strong>
can <strong>start and stop</strong> the <code>ecoin sync</code>
service.</li>
</ul></li>
</ul></li>
</ol>
<hr />
<h2 id="step-6--attacker-logic"><strong>Step 6 – Attacker
Logic</strong></h2>
<p>From an attacker’s perspective, the findings are:</p>
<ol type="1">
<li><strong><code>svc_ecoin_sync</code></strong> is in the
<strong>Administrators group</strong>.</li>
<li>This account runs the <strong><code>Ecoin Sync</code></strong>
service.</li>
<li>The service binary is located in a directory <strong>writable by the
current user</strong>.</li>
<li>The current user has <strong>permissions to start/stop the
service</strong>.</li>
</ol>
<p>This makes the service a <strong>privilege escalation
target</strong>.</p>
<hr />
<h2 id="key-takeaways"><strong>Key Takeaways</strong></h2>
<ul>
<li><strong>Purpose:</strong> Simulate realistic post-compromise
enumeration to create telemetry artifacts for later detection
engineering.</li>
<li><strong>Findings in This Lab:</strong>
<ul>
<li><code>svc_ecoin_sync</code> (local admin)</li>
<li>Service binary located in writable path</li>
<li>Current user (<code>rlyn</code>) can control the service</li>
</ul></li>
<li><strong>Next Step:</strong> Use this misconfiguration for
<strong>privilege escalation</strong>.</li>
</ul>
<hr />
<p>✅ <strong>Lab Checklist Completed:</strong></p>
<ul class="task-list">
<li><label><input type="checkbox" checked="" />Verified current user and
privileges</label></li>
<li><label><input type="checkbox" checked="" />Enumerated local
administrators</label></li>
<li><label><input type="checkbox" checked="" />Identified services and
start accounts</label></li>
<li><label><input type="checkbox" checked="" />Located vulnerable binary
path</label></li>
<li><label><input type="checkbox" checked="" />Confirmed user
permissions on service</label></li>
</ul>
<hr />
<p>Next lecture: <strong>Privilege Escalation with Vulnerable
Service</strong></p>
</section>
</div>
