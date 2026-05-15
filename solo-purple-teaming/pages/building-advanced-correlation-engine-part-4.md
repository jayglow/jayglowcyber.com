---
layout: default
title: "Building Advanced Correlation Engine - Part 4"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-4/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 4</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h2 id="1-roadmap-overview"><strong>1. Roadmap Overview</strong></h2>
<p>We’ve already:</p>
<ul>
<li>Built our advanced correlation engine.</li>
<li>Logged correlation events to <code>correlation.json</code>.</li>
</ul>
<p>Today we will:</p>
<ol type="1">
<li>Fix permissions on <code>/opt/wazuh-tools</code>.</li>
<li>Set up the Python script as a Linux <code>systemd</code>
service.</li>
<li>Configure log rotation for <code>correlation.json</code>.</li>
<li>Instruct Wazuh to monitor <code>correlation.json</code>.</li>
<li>Write a custom decoder and rule so Wazuh can parse and alert on
correlation events.</li>
</ol>
<hr />
<h3 id="2-fix-directory-permissions"><strong>2. Fix Directory
Permissions</strong></h3>
<p>The Wazuh service user must own <code>/opt/wazuh-tools</code> so it
can write logs.</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span> <span class="at">-al</span></span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a><span class="co"># Root currently owns wazuh-tools</span></span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> chown <span class="at">-R</span> wazuh:wazuh wazuh-tools</span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span> <span class="at">-al</span> wazuh-tools</span></code></pre></div>
<p>✅ Now <code>wazuh</code> owns the directory and script.</p>
<hr />
<h3 id="3-create-a-systemd-service-for-the-engine"><strong>3. Create a
Systemd Service for the Engine</strong></h3>
<p>We’ll run the correlation engine as a background service.</p>
<ol type="1">
<li>Create the service file:</li>
</ol>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi /etc/systemd/system/wazuh-ace.service</span></code></pre></div>
<ol type="1">
<li>Add:</li>
</ol>
<pre><code>[Unit]
Description=Wazuh Advanced Correlation Engine
After=network.target

[Service]
WorkingDirectory=/opt/wazuh-tools
ExecStart=/usr/bin/python3 /opt/wazuh-tools/wazuh_ace.py
User=wazuh
Group=wazuh
Restart=always
RestartSec=5
StandardOutput=append:/var/log/wazuh-ace/ace.out
StandardError=append:/var/log/wazuh-ace/ace.out

