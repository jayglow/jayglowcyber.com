---
layout: default
title: "Mythic Tasking"
permalink: /solo-purple-teaming/mythic-tasking/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Mythic Tasking</h1>
</section>
<section class="spt-content">
<h2 id="mythic-tasking-basics"><strong>Mythic Tasking
Basics</strong></h2>
<h3 id="1-understanding-tasking-in-mythic"><strong>1. Understanding
Tasking in Mythic</strong></h3>
<p><img src="/assets/images/solo-purple-teaming/mythic-tasking/image.png" alt="image.png" /></p>
<p>In Mythic, <em>tasking</em> is the process of sending commands to
agents deployed inside the target environment.</p>
<ul>
<li><p>This is the primary way you interact with compromised
systems.</p></li>
<li><p>Mythic supports both <strong>manual</strong> and
<strong>automated</strong> tasking:</p></li>
<li><p>Through the <strong>web UI</strong></p></li>
<li><p>Using <strong>prebuilt scripts</strong></p></li>
<li><p>Via the <strong>Mythic API</strong></p></li>
</ul>
<p>The Mythic UI includes features that make tasking more efficient and
safer:</p>
<p><img src="/assets/images/solo-purple-teaming/mythic-tasking/image%201.png" alt="image.png" /></p>
<ul>
<li><strong>Autocomplete</strong> and <strong>tool tips</strong> for
accurate commands</li>
<li>Ability to <strong>preview commands</strong> before dispatching</li>
<li><strong>OPSEC warnings</strong> for potentially noisy actions</li>
<li>Command <strong>tagging</strong> and <strong>grouping</strong> for
organized operation tracking</li>
</ul>
<hr />
<h3 id="2-navigating-to-callbacks"><strong>2. Navigating to
Callbacks</strong></h3>
<ol type="1">
<li>Log in to Mythic.</li>
<li>Go to the <strong>Callbacks</strong> page.</li>
<li>Locate <strong>Callback 1</strong> (our initial foothold).</li>
<li>You can rename the callback tab for clarity:
<ul>
<li>Right-click the tab → <strong>Set Tab Description</strong></li>
<li>Example: Rename to <em>Initial Foothold – LAN</em></li>
</ul></li>
</ol>
<hr />
<h3 id="3-discovering-available-commands"><strong>3. Discovering
Available Commands</strong></h3>
<p>You can find available tasks/commands in several ways:</p>
<p><strong>Method 1 – Using the <code>help</code> command:</strong></p>
<ul>
<li>Type <code>help</code> and execute it.</li>
<li>Mythic will list all available commands for that agent.</li>
</ul>
<p><strong>Method 2 – Help on a specific command:</strong></p>
<ul>
<li>Type <code>help <command_name></code> (e.g.,
<code>help assembly_inject</code>).</li>
<li>Mythic will display detailed information about that specific
command.</li>
</ul>
<p><strong>Method 3 – Command modal for parameters:</strong></p>
<ul>
<li>If a command takes parameters (e.g., <code>run</code>), type the
command and press <strong>Shift + Enter</strong>.</li>
<li>This opens a dialog box where you can fill out parameters such as:
<ul>
<li>Executable path or filename</li>
<li>Arguments</li>
</ul></li>
</ul>
<p><strong>Method 4 – Viewing command metadata:</strong></p>
<ul>
<li>Click the arrow next to the <strong>Callback ID</strong> →
<strong>Meta</strong> → <strong>View Metadata</strong></li>
<li>This shows all commands with direct <strong>documentation
links</strong>.</li>
<li>Clicking a link opens a new tab with usage details.</li>
</ul>
<hr />
<h3 id="4-running-your-first-task"><strong>4. Running Your First
Task</strong></h3>
<p>Example: Running the <code>ps</code> command to list processes.</p>
<ol type="1">
<li>Type <code>ps</code> and execute.</li>
<li><strong>Task lifecycle:</strong>
<ul>
<li><strong>Submitted</strong> – Waiting for the agent to check in.</li>
<li><strong>Agent Processing</strong> – Agent picks up the task.</li>
<li><strong>Data Returned</strong> – Output is displayed in the UI.</li>
</ul></li>
</ol>
<p>The results use a <strong>browser script</strong> to format the
output as a table for easier viewing.</p>
<hr />
<h3 id="5-taking-actions-on-returned-results"><strong>5. Taking Actions
on Returned Results</strong></h3>
<p>For each task result:</p>
<ul>
<li>Click the <strong>Action</strong> button.</li>
<li>Possible actions include:
<ul>
<li>Kill the process</li>
<li>Inject a keylogger</li>
<li>Capture a screenshot</li>
<li>Steal a token</li>
<li>View more details about the process</li>
</ul></li>
</ul>
<hr />
<h3 id="6-before-moving-to-enumeration"><strong>6. Before Moving to
Enumeration</strong></h3>
<p>Before continuing to the enumeration phase in the next lecture:</p>
<ul>
<li>Run <strong>at least one task</strong> (e.g., <code>ps</code>)</li>
<li>Ensure you can see the task’s lifecycle and returned results</li>
</ul>
<hr />
<h3 id="next-steps"><strong>Next Steps</strong></h3>
<p>In the next phase, you will:</p>
<ul>
<li>Enumerate local admin group members</li>
<li>Identify installed services</li>
<li>Check for vulnerable services</li>
<li>Simulate attacker enumeration to later create
<strong>detections</strong> for these activities</li>
</ul>
</section>
</div>
