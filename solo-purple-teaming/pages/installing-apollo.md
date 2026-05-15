---
layout: default
title: "Installing Apollo"
permalink: /solo-purple-teaming/installing-apollo/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Installing Apollo</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1
id="hammer_and_wrench-installing-apollo-agent-and-c2-profiles-in-mythic">🛠️
Installing Apollo Agent and C2 Profiles in Mythic</h1>
<h2 id="jigsaw-prerequisites">🧩 Prerequisites</h2>
<ul>
<li>Mythic is already installed and running on your Kali VM.</li>
<li>You have internet access (required to clone from GitHub).</li>
<li>You are using the Mythic CLI for installing agents and
profiles.</li>
<li>You have access to the Mythic Web UI.</li>
</ul>
<hr />
<h2 id="white_check_mark-step-1-clone-and-install-apollo-agent">✅ Step
1: Clone and Install Apollo Agent</h2>
<ol type="1">
<li><p><strong>Open Terminal in your Kali VM</strong></p>
<p>Confirm Mythic is installed and running.</p></li>
<li><p><strong>Navigate to the Apollo GitHub Repo</strong></p>
<p>Visit: [<a
href="https://github.com/MythicAgents/apollo](https://github.com/MythicAgents/apollo)">https://github.com/MythicAgents/apollo](https://github.com/MythicAgents/apollo)</a></p>
<p>Scroll down to find the installation command.</p></li>
<li><p><strong>Stop Mythic (Optional but Recommended for Clean
Install)</strong></p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli stop</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Apollo/image.png" alt="image.png" /></p>
<ol type="1">
<li><p><strong>Install Apollo Agent Using the CLI</strong></p>
<p>Run the following:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli install github https://github.com/MythicAgents/apollo</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Apollo/image%201.png" alt="image.png" /></p>
<p><strong>Wait for Installation to Complete.</strong> This step pulls
the agent code from GitHub and may take some time.</p>
<ol type="1">
<li><p><strong>Start Mythic Again</strong></p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli start</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Apollo/image%202.png" alt="image.png" /></p>
<hr />
<h2 id="white_check_mark-step-2-verify-apollo-agent-in-web-ui">✅ Step
2: Verify Apollo Agent in Web UI</h2>
<ol type="1">
<li><p><strong>Open Your Browser</strong></p>
<p>Go to <code>https://localhost:7443</code></p></li>
<li><p><strong>Log in to Mythic Web UI</strong></p>
<p>If needed, get your login password using:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">grep</span> <span class="st">&quot;MythicAdminPassword&quot;</span> mythic-docker/.env</span></code></pre></div></li>
<li><p><strong>Check Installed Agents</strong></p>
<p>Go to the “Payload/ C2 Services” section.</p>
<p><img src="Installing%20Apollo/image%203.png" alt="image.png" /></p>
<p>Apollo agent should now be listed as installed.</p></li>
</ol>
<hr />
<h2 id="white_check_mark-step-3-install-http-c2-profile">✅ Step 3:
Install HTTP C2 Profile</h2>
<ol type="1">
<li><p><strong>Get HTTP C2 Profile</strong></p>
<p>Visit: [<a
href="https://github.com/MythicC2Profiles/http](https://github.com/MythicC2Profiles/http)">https://github.com/MythicC2Profiles/http](https://github.com/MythicC2Profiles/http)</a></p></li>
<li><p><strong>Stop Mythic (Before Adding Profile)</strong></p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli stop</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Apollo/image%204.png" alt="image.png" /></p>
<ol type="1">
<li><p><strong>Install the HTTP Profile</strong></p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli install github https://github.com/MythicC2Profiles/http</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Apollo/image%205.png" alt="image.png" /></p>
<p><strong>Wait for Installation to Finish</strong></p>
<hr />
<h2 id="white_check_mark-step-4-install-smb-c2-profile">✅ Step 4:
Install SMB C2 Profile</h2>
<ol type="1">
<li><p><strong>Get SMB C2 Profile</strong></p>
<p>Visit: [<a
href="https://github.com/MythicC2Profiles/smb](https://github.com/MythicC2Profiles/smb)">https://github.com/MythicC2Profiles/smb](https://github.com/MythicC2Profiles/smb)</a></p></li>
<li><p><strong>Install the SMB Profile</strong></p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli install github https://github.com/MythicC2Profiles/smb.git</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Apollo/image%206.png" alt="image.png" /></p>
<p><strong>Wait for Installation to Finish</strong></p>
<hr />
<h2 id="white_check_mark-step-5-start-mythic-and-verify-everything">✅
Step 5: Start Mythic and Verify Everything</h2>
<ol type="1">
<li><p><strong>Start Mythic Again</strong></p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli start</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Apollo/image%207.png" alt="image.png" /></p>
<ol type="1">
<li><strong>Refresh Mythic Web UI in Browser</strong></li>
</ol>
<ul>
<li>Check that the <strong>Apollo Agent</strong> is online.</li>
<li>Confirm <strong>HTTP Profile</strong> is online and accepting
connections.</li>
<li>Confirm <strong>SMB Profile</strong> is online and active.</li>
</ul>
<p><img src="Installing%20Apollo/image%208.png" alt="image.png" /></p>
<hr />
<h2 id="dart-summary--before-moving-to-the-next-lecture">🎯 Summary –
Before Moving to the Next Lecture</h2>
<p>Make sure you have all of the following working:</p>
<p>✅ Mythic is running and accessible via the web UI</p>
<p>✅ Apollo agent is installed and listed</p>
<p>✅ HTTP C2 profile is installed and active</p>
<p>✅ SMB C2 profile is installed and active</p>
<p>Once everything is in place, you're ready for the next lecture on
<strong>reflection techniques</strong>.</p>
</section>
</div>