[Install]
WantedBy=multi-user.target
</code></pre>
<hr />
<h3 id="4-create-log-directory-for-the-service"><strong>4. Create Log
Directory for the Service</strong></h3>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> mkdir <span class="at">-p</span> /var/log/wazuh-ace</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> chown wazuh:wazuh /var/log/wazuh-ace</span></code></pre></div>
<hr />
<h3 id="5-start-and-enable-the-service"><strong>5. Start and Enable the
Service</strong></h3>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl daemon-reload</span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl start wazuh-ace</span>
<span id="cb5-3"><a href="#cb5-3" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl status wazuh-ace</span></code></pre></div>
<p>✅ Service is running.</p>
<hr />
<h3 id="6-test-log-writing"><strong>6. Test Log Writing</strong></h3>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">tail</span> <span class="at">-f</span> /opt/wazuh-tools/correlation.json</span></code></pre></div>
<p>Trigger an event (e.g., run the initial access payload on the assumed
breach host) and verify the log is updated.</p>
<hr />
<h3 id="7-configure-log-rotation"><strong>7. Configure Log
Rotation</strong></h3>
<p><strong>Why:</strong> Prevents <code>correlation.json</code> from
consuming all disk space.</p>
<ol type="1">
<li>Create a config:</li>
</ol>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi /etc/logrotate.d/wazuh-ace</span></code></pre></div>
<ol type="1">
<li>Add:</li>
</ol>
<pre><code>/opt/wazuh-tools/correlation.json {
    size 10M
    rotate 10
    compress
    copytruncate
}
</code></pre>
<ol type="1">
<li>Test rotation:</li>
</ol>
<div class="sourceCode" id="cb9"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> logrotate <span class="at">-f</span> /etc/logrotate.d/wazuh-ace</span></code></pre></div>
<p>✅ <code>correlation.json</code> is now empty, backups stored as
<code>.gz</code>.</p>
<hr />
<h3 id="8-configure-wazuh-to-monitor-the-log"><strong>8. Configure Wazuh
to Monitor the Log</strong></h3>
<ol type="1">
<li>Edit the main config:</li>
</ol>
<div class="sourceCode" id="cb10"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi /var/ossec/etc/ossec.conf</span></code></pre></div>
<ol type="1">
<li>Add inside <code>&lt;localfile&gt;</code> section:</li>
</ol>
<div class="sourceCode" id="cb11"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">localfile</span>&gt;</span>
<span id="cb11-2"><a href="#cb11-2" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">log_format</span>&gt;json&lt;/<span class="kw">log_format</span>&gt;</span>
<span id="cb11-3"><a href="#cb11-3" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">location</span>&gt;/opt/wazuh-tools/correlation.json&lt;/<span class="kw">location</span>&gt;</span>
<span id="cb11-4"><a href="#cb11-4" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">localfile</span>&gt;</span></code></pre></div>
<ol type="1">
<li>Restart Wazuh Manager:</li>
</ol>
<div class="sourceCode" id="cb12"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb12-1"><a href="#cb12-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart wazuh-manager</span></code></pre></div>
<hr />
<h3 id="9-add-event-type-to-python-script"><strong>9. Add Event Type to
Python Script</strong></h3>
<p>Modify <code>wazuh_ace.py</code> to tag correlation events:</p>
<div class="sourceCode" id="cb13"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb13-1"><a href="#cb13-1" aria-hidden="true" tabindex="-1"></a><span class="co">&quot;event_type&quot;</span>: <span class="st">&quot;correlation&quot;</span></span></code></pre></div>
<p>Restart service:</p>
<div class="sourceCode" id="cb14"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb14-1"><a href="#cb14-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart wazuh-ace</span></code></pre></div>
<hr />
<h3 id="10-verify-decoder-functionality"><strong>10. Verify Decoder
Functionality</strong></h3>
<ul>
<li>In Wazuh dashboard, go to <strong>Server Management →
Decoders</strong>.</li>
<li>Use the <strong>JSON default decoder</strong>
(<code>0006-json_decoders.xml</code>) to parse events.</li>
<li>Paste a sample correlation event in the decoder test and confirm all
fields are parsed.</li>
</ul>
<hr />
<h3 id="11-write-a-custom-rule"><strong>11. Write a Custom
Rule</strong></h3>
<ol type="1">
<li>Go to <strong>Server Management → Rules</strong>.</li>
<li>Create new file: <code>correlation-alerts.xml</code>.</li>
<li>Add:</li>
</ol>
<div class="sourceCode" id="cb15"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb15-1"><a href="#cb15-1" aria-hidden="true" tabindex="-1"></a>&lt;<span class="kw">group</span><span class="ot"> name=</span><span class="st">&quot;correlation-alerts&quot;</span>&gt;</span>
<span id="cb15-2"><a href="#cb15-2" aria-hidden="true" tabindex="-1"></a>  &lt;<span class="kw">rule</span><span class="ot"> id=</span><span class="st">&quot;100500&quot;</span><span class="ot"> level=</span><span class="st">&quot;12&quot;</span>&gt;</span>
<span id="cb15-3"><a href="#cb15-3" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">decoded_as</span>&gt;json&lt;/<span class="kw">decoded_as</span>&gt;</span>
<span id="cb15-4"><a href="#cb15-4" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">field</span><span class="ot"> name=</span><span class="st">&quot;event_type&quot;</span>&gt;correlation&lt;/<span class="kw">field</span>&gt;</span>
<span id="cb15-5"><a href="#cb15-5" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">match</span><span class="ot"> type=</span><span class="st">&quot;pcre2&quot;</span>&gt;.*c2.*&lt;/<span class="kw">match</span>&gt;</span>
<span id="cb15-6"><a href="#cb15-6" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">description</span>&gt;Possible C2 activity detected&lt;/<span class="kw">description</span>&gt;</span>
<span id="cb15-7"><a href="#cb15-7" aria-hidden="true" tabindex="-1"></a>    &lt;<span class="kw">group</span>&gt;attack,suspicious,c2&lt;/<span class="kw">group</span>&gt;</span>
<span id="cb15-8"><a href="#cb15-8" aria-hidden="true" tabindex="-1"></a>  &lt;/<span class="kw">rule</span>&gt;</span>
<span id="cb15-9"><a href="#cb15-9" aria-hidden="true" tabindex="-1"></a>&lt;/<span class="kw">group</span>&gt;</span></code></pre></div>
<ol type="1">
<li>Restart Wazuh Manager:</li>
</ol>
<div class="sourceCode" id="cb16"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb16-1"><a href="#cb16-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> systemctl restart wazuh-manager</span></code></pre></div>
<hr />
<h3 id="12-test-the-rule"><strong>12. Test the Rule</strong></h3>
<ul>
<li><p>Trigger a correlation event.</p></li>
<li><p>Check Wazuh dashboard — alert should appear as:</p>
<p><strong>"Possible C2 activity detected"</strong></p>
<p>with all parsed fields (<code>event_type</code>,
<code>hostname</code>, <code>image</code>, etc.).</p></li>
</ul>
<hr />
<h3 id="13-refresh-field-mapping"><strong>13. Refresh Field
Mapping</strong></h3>
<p>If you see a “no mapping” warning for <code>event_type</code>:</p>
<ul>
<li>Go to <strong>Dashboards → Management → Index
Patterns</strong>.</li>
<li>Refresh fields for the <code>alerts</code> index.</li>
</ul>
<hr />
<p>✅ <strong>End Result:</strong></p>
<p>Your <strong>custom advanced correlation engine</strong> now:</p>
<ul>
<li>Runs automatically as a background service.</li>
<li>Rotates logs to prevent disk overflow.</li>
<li>Sends events to Wazuh for parsing.</li>
<li>Generates alerts in the dashboard for correlation matches.</li>
</ul>
</section>
</div>
