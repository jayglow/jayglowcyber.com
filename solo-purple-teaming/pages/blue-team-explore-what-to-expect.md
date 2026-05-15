---
layout: default
title: "Blue Team Explore - What to Expect"
permalink: /solo-purple-teaming/blue-team-explore-what-to-expect/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - What to Expect</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h2 id="1-purpose-of-the-explore-phase"><strong>1. Purpose of the
Explore Phase</strong></h2>
<p>The <strong>Blue Team Explore Phase</strong> focuses on researching
the <strong>specific TTPs</strong> (Tactics, Techniques, and Procedures)
you encountered during the <strong>Engage Phase</strong>. Your job here
is to:</p>
<ul>
<li>Understand the techniques used in your <strong>attack
path</strong>.</li>
<li>Learn how they typically operate in real-world attacks.</li>
<li>Identify how they are detected in live environments.</li>
</ul>
<hr />
<h3 id="2-step-by-step-process"><strong>2. Step-by-Step
Process</strong></h3>
<h3 id="step-1--start-with-the-techniques-used"><strong>Step 1 – Start
with the Techniques Used</strong></h3>
<ul>
<li>Review the exact TTPs from your <strong>Engage Phase</strong>.</li>
<li>Ask yourself:
<ul>
<li><em>What are these techniques?</em></li>
<li><em>How do they work in practice?</em></li>
<li><em>What does a real-world detection look like for these
techniques?</em></li>
</ul></li>
</ul>
<hr />
<h3 id="step-2--research-from-credible-sources"><strong>Step 2 –
Research from Credible Sources</strong></h3>
<ul>
<li><strong>Threat Hunting Blogs</strong> – Look for posts that break
down detection logic.</li>
<li><strong>Detection Rule Repositories</strong> – Explore Sigma rules,
Splunk queries, or YARA rules related to the TTPs.</li>
<li><strong>Incident Response Write-ups</strong> – Real-world cases show
both attacker behaviors and defender responses.</li>
<li><strong>Offensive Security Blogs</strong> – While attacker-focused,
they often reveal evasion strategies you need to know about.</li>
</ul>
<hr />
<h3 id="step-3--examine-your-existing-security-tools"><strong>Step 3 –
Examine Your Existing Security Tools</strong></h3>
<ul>
<li>Check the <strong>documentation</strong> for your SIEM, EDR, Sysmon
config, or other monitoring tools.</li>
<li>Confirm they <strong>generate telemetry</strong> relevant to your
researched TTPs.
<ul>
<li>Example: If the TTP involves <strong>process injection</strong>,
verify if your tools log <strong>Sysmon Event ID 10</strong>.</li>
</ul></li>
<li>If your telemetry is missing something, note the gap.</li>
</ul>
<hr />
<h3 id="step-4--identify-and-document-visibility-gaps"><strong>Step 4 –
Identify and Document Visibility Gaps</strong></h3>
<ul>
<li>If something isn’t being logged or detected:
<ul>
<li>Record the gap in your notes.</li>
<li>Consider <strong>what extra tools, logging configurations, or data
sources</strong> could close it.</li>
</ul></li>
</ul>
<hr />
<h3 id="3-course-simplification"><strong>3. Course
Simplification</strong></h3>
<p>In this training, the Explore Phase has been
<strong>streamlined</strong>:</p>
<ul>
<li>You’ll still <strong>research TTPs</strong> from your Engage
Phase.</li>
<li>You’ll still <strong>check your telemetry stack</strong> for
coverage.</li>
<li><strong>Omitted for brevity:</strong>
<ul>
<li>Deep dives into every detection tool.</li>
<li>Full threat hunting walk-throughs.</li>
</ul></li>
</ul>
<p><em>These omitted steps are for you to practice later on your
own.</em></p>
<hr />
<h3 id="4-goal-of-this-phase"><strong>4. Goal of This
Phase</strong></h3>
<ul>
<li>Build the <strong>mindset and workflow</strong> for TTP
research.</li>
<li>Avoid being overwhelmed with too much detail at once.</li>
<li>Gain a <strong>solid foundation</strong> to expand upon in
real-world or advanced lab scenarios.</li>
</ul>
<hr />
<h3 id="5-next-step"><strong>5. Next Step</strong></h3>
<ul>
<li>Begin your <strong>Blue Team Explore Phase</strong> research
now.</li>
<li>Document each TTP, its detection methods, and any visibility gaps
you find.</li>
</ul>
</section>
</div>
