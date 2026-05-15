---
layout: default
title: "Building Advanced Correlation Engine - Part 2"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-2/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 2</h1>
</section>
<section class="spt-content">
<h2 id="objective"><strong>Objective</strong></h2>
<p>We’re enhancing our <strong>advanced correlation engine</strong> by
extracting additional useful fields from Wazuh alerts.</p>
<p>In this lesson, we’ll focus on:</p>
<ol type="1">
<li>Parsing <strong>host name</strong> from the alert.</li>
<li>Parsing <strong>image</strong> from event data.</li>
<li>Updating the correlation logic to include these fields.</li>
</ol>
<hr />
<h2 id="step-1--why-parse-extra-fields"><strong>Step 1 – Why Parse Extra
Fields?</strong></h2>
<p>In the previous lecture, our alerts lacked critical context — most
importantly, <strong>which host</strong> the alert originated from.
Without this, correlation loses value. We also want to include other
relevant details (like <code>image</code>) for better analysis.</p>
<hr />
<h2 id="step-2--access-the-script"><strong>Step 2 – Access the
Script</strong></h2>
<p>We’ll modify our Python correlation engine script:</p>
<div class="sourceCode" id="cb1"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="bu">cd</span> /opt/wazuh-tools</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a><span class="fu">sudo</span> vi wazuh_ace.py</span></code></pre></div>
<p>Locate the <strong><code>parse_alert()</code></strong> function.</p>
<hr />
<h2 id="step-3--parsing-the-host-name"><strong>Step 3 – Parsing the Host
Name</strong></h2>
<ul>
<li>In Wazuh event data, <strong>host name</strong> is stored under:
<ul>
<li><code>agent</code> → <code>name</code></li>
</ul></li>
<li>In Python, this means we first pull the
<strong><code>agent</code></strong> dictionary, then get the
<strong><code>name</code></strong> key.</li>
</ul>
<p><strong>Example Code:</strong></p>
<div class="sourceCode" id="cb2"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a>agent <span class="op">=</span> alert.get(<span class="st">"agent"</span>, {})</span>
<span id="cb2-2"><a href="#cb2-2" aria-hidden="true" tabindex="-1"></a>host_name <span class="op">=</span> agent.get(<span class="st">"name"</span>, <span class="st">""</span>)</span></code></pre></div>
<p>We:</p>
<ul>
<li>Use <code>.get()</code> to safely retrieve values.</li>
<li>Default to <code>{}</code> (empty dict) or <code>""</code> to avoid
errors if a key doesn’t exist.</li>
<li>Add <code>host_name</code> to the return statement of
<code>parse_alert()</code>.</li>
</ul>
<hr />
<h2
id="step-4--update-correlate_events-to-include-host-name"><strong>Step 4
– Update <code>correlate_events()</code> to Include Host
Name</strong></h2>
<p>Inside <strong><code>correlate_events()</code></strong>:</p>
<ul>
<li>Capture <code>host_name</code> from the <code>parse_alert()</code>
return values.</li>
<li>Update any tuples or comprehensions to include it.</li>
<li>Modify the alert string to use <strong>string interpolation</strong>
for displaying the host name.</li>
</ul>
<p><strong>Example Change:</strong></p>
<div class="sourceCode" id="cb3"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>alert_message <span class="op">=</span> <span class="ss">f"Correlated possible C2 activity on </span><span class="sc">{</span>host_name<span class="sc">}</span><span class="ss">"</span></span></code></pre></div>
<hr />
<h2 id="step-5--test-host-name-parsing"><strong>Step 5 – Test Host Name
Parsing</strong></h2>
<ol type="1">
<li><p>Save and exit (<code>:wq</code>).</p></li>
<li><p>Run the script:</p>
<div class="sourceCode" id="cb4"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> wazuh_ace.py</span></code></pre></div></li>
<li><p>Trigger an alert (e.g., run the initial access payload).</p></li>
<li><p>Check output — if the host name shows correctly, parsing
works.</p></li>
</ol>
<hr />
<h2 id="step-6--debugging-host-name-issues"><strong>Step 6 – Debugging
Host Name Issues</strong></h2>
<p>If you see an empty host name:</p>
<ul>
<li><p>Ensure you used:</p>
<div class="sourceCode" id="cb5"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb5-1"><a href="#cb5-1" aria-hidden="true" tabindex="-1"></a>agent <span class="op">=</span> alert.get(<span class="st">"agent"</span>, {})</span>
<span id="cb5-2"><a href="#cb5-2" aria-hidden="true" tabindex="-1"></a>host_name <span class="op">=</span> agent.get(<span class="st">"name"</span>, <span class="st">""</span>)</span></code></pre></div></li>
<li><p><strong>Do not</strong> try to get <code>"name"</code> directly
from <code>alert</code> — it lives under the <code>agent</code>
key.</p></li>
</ul>
<hr />
<h2 id="step-7--parsing-the-image-field"><strong>Step 7 – Parsing the
Image Field</strong></h2>
<p>The <code>image</code> field lives deeper in the structure:</p>
<ul>
<li>Path: <code>data</code> → <code>win</code> → <code>eventdata</code>
→ <code>Image</code></li>
</ul>
<p>We use chained <code>.get()</code> calls to safely navigate:</p>
<p><strong>Example Code:</strong></p>
<div class="sourceCode" id="cb6"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a>event_data <span class="op">=</span> alert.get(<span class="st">"data"</span>, {}).get(<span class="st">"win"</span>, {}).get(<span class="st">"eventdata"</span>, {})</span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a>image <span class="op">=</span> event_data.get(<span class="st">"Image"</span>, <span class="st">"unknown"</span>)</span></code></pre></div>
<ul>
<li>If any part is missing, we default to <code>{}</code> to prevent key
errors.</li>
<li>If <code>Image</code> isn’t found, we set
<code>"unknown"</code>.</li>
</ul>
<p>Add <code>image</code> to the
<strong><code>parse_alert()</code></strong> return values.</p>
<hr />
<h2 id="step-8--update-correlate_events-to-include-image"><strong>Step 8
– Update <code>correlate_events()</code> to Include Image</strong></h2>
<ul>
<li>Capture <code>image</code> from <code>parse_alert()</code>.</li>
<li>Add it to the tuple and comprehension logic.</li>
<li>Display it in alerts (e.g., in square brackets).</li>
</ul>
<p><strong>Example:</strong></p>
<div class="sourceCode" id="cb7"><pre
class="sourceCode python"><code class="sourceCode python"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a>alert_message <span class="op">=</span> <span class="ss">f"Correlated possible C2 activity on </span><span class="sc">{</span>host_name<span class="sc">}</span><span class="ss"> [</span><span class="sc">{</span>image<span class="sc">}</span><span class="ss">]"</span></span></code></pre></div>
<hr />
<h2 id="step-9--test-image-parsing"><strong>Step 9 – Test Image
Parsing</strong></h2>
<ol type="1">
<li><p>Save and exit the script.</p></li>
<li><p>Run:</p>
<div class="sourceCode" id="cb8"><pre
class="sourceCode bash"><code class="sourceCode bash"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="ex">python3</span> wazuh_ace.py</span></code></pre></div></li>
<li><p>Trigger an alert again.</p></li>
<li><p>Verify that the <strong>image</strong> value now appears
alongside the host name.</p></li>
</ol>
<hr />
<h2 id="step-10--student-homework"><strong>Step 10 – Student
Homework</strong></h2>
<p>Enhance the correlation engine to parse <strong>all other fields you
think are important</strong> for detection — for example:</p>
<ul>
<li>Process GUID</li>
<li>Destination IP/Port</li>
<li>Command line arguments</li>
<li>User name</li>
</ul>
<p><strong>Tip:</strong> More fields give richer alerts, but too many
can create noise. Aim for balance.</p>
<hr />
<h2 id="next-steps"><strong>Next Steps</strong></h2>
<p>In the next lecture, we’ll:</p>
<ul>
<li>Turn this correlation engine into a <strong>service</strong>.</li>
<li>Make it monitor a file.</li>
<li>Have Wazuh watch that file and ingest the correlation results into
the dashboard.</li>
</ul>
</section>
</div>
