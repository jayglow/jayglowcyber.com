---
layout: default
title: "Build Custom Rule for Unsigned ImageLoads"
permalink: /solo-purple-teaming/build-custom-rule-for-unsigned-imageloads/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Build Custom Rule for Unsigned ImageLoads</h1>
</section>
<section class="spt-content">
<h3 id="1-objective"><strong>1. Objective</strong></h3>
<p>In this lecture, we’ll create a <strong>custom Wazuh rule</strong> to
detect unsigned binaries loaded from risky locations like
<strong>Downloads</strong> or <strong>Temp</strong> directories.</p>
<p>This rule will act as:</p>
<ul>
<li>A <strong>base detection rule</strong> now</li>
<li>A <strong>correlation rule</strong> in future lectures</li>
</ul>
<hr />
<h3
id="2-recap--indicators-of-compromise-iocs-in-attack-path-level-zero"><strong>2.
Recap – Indicators of Compromise (IoCs) in Attack Path Level
Zero</strong></h3>
<p>Before building the rule, here’s what we saw in the attack path:</p>
<ol type="1">
<li>An <strong>unsigned binary</strong> loaded from the Downloads
directory.</li>
<li>That binary made <strong>network connections to port
80</strong>.</li>
<li>Followed by <strong>user enumeration commands</strong>.</li>
<li>Then <strong>service enumeration commands</strong>.</li>
</ol>
<p>We’ll focus on the <strong>unsigned binary</strong> as our starting
detection point.</p>
<hr />
<h3 id="3-rule-design--suspicious-image-load"><strong>3. Rule Design –
Suspicious Image Load</strong></h3>
<p>We’ll create a <strong>suspicious image load rule</strong> with these
key components:</p>
<h3 id="group-tags"><strong>Group Tags</strong></h3>
<ul>
<li><code>solo_purple_teaming_c2</code> (custom group for solo purple
teaming rules)</li>
<li>Add to <strong>Windows</strong> and <strong>Sysmon</strong> groups
for proper categorization.</li>
</ul>
<h3 id="rule-metadata"><strong>Rule Metadata</strong></h3>
<ul>
<li><strong>Rule ID range</strong> for custom rules:
<code>100000–120000</code></li>
<li>Start with: <strong>100100</strong> (offset from start of range for
clarity)</li>
<li><strong>Level</strong>:
<ul>
<li>12 = Low severity</li>
<li>13 = Medium</li>
<li>14 = High</li>
<li>15 = Critical</li>
<li>For this rule: <strong>Level 12</strong></li>
</ul></li>
</ul>
<h3 id="event-type-matching"><strong>Event Type Matching</strong></h3>
<ul>
<li>We will check for <strong>Sysmon Event ID 7</strong> (Image Load
event)</li>
<li>Corresponding Wazuh SID: <strong>616009</strong></li>
</ul>
<h3 id="field-checks"><strong>Field Checks</strong></h3>
<ul>
<li><strong>Image Loaded Path</strong> matches:
<ul>
<li><code>C:\Users\...</code> through <code>Z:\Users\...</code></li>
<li>OR <code>C:\Windows\Temp\...</code> through
<code>Z:\Windows\Temp\...</code></li>
</ul></li>
<li><strong>Signed field</strong> must be <code>false</code> (indicating
unsigned binary)</li>
</ul>
<h3 id="description"><strong>Description</strong></h3>
<ul>
<li><code>"Unsigned image loaded from user or temp directory"</code></li>
</ul>
<hr />
<h3 id="4-finding-the-sysmon-event-sid"><strong>4. Finding the Sysmon
Event SID</strong></h3>
<p>Before writing the rule, find the SID for Event ID 7:</p>
<ol type="1">
<li>Go to <strong>Server Management → Rules</strong>.</li>
<li>Filter by <strong>"Sysmon"</strong>.</li>
<li>Scroll until you find <strong>Event ID 7</strong> description.</li>
<li>Note the SID: <strong>616009</strong>.</li>
</ol>
<hr />
<h3 id="5-creating-the-custom-rule-file"><strong>5. Creating the Custom
Rule File</strong></h3>
<ol type="1">
<li>In the Wazuh Manager, <strong>add a new rules file</strong>.</li>
<li>Paste the custom rule (ensure no bad characters – use standard
double quotes, remove trailing commas).</li>
<li>Example file name: <code>solo_purple_teaming.xml</code></li>
</ol>
<p><strong>Example Rule Structure</strong></p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">group</span><span class="ot"> name=</span><span class="st">"solo_purple_teaming_c2,windows,sysmon"</span>></span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>  <<span class="kw">rule</span><span class="ot"> id=</span><span class="st">"100100"</span><span class="ot"> level=</span><span class="st">"12"</span>></span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">if_sid</span>>616009</<span class="kw">if_sid</span>></span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">field</span><span class="ot"> name=</span><span class="st">"ImageLoaded"</span><span class="ot"> type=</span><span class="st">"pcre2"</span>>^[c-zC-Z]:\\Users\\|^[c-zC-Z]:\\Windows\\Temp\\</<span class="kw">field</span>></span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">field</span><span class="ot"> name=</span><span class="st">"Signed"</span>>false</<span class="kw">field</span>></span>
<span id="cb1-6"><a href="#cb1-6" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">description</span>>Unsigned image loaded from user or temp directory</<span class="kw">description</span>></span>
<span id="cb1-7"><a href="#cb1-7" aria-hidden="true" tabindex="-1"></a>  </<span class="kw">rule</span>></span>
<span id="cb1-8"><a href="#cb1-8" aria-hidden="true" tabindex="-1"></a></<span class="kw">group</span>></span></code></pre></div>
<ol type="1">
<li>Save the rule file.</li>
</ol>
<hr />
<h3 id="6-restarting-the-manager"><strong>6. Restarting the
Manager</strong></h3>
<ol type="1">
<li>Restart the Wazuh Manager to load the new rules.</li>
<li>Confirm the restart completes successfully.</li>
</ol>
<hr />
<h3 id="7-testing-the-rule"><strong>7. Testing the Rule</strong></h3>
<ol type="1">
<li><p>On the <strong>assumed breach host</strong>, run the malicious
payload.</p></li>
<li><p>Wait for the Sysmon event to be generated and sent to
Wazuh.</p></li>
<li><p>In Wazuh, go to <strong>Explore → Discover</strong>.</p></li>
<li><p>Change the time filter to the <strong>last 5
minutes</strong>.</p></li>
<li><p>In the <strong>alerts index</strong>, filter by:</p>
rule.id:100100
</li>
<li><p>Confirm you see the event triggered by the unsigned
binary.</p></li>
</ol>
<hr />
<h3 id="8-success-criteria"><strong>8. Success Criteria</strong></h3>
<p>✅ Rule created in Wazuh Manager</p>
<p>✅ Event triggers on payload execution</p>
<p>✅ Visible in Discover with correct <strong>rule ID</strong> and
description</p>
<hr />
<h3 id="9-before-moving-on"><strong>9. Before Moving On</strong></h3>
<p>Make sure you have:</p>
<ul>
<li>A <strong>working custom rule</strong> detecting unsigned image
loads from <strong>Downloads</strong> or <strong>Temp</strong></li>
<li>Verified that it triggers in a test scenario</li>
</ul>
<hr />
<h3 id="10-next-lecture"><strong>10. Next Lecture</strong></h3>
<p>We’ll build another custom rule for <strong>detecting network
connections</strong> related to this attack chain.</p>
</section>
</div>
