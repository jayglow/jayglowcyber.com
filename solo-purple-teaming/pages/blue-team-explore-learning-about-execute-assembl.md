---
layout: default
title: "Blue Team Explore - Learning About execute_assembl"
permalink: /solo-purple-teaming/blue-team-explore-learning-about-execute-assembl/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Learning About execute_assembl</h1>
</section>
<section class="spt-content">
<h1
id="blue-team-explore---learning-about-execute_assembly-process-injection-and-ipc">Blue
Team Explore - Learning About execute_assembly, Process Injection, and
IPC</h1>
<p>Owner: Mike Sterrett</p>
<h2 id="1-introduction-to-execute-assembly-in-apollo"><strong>1.
Introduction to Execute Assembly in Apollo</strong></h2>
<ul>
<li><strong>Purpose:</strong> Execute a .NET assembly entirely in memory
without writing it to disk.</li>
<li><strong>Execution Flow:</strong>
<ol type="1">
<li>Apollo launches a <strong>sacrificial process</strong> — a clean
host process determined by the <code>spawnto_x64</code> setting.</li>
<li>Into this process, Apollo injects a <strong>Common Language Runtime
(CLR) loader</strong>.</li>
<li>The actual .NET assembly is injected directly into the process
memory — <strong>never written to disk</strong>.</li>
<li>Using <code>System.Reflection.Assembly.Load</code>, the loader
<strong>reflectively loads</strong> the assembly.</li>
<li>The specified <strong>entry point</strong> of the assembly is
invoked with any provided arguments.</li>
<li>As it runs, <strong>standard output</strong> is streamed back to the
Apollo agent over a <strong>named pipe</strong>.</li>
</ol></li>
<li><strong>Key Advantage:</strong>
<ul>
<li>Stealthy, fileless execution avoids many traditional detection
mechanisms.</li>
<li>However, it still presents <strong>operational risks</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="2-primer-on-process-injection"><strong>2. Primer on Process
Injection</strong></h3>
<ul>
<li><strong>Definition:</strong> Technique allowing an attacker to
execute code inside another running process’s memory space.</li>
<li><strong>Basic Steps:</strong>
<ol type="1">
<li><strong>Select a legitimate process</strong> to blend in with normal
activity.</li>
<li><strong>Obtain a handle</strong> to that process with permissions to
manipulate it.</li>
<li><strong>Allocate memory</strong> inside the target process for the
malicious payload.</li>
<li><strong>Write the payload</strong> into the allocated memory.</li>
<li><strong>Create a remote thread</strong> inside the target process
pointing to the payload address.</li>
<li>Payload <strong>executes within the trusted process</strong>, making
detection harder.</li>
</ol></li>
<li><strong>Note:</strong>
<ul>
<li>Creating a remote thread is just one method; there are other process
injection techniques.</li>
<li>Students should research and learn multiple injection methods for
<strong>both offensive and defensive purposes</strong>.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-operational-risks-of-execute-assembly"><strong>3. Operational
Risks of Execute Assembly</strong></h3>
<ul>
<li><strong>Risk Origin:</strong>
<ul>
<li>Process access events</li>
<li>Inter-process communication (IPC) via named pipes</li>
</ul></li>
<li><strong>Detection Opportunities with Sysmon:</strong>
<ul>
<li><strong>Event ID 10 – Process Access</strong>
<ul>
<li>Logs when one process opens a handle to another.</li>
<li>Useful fields:
<ul>
<li><strong>SourceProcessGUID / SourceImage</strong> – who initiated the
access.</li>
<li><strong>TargetProcessGUID / TargetImage</strong> – which process was
targeted.</li>
<li><strong>GrantedAccess</strong> – level of access given (e.g.,
<code>PROCESS_ALL_ACCESS</code>).</li>
</ul></li>
</ul></li>
<li><strong>Event ID 17 – Pipe Created</strong>
<ul>
<li>Triggered when a named pipe is created.</li>
<li>Logs <strong>ProcessGUID</strong>, <strong>Image</strong>, and
<strong>PipeName</strong>.</li>
</ul></li>
<li><strong>Event ID 18 – Pipe Connected</strong>
<ul>
<li>Triggered when a connection is made to a named pipe.</li>
<li>Captures similar fields to Event ID 17.</li>
</ul></li>
</ul></li>
<li><strong>Why Important:</strong>
<ul>
<li>These events can reveal stealthy command-and-control channels or
tool staging via named pipes.</li>
<li>Combining process access logs with pipe creation/connection data
helps map attacker activity.</li>
</ul></li>
</ul>
<hr />
<h3 id="4-next-steps-in-the-lab"><strong>4. Next Steps in the
Lab</strong></h3>
<ol type="1">
<li><strong>Enable Sysmon logging</strong> for Event IDs 10, 17, and
18.</li>
<li><strong>Re-engage</strong> and run <code>execute-assembly</code> in
Apollo.</li>
<li><strong>Audit the logs</strong>:
<ul>
<li>Note <strong>Granted Access</strong> values.</li>
<li>Track process relationships (who accessed who).</li>
</ul></li>
<li>Begin <strong>formulating detection logic</strong> for execute
assembly activity.</li>
</ol>
<hr />
<p>This walkthrough takes students from understanding <strong>how
execute assembly works</strong>, through <strong>its relationship with
process injection</strong>, to <strong>how defenders can detect
it</strong> with Sysmon telemetry, before setting them up for hands-on
analysis in the next lab.</p>
</section>
</div>
