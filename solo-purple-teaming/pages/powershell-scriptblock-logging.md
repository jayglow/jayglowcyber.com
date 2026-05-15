---
layout: default
title: "PowerShell ScriptBlock Logging"
permalink: /solo-purple-teaming/powershell-scriptblock-logging/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>PowerShell ScriptBlock Logging</h1>
</section>
<section class="spt-content">
<h3 id="overview"><strong>Overview</strong></h3>
<p>In this lab, we will enable <strong>PowerShell Script Block
Logging</strong> on the assumed breached host and verify that the
telemetry from our initial access payload is captured both locally and
in the <strong>Wazuh Manager</strong>. Script Block Logging records the
<em>entire</em> content of executed PowerShell code, including
obfuscated or encoded scripts, making it an essential capability for
<strong>solo purple teaming</strong>.</p>
<hr />
<h3 id="why-script-block-logging-matters"><strong>Why Script Block
Logging Matters</strong></h3>
<ul>
<li>Captures <strong>actual script logic</strong>, not just command
lines.</li>
<li>Detects malicious PowerShell activity such as
<strong>LOLBins</strong>, encoded payloads, and reflective loading.</li>
<li>Critical for <strong>red-to-blue feedback loops</strong>—every
simulated malicious action can be validated from the defender’s
perspective.</li>
<li>Supports high-fidelity detection tuning and hardening against bypass
techniques.</li>
</ul>
<hr />
<h2 id="step-by-step-lab-instructions"><strong>Step-by-Step Lab
Instructions</strong></h2>
<h3 id="1-connect-to-the-assumed-breach-host"><strong>1. Connect to the
Assumed Breach Host</strong></h3>
<ol type="1">
<li>Use <strong>Remote Desktop</strong> to log in to the assumed breach
host.</li>
<li>Open a <strong>PowerShell window</strong> as
<strong>Administrator</strong>.</li>
</ol>
<hr />
<h3 id="2-open-group-policy-management"><strong>2. Open Group Policy
Management</strong></h3>
<ol type="1">
<li>In the PowerShell window, type <code>mmc</code> and press
<strong>Enter</strong> to open the Microsoft Management Console.</li>
<li>Go to <strong>File → Add/Remove Snap-in</strong>.</li>
<li>Select <strong>Group Policy Management</strong> and click
<strong>Add</strong> → <strong>OK</strong>.</li>
</ol>
<hr />
<h3 id="3-edit-the-default-domain-policy"><strong>3. Edit the Default
Domain Policy</strong></h3>
<ol type="1">
<li>In <strong>Group Policy Management</strong>, navigate to the
<strong>Default Domain Policy</strong>.</li>
<li>Right-click <strong>Edit</strong>.</li>
</ol>
<hr />
<h3 id="4-navigate-to-powershell-logging-settings"><strong>4. Navigate
to PowerShell Logging Settings</strong></h3>
<ol type="1">
<li><p>In the Group Policy Editor, go to:</p>
Computer Configuration
   → Policies
      → Administrative Templates
         → Windows Components
            → Windows PowerShell
</li>
<li><p>Here you will see several valuable settings:</p>
<ul>
<li>Module Logging</li>
<li>Script Block Logging</li>
<li>Script Execution</li>
<li>PowerShell Transcription</li>
</ul></li>
</ol>
<hr />
<h3 id="5-enable-script-block-logging"><strong>5. Enable Script Block
Logging</strong></h3>
<ol type="1">
<li>Double-click <strong>Turn on PowerShell Script Block
Logging</strong>.</li>
<li>Select <strong>Enabled</strong>.</li>
<li>Check the box to log <strong>invocation start/stop events</strong>
(generates Event IDs <strong>4103</strong>, <strong>4104</strong>, and
others).</li>
<li>Click <strong>Apply</strong> → <strong>OK</strong>.</li>
<li>Confirm the <strong>State</strong> is now <strong>Enabled</strong>
in Group Policy Management.</li>
<li>Close the editor.</li>
</ol>
<hr />
<h3 id="6-apply-the-policy"><strong>6. Apply the Policy</strong></h3>
<ol type="1">
<li><p>Back in the <strong>PowerShell (Admin)</strong> window, run:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>gpupdate <span class="op">/</span>force</span></code></pre></div></li>
<li><p>Wait for both <strong>Computer Policy</strong> and <strong>User
Policy</strong> updates to complete.</p></li>
</ol>
<hr />
<h3 id="7-test-with-initial-access-payload"><strong>7. Test with Initial
Access Payload</strong></h3>
<ol type="1">
<li>Navigate to <strong>Downloads</strong>.</li>
<li>Run your <strong>initial access payload</strong> to establish a new
callback.</li>
<li>Verify in your C2 framework (e.g., Mythic) that the callback is
active.</li>
</ol>
<hr />
<h3 id="8-view-script-block-logs-locally"><strong>8. View Script Block
Logs Locally</strong></h3>
<ol type="1">
<li><p>In PowerShell, type:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>eventvwr</span></code></pre></div>
<p>and press <strong>Enter</strong>.</p></li>
<li><p>Navigate to:</p>
Applications and Services Logs
   → Microsoft
      → Windows
         → PowerShell
            → Operational
