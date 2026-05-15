---
layout: default
title: "Create Mythic Operations"
permalink: /solo-purple-teaming/create-mythic-operations/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Create Mythic Operations</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h2
id="creating-a-mythic-operation-for-attack-path-level-zero"><strong>Creating
a Mythic Operation for Attack Path Level Zero</strong></h2>
<p>In this walkthrough, we will create a new operation in Mythic for our
<strong>Attack Path Level Zero</strong> exercise. Operations in Mythic
manage the lifecycle of red team campaigns, allowing us to track, switch
between, and eventually complete engagements while preserving their
details for later analysis.</p>
<hr />
<h3 id="1-understanding-mythic-operations"><strong>1. Understanding
Mythic Operations</strong></h3>
<ul>
<li><strong>Create Operation</strong> – Allows you to define a new
operation, including selecting the agent profile and associated
payloads.</li>
<li><strong>Mark as Complete</strong> – Archives the operation so you
can still analyze its data, but prevents creating new active callbacks
in it.</li>
<li><strong>Make Current</strong> – Switches the active focus to the
selected operation, useful when juggling multiple engagements.</li>
<li><strong>Command Block Lists</strong> – Restrict the use of risky or
easily detectable commands.
<ul>
<li>Improves operational security.</li>
<li>Forces you to find alternative, stealthier methods to accomplish
objectives.</li>
<li>Acts as a training tool for solo purple teaming.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-navigating-to-the-operations-section"><strong>2. Navigating to
the Operations Section</strong></h3>
<ol type="1">
<li>Log in to Mythic.</li>
<li>You can access operations in two ways:
<ul>
<li>Click on the <strong>Current Operation</strong> name at the top of
the Mythic interface.</li>
<li>OR navigate to <strong>Operation Config → Modify
Operations</strong>.</li>
</ul></li>
</ol>
<p><img src="Create%20Mythic%20Operations/image.png"
alt="image.png" /></p>
<hr />
<h3 id="3-reviewing-existing-operations"><strong>3. Reviewing Existing
Operations</strong></h3>
<ul>
<li>In our example, there is an existing operation called <strong>test
Apollo reflection</strong>, which we previously used for payload
testing.</li>
<li>To close it out:
<ol type="1">
<li>Click <strong>Edit</strong> on the operation.</li>
<li>Change the name if needed.</li>
<li>Click <strong>Mark as Complete</strong>.</li>
<li>Click <strong>Update</strong> to save changes.</li>
</ol></li>
</ul>
<p>This allows you to keep the data for analysis but prevents adding new
active callbacks to that operation.</p>
<hr />
<h3 id="4-creating-a-new-operation"><strong>4. Creating a New
Operation</strong></h3>
<ol type="1">
<li>Click <strong>New Operation</strong>.</li>
<li>Enter a name for the operation:
<ul>
<li>For this example: <code>ecoin attack path level zero</code>.</li>
</ul></li>
<li>Click <strong>Submit</strong>.</li>
<li>If the name already exists, adjust it slightly and try again.</li>
<li>Once created, click <strong>Make Current</strong> to set it as your
active operation.</li>
</ol>
<hr />
<h3 id="5-creating-a-command-block-list"><strong>5. Creating a Command
Block List</strong></h3>
<p>As you progress and mature in solo purple teaming, you can use
command block lists to train yourself to work around restricted
commands.</p>
<ol type="1">
<li>Click <strong>New Block List</strong>.</li>
<li>Select the installed agent (in this case,
<strong>Apollo</strong>).</li>
<li>Give the block list a descriptive name — e.g.,
<code>Beginner Solo Purple Teamer</code>.</li>
<li>Choose commands to block:
<ul>
<li>Example: Select <strong>get_system</strong> and move it to the
<strong>Blocked Commands</strong> list.</li>
</ul></li>
<li>Click <strong>Submit</strong> to save the block list.</li>
</ol>
<hr />
<h3 id="6-applying-the-block-list-to-an-operation-or-user"><strong>6.
Applying the Block List to an Operation or User</strong></h3>
<ol type="1">
<li>Edit the operation or user profile.</li>
<li>Select your new block list
(<code>Beginner Solo Purple Teamer</code>).</li>
<li>Click <strong>Update</strong>.</li>
<li>Now, if you attempt to run the blocked command (e.g.,
<code>get_system</code>) with the Apollo agent, Mythic will prevent
it.</li>
</ol>
<hr />
<h3 id="7-summary"><strong>7. Summary</strong></h3>
<p>You have now:</p>
<ul>
<li>Marked an old operation as complete.</li>
<li>Created a new operation for <strong>ecoin attack path level
zero</strong>.</li>
<li>Created and applied a command block list for training and
operational security.</li>
</ul>
<hr />
<p><strong>✅ Before moving to the next lecture:</strong></p>
<p>Make sure your new operation for <strong>Attack Path Level
Zero</strong> is created and set as your current operation.</p>
<p><strong>Next Step:</strong> In the upcoming lecture, we will generate
the payload for Attack Path Level Zero.</p>
</section>
</div>
