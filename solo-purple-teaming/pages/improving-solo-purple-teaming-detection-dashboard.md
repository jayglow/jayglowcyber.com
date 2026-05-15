---
layout: default
title: "Improving Solo Purple Teaming Detection Dashboard"
permalink: /solo-purple-teaming/improving-solo-purple-teaming-detection-dashboard/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Improving Solo Purple Teaming Detection Dashboard</h1>
</section>
<section class="spt-content">
<p>Owner: Mike Sterrett</p>
<h3 id="1-why-dashboards-matter-in-solo-purple-teaming"><strong>1. Why
Dashboards Matter in Solo Purple Teaming</strong></h3>
<ul>
<li><strong>Purpose:</strong> Dashboards turn raw logs and alerts into
clear, actionable insights.</li>
<li><strong>Benefits for solo purple teamers:</strong>
<ul>
<li>Quickly spot anomalies and track attacker movements.</li>
<li>Instantly see if simulated attacks triggered alerts.</li>
<li>Monitor detection rule performance and false positives.</li>
<li>Save time by consolidating critical data in one place.</li>
</ul></li>
<li><strong>Goal:</strong> Build a simple starting dashboard, then
expand with more visualizations.</li>
</ul>
<hr />
<h3 id="2-lab-setup--accessing-wazuh"><strong>2. Lab Setup – Accessing
Wazuh</strong></h3>
<ol type="1">
<li>Log into the <strong>Wazuh Manager</strong>.</li>
<li>Begin by creating <strong>saved searches</strong> to feed our
dashboard.</li>
</ol>
<hr />
<h3 id="3-creating-saved-searches"><strong>3. Creating Saved
Searches</strong></h3>
<p>We’ll create four searches based on <strong>rule levels</strong>
(severity):</p>
<h3 id="a-rules-above-level-12"><strong>a. Rules above Level
12</strong></h3>
<ul>
<li><p>Go to <strong>Explore → Discover</strong>.</p></li>
<li><p>Search:</p>
<pre><code>rule.level &gt; 12
</code></pre></li>
<li><p>Adjust time filter to <strong>Last 7 days</strong>.</p></li>
<li><p>Save search as:</p>
<p><strong>solo purple teaming rules greater than
twelve</strong>.</p></li>
</ul>
<h3 id="b-critical-alerts-level-15"><strong>b. Critical Alerts (Level
15)</strong></h3>
<ul>
<li><p>Search:</p>
<pre><code>rule.level = 15
</code></pre></li>
<li><p>Save as:</p>
<p><strong>purple teaming critical alerts</strong>.</p></li>
</ul>
<h3 id="c-high-alerts-level-14"><strong>c. High Alerts (Level
14)</strong></h3>
<ul>
<li><p>Search:</p>
<pre><code>rule.level = 14
</code></pre></li>
<li><p>Save as:</p>
<p><strong>high alerts</strong>.</p></li>
</ul>
<h3 id="d-medium-alerts-level-13"><strong>d. Medium Alerts (Level
13)</strong></h3>
<ul>
<li><p>Search:</p>
<pre><code>rule.level = 13
</code></pre></li>
<li><p>Save as:</p>
<p><strong>medium alerts</strong>.</p></li>
</ul>
<hr />
<h3 id="4-creating-the-dashboard"><strong>4. Creating the
Dashboard</strong></h3>
<ol type="1">
<li><p>Go to <strong>Explore → Dashboards</strong>.</p></li>
<li><p><strong>Create New Dashboard</strong> → Name it:</p>
<p><strong>Solo Purple Teaming Dashboard</strong>.</p></li>
<li><p><strong>Add Existing Search</strong> → Select:</p>
<ul>
<li><strong>solo purple teaming rules greater than twelve</strong>.</li>
</ul></li>
<li><p><strong>Resize</strong> as needed.</p></li>
</ol>
<hr />
<h3 id="5-improving-search-display"><strong>5. Improving Search
Display</strong></h3>
<ol type="1">
<li>Open <strong>Dashboard Management → Saved Objects</strong>.</li>
<li>Edit each saved search:
<ul>
<li>Add columns:
<ul>
<li><strong>Agent name</strong></li>
<li><strong>Rule level</strong></li>
<li><strong>Rule name/group</strong></li>
<li><strong>Rule description</strong></li>
</ul></li>
</ul></li>
<li>Save changes.</li>
<li>Refresh dashboard to see updated columns.</li>
</ol>
<hr />
<h3 id="6-adding-metrics-to-the-dashboard"><strong>6. Adding Metrics to
the Dashboard</strong></h3>
<p>We’ll create <strong>metric visualizations</strong> for quick
counts.</p>
<h3 id="a-critical-alerts-metric"><strong>a. Critical Alerts
Metric</strong></h3>
<ul>
<li>Edit dashboard → <strong>Create New → Metric</strong>.</li>
<li>Select: <strong>purple teaming critical alerts</strong> search.</li>
<li>Options:
<ul>
<li>Add range (for color coding).</li>
<li>Reverse <strong>green → red</strong> scale.</li>
<li>Set font size to <strong>90</strong>.</li>
</ul></li>
<li>Save as: <strong>Number of Critical Alerts</strong>.</li>
<li>Place and resize in dashboard.</li>
</ul>
<h3 id="b-high-alerts-metric"><strong>b. High Alerts
Metric</strong></h3>
<ul>
<li>If missing, recreate saved search for Level 14.</li>
<li>Create metric as above.</li>
<li>Color scale: <strong>yellow → red</strong>.</li>
<li>Save as: <strong>Number of High Alerts</strong>.</li>
<li>Position next to Critical Alerts.</li>
</ul>
<h3 id="c-medium-alerts-metric"><strong>c. Medium Alerts
Metric</strong></h3>
<ul>
<li>Use Level 13 saved search.</li>
<li>Color scale: <strong>green → red</strong>.</li>
<li>Font size: <strong>90</strong>.</li>
<li>Save as: <strong>Number of Medium Alerts</strong>.</li>
<li>Position next to High Alerts.</li>
</ul>
<hr />
<h3 id="7-final-dashboard-layout"><strong>7. Final Dashboard
Layout</strong></h3>
<ul>
<li>Three large metrics:
<ul>
<li><strong>Number of Critical Alerts</strong></li>
<li><strong>Number of High Alerts</strong></li>
<li><strong>Number of Medium Alerts</strong></li>
</ul></li>
<li>Table with rule details for deeper analysis.</li>
<li>All elements resized for a clean, uniform look.</li>
</ul>
<hr />
<h3 id="8-saving-and-testing"><strong>8. Saving and
Testing</strong></h3>
<ol type="1">
<li>Save the dashboard layout.</li>
<li>Refresh to ensure changes persist.</li>
<li>Prepare for the next lecture where we’ll <strong>re-engage the
attack</strong> to watch alerts appear in real time.</li>
</ol>
<hr />
<p>✅ <strong>End Result:</strong> You now have a working Solo Purple
Teaming Dashboard that gives you both <strong>detailed rule
data</strong> and <strong>quick severity counts</strong>—a perfect
starting point for monitoring attack simulations and tuning
detections.</p>
</section>
</div>
