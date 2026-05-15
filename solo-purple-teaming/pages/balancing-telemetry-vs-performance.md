---
layout: default
title: "Balancing Telemetry VS Performance"
permalink: /solo-purple-teaming/balancing-telemetry-vs-performance/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Balancing Telemetry VS Performance</h1>
</section>
<section class="spt-content">
<h3 id="1-introduction--your-role-as-a-solo-purple-teamer"><strong>1.
Introduction – Your Role as a Solo Purple Teamer</strong></h3>
<ul>
<li>As a <strong>solo purple teamer</strong>, you’re responsible for
<strong>both detecting threats and simulating them</strong>.</li>
<li><strong>Telemetry</strong> (logs, events, metrics) is your
<strong>primary source of truth</strong> for investigations.</li>
<li>Collecting too much telemetry:
<ul>
<li>Can slow tools and searches.</li>
<li>Wastes time processing irrelevant data.</li>
<li>Risks obscuring critical indicators.</li>
</ul></li>
<li>For training purposes, we will <strong>lean towards
over-collection</strong> to ensure we identify the best detection
opportunities.</li>
<li><strong>Goal:</strong> Strike a balance — enough data for detection
and investigation without making the environment unmanageable.</li>
<li>Note: In the lab, you won’t feel these constraints heavily, but in
the <strong>real world SOC</strong>, they are significant.</li>
</ul>
<hr />
<h3 id="2-key-terms"><strong>2. Key Terms</strong></h3>
<ol type="1">
<li><strong>Telemetry</strong> – Logs, events, and metrics from
endpoints, networks, and applications.</li>
<li><strong>Search Performance</strong> – The <strong>speed and
responsiveness</strong> of queries in your SIEM or EDR platform.</li>
<li><strong>Signal-to-Noise Ratio</strong> – The
<strong>quality</strong> of your data versus the
<strong>quantity</strong> of irrelevant or low-value data.</li>
</ol>
<hr />
<h3 id="3-over-collection-in-solo-purple-teaming"><strong>3.
Over-Collection in Solo Purple Teaming</strong></h3>
<ul>
<li>Over-collection ensures <strong>full visibility</strong> during
simulations.</li>
<li><strong>Trade-offs</strong> of more telemetry:
<ul>
<li>Increased <strong>storage</strong> needs.</li>
<li>Greater <strong>processing load</strong> on systems.</li>
<li>Slower <strong>query performance</strong>.</li>
</ul></li>
<li><strong>Best Practice:</strong> Build your pipeline so
<strong>high-value data</strong> is easy to access and analyze.</li>
<li><strong>Mindset shift:</strong> It’s not about "more logs," it’s
about <strong>the right logs</strong>.</li>
</ul>
<hr />
<h3 id="4-operational-costs-of-over-collection"><strong>4. Operational
Costs of Over-Collection</strong></h3>
<p>While over-collection helps visibility, it introduces challenges,
especially in SOC environments:</p>
<ol type="1">
<li><strong>Query Timeouts</strong> – Large data volumes slow
investigations.</li>
<li><strong>Alert Fatigue</strong> – Excess non-actionable alerts hide
real threats.</li>
<li><strong>Increased Costs</strong> – Storage and licensing expenses in
commercial SIEMs.</li>
</ol>
<p><strong>Lab vs. SOC:</strong></p>
<ul>
<li>In your lab — not a big problem.</li>
<li>In a SOC — can cause <strong>investigation delays</strong>,
<strong>missed detections</strong>, and <strong>budget
issues</strong>.</li>
</ul>
<hr />
<h3 id="5-skills-developed-through-solo-purple-teaming"><strong>5.
Skills Developed Through Solo Purple Teaming</strong></h3>
<p>By designing your own telemetry pipeline, you will:</p>
<ul>
<li><strong>Think critically</strong> about what data matters.</li>
<li>Gain <strong>hands-on experience</strong> filtering noise and
prioritizing high-value logs.</li>
<li>Learn to <strong>support detection use cases</strong> with relevant
telemetry.</li>
<li>Enter a SOC with a <strong>signal-focused mindset</strong> instead
of a “collect everything” approach.</li>
</ul>
<hr />
<h3 id="6-the-value-of-baselining"><strong>6. The Value of
Baselining</strong></h3>
<ul>
<li><strong>Baselining</strong>: Identifying what “normal” looks like in
your environment.</li>
<li>Benefits:
<ul>
<li>Distinguishes <strong>expected behavior</strong> from
<strong>potential threats</strong>.</li>
<li>Helps <strong>fine-tune detections</strong> and reduce false
positives.</li>
<li>Improves <strong>alert fidelity</strong> and signal-to-noise
ratio.</li>
</ul></li>
<li><strong>Examples of normal patterns to baseline:</strong>
<ul>
<li>Regular user logon behavior.</li>
<li>Routine DNS queries.</li>
<li>Benign, repetitive processes.</li>
</ul></li>
</ul>
<hr />
<h3 id="7-looking-ahead"><strong>7. Looking Ahead</strong></h3>
<p>In the <strong>next lecture</strong>:</p>
<ul>
<li>Begin the <strong>Blue Team Explore Phase</strong>:
<ul>
<li>Search and analyze telemetry.</li>
<li>Identify telemetry gaps.</li>
<li>Investigate alerts.</li>
<li>Validate detection logic.</li>
</ul></li>
</ul>
</section>
</div>
