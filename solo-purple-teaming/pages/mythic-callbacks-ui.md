---
layout: default
title: "Mythic Callbacks UI"
permalink: /solo-purple-teaming/mythic-callbacks-ui/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Mythic Callbacks UI</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h2 id="1-introduction-to-callbacks"><strong>1. Introduction to
Callbacks</strong></h2>
<p>Before starting enumeration on a target host, it’s important to
understand <strong>Mythic callbacks</strong>.</p>
<p>In Mythic, a <strong>callback</strong> is an active communication
channel between the C2 server and a deployed agent on a compromised
system.</p>
<ul>
<li>When a payload is executed on a target, it “calls back” to the
Mythic server, creating a callback entry on the
<strong>Callbacks</strong> page.</li>
<li>This callback acts as your <strong>control point</strong> for:
<ul>
<li>Sending tasks to the agent.</li>
<li>Receiving output from those tasks.</li>
<li>Viewing valuable metadata (e.g., username, hostname, parent
process).</li>
</ul></li>
</ul>
<hr />
<h3 id="2-callback-lifecycle"><strong>2. Callback
Lifecycle</strong></h3>
<p><img src="Mythic%20Callbacks%20UI/image.png" alt="image.png" /></p>
<p>Callbacks go through a natural life cycle:</p>
<ol type="1">
<li><strong>Payload execution</strong> – The agent initiates its first
callback.</li>
<li><strong>Beaconing</strong> – The agent sends periodic “I’m alive”
signals to the server.</li>
<li><strong>Tasking</strong> – The operator sends commands through the
callback, and results are returned.</li>
<li><strong>Loss of communication</strong> – If the process is killed or
blocked, the callback eventually becomes stale or dies.</li>
</ol>
<p>💡 <strong>Why persistence matters</strong>:</p>
<p>Attackers often set up persistence immediately after gaining access
so that callbacks are re-established after reboots.</p>
<hr />
<h3 id="3-callback-metadata"><strong>3. Callback Metadata</strong></h3>
<p>Each callback contains rich details, such as:</p>
<ul>
<li>Process ID (PID)</li>
<li>Username</li>
<li>Privilege level</li>
<li>Integrity level</li>
</ul>
<p>You can <strong>rename</strong> callbacks or apply
<strong>tags</strong> to organize targets—especially useful in
engagements with many hosts.</p>
<hr />
<h3 id="4-interacting-with-callbacks"><strong>4. Interacting with
Callbacks</strong></h3>
<p>Once active, you can:</p>
<ul>
<li><strong>Send tasks</strong> (commands or actions) to the agent.</li>
<li>View <strong>real-time results</strong> in the callback
interface.</li>
<li><strong>Pin</strong> important results for easy reference.</li>
</ul>
<p>⚠ <strong>Note</strong>: “Real-time” depends on the <strong>sleep
time</strong> and <strong>jitter</strong> settings of the agent—longer
sleep times mean slower responses.</p>
<hr />
<h3 id="5-managing-multiple-callbacks"><strong>5. Managing Multiple
Callbacks</strong></h3>
<p>In real-world operations, you may have dozens of callbacks.</p>
<p>Mythic provides:</p>
<ul>
<li><strong>Filtering</strong> (e.g., by user, host, operation)</li>
<li><strong>Tags</strong> for grouping systems</li>
<li><strong>Sorting</strong> by any column</li>
</ul>
<hr />
<h3 id="6-exploring-the-callbacks-page"><strong>6. Exploring the
Callbacks Page</strong></h3>
<p><img src="Mythic%20Callbacks%20UI/image%201.png"
alt="image.png" /></p>
<ol type="1">
<li><strong>Navigate to the Callbacks page</strong> in Mythic.</li>
<li><strong>View callback details</strong>:
<ul>
<li>Callback ID</li>
<li>Associated operators</li>
<li>Hostname, user, domain</li>
<li>PID, last check-in time</li>
<li>Agent type, IP addresses</li>
</ul></li>
<li><strong>Lock/Unlock callbacks</strong>:
<ul>
<li>Locking restricts interaction to the lead and the operator who
locked it.</li>
<li>Unlock to allow others to issue tasks.</li>
</ul></li>
<li><strong>Resize columns</strong>:
<ul>
<li><strong>Double-click</strong> column borders to auto-resize.</li>
<li><strong>Click and drag</strong> to adjust width manually.</li>
</ul></li>
<li><strong>Customize visible metadata</strong>:
<ul>
<li><strong>Right-click</strong> a header → <em>Reorder Columns / Adjust
Visibility</em></li>
<li>Toggle fields (e.g., External IP) → Click
<strong>Submit</strong></li>
<li>Verify changes in the table.</li>
</ul></li>
<li><strong>Relating metadata to your network diagram</strong>:
<ul>
<li>Example: External IP <code>192.168.100.102</code> matches the WAN
interface on the Ecoin edge device.</li>
<li>Internal IP <code>10.0.2.100</code> matches the static IP assigned
to the R-LIN host.</li>
</ul></li>
<li><strong>Sorting and filtering</strong>:
<ul>
<li>Sort by clicking a column header.</li>
<li>Filter by entering criteria in a column filter field.</li>
</ul></li>
</ol>
<hr />
<h3 id="7-student-action"><strong>7. Student Action</strong></h3>
<p>Before moving on:</p>
<ul>
<li>Review the <strong>Callbacks</strong> page in Mythic UI.</li>
<li>Adjust <strong>visible columns</strong> and
<strong>metadata</strong> to suit your preferences.</li>
<li>Add any fields you find useful for tracking hosts.</li>
</ul>
<hr />
<h3 id="8-next-steps"><strong>8. Next Steps</strong></h3>
<p>In the next lecture, you’ll learn:</p>
<ul>
<li>The basics of <strong>Mythic tasking</strong>.</li>
<li>How tasking is used for host enumeration.</li>
<li>Steps to begin working toward <strong>privilege
escalation</strong>.</li>
</ul>
</section>
</div>
