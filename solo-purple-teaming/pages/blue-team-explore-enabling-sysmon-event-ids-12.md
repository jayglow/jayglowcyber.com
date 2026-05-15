---
layout: default
title: "Blue Team Explore - Enabling Sysmon Event IDs 12,"
permalink: /solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-12/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Enabling Sysmon Event IDs 12,</h1>
</section>
<section class="spt-content">
<h1 id="blue-team-explore---enabling-sysmon-event-ids-12-13--14">Blue
Team Explore - Enabling Sysmon Event IDs 12, 13 &amp; 14</h1>
<h2 id="overview"><strong>Overview</strong></h2>
<p>In this exercise, we will enable <strong>Sysmon</strong> event IDs
<strong>12</strong>, <strong>13</strong>, and <strong>14</strong> to
monitor Windows registry changes. These events are critical for
detecting suspicious activity—particularly attacker actions involving
persistence or execution via the Windows registry.</p>
<p>For <strong>Attack Path Level One</strong>, we are specifically
interested in monitoring the <strong>Run MRU</strong> registry key,
which records commands executed from the <strong>Windows Run
dialog</strong> (e.g., <code>cmd.exe</code>,
<code>powershell.exe</code>). Detecting modifications to this key can
reveal attacker activity early in the intrusion.</p>
<hr />
<h3 id="sysmon-registry-events"><strong>Sysmon Registry
Events</strong></h3>
<ul>
<li><strong>Event ID 12 – Registry Object Create/Delete</strong>
<ul>
<li>Logs when registry keys or values are created or deleted.</li>
<li>Useful for detecting new persistence mechanisms in <strong>Run
MRU</strong> or autostart locations.</li>
</ul></li>
<li><strong>Event ID 13 – Registry Value Set</strong>
<ul>
<li>Logs when an existing registry value is modified.</li>
<li>For the <strong>Run MRU</strong> key, this shows the exact command
entered in the Run prompt.</li>
<li><strong>Most important</strong> for Attack Path Level One
detection.</li>
</ul></li>
<li><strong>Event ID 14 – Registry Object Rename</strong>
<ul>
<li>Logs when registry keys are renamed.</li>
<li>Useful for catching malware that attempts to rename keys for
obfuscation.</li>
</ul></li>
</ul>
<p>💡 <strong>Tip:</strong> Sysmon abbreviates root registry hives:</p>
<ul>
<li><code>HKCU</code> → <strong>HKEY_CURRENT_USER</strong></li>
<li><code>HKLM</code> → <strong>HKEY_LOCAL_MACHINE</strong></li>
</ul>
<hr />
<h3 id="lab-steps"><strong>Lab Steps</strong></h3>
<h3 id="step-1--open-the-sysmon-configuration"><strong>Step 1 – Open the
Sysmon Configuration</strong></h3>
<ol type="1">
<li>Log in to the <strong>Assumed Breach</strong> host.</li>
<li>Open your <strong>base Sysmon configuration file</strong>.</li>
<li>Scroll down to the section for <strong>Event IDs 12, 13, and
14</strong>.</li>
</ol>
<h3 id="step-2--enable-the-events"><strong>Step 2 – Enable the
Events</strong></h3>
<ol type="1">
<li>Locate the <code><Include></code> tag for these events.</li>
<li>Change the empty <code><Include></code> to
<code><Exclude></code> and <strong>leave it empty</strong> to
ensure <em>no registry events are excluded</em>.</li>
<li>Save the configuration file.</li>
</ol>
<hr />
<h3 id="step-3--apply-the-sysmon-config"><strong>Step 3 – Apply the
Sysmon Config</strong></h3>
<ol type="1">
<li><p>Open <strong>Command Prompt</strong> as Administrator.</p></li>
<li><p>Enter your admin credentials when prompted.</p></li>
<li><p>Change directory to the <code>Downloads</code> folder:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cd</span> C<span class="op">:</span>\Users\<span class="op"><</span>username<span class="op">></span>\Downloads</span></code></pre></div></li>
<li><p>Apply the updated Sysmon config:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>sysmon64<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>c basicsmon_sysmonconfig<span class="op">.</span><span class="fu">xml</span></span></code></pre></div></li>
<li><p>Verify that the configuration file was <strong>validated and
updated</strong>.</p></li>
</ol>
<hr />
<h3 id="step-4--verify-in-wazuh"><strong>Step 4 – Verify in
Wazuh</strong></h3>
<ol type="1">
<li><p>Open <strong>Wazuh</strong>.</p></li>
<li><p>Navigate to <strong>Explore → Discover</strong>.</p></li>
<li><p>Change the index to <strong>archives</strong>.</p></li>
<li><p>Search for:</p>
data.win.system.eventID:13

<ul>
<li>Confirm <strong>Event ID 13</strong> entries are appearing.</li>
</ul></li>
<li><p>Repeat the search for <strong>Event IDs 12 and 14</strong>:</p>
<ul>
<li>Event 12 entries should appear if registry keys have been created or
deleted.</li>
<li>Event 14 may not appear yet unless a registry rename event has
occurred.</li>
</ul></li>
</ol>
<hr />
<h3 id="validation"><strong>Validation</strong></h3>
<ul>
<li>Ensure <strong>Event ID 13</strong> logs are present—this is the key
event for Run MRU monitoring.</li>
<li>If 12 and 13 are working, 14 should also be captured when
applicable.</li>
<li>Before moving on, verify you see these events in Wazuh.</li>
</ul>
<hr />
<h3 id="homework"><strong>Homework</strong></h3>
<ul>
<li>Read the provided research article about the <strong>Run
MRU</strong> registry key and how attackers can abuse it.</li>
<li>Be prepared for the <strong>next lecture</strong>, where we will
hunt for <strong>malicious registry events</strong> and analyze their
relationship to <strong>Attack Path Level One</strong>.</li>
</ul>
</section>
</div>
