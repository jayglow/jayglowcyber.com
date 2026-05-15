---
layout: default
title: "Build Custom Rule for Network Connect to Common C2"
permalink: /solo-purple-teaming/build-custom-rule-for-network-connect-to-common-c2/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Build Custom Rule for Network Connect to Common C2</h1>
</section>
<section class="spt-content">
<h1 id="build-custom-rule-for-network-connect-to-common-c2-port">Build
Custom Rule for Network Connect to Common C2 Port</h1>
<p>Owner: Mike Sterrett</p>
<h3 id="1-purpose-of-the-rule">1. <strong>Purpose of the
Rule</strong></h3>
<p>In this lab, we’ll create a <strong>base detection rule</strong> for
spotting Command-and-Control (C2) activity. Specifically, we’ll focus on
<strong>network connections to known C2 ports</strong> (HTTP/HTTPS)
originating from suspicious directories.</p>
<p>We’ve already created a rule for detecting unsigned binaries loaded
from the Downloads folder in a previous exercise. Now, we’ll add another
rule to detect when that unsigned binary makes outbound network
connections.</p>
<hr />
<h3 id="2-indicators-of-compromise-iocs-for-attack-path-level-zero">2.
<strong>Indicators of Compromise (IoCs) for Attack Path Level
Zero</strong></h3>
<ul>
<li><strong>Unsigned binary</strong> loaded from the Downloads
folder.</li>
<li><strong>Network connections</strong> made by that unsigned binary to
<strong>port 80 (HTTP)</strong> or <strong>port 443
(HTTPS)</strong>.</li>
<li><strong>Suspicious directories</strong> involved:
<ul>
<li><code>C:\Users\...</code></li>
<li><code>C:\Windows\Temp</code></li>
</ul></li>
</ul>
<hr />
<h3 id="3-rule-definition">3. <strong>Rule Definition</strong></h3>
<p>Our new rule will:</p>
<ul>
<li>Have <strong>Rule ID</strong>: <code>100101</code> (incremented from
the previous rule’s ID).</li>
<li>Set <strong>Level</strong>: <code>12</code> (low-level security
event).</li>
<li>Reference <strong>SID 61605</strong>, which corresponds to
<strong>Sysmon Event ID 3</strong> (network connect).</li>
<li>Match the following fields:
<ul>
<li><strong>Destination Port</strong>: <code>80</code> or
<code>443</code></li>
<li><strong>Image Path</strong>: from <code>C:\Users\</code> or
<code>C:\Windows\Temp</code></li>
</ul></li>
<li><strong>Description</strong>: “Network connection to common C2 ports
from Downloads, Users, or Windows Temp directories.”</li>
</ul>
<hr />
<h3 id="4-steps-to-create-the-rule-in-wazuh-manager">4. <strong>Steps to
Create the Rule in Wazuh Manager</strong></h3>
<ol type="1">
<li><strong>Identify the Correct SID</strong>:
<ul>
<li>Search for Sysmon network connect events.</li>
<li>Confirm that <strong>Event ID 3</strong> maps to <strong>SID
61605</strong>.</li>
</ul></li>
<li><strong>Open Rules Management</strong>:
<ul>
<li>Navigate to <strong>Server Management → Rules</strong>.</li>
<li>Open your <strong>solo_purple_teaming</strong> custom rules file for
editing.</li>
</ul></li>
<li><strong>Add the New Rule</strong>:
<ul>
<li>Inside your custom group, paste the new rule block.</li>
<li>Make sure it references <strong>SID 61605</strong>.</li>
<li>Align and format the XML properly.</li>
</ul></li>
<li><strong>Save Changes</strong>:
<ul>
<li>Save the rule file.</li>
<li>Restart the Wazuh manager to apply the new rule.</li>
</ul></li>
</ol>
<hr />
<h3 id="5-testing-the-rule">5. <strong>Testing the Rule</strong></h3>
<ol type="1">
<li><strong>Restart the Manager</strong>:
<ul>
<li>Wait for it to fully restart before testing.</li>
</ul></li>
<li><strong>Trigger the Rule</strong>:
<ul>
<li>On your assumed breached host, run the initial access payload
again.</li>
<li>Confirm you receive a callback.</li>
</ul></li>
<li><strong>Check for Alerts</strong>:
<ul>
<li>In Wazuh, go to <strong>Explore → Discover</strong>.</li>
<li>Search within the last 5 minutes for <strong>Rule ID
100101</strong>.</li>
<li>Be patient—events can take a few moments to arrive.</li>
</ul></li>
<li><strong>Verify Detection</strong>:
<ul>
<li>Ensure that network connect events from the suspicious directories
are appearing in the Wazuh manager.</li>
</ul></li>
</ol>
<hr />
<h3 id="6-before-moving-on">6. <strong>Before Moving On</strong></h3>
<ul>
<li>Confirm that you have:
<ul>
<li>A <strong>custom alert</strong> for network connections to ports
80/443 from <code>Downloads</code>, <code>Users</code>, or
<code>Windows Temp</code>.</li>
<li>Successfully tested it in your environment.</li>
<li>Verified that the alerts appear in Wazuh.</li>
</ul></li>
</ul>
<hr />
<h3 id="7-next-steps">7. <strong>Next Steps</strong></h3>
<p>In the next lecture, we’ll:</p>
<ul>
<li>Build our <strong>first correlation alert</strong>.</li>
<li>Discuss the <strong>weaknesses of correlation alerts in
Wazuh</strong>.</li>
<li>Work toward a <strong>more advanced custom detection
solution</strong>.</li>
</ul>
</section>
</div>
