---
layout: default
title: Solo Purple Teaming
permalink: /solo-purple-teaming/
---

<section class="page-hero course-hero">
  <p class="tag">Course Hub</p>
  <h1>Solo Purple Teaming</h1>
  <p>Layout, execute, export, and trace your way through hands-on purple teaming labs.</p>
</section>

<section class="content-section course-page">
  <div class="content-card">
    <h2>Welcome to Solo Purple Teaming</h2>

    <p>
      As I advanced in my career as a defensive cybersecurity professional, I quickly realized that building effective detections,
      hunting sophisticated threats, and responding to incidents required more than just a defensive mindset. It demanded a deep
      understanding of offensive tactics.
    </p>

    <p>
      To close that gap, I trained myself in offensive security, learning to think and operate like an attacker. Along the way,
      I developed a structured process that led to real defensive wins.
    </p>

    <p>
      This course is the result of that experience: a method I call <strong>solo purple teaming</strong>.
    </p>

    <h2>Course Target Audience</h2>

    <p>
      This course is intended for highly motivated individuals seeking an advanced understanding of both offensive and defensive
      cybersecurity concepts. It is best suited for learners who possess the time, dedication, and perseverance to engage in deep,
      challenging study, and who are willing to push through difficulties in pursuit of meaningful breakthroughs.
    </p>

    <h3>This Course May Not Be Suitable If You:</h3>

    <ul>
      <li>Are unwilling to conduct independent research outside of class materials</li>
      <li>Lack the desire to think critically and independently</li>
      <li>Prefer routine methods over the challenge of innovating or discovering new techniques</li>
      <li>Are not prepared to invest time, effort, and resources into maintaining a personal lab environment for hands-on practice</li>
    </ul>

    <div class="callout warning-callout">
      <h3>Warning</h3>
      <p>
        This course is designed to foster independent learning. <strong>Support will be intentionally limited</strong> to help you build
        the critical thinking and self-reliance required to become an effective and resourceful cybersecurity professional.
      </p>
    </div>

    <h2>Disclaimer About Wazuh</h2>

    <p>
      While I haven’t used Wazuh in a professional setting, I’ve dedicated many hours to learning how to configure and operate it.
      My understanding is still growing, but I selected Wazuh for this course because it’s a powerful, open-source security platform
      that offers a wide range of features without adding extra cost.
    </p>

    <p>
      Wazuh does have some limitations compared to commercial SIEM solutions, and managing it in your lab environment may occasionally
      be challenging. However, the hands-on experience you’ll gain from working with a more complex setup will be incredibly valuable.
    </p>

    <p>
      The lessons learned from configuring and maintaining this platform will more than pay off in your future cybersecurity work.
    </p>

    <h2>Course Resources</h2>

    <div class="resource-grid">
      <a class="resource-card" href="https://github.com/jayglow/SoloPurpleTeaming" target="_blank" rel="noopener">
        <span class="resource-label">GitHub</span>
        <strong>Solo Purple Teaming Repository</strong>
        <p>Code snippets and configuration files for the course.</p>
      </a>

      <a class="resource-card" href="https://discord.com/invite/ghXJszBWcb" target="_blank" rel="noopener">
        <span class="resource-label">Community</span>
        <strong>Discord Server</strong>
        <p>Join the JayGlowCyber community and course discussion space.</p>
      </a>
    </div>

    <h2>Course Requirements</h2>

    <p>Intermediate knowledge of:</p>

    <ul>
      <li>Networking and routing</li>
      <li>Programming in C++, C#, and Python</li>
      <li>Offensive tactics, techniques, and procedures</li>
      <li>Reverse engineering, malware analysis, and Windows internals</li>
      <li>Threat hunting, incident response, and detection engineering</li>
    </ul>

    <div class="callout">
      <h3>Need a Foundation First?</h3>
      <p>
        If you need to build a foundation before jumping into this course, we recommend
        <strong>The Complete Hands-On Cybersecurity Analyst Course</strong>.
      </p>
      <p>
        <a href="https://www.udemy.com/course/the-complete-hands-on-cybersecurity-analyst-course/?kw=the+complete+cybersecurity&src=sac" target="_blank" rel="noopener">
          View the course on Udemy
        </a>
      </p>
      <p>Reach out on Discord, and we will get you a discount code.</p>
    </div>


    <h2>Course Outline</h2>

    <p>Use this table as the main index for the Solo Purple Teaming course pages and walkthrough supplements.</p>

