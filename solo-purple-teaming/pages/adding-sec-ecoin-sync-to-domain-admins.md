---
layout: default
title: "Adding sec_ecoin_sync to Domain Admins"
permalink: /solo-purple-teaming/adding-sec-ecoin-sync-to-domain-admins/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Adding sec_ecoin_sync to Domain Admins</h1>
</section>
<section class="spt-content">
<h3 id="objective"><strong>Objective</strong></h3>
<p>In this lab step, we’re going to make a small configuration change so
we can easily pivot from our Linux (LIN) workstation to the Domain
Controller (DC).</p>
<p>We’ll do this by temporarily adding the <code>svc_ecoin_sync</code>
service account to the <strong>Domain Admins</strong> group.</p>
<blockquote>
<p>Note: This is not good security practice in a real environment.
Adding a service account to Domain Admins significantly increases risk.
Here, we’re only doing it to simplify our Level 1 exercise. In later
levels, you’ll work with more realistic (and restricted) scenarios.</p>
</blockquote>
<hr />
<h3 id="steps"><strong>Steps</strong></h3>
<h3 id="1-run-command-prompt-as-administrator"><strong>1. Run Command
Prompt as Administrator</strong></h3>
<ul>
<li>Click <strong>Start</strong> and type <code>cmd</code>.</li>
<li><strong>Right-click</strong> on Command Prompt and select
<strong>Run as administrator</strong>.</li>
<li>This gives you the elevated permissions needed to make changes in
Active Directory.</li>
</ul>
<hr />
<h3 id="2-open-the-microsoft-management-console-mmc"><strong>2. Open the
Microsoft Management Console (MMC)</strong></h3>
<ul>
<li><p>In the Command Prompt window, type:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="ex">mmc</span></span></code></pre></div>
<p>and press <strong>Enter</strong>.</p></li>
<li><p>This opens the <strong>Microsoft Management
Console</strong>.</p></li>
</ul>
<hr />
<h3
id="3-add-the-active-directory-users-and-computers-snap-in"><strong>3.
Add the "Active Directory Users and Computers" Snap-in</strong></h3>
<ul>
<li>Inside MMC, go to:
<ul>
<li><strong>File</strong> → <strong>Add/Remove Snap-in…</strong></li>
</ul></li>
<li>Select <strong>Active Directory Users and Computers</strong> from
the left list.</li>
<li>Click <strong>Add →</strong>, then <strong>OK</strong>.</li>
</ul>
<hr />
<h3 id="4-locate-the-svc_ecoin_sync-account"><strong>4. Locate the
<code>svc_ecoin_sync</code> Account</strong></h3>
<ul>
<li><p>Expand your domain in the left-hand panel.</p></li>
<li><p>Click on <strong>Users</strong>.</p></li>
<li><p>Find the account named:</p>
svc_ecoin_sync

<p>(This is the service account we’ll modify.)</p></li>
</ul>
<hr />
<h3 id="5-add-the-account-to-domain-admins"><strong>5. Add the Account
to Domain Admins</strong></h3>
<ul>
<li><p><strong>Right-click</strong> <code>svc_ecoin_sync</code> →
<strong>Properties</strong>.</p></li>
<li><p>Go to the <strong>Member Of</strong> tab.</p></li>
<li><p>Click <strong>Add…</strong></p></li>
<li><p>In the dialog box, type:</p>
Domain Admins

<ul>
<li><p>If needed, use the format:</p>
ecoin\Domain Admins
</li>
</ul></li>
<li><p>Click <strong>Check Names</strong> to verify the group
name.</p></li>
<li><p>Click <strong>OK</strong>, then <strong>Apply</strong>, and
finally <strong>OK</strong> again.</p></li>
</ul>
<hr />
<h3 id="6-verify-membership"><strong>6. Verify Membership</strong></h3>
<ul>
<li><p>Open Command Prompt (Administrator).</p></li>
<li><p>To check your current logged-in account:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a><span class="ex">net</span> user <span class="op"><</span>your-username<span class="op">></span> /domain</span></code></pre></div></li>
<li><p>To check the service account:</p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="ex">net</span> user svc_ecoin_sync /domain</span></code></pre></div></li>
<li><p>Confirm that <strong>Domain Admins</strong> is listed under
<strong>Group Memberships</strong> for
<code>svc_ecoin_sync</code>.</p></li>
</ul>
<hr />
<h3 id="7-proceed-to-next-steps"><strong>7. Proceed to Next
Steps</strong></h3>
<ul>
<li>You’ve now added <code>svc_ecoin_sync</code> to Domain Admins.</li>
<li>This will make Level 1 challenges easier, since you won’t need to
escalate privileges from this account.</li>
<li>In later levels, this shortcut will be removed, and you’ll need to
perform privilege escalation manually.</li>
</ul>
<hr />
<p>✅ <strong>Checkpoint:</strong></p>
<p>Before moving on, ensure <code>svc_ecoin_sync</code> appears in the
<strong>Domain Admins</strong> group when you run:</p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="ex">net</span> user svc_ecoin_sync /domain</span></code></pre></div>
</section>
</div>
