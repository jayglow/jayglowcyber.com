---
layout: default
title: "Balancing Accuracy VS Coverage"
permalink: /solo-purple-teaming/balancing-accuracy-vs-coverage/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Balancing Accuracy VS Coverage</h1>
</section>
<section class="spt-content">
<h3 id="1-introduction-to-the-trade-off"><strong>1. Introduction to the
Trade-Off</strong></h3>
<p>In this lecture, we examine one of the most important challenges in
detection engineering:</p>
<p><strong>Balancing coverage vs accuracy</strong> in detection
systems.</p>
<ul>
<li>These two goals are often in tension.</li>
<li>Finding the right balance is essential for building effective and
sustainable detection capabilities.</li>
</ul>
<hr />
<h3 id="2-understanding-detection-outcomes"><strong>2. Understanding
Detection Outcomes</strong></h3>
<p>Before addressing coverage and accuracy, it’s important to understand
the <strong>four possible outcomes</strong> when a detection rule
triggers:</p>
<ol type="1">
<li><strong>True Positive (TP)</strong> – Malicious activity that is
correctly detected.</li>
<li><strong>True Negative (TN)</strong> – Benign activity that is
correctly ignored.</li>
<li><strong>False Positive (FP)</strong> – Benign activity that is
incorrectly flagged as malicious.</li>
<li><strong>False Negative (FN)</strong> – Malicious activity that goes
undetected.</li>
</ol>
<blockquote>
<p>Why this matters:</p>
<ul>
<li><strong>False Positives</strong> → Alert fatigue, wasted SOC
time.</li>
<li><strong>False Negatives</strong> → Missed detection opportunities,
allowing threats to persist.</li>
</ul>
</blockquote>
<p><img src="/_assets/balancing-accuracy-vs-coverage/image.png"
alt="image.png" /></p>
<hr />
<h3 id="3-key-metrics-for-evaluating-detections"><strong>3. Key Metrics
for Evaluating Detections</strong></h3>
<p>Detection engineers commonly use three metrics to evaluate
performance:</p>
<ul>
<li><p><strong>Precision</strong> – Of all the alerts generated, what
percentage were <em>actual threats</em>?</p>
<p><em>Focus:</em> Correctness of alerts.</p></li>
<li><p><strong>Recall</strong> – Of all the <em>actual threats</em>, how
many did we detect?</p>
<p><em>Focus:</em> Detection coverage.</p></li>
<li><p><strong>Accuracy</strong> – The overall correctness (TP + TN) ÷
total cases.</p>
<p><em>Caution:</em> In security, accuracy can be misleading because
true negatives (benign activities) can dominate the results and make
detection appear better than it actually is.</p></li>
</ul>
<hr />
<h3 id="4-accuracy-vs-coverage"><strong>4. Accuracy vs
Coverage</strong></h3>
<h3 id="accuracy"><strong>Accuracy</strong></h3>
<ul>
<li>High accuracy means detections trigger <strong>only when something
truly malicious happens</strong>.</li>
<li><strong>Advantages:</strong>
<ul>
<li>Low false positive rates.</li>
<li>Builds trust in SOC alerts.</li>
</ul></li>
<li><strong>Risk:</strong>
<ul>
<li>Can result in missed threats (higher false negatives) if rules are
too narrow.</li>
</ul></li>
</ul>
<h3 id="coverage"><strong>Coverage</strong></h3>
<ul>
<li>Measures how much of the <strong>attack surface</strong> your
detections monitor.</li>
<li><strong>Advantages:</strong>
<ul>
<li>Increases chances of catching novel or evasive threats.</li>
</ul></li>
<li><strong>Risk:</strong>
<ul>
<li>More false positives → Analyst fatigue.</li>
</ul></li>
</ul>
<hr />
<h3 id="5-the-constant-push-and-pull"><strong>5. The Constant Push and
Pull</strong></h3>
<p>In detection engineering:</p>
<ul>
<li><strong>Improving accuracy</strong> often <strong>reduces
coverage</strong>.</li>
<li><strong>Increasing coverage</strong> often <strong>reduces
accuracy</strong>.</li>
<li>The “right” balance depends on:
<ul>
<li>Your <strong>organization’s risk tolerance</strong>.</li>
<li><strong>Available SOC resources</strong>.</li>
<li><strong>Threat landscape</strong> (what attackers you expect to
face).</li>
</ul></li>
</ul>
<hr />
<h3 id="6-example-scenarios"><strong>6. Example Scenarios</strong></h3>
<ul>
<li><strong>High Accuracy:</strong>
<ul>
<li>Minimal false positives.</li>
<li>Higher miss rate for new or unusual threats.</li>
</ul></li>
<li><strong>High Coverage:</strong>
<ul>
<li>Broad visibility into activity.</li>
<li>More false positives → SOC must handle higher workload.</li>
</ul></li>
</ul>
<hr />
<h3 id="7-solo-purple-teaming-challenges"><strong>7. Solo Purple Teaming
Challenges</strong></h3>
<p>Solo purple teaming can be powerful, but simulating a real-world
environment introduces limitations:</p>
<ul>
<li>Lack of <strong>real user behavior</strong> or <strong>network
noise</strong> → Hard to simulate realistic false positives.</li>
<li>May miss techniques you don’t emulate yourself.</li>
<li>No incident response team or production telemetry → No feedback loop
to improve detections.</li>
<li>Risk of <strong>overly optimistic results</strong> or <strong>blind
spots</strong> in coverage.</li>
</ul>
<hr />
<h3 id="8-preparing-for-the-next-lesson"><strong>8. Preparing for the
Next Lesson</strong></h3>
<p>In the next lecture, we’ll discuss:</p>
<ul>
<li><strong>Balancing telemetry volume</strong> vs <strong>search
performance</strong>.</li>
<li>Why too much telemetry can have a performance and cost impact.</li>
<li>How to apply these lessons to production environments.</li>
</ul>
</section>
</div>
