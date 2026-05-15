---
layout: default
title: "Attack Path Level 0 Lab Setup"
permalink: /solo-purple-teaming/attack-path-level-0-lab-setup/
---
<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Attack Path Level 0 Lab Setup</h1>
</section>
<section class="spt-content">
<h3 id="overview"><strong>Overview</strong></h3>
<p>This walkthrough guides you through the initial setup for the
<em>Attack Path Level Zero</em> lab environment. This lab is designed
specifically for <strong>solo purple teaming</strong>, a methodology
where a single individual plays both the attacker and defender to gain a
complete understanding of offensive tactics and defensive detection.</p>
<p>By the end of this walkthrough, you’ll understand:</p>
<ul>
<li>The structure and purpose of the lab</li>
<li>The systems involved</li>
<li>The role of each network segment</li>
<li>What comes next in the setup process</li>
</ul>
<hr />
<p><img src="/assets/images/solo-purple-teaming/attack-path-level-0-lab-setup/image.png"
alt="image.png" /></p>
<h2 id="1-purpose-of-the-lab"><strong>1. Purpose of the
Lab</strong></h2>
<p>This lab simulates a small but realistic enterprise environment. The
goal is to allow you to:</p>
<ul>
<li>Develop and test attack payloads</li>
<li>Deploy and manage implants</li>
<li>Conduct post-compromise operations</li>
<li>Create and tune detection mechanisms in a controlled and segmented
network</li>
</ul>
<p>This setup is ideal for:</p>
<ul>
<li>Practicing red and blue team skills</li>
<li>Developing detection strategies</li>
<li>Understanding the full attack kill chain</li>
</ul>
<hr />
<h2 id="2-lab-architecture"><strong>2. Lab Architecture</strong></h2>
<p>The lab environment consists of <strong>two isolated
LANs</strong>:</p>
<h3 id="a-attack-lan"><strong>A. Attack LAN</strong></h3>
<p>This network represents the attacker’s infrastructure and
includes:</p>
<ul>
<li><strong>Reverse Engineering Workstation</strong>
<ul>
<li>Purpose: Used to <strong>develop and obfuscate custom
payloads</strong></li>
</ul></li>
<li><strong>Kali Linux System</strong>
<ul>
<li>Purpose: Hosts the <strong>Mythic Command and Control (C2)</strong>
framework</li>
<li>Role: Enables <strong>implant management</strong> and
<strong>post-exploitation activities</strong></li>
</ul></li>
</ul>
<p>These two machines are already provisioned but must be moved to the
appropriate virtual or physical network segment to be effective.</p>
<h3 id="b-target-lan-ecoin"><strong>B. Target LAN (ECOIN)</strong></h3>
<p>This network simulates a typical victim environment and currently
includes:</p>
<ul>
<li><strong>One Windows 11 Host</strong>
<ul>
<li>Role: Assumed <strong>breach point</strong> for the attacker</li>
<li>Purpose: Allows simulation of:
<ul>
<li>Privilege escalation</li>
<li>Credential harvesting</li>
<li>Lateral movement</li>
<li>Persistence mechanisms</li>
</ul></li>
</ul></li>
</ul>
<p>Each LAN is <strong>protected by a pfSense firewall</strong>, which
serves as the <strong>edge device</strong> and simulates perimeter
defenses common in enterprise networks.</p>
<hr />
<h2 id="3-future-expansion-the-ecoin-enterprise"><strong>3. Future
Expansion: The ECOIN Enterprise</strong></h2>
<p>The lab will evolve into a more complex simulated environment:</p>
<ul>
<li><strong>Ecoin</strong> will represent a growing enterprise
network</li>
<li>It will become a <strong>subdomain of Ecorp</strong>, supporting
advanced attack paths such as:
<ul>
<li>Active Directory trust abuse</li>
<li>Multi-domain lateral movement</li>
<li>Advanced red/blue team scenarios</li>
</ul></li>
</ul>
<p>This future modularity supports realistic simulations and structured
learning paths as you progress through the purple teaming program.</p>
<hr />
<h2 id="4-solo-purple-teaming-benefits"><strong>4. Solo Purple Teaming
Benefits</strong></h2>
<p>This lab setup is purpose-built for solo practitioners, allowing you
to:</p>
<ul>
<li>Practice <strong>both offensive and defensive
techniques</strong></li>
<li>Simulate <strong>realistic enterprise conditions</strong></li>
<li>Develop a <strong>deep understanding</strong> of tactics,
techniques, and procedures (TTPs)</li>
<li>Test <strong>detections and evasion strategies</strong> in a safe
and repeatable environment</li>
</ul>
<hr />
<h2 id="5-whats-next"><strong>5. What’s Next</strong></h2>
<p>In the next stage of the lab setup, you will:</p>
<ul>
<li><strong>Deploy the edge devices</strong> (pfSense firewalls)</li>
<li>Define the boundaries and routing between the attack and target
LANs</li>
<li>Control traffic flow and create a realistic network perimeter</li>
</ul>
<p>These edge devices will:</p>
<ul>
<li>Simulate <strong>real-world enterprise segmentation</strong></li>
<li>Enable <strong>traffic filtering and logging</strong></li>
<li>Provide a <strong>safe sandbox</strong> for testing offensive and
defensive actions</li>
</ul>
<hr />
<h2 id="summary"><strong>Summary</strong></h2>
<p>This foundational setup gives you the infrastructure to:</p>
<ul>
<li>Launch attacks</li>
<li>Analyze responses</li>
<li>Build detections</li>
<li>Iterate on both red and blue team capabilities</li>
</ul>
</section>
</div>