</li>
<li><p>Look for <strong>Event ID 4104</strong>.</p></li>
<li><p>Inspect the log to see:</p>
<ul>
<li><strong>Invoke-WebRequest</strong> fetching the Apollo agent.</li>
<li><strong>System.Reflection.Assembly.Load</strong> for reflective
loading.</li>
<li>Payload execution details.</li>
</ul></li>
</ol>
<hr />
<h3 id="9-configure-wazuh-to-capture-script-block-logs"><strong>9.
Configure Wazuh to Capture Script Block Logs</strong></h3>
<ol type="1">
<li><p>In the <strong>Wazuh Manager</strong>, go to:</p>
<ul>
<li><strong>Agent Management</strong> → <strong>Groups</strong>.</li>
<li>Select the <strong>Windows group</strong> configured for Sysmon
logs.</li>
</ul></li>
<li><p>Edit the group configuration:</p>
<ul>
<li><p>Copy an existing <code><localfile></code> tag.</p></li>
<li><p>Paste below the existing entry.</p></li>
<li><p>Change the channel to:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">localfile</span>></span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">location</span>>Microsoft-Windows-PowerShell/Operational</<span class="kw">location</span>></span>
<span id="cb5-3"><a href="#cb5-3" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">log_format</span>>eventchannel</<span class="kw">log_format</span>></span>
<span id="cb5-4"><a href="#cb5-4" aria-hidden="true" tabindex="-1"></a></<span class="kw">localfile</span>></span></code></pre></div></li>
</ul></li>
<li><p>Save the configuration.</p></li>
</ol>
<hr />
<h3 id="10-push-config-to-agents"><strong>10. Push Config to
Agents</strong></h3>
<ul>
<li>The <strong>auto-restart</strong> setting on the agent will apply
the new config automatically.</li>
</ul>
<hr />
<h3 id="11-verify-in-wazuh"><strong>11. Verify in Wazuh</strong></h3>
<ol type="1">
<li><p>In Wazuh, go to:</p>
<ul>
<li><strong>Discover → Archives</strong>.</li>
</ul></li>
<li><p>Search for:</p>
4104
</li>
<li><p>If no results appear:</p>
<ul>
<li>Return to the assumed breach host.</li>
<li>Rerun the initial access payload.</li>
<li>Wait for a new callback in Mythic.</li>
</ul></li>
<li><p>Refresh the Wazuh search:</p>
<ul>
<li>You should now see <strong>Event ID 4104</strong> entries.</li>
<li>The <strong>script_block_text</strong> field should contain the
entire PowerShell code from the payload.</li>
</ul></li>
</ol>
<hr />
<h3 id="final-check"><strong>Final Check</strong></h3>
<ul>
<li>Confirm that:
<ul>
<li>Script Block Logging is enabled in Group Policy.</li>
<li>Logs appear both locally (<strong>Event Viewer</strong>) and in
<strong>Wazuh</strong>.</li>
<li>The captured script clearly shows the malicious PowerShell
activity.</li>
</ul></li>
</ul>
<hr />
<p>✅ <strong>You have successfully enabled PowerShell Script Block
Logging, verified telemetry capture locally, and integrated it into your
centralized logging platform for detection and analysis.</strong></p>
</section>
</div>
