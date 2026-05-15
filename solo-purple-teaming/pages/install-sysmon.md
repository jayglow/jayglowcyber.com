---
layout: default
title: "Install Sysmon"
permalink: /solo-purple-teaming/install-sysmon/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Install Sysmon</h1>
</section>
<section class="spt-content">
<h3 id="1-lab-recap"><strong>1. Lab Recap</strong></h3>
<p><img src="/assets/images/solo-purple-teaming/install-sysmon/image.png" alt="image.png" /></p>
<p>Before we start installing Sysmon, here’s where we are in the
lab:</p>
<ul>
<li>We created a new LAN called <strong>AllSafe</strong>.</li>
<li>Installed a <strong>Wazuh server</strong> inside the AllSafe
LAN.</li>
<li>Installed the <strong>Wazuh agent</strong> on the assumed breach
host.</li>
<li>Configured <strong>port forwarding</strong> on the AllSafe edge
device to allow agent traffic from the assumed breach host to reach the
Wazuh server manager.</li>
</ul>
<hr />
<h3 id="2-download-sysmon"><strong>2. Download Sysmon</strong></h3>
<ol type="1">
<li><strong>Connect to the assumed breach host</strong> via Remote
Desktop.</li>
<li>Open a browser and search for <strong>“Sysmon
download”</strong>.</li>
<li>Navigate to the official <strong>Microsoft Sysmon download
page</strong>.</li>
<li>Download the latest version (at the time of recording:
<strong>Sysmon v15.15</strong>).</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/install-sysmon/image%201.png" alt="image.png" /></p>
<ol type="1">
<li>Locate the downloaded file in your <strong>Downloads</strong>
folder.</li>
<li><strong>Extract the archive</strong>:
<ul>
<li>Right-click the downloaded ZIP file → <strong>Extract All</strong> →
Confirm extraction.</li>
<li>You should now see the Sysmon binaries in the extracted folder.</li>
</ul></li>
</ol>
<hr />
<h3 id="3-download-the-base-configuration"><strong>3. Download the Base
Configuration</strong></h3>
<ol type="1">
<li>Go to the <strong>GitHub page</strong> associated with this course
(Sysmon Modular repository).</li>
<li>Locate the <strong>base Sysmon config</strong> file.</li>
<li>Click the config file to open it, then click
<strong>Download</strong>.</li>
<li>If prompted by the browser, click <strong>Keep</strong> to save
it.</li>
<li>The base config should now be in your <strong>Downloads</strong>
folder.</li>
</ol>
<hr />
<h3 id="4-modify-the-sysmon-configuration"><strong>4. Modify the Sysmon
Configuration</strong></h3>
<ol type="1">
<li><p>Right-click the downloaded base config file → <strong>Open with
Notepad</strong>.</p></li>
<li><p>Zoom in if needed for readability.</p></li>
<li><p><strong>Add a process creation rule</strong> to log PowerShell
executions:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode xml"><code class="sourceCode xml"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><<span class="kw">ProcessCreate</span><span class="ot"> onmatch=</span><span class="st">"include"</span>></span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>    <<span class="kw">Image</span><span class="ot"> condition=</span><span class="st">"is"</span>>powershell.exe</<span class="kw">Image</span>></span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a></<span class="kw">ProcessCreate</span>></span></code></pre></div></li>
<li><p>Save the file.</p></li>
</ol>
<hr />
<h3 id="5-install-sysmon"><strong>5. Install Sysmon</strong></h3>
<ol type="1">
<li><p>Open <strong>PowerShell as Administrator</strong> (can use the
same admin session from the Wazuh agent install).</p></li>
<li><p>Navigate to the Downloads folder:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cd</span> C<span class="op">:</span>\Users\rlynn\Downloads</span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span></span></code></pre></div>
<p>(Replace <code>rlynn</code> with your username if
different.)</p></li>
<li><p>Verify you see:</p>
<ul>
<li><strong>Sysmon folder</strong> (with binaries)</li>
<li><strong>Base config file</strong></li>
</ul></li>
<li><p>Run the Sysmon installation command:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode powershell"><code class="sourceCode powershell"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="op">.</span>\Sysmon64<span class="op">.</span><span class="fu">exe</span> <span class="op">-</span>i baseconfig<span class="op">.</span><span class="fu">xml</span> <span class="op">-</span>accepteula</span></code></pre></div>
<p>(Replace <code>baseconfig.xml</code> with your config file’s exact
name if different.)</p></li>
<li><p>Confirm installation messages show:</p>
<ul>
<li>Driver installed</li>
<li>Service started</li>
</ul></li>
</ol>
<hr />
<h3 id="6-verify-sysmon-logging"><strong>6. Verify Sysmon
Logging</strong></h3>
<ol type="1">
<li><p>Still in the admin session, open <strong>Event
Viewer</strong>.</p></li>
<li><p>Navigate to:</p>
Applications and Services Logs
    └── Microsoft
        └── Windows
            └── Sysmon
                └── Operational
</li>
<li><p>Double-click <strong>Operational</strong> and confirm you see
active Sysmon events.</p></li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/install-sysmon/image%202.png" alt="image.png" /></p>
<hr />
<h3 id="7-lab-checkpoint"><strong>7. Lab Checkpoint</strong></h3>
<p>✅ Sysmon is now installed and logging events on the assumed breach
host.</p>
<p>Before moving on:</p>
<ul>
<li>Ensure Sysmon events are visible in Event Viewer under the
<strong>Operational</strong> log.</li>
</ul>
<hr />
<p><strong>Next Step:</strong> In the following lecture, we will
configure <strong>Wazuh</strong> to collect and process these Sysmon
logs using the central configuration file <code>agent.conf</code>.</p>
</section>
</div>
