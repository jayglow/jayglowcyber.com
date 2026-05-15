---
layout: default
title: "Mythic User Settings"
permalink: /solo-purple-teaming/mythic-user-settings/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Mythic User Settings</h1>
</section>
<section class="spt-content">
<p>In this lesson, we’ll cover how to <strong>add and manage
users</strong> in Mythic, as well as how to <strong>adjust personal
preferences</strong> such as font size and display settings.</p>
<hr />
<h3 id="1-logging-into-mythic"><strong>1. Logging into
Mythic</strong></h3>
<ul>
<li>You can log into Mythic using either:
<ul>
<li>The <strong>mythic admin</strong> account, or</li>
<li>Another account you’ve created for daily use.</li>
</ul></li>
<li>In this example, the instructor is logging in with a
<strong>non-admin</strong> account to demonstrate user management.</li>
</ul>
<p><img src="/assets/images/solo-purple-teaming/mythic-user-settings/image.png" alt="image.png" /></p>
<hr />
<h3 id="2-retrieving-and-resetting-the-mythic-admin-password"><strong>2.
Retrieving and Resetting the Mythic Admin Password</strong></h3>
<p>If you’ve forgotten the admin password or want to set a new one, you
can use the Mythic CLI tool.</p>
<p><strong>Steps:</strong></p>
<ol type="1">
<li><p>SSH into your Kali VM where Mythic is installed.</p></li>
<li><p>Navigate to the Mythic installation directory.</p>
<p>Example:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">bash</span></span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/mythic/mythic</span></code></pre></div></li>
<li><p>Use the Mythic CLI to retrieve the current admin credentials:</p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli config get mythic_admin</span></code></pre></div>
<p><img src="/assets/images/solo-purple-teaming/mythic-user-settings/image%201.png"
alt="image.png" /></p>
<ul>
<li>This will display:
<ul>
<li><code>mythic_admin_user</code></li>
<li><code>mythic_admin_password</code></li>
</ul></li>
</ul></li>
<li><p>To change the password, run:</p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli config set mythic_admin_password mythicpassword</span></code></pre></div>
<p><img src="/assets/images/solo-purple-teaming/mythic-user-settings/image%202.png"
alt="image.png" /></p>
<ul>
<li>Replace <code>mythicpassword</code> with your desired password.</li>
</ul></li>
<li><p>Verify the change:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> ./mythic-cli config get mythic_admin</span></code></pre></div></li>
<li><p>You can now log into Mythic using the updated admin
credentials.</p></li>
</ol>
<hr />
<h3 id="3-accessing-user-settings-in-the-ui"><strong>3. Accessing User
Settings in the UI</strong></h3>
<p>Once logged in:</p>
<ol type="1">
<li>In the <strong>top right corner</strong> of the Mythic interface,
click the <strong>person icon with a gear</strong>.</li>
</ol>
<p><img src="/assets/images/solo-purple-teaming/mythic-user-settings/image%203.png"
alt="image.png" /></p>
<ol type="1">
<li>Click your username.
<ul>
<li>This will open the <strong>Users</strong> page.</li>
</ul></li>
</ol>
<hr />
<h3 id="4-managing-users"><strong>4. Managing Users</strong></h3>
<ul>
<li><p>The Users page lists:</p>
<ul>
<li>Bot accounts</li>
<li>The <code>mythic_admin</code> account</li>
<li>Your currently logged-in user</li>
</ul>
<p><img src="/assets/images/solo-purple-teaming/mythic-user-settings/image%204.png"
alt="image.png" /></p></li>
<li><p>From here, you can:</p>
<ul>
<li><strong>Add new users</strong> (recommended: create a secondary
admin account so you don’t always need to use
<code>mythic_admin</code>).</li>
<li><strong>Assign different roles and permissions</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="5-adjusting-time-and-font-settings"><strong>5. Adjusting Time
and Font Settings</strong></h3>
<ul>
<li><strong>Time Setting:</strong>
<ul>
<li>Toggle <strong>UTC</strong> time display on or off depending on your
preference.</li>
</ul></li>
<li><strong>Font Settings:</strong>
<ol type="1">
<li>Increase the font size for easier readability (e.g., set to
<code>16</code>).</li>
<li>Keep or change the font family.</li>
<li>Adjust other visual settings such as <strong>color
schema</strong>.</li>
</ol></li>
</ul>
<blockquote>
<p>Tip: Larger fonts can significantly improve readability, especially
if you’re working long hours in the UI.</p>
</blockquote>
<hr />
<h3 id="6-preparing-for-the-next-module"><strong>6. Preparing for the
Next Module</strong></h3>
<p>Before moving on:</p>
<ul>
<li>Ensure you have a Mythic user ready for <strong>Operation: Attack
Path Level Zero</strong>.
<ul>
<li>You can use the <code>mythic_admin</code> account or a custom
account you created.</li>
</ul></li>
<li>Customize the UI to match your preferences:
<ul>
<li>Fonts</li>
<li>Colors</li>
<li>Time format</li>
</ul></li>
</ul>
<hr />
<p><strong>Next Step:</strong></p>
<p>In the next lecture, we’ll cover <strong>operations</strong> in
Mythic — including command blocks, lists, and how to create your new
operation for Attack Path Level Zero.</p>
</section>
</div>