<div class="course-outline-table-wrap">
<table class="course-outline-table">
<thead><tr><th>Section</th><th>Lecture</th><th>Walkthrough/Lecture Supplement</th></tr></thead>
<tbody>
<tr><td>Introduction</td><td>Course Introduction</td><td>N/A</td></tr>
<tr><td>Introduction</td><td>Solo Purple Teaming</td><td><a href="/solo-purple-teaming/walkthrough-what-is-solo-purple-teaming/">Supplement: What is Solo Purple Teaming?</a></td></tr>
<tr><td>Introduction</td><td>Scenario 1</td><td><a href="/solo-purple-teaming/supplement-scenario-1/">Supplement: Scenario 1</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Initial Lab Setup</td><td><a href="/solo-purple-teaming/suplement-initial-lab-setup/">Suplement: Initial Lab Setup</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Choosing C2 Framework</td><td><a href="/solo-purple-teaming/supplement-choosing-c2-framework/">Supplement: Choosing C2 Framework</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>PowerShell Reverse Shell Without Defender</td><td><a href="/solo-purple-teaming/walkthrough-powershell-reverse-shell-without-defen/">Walkthrough:PowerShell Reverse Shell Without Defender</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Determining Root Cause</td><td><a href="/solo-purple-teaming/supplement-determining-root-cause/">Walkthrough: Determining Root Cause</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>What is AMSI?</td><td><a href="/solo-purple-teaming/supplement-what-is-amsi/">Supplement: What is AMSI?</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Bypassing AMSI Using x64dbg</td><td><a href="/solo-purple-teaming/walkthrough-bypassing-amsi-using-x64dbg/">Walkthrough:Bypassing AMSI Using x64dbg</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>When Patching Doesn’t Go As Planned</td><td><a href="/solo-purple-teaming/when-patching-does-not-go-as-planned/">When Patching Does Not Go As Planned</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Introduction to Managed Code and .NET Assemblies</td><td><a href="/solo-purple-teaming/managed-vs-unmanaged/">Managed VS Unmanaged</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Full AMSI Bypass</td><td><a href="/solo-purple-teaming/full-amsi-bypass/">Full AMSI Bypass</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>A Warning and  a Challenge</td><td><a href="/solo-purple-teaming/a-warning-and-a-challenge/">A Warning and a Challenge</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Installing Mythic C2 Framework</td><td><a href="/solo-purple-teaming/installing-mythic-c2/">Installing Mythic C2</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Installing Apollo and a Couple C2 Profiles</td><td><a href="/solo-purple-teaming/installing-apollo/">Installing Apollo</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Introduction to Reflection</td><td><a href="/solo-purple-teaming/introduction-to-reflection/">Introduction to Reflection</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Prepping Apollo for Reflection</td><td><a href="/solo-purple-teaming/prepping-the-apollo-agent/">Prepping the Apollo Agent</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Introduction to AV</td><td><a href="/solo-purple-teaming/introduction-to-av/">Introduction to AV</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Bypassing Signature Based Detections</td><td><a href="/solo-purple-teaming/bypassing-signature-based-detections/">Bypassing Signature-Based Detections</a></td></tr>
<tr><td>Red Team Layout for Level Zero</td><td>Moving Custom Apollo to Mythic Sever</td><td><a href="/solo-purple-teaming/moving-tuned-apollo-agent-to-mythic-server/">Moving Tuned Apollo Agent to Mythic Server</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Attack Path Outline</td><td><a href="/solo-purple-teaming/attack-path-level-0-lab-setup/">Attack Path Level 0 Lab Setup</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Setting Up Edge Appliances</td><td><a href="/solo-purple-teaming/setting-up-edge-appliances/">Setting Up Edge Appliances</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Moving Kali to Attack LAN</td><td><a href="/solo-purple-teaming/moving-kali-to-attack-lan/">Moving Kali To Attack LAN</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Configuring Firewall Rules for Attack LAN</td><td><a href="/solo-purple-teaming/configure-firewall-rules/">Configure Firewall Rules</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Setting Up Assumed Breach Host</td><td><a href="/solo-purple-teaming/setting-up-assumed-breach/">Setting Up Assumed Breach</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Testing Apollo on Assumed Breach Host</td><td><a href="/solo-purple-teaming/testing-apollo-on-assumed-breach-host/">Testing Apollo on Assumed Breach Host</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Setting Up ECORP Edge Appliance</td><td><a href="/solo-purple-teaming/setting-up-ecorp-edge-appliance/">Setting Up ECORP Edge Appliance</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Setting UP ECORP DC and Domain</td><td><a href="/solo-purple-teaming/building-the-e-corp-domain-controller/">**Building the E Corp Domain Controller**</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Joining Assumed Breach Host to ECOIN Domain</td><td><a href="/solo-purple-teaming/joining-assumed-breach-host-to-ecoin-domain/">Joining Assumed Breach Host to ECOIN Domain</a></td></tr>
<tr><td>Lab Setup for Level Zero</td><td>Creating Service Account and Service</td><td><a href="/solo-purple-teaming/create-vulnerable-service-binary/">Create Vulnerable Service Binary</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Review Attack Path Level 0</td><td><a href="/solo-purple-teaming/level-zero-attack-path-review/">**Level Zero Attack Path Review**</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Introduction to Mythic UI</td><td><a href="/solo-purple-teaming/introduction-to-mythic-ui-basics/">Introduction to Mythic UI Basics</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Mythic User Settings</td><td><a href="/solo-purple-teaming/mythic-user-settings/">Mythic User Settings</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Create Operation for Level 0 Attack Path</td><td><a href="/solo-purple-teaming/create-mythic-operations/">Create Mythic Operations</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Generate Payload for Attack Level 0</td><td><a href="/solo-purple-teaming/generate-payload/">Generate Payload</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Establish Foothold in ECOIN</td><td><a href="/solo-purple-teaming/establish-foothold-in-ecoin/">Establish Foothold in ECOIN</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Mythic Callbacks UI</td><td><a href="/solo-purple-teaming/mythic-callbacks-ui/">Mythic Callbacks UI</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Mythic Tasking</td><td><a href="/solo-purple-teaming/mythic-tasking/">Mythic Tasking</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Enumeration</td><td><a href="/solo-purple-teaming/enumeration/">Enumeration</a></td></tr>
<tr><td>Red Team Engage for Level Zero</td><td>Privilege Escalation</td><td><a href="/solo-purple-teaming/privilege-escalation/">Privilege Escalation</a></td></tr>
<tr><td>Lab Setup for Blue Team Activities</td><td>Setting Up the AllSafe Edge Appliance</td><td><a href="/solo-purple-teaming/allsafe-edge-device-setup/">AllSafe Edge Device Setup</a></td></tr>
<tr><td>Lab Setup for Blue Team Activities</td><td>Setting Up the Wazuh Server</td><td><a href="/solo-purple-teaming/setup-the-wazuh-server/">Setup the Wazuh Server</a></td></tr>
<tr><td>Lab Setup for Blue Team Activities</td><td>Port Forwarding to Wazuh Server</td><td><a href="/solo-purple-teaming/port-forwarding-to-wazuh-server/">Port Forwarding to Wazuh Server</a></td></tr>
<tr><td>Lab Setup for Blue Team Activities</td><td>Installing Wazuh Agent on Assumed Breach Host</td><td><a href="/solo-purple-teaming/install-wazuh-agent/">Install Wazuh Agent</a></td></tr>
<tr><td>Lab Setup for Blue Team Activities</td><td>Installing Sysmon on Assumed Breach Host</td><td><a href="/solo-purple-teaming/install-sysmon/">Install Sysmon</a></td></tr>
<tr><td>Lab Setup for Blue Team Activities</td><td>Configuring Wazuh for Defender and Sysmon</td><td><a href="/solo-purple-teaming/configuring-wazuh-for-defender-and-sysmon/">Configuring Wazuh for Defender and Sysmon</a></td></tr>
<tr><td>Lab Setup for Blue Team Activities</td><td>Sending Syslog Events From ECOIN Edge</td><td><a href="/solo-purple-teaming/sending-syslog-events-from-ecoin-edge/">Sending Syslog Events From ECOIN Edge</a></td></tr>
<tr><td>Detection Engineering 101</td><td>What is Detection Engineering?</td><td><a href="/solo-purple-teaming/what-is-detection-engineering/">What is Detection Engineering?</a></td></tr>
<tr><td>Detection Engineering 101</td><td>Pyramid of Pain</td><td><a href="/solo-purple-teaming/pyramid-of-pain/">Pyramid of Pain</a></td></tr>
<tr><td>Detection Engineering 101</td><td>Balancing Accuracy VS Coverage</td><td><a href="/solo-purple-teaming/balancing-accuracy-vs-coverage/">Balancing Accuracy VS Coverage</a></td></tr>
<tr><td>Detection Engineering 101</td><td>Balancing Telemetry VS Performance</td><td><a href="/solo-purple-teaming/balancing-telemetry-vs-performance/">Balancing Telemetry VS Performance</a></td></tr>
<tr><td>Blue Team Explore For Level 0</td><td>Detection Opportunities</td><td><a href="/solo-purple-teaming/detection-opportunities/">Detection Opportunities</a></td></tr>
<tr><td>Blue Team Explore For Level 0</td><td>Sysmon ProcessCreate Event ID 1 &amp; NetworkConnect Event ID 3</td><td><a href="/solo-purple-teaming/sysmon-processcreate-event-id-1-and-networkconnect-e/">Sysmon ProcessCreate Event ID 1 &amp; NetworkConnect Event ID 3</a></td></tr>
<tr><td>Blue Team Explore For Level 0</td><td>Sysmon ImageLoad Event ID 7</td><td><a href="/solo-purple-teaming/sysmon-imageload-event-id-7/">Sysmon ImageLoad Event ID 7</a></td></tr>
<tr><td>Blue Team Explore For Level 0</td><td>Sysmon FileCreate Event ID 11</td><td><a href="/solo-purple-teaming/sysmon-filecreate-event-id-11/">Sysmon FileCreate Event ID 11</a></td></tr>
<tr><td>Blue Team Explore For Level 0</td><td>PowerShell ScriptBlock Logging</td><td><a href="/solo-purple-teaming/powershell-scriptblock-logging/">PowerShell ScriptBlock Logging</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Setting Up Wazuh for Trace Activities</td><td><a href="/solo-purple-teaming/setting-up-wazuh-for-trace-activities/">Setting Up Wazuh for Trace Activities</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Build Custom Rule for Unsigned ImageLoads</td><td><a href="/solo-purple-teaming/build-custom-rule-for-unsigned-imageloads/">Build Custom Rule for Unsigned ImageLoads</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Build Custom Rule for Network Connect to Common C2 Port</td><td><a href="/solo-purple-teaming/build-custom-rule-for-network-connect-to-common-c2/">Build Custom Rule for Network Connect to Common C2 Port</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Build Custom Correlation Rule</td><td><a href="/solo-purple-teaming/build-custom-correlation-rule/">Build Custom Correlation Rule</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Building Advanced Correlation Engine - Part 1</td><td><a href="/solo-purple-teaming/building-advanced-correlation-engine-part-1/">Building Advanced Correlation Engine - Part 1</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Building Advanced Correlation Engine - Part 2</td><td><a href="/solo-purple-teaming/building-advanced-correlation-engine-part-2/">Building Advanced Correlation Engine - Part 2</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Building Advanced Correlation Engine - Part 3</td><td><a href="/solo-purple-teaming/building-advanced-correlation-engine-part-3/">Building Advanced Correlation Engine - Part 3</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Building Advanced Correlation Engine - Part 4</td><td><a href="/solo-purple-teaming/building-advanced-correlation-engine-part-4/">Building Advanced Correlation Engine - Part 4</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Hunting For PowerShell IOCs and Building Detections</td><td><a href="/solo-purple-teaming/hunting-for-powershell-iocs-and-building-detection/">Hunting For PowerShell IOCs and Building Detections</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Creating Custom Rule for PowerShell Reflection</td><td><a href="/solo-purple-teaming/creating-custom-rule-for-powershell-reflection/">Creating Custom Rule for PowerShell Reflection</a></td></tr>
<tr><td>Blue Team Trace For Level 0</td><td>Improving Solo Purple Teaming Detection Dashboard</td><td><a href="/solo-purple-teaming/improving-solo-purple-teaming-detection-dashboard/">Improving Solo Purple Teaming Detection Dashboard</a></td></tr>
<tr><td>Red Team Re-Engage For Level 0</td><td>Red Team Re-Engage For Level - Testing All Detections</td><td><a href="/solo-purple-teaming/red-team-re-engage-for-level-testing-all-detecti/">Red Team Re-Engage For Level - Testing All Detections</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Red Team Layout - Using Threat Intel to Formulate Next Attack Path</td><td><a href="/solo-purple-teaming/red-team-layout-using-threat-intel-to-formulate/">Red Team Layout - Using Threat Intel to Formulate Next Attack Path</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Attack Path Level 1</td><td><a href="/solo-purple-teaming/attack-path-level-1/">Attack Path Level 1</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Using Run Box to Bypass UnSigned ImageLoads From Downloads</td><td><a href="/solo-purple-teaming/using-run-box-to-bypass-unsigned-imageloads-from-d/">Using Run Box to Bypass UnSigned ImageLoads From Downloads</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Red Team Layout - Using Application Domain to Bypass Reflection Detection</td><td><a href="/solo-purple-teaming/red-team-layout-using-application-domain-to-bypa/">Red Team Layout - Using Application Domain to Bypass Reflection Detection</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Red Team Layout - Writing A Service Enumeration Tool - Part 1</td><td><a href="/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to/">Red Team Layout - Writing A Service Enumeration Tool - Part 1</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Red Team Layout - Writing A Service Enumeration Tool - Part 2</td><td><a href="/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-4/">Red Team Layout - Writing A Service Enumeration Tool - Part 2</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Red Team Layout - Writing A Service Enumeration Tool - Part 3</td><td><a href="/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-3/">Red Team Layout - Writing A Service Enumeration Tool - Part 3</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Red Team Layout - Writing A Service Enumeration Tool - Part 4</td><td><a href="/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-2/">Red Team Layout - Writing A Service Enumeration Tool - Part 4</a></td></tr>
<tr><td>Red Team Layout For Level 1</td><td>Red Team Layout - Testing Service Enumeration Bypass</td><td><a href="/solo-purple-teaming/red-team-layout-testing-service-enumeration-bypa/">Red Team Layout - Testing Service Enumeration Bypass</a></td></tr>
<tr><td>Attack Path Level 1 Lab Setup</td><td>Adding sec_ecoin_sync to Domain Admins</td><td><a href="/solo-purple-teaming/adding-sec-ecoin-sync-to-domain-admins/">Adding sec_ecoin_sync to Domain Admins</a></td></tr>
<tr><td>Red Team Engage For Level 1</td><td>Red Team Engage - Initial Access</td><td><a href="/solo-purple-teaming/red-team-engage-initial-access/">Red Team Engage - Initial Access</a></td></tr>
<tr><td>Red Team Engage For Level 1</td><td>Red Team Engage - Privilege Escalation</td><td><a href="/solo-purple-teaming/red-team-engage-privilege-escalation/">Red Team Engage - Privilege Escalation</a></td></tr>
<tr><td>Red Team Engage For Level 1</td><td>Red Team Engage - Lateral Movement</td><td><a href="/solo-purple-teaming/red-team-engage-lateral-movement/">Red Team Engage - Lateral Movement</a></td></tr>
<tr><td>Blue Team Explore For Level 1</td><td>Blue Team Explore - What to Expect</td><td><a href="/solo-purple-teaming/blue-team-explore-what-to-expect/">Blue Team Explore - What to Expect</a></td></tr>
<tr><td>Blue Team Explore For Level 1</td><td>Blue Team Explore - Learning About execute_assembly, Process Injection, and IPC</td><td><a href="/solo-purple-teaming/blue-team-explore-learning-about-execute-assembl/">Blue Team Explore - Learning About execute_assembly, Process Injection, and IPC</a></td></tr>
<tr><td>Blue Team Explore For Level 1</td><td>Blue Team Explore - Enabling Sysmon Event IDs 10, 17 &amp; 18</td><td><a href="/solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-10/">Blue Team Explore - Enabling Sysmon Event IDs 10, 17 &amp; 18</a></td></tr>
<tr><td>Blue Team Explore For Level 1</td><td>Blue Team Explore: Hunting For Indications of execute_assembly</td><td><a href="/solo-purple-teaming/blue-team-explore-hunting-for-indications-of-execu/">Blue Team Explore: Hunting For Indications of execute_assembly</a></td></tr>
<tr><td>Blue Team Explore For Level 1</td><td>Blue Team Explore - Enabling Sysmon Event IDs 12, 13 &amp; 14</td><td><a href="/solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-12/">Blue Team Explore - Enabling Sysmon Event IDs 12, 13 &amp; 14</a></td></tr>
<tr><td>Blue Team Explore For Level 1</td><td>Blue Team Explore  - Hunting For Initial Access Execution</td><td><a href="/solo-purple-teaming/blue-team-explore-hunting-for-initial-access-exe/">Blue Team Explore  - Hunting For Initial Access Execution</a></td></tr>
<tr><td>Blue Team Trace For Level 1</td><td>Blue Team Trace - Reversing Execute Assembly and Customizing Mythic</td><td><a href="/solo-purple-teaming/reversing-execute-assembly-and-customizing-mythic/">Blue Team Trace - Reversing Execute Assembly and Customizing Mythic</a></td></tr>
<tr><td>Blue Team Trace For Level 1</td><td>Blue Team Trace - Extracting Payload</td><td><a href="/solo-purple-teaming/walkthrough-extracting-payload/">Walkthrough: Extracting Payload</a></td></tr>
<tr><td>Blue Team Trace For Level 1</td><td>Blue Team Trace - Basic Malware Analysis</td><td><a href="/solo-purple-teaming/walkthrough-basic-malware-analysis/">Walkthrough: Basic Malware Analysis</a></td></tr>
<tr><td>Blue Team Trace For Level 1</td><td>Build Detections For IWR and RunMRU</td><td><a href="/solo-purple-teaming/building-detections-for-iwr-and-runmru/">Building Detections for IWR and RunMRU</a></td></tr>
<tr><td>Final Thoughts</td><td>Final Thoughts</td><td><a href="/solo-purple-teaming/final-thoughts/">Final Thoughts</a></td></tr>
</tbody>
</table>
</div>
  </div>
</section>
