---
layout: default
title: "Installing Mythic C2"
permalink: /solo-purple-teaming/installing-mythic-c2/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Installing Mythic C2</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h1 id="toolbox-mythic-c2-installation-walkthrough">🧰 Mythic C2
Installation Walkthrough</h1>
<blockquote>
<p>This walkthrough installs Mythic on a Kali VM inside a
<strong>Proxmox lab;</strong> however, Mythic also works on
<strong>Ubuntu 20.04 or later</strong>.</p>
</blockquote>
<hr />
<h2 id="small_blue_diamond-step-1-prepare-your-kali-vm">🔹 Step 1:
Prepare Your Kali VM</h2>
<blockquote>
<p>First, we need to prepare our system, to do that we need internet
access."</p>
</blockquote>
<ol type="1">
<li><p>Open a terminal in Kali.</p></li>
<li><p>Navigate to <code>/opt</code>, the preferred location for
optional tools.</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt</span></code></pre></div></li>
<li><p><em>(Optional)</em> You can group installs inside a dedicated
directory:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> mkdir <span class="at">-p</span> /opt/mythic</span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/mythic</span></code></pre></div></li>
</ol>
<blockquote>
<p>You don’t need to make this directory. Cloning the repo will make it,
but I like organizing things this way.</p>
</blockquote>
<p><img src="Installing%20Mythic%20C2/image.png" alt="image.png" /></p>
<hr />
<h2 id="small_blue_diamond-step-2-clone-the-mythic-github-repository">🔹
Step 2: Clone the Mythic GitHub Repository</h2>
<blockquote>
<p>The first thing we do is copy this clone command from the docs.</p>
</blockquote>
<ol type="1">
<li><p>Run the following command to clone the Mythic repo:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> git clone https://github.com/its-a-feature/Mythic.git</span></code></pre></div></li>
<li><p>Move into the cloned directory:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> Mythic</span></code></pre></div></li>
</ol>
<p><img src="Installing%20Mythic%20C2/image%201.png"
alt="image.png" /></p>
<ol type="1">
<li><p>List the contents to verify the <code>Makefile</code> is
present:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span></span></code></pre></div></li>
</ol>
<p><img src="Installing%20Mythic%20C2/image%202.png"
alt="image.png" /></p>
<hr />
<h2 id="small_blue_diamond-step-3-install-docker-if-needed">🔹 Step 3:
Install Docker (If Needed)</h2>
<blockquote>
<p>Ensure we have Docker installed.</p>
<p>Mythic provides a helper script for Kali users:</p>
</blockquote>
<ol type="1">
<li><p>Run the install script provided in the repo:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./install_docker_kali.sh</span></code></pre></div>
<p><img src="Installing%20Mythic%20C2/image%203.png"
alt="image.png" /></p></li>
</ol>
<blockquote>
<p>If you're using a fresh Kali install, Docker might not be installed.
This script handles it.</p>
<p>If Docker is already installed, the script will exit cleanly.</p>
</blockquote>
<hr />
<h2 id="small_blue_diamond-step-4-build-mythic-with-make">🔹 Step 4:
Build Mythic with Make</h2>
<blockquote>
<p>Now that we have Docker installed, we can use make in the Mythic
directory where the makefile is located.</p>
</blockquote>
<ol type="1">
<li><p>Build Mythic using <code>make</code>:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> make</span></code></pre></div></li>
<li><p>Confirm the Mythic CLI binary was created:</p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="fu">ls</span></span></code></pre></div></li>
</ol>
<blockquote>
<p>"You should now see the mythic CLI binary in the directory."</p>
</blockquote>
<p><img src="Installing%20Mythic%20C2/image%204.png"
alt="image.png" /></p>
<hr />
<h2 id="small_blue_diamond-step-5-start-mythic">🔹 Step 5: Start
Mythic</h2>
<blockquote>
<p>Now, when we run this, it will make a .env file with the
passwords.</p>
<p>We’re going to start Mythic with the CLI.</p>
</blockquote>
<ol type="1">
<li><p>Start Mythic using the CLI tool:</p>
<div class="sourceCode" id="cb9"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli start</span></code></pre></div></li>
</ol>
<blockquote>
<p>It’s going to grab everything, download the containers, and then
start them up. Wait until all Docker containers are downloaded and
initialized.</p>
</blockquote>
<p><img src="Installing%20Mythic%20C2/image%205.png"
alt="image.png" /></p>
<hr />
<h2 id="small_blue_diamond-step-6-access-the-mythic-web-ui">🔹 Step 6:
Access the Mythic Web UI</h2>
<blockquote>
<p>"We should be able to jump into our browser and go to localhost on
port 7443..."</p>
</blockquote>
<ol type="1">
<li><p>In your Kali browser, navigate to:</p>
<pre><code>https://localhost:7443
</code></pre></li>
<li><p>If prompted, click <strong>Advanced</strong> and <strong>Accept
the Risk</strong> (for the self-signed cert).</p></li>
</ol>
<p><img src="Installing%20Mythic%20C2/image%206.png"
alt="image.png" /></p>
<ol type="1">
<li>You’ll arrive at the Mythic login page.</li>
</ol>
<p><img src="Installing%20Mythic%20C2/image%207.png"
alt="image.png" /></p>
<hr />
<h2 id="small_blue_diamond-step-7-get-the-mythic-admin-password">🔹 Step
7: Get the Mythic Admin Password</h2>
<blockquote>
<p>Now we need to get the default mythic admin password.</p>
</blockquote>
<ol type="1">
<li><p>Run the following to extract the password:</p>
<div class="sourceCode" id="cb11"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a><span class="fu">cat</span> .env <span class="kw">|</span> <span class="fu">grep</span> <span class="at">-i</span> password</span></code></pre></div></li>
</ol>
<blockquote>
<p>The password is randomly generated. You’ll see something like
MYTHIC_ADMIN_PASSWORD=XXXXXXXXXX.</p>
<p>Copy this password.</p>
<p><img src="Installing%20Mythic%20C2/image%208.png"
alt="image.png" /></p>
</blockquote>
<hr />
<h2 id="small_blue_diamond-step-8-log-in-to-the-mythic-ui">🔹 Step 8:
Log In to the Mythic UI</h2>
<blockquote>
<p>The username is mythic_admin, and the password is the one we just
copied.</p>
</blockquote>
<ol type="1">
<li>Use:
<ul>
<li><strong>Username</strong>: <code>mythic_admin</code></li>
<li><strong>Password</strong>: (from the <code>.env</code> file)</li>
</ul></li>
<li>Click <strong>Log In</strong>.</li>
</ol>
<blockquote>
<p>Now we’re logged into Mythic. You can explore the UI, check out the
documentation, and prepare for the next step.</p>
</blockquote>
<p><img src="Installing%20Mythic%20C2/image%209.png"
alt="image.png" /></p>
<hr />
<h2 id="white_check_mark-final-reminder-before-the-next-lecture">✅
Final Reminder Before the Next Lecture</h2>
<blockquote>
<p>"Make sure you have Mythic installed and running in your Kali VM or
whatever OS you're using."</p>
<p><em>"We’ll be using it in the next lecture to install the Apollo
agent and generate payloads."</em></p>
</blockquote>
<ul>
<li>Confirm you can:
<ul>
<li>Log into the web UI</li>
<li>View the interface</li>
<li>Browse through available options</li>
</ul></li>
</ul>
<hr />
<h2></h2>
</section>
</div>
