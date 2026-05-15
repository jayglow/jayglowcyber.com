---
layout: default
title: "Solo Purple Teaming"
permalink: /solo-purple-teaming/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<section class="spt-hero">
<p class="spt-kicker">Course Hub</p>
<h1>Solo Purple Teaming</h1>
<p>Layout, execute, export, and trace your way through hands-on purple teaming labs.</p>
</section>
<section class="spt-content">

# **Welcome to Solo Purple Teaming**

As I advanced in my career as a defensive cybersecurity professional, I quickly realized that building effective detections, hunting sophisticated threats, and responding to incidents required more than just a defensive mindset—it demanded a deep understanding of offensive tactics. To close that gap, I trained myself in offensive security, learning to think and operate like an attacker.

Along the way, I developed a structured process that led to real defensive wins. This course is the result of that experience—a method I call **solo purple teaming**.

## **Course Target Audience**

This course is intended for highly motivated individuals seeking an advanced understanding of both offensive and defensive cybersecurity concepts. It is best suited for learners who possess the time, dedication, and perseverance to engage in deep, challenging study—and who are willing to push through difficulties in pursuit of meaningful breakthroughs.

**This Course May Not Be Suitable If You:**

- Are unwilling to conduct independent research outside of class materials
- Lack the desire to think critically and independently
- Prefer routine methods over the challenge of innovating or discovering new techniques
- Are not prepared to invest time, effort, and resources into maintaining a personal lab environment for hands-on practice

## Warning!

This course is designed to foster independent learning. **Support will be intentionally limited to help you build the critical thinking and self-reliance required to become an effective and resourceful cybersecurity professional.**

# Disclaimer About Wazuh

While I haven’t used Wazuh in a professional setting, I’ve dedicated many hours to learning how to configure and operate it. My understanding is still growing, but I selected Wazuh for this course because it’s a powerful, open-source security platform that offers a wide range of features without adding extra cost.

Wazuh does have some limitations compared to commercial SIEM solutions, and managing it in your lab environment may occasionally be challenging. However, the hands-on experience you’ll gain from working with a more complex setup will be incredibly valuable. The lessons learned from configuring and maintaining this platform will more than pay off in your future cybersecurity work.

# Course Resources

All code snippets and configuration files are hosted on GitHub and can be accessed here:

[https://github.com/jayglow/SoloPurpleTeaming](https://github.com/jayglow/SoloPurpleTeaming)

Discord Server:

[https://discord.com/invite/ghXJszBWcb](https://discord.com/invite/ghXJszBWcb)

# Course Requirements

Intermediate Knowledge of:

- Networking and Routing
- Programming in C++, C#, and Python
- Offensive tactics, techniques, and procedures
- Reverse Engineering, Malware Analysis, and Windows Internals
- Threat Hunting, Incident Response, and Detection Engineering

If you need to build a foundation before jumping into this course, we recommend **The Complete Hands-On Cybersecurity Analyst Course:**

[https://www.udemy.com/course/the-complete-hands-on-cybersecurity-analyst-course/?kw=the+complete+cybersecurity&src=sac](https://www.udemy.com/course/the-complete-hands-on-cybersecurity-analyst-course/?kw=the+complete+cybersecurity&src=sac)

Reach out to us on Discord,  and we will get you a discount code.

Stage 0 Lab to Start the Course:

Kali VM

- Mythic Installed

Windows Workstation for Reverse Engineering and Testing

- Preferably Windows 11
- Software
    - Visual Studio Community Addition
    - x64dbg and x32dbg or alternative
    - Notepad++
    - SystemInformer
    - DNSpy
    - Sysinternal Suite

# Final Lab Environment

![image.png](/assets/images/solo-purple-teaming/when-patching-does-not-go-as-planned/image.png)

# Course Outline

| Section | Lecture | Walkthrough/Lecture Supplement |  |
| --- | --- | --- | --- |
| Introduction | Course Introduction | N/A |  |
| Introduction | Solo Purple Teaming | [Supplement: What is Solo Purple Teaming?](/solo-purple-teaming/walkthrough-what-is-solo-purple-teaming/) |  |
| Introduction | Scenario 1 |  [Supplement: Scenario 1](/solo-purple-teaming/supplement-scenario-1/) |  |
| Red Team Layout for Level Zero | Initial Lab Setup | [Suplement: Initial Lab Setup](/solo-purple-teaming/suplement-initial-lab-setup/) |  |
| Red Team Layout for Level Zero | Choosing C2 Framework | [Supplement: Choosing C2 Framework](/solo-purple-teaming/supplement-choosing-c2-framework/) |  |
| Red Team Layout for Level Zero | PowerShell Reverse Shell Without Defender | [Walkthrough:PowerShell Reverse Shell Without Defender](/solo-purple-teaming/walkthrough-powershell-reverse-shell-without-defen/) |  |
| Red Team Layout for Level Zero | Determining Root Cause | [Walkthrough: Determining Root Cause](/solo-purple-teaming/supplement-determining-root-cause/) |  |
| Red Team Layout for Level Zero | What is AMSI? | [Supplement: What is AMSI?](/solo-purple-teaming/supplement-what-is-amsi/) |  |
| Red Team Layout for Level Zero | Bypassing AMSI Using x64dbg | [Walkthrough:Bypassing AMSI Using x64dbg](/solo-purple-teaming/walkthrough-bypassing-amsi-using-x64dbg/) |  |
| Red Team Layout for Level Zero | When Patching Doesn’t Go As Planned | [When Patching Does Not Go As Planned](/solo-purple-teaming/when-patching-does-not-go-as-planned/)  |  |
| Red Team Layout for Level Zero | Introduction to Managed Code and .NET Assemblies | [Managed VS Unmanaged](/solo-purple-teaming/managed-vs-unmanaged/)  |  |
| Red Team Layout for Level Zero | Full AMSI Bypass | [Full AMSI Bypass](/solo-purple-teaming/full-amsi-bypass/)  |  |
| Red Team Layout for Level Zero | A Warning and  a Challenge | [A Warning and a Challenge](/solo-purple-teaming/a-warning-and-a-challenge/)  |  |
| Red Team Layout for Level Zero | Installing Mythic C2 Framework | [Installing Mythic C2](/solo-purple-teaming/installing-mythic-c2/)  |  |
| Red Team Layout for Level Zero | Installing Apollo and a Couple C2 Profiles | [Installing Apollo](/solo-purple-teaming/installing-apollo/)  |  |
| Red Team Layout for Level Zero | Introduction to Reflection | [Introduction to Reflection](/solo-purple-teaming/introduction-to-reflection/)  |  |
| Red Team Layout for Level Zero | Prepping Apollo for Reflection | [Prepping the Apollo Agent](/solo-purple-teaming/prepping-the-apollo-agent/)  |  |
| Red Team Layout for Level Zero | Introduction to AV | [Introduction to AV](/solo-purple-teaming/introduction-to-av/)  |  |
| Red Team Layout for Level Zero | Bypassing Signature Based Detections | [Bypassing Signature-Based Detections](/solo-purple-teaming/bypassing-signature-based-detections/)  |  |
| Red Team Layout for Level Zero | Moving Custom Apollo to Mythic Sever | [Moving Tuned Apollo Agent to Mythic Server](/solo-purple-teaming/moving-tuned-apollo-agent-to-mythic-server/)  |  |
| Lab Setup for Level Zero | Attack Path Outline | [Attack Path Level 0 Lab Setup](/solo-purple-teaming/attack-path-level-0-lab-setup/)  |  |
| Lab Setup for Level Zero | Setting Up Edge Appliances | [Setting Up Edge Appliances](/solo-purple-teaming/setting-up-edge-appliances/)  |  |
| Lab Setup for Level Zero | Moving Kali to Attack LAN | [Moving Kali To Attack LAN](/solo-purple-teaming/moving-kali-to-attack-lan/)  |  |
| Lab Setup for Level Zero | Configuring Firewall Rules for Attack LAN | [Configure Firewall Rules](/solo-purple-teaming/configure-firewall-rules/)  |  |
| Lab Setup for Level Zero | Setting Up Assumed Breach Host | [Setting Up Assumed Breach](/solo-purple-teaming/setting-up-assumed-breach/)  |  |
| Lab Setup for Level Zero | Testing Apollo on Assumed Breach Host | [Testing Apollo on Assumed Breach Host](/solo-purple-teaming/testing-apollo-on-assumed-breach-host/)  |  |
| Lab Setup for Level Zero | Setting Up ECORP Edge Appliance | [Setting Up ECORP Edge Appliance](/solo-purple-teaming/setting-up-ecorp-edge-appliance/)  |  |
| Lab Setup for Level Zero | Setting UP ECORP DC and Domain | [**Building the E Corp Domain Controller**](/solo-purple-teaming/building-the-e-corp-domain-controller/)  |  |
| Lab Setup for Level Zero | Joining Assumed Breach Host to ECOIN Domain | [Joining Assumed Breach Host to ECOIN Domain](/solo-purple-teaming/joining-assumed-breach-host-to-ecoin-domain/)  |  |
| Lab Setup for Level Zero | Creating Service Account and Service | [Create Vulnerable Service Binary](/solo-purple-teaming/create-vulnerable-service-binary/)  |  |
| Red Team Engage for Level Zero | Review Attack Path Level 0 | [**Level Zero Attack Path Review**](/solo-purple-teaming/level-zero-attack-path-review/)  |  |
| Red Team Engage for Level Zero | Introduction to Mythic UI | [Introduction to Mythic UI Basics](/solo-purple-teaming/introduction-to-mythic-ui-basics/)  |  |
| Red Team Engage for Level Zero | Mythic User Settings | [Mythic User Settings](/solo-purple-teaming/mythic-user-settings/)  |  |
| Red Team Engage for Level Zero | Create Operation for Level 0 Attack Path | [Create Mythic Operations ](/solo-purple-teaming/create-mythic-operations/)  |  |
| Red Team Engage for Level Zero | Generate Payload for Attack Level 0 | [Generate Payload](/solo-purple-teaming/generate-payload/)  |  |
| Red Team Engage for Level Zero | Establish Foothold in ECOIN | [Establish Foothold in ECOIN](/solo-purple-teaming/establish-foothold-in-ecoin/)  |  |
| Red Team Engage for Level Zero | Mythic Callbacks UI | [Mythic Callbacks UI](/solo-purple-teaming/mythic-callbacks-ui/)  |  |
| Red Team Engage for Level Zero | Mythic Tasking | [Mythic Tasking](/solo-purple-teaming/mythic-tasking/)  |  |
| Red Team Engage for Level Zero | Enumeration | [Enumeration](/solo-purple-teaming/enumeration/)  |  |
| Red Team Engage for Level Zero | Privilege Escalation | [Privilege Escalation](/solo-purple-teaming/privilege-escalation/)  |  |
| Lab Setup for Blue Team Activities | Setting Up the AllSafe Edge Appliance | [AllSafe Edge Device Setup](/solo-purple-teaming/allsafe-edge-device-setup/)  |  |
| Lab Setup for Blue Team Activities | Setting Up the Wazuh Server | [Setup the Wazuh Server](/solo-purple-teaming/setup-the-wazuh-server/)  |  |
| Lab Setup for Blue Team Activities | Port Forwarding to Wazuh Server | [Port Forwarding to Wazuh Server](/solo-purple-teaming/port-forwarding-to-wazuh-server/)  |  |
| Lab Setup for Blue Team Activities | Installing Wazuh Agent on Assumed Breach Host | [Install Wazuh Agent](/solo-purple-teaming/install-wazuh-agent/)  |  |
| Lab Setup for Blue Team Activities | Installing Sysmon on Assumed Breach Host | [Install Sysmon](/solo-purple-teaming/install-sysmon/)  |  |
| Lab Setup for Blue Team Activities | Configuring Wazuh for Defender and Sysmon | [Configuring Wazuh for Defender and Sysmon](/solo-purple-teaming/configuring-wazuh-for-defender-and-sysmon/)  |  |
| Lab Setup for Blue Team Activities | Sending Syslog Events From ECOIN Edge | [Sending Syslog Events From ECOIN Edge](/solo-purple-teaming/sending-syslog-events-from-ecoin-edge/)  |  |
| Detection Engineering 101 | What is Detection Engineering? | [What is Detection Engineering?](/solo-purple-teaming/what-is-detection-engineering/)  |  |
| Detection Engineering 101 | Pyramid of Pain | [Pyramid of Pain](/solo-purple-teaming/pyramid-of-pain/)  |  |
| Detection Engineering 101 | Balancing Accuracy VS Coverage | [Balancing Accuracy VS Coverage](/solo-purple-teaming/balancing-accuracy-vs-coverage/)  |  |
| Detection Engineering 101 | Balancing Telemetry VS Performance | [Balancing Telemetry VS Performance](/solo-purple-teaming/balancing-telemetry-vs-performance/)  |  |
| Blue Team Explore For Level 0 | Detection Opportunities | [Detection Opportunities](/solo-purple-teaming/detection-opportunities/)  |  |
| Blue Team Explore For Level 0 | Sysmon ProcessCreate Event ID 1 & NetworkConnect Event ID 3 | [Sysmon ProcessCreate Event ID 1 & NetworkConnect Event ID 3](/solo-purple-teaming/sysmon-processcreate-event-id-1-and-networkconnect-e/)  |  |
| Blue Team Explore For Level 0 | Sysmon ImageLoad Event ID 7 | [Sysmon ImageLoad Event ID 7](/solo-purple-teaming/sysmon-imageload-event-id-7/)  |  |
| Blue Team Explore For Level 0 | Sysmon FileCreate Event ID 11 | [Sysmon FileCreate Event ID 11](/solo-purple-teaming/sysmon-filecreate-event-id-11/)  |  |
| Blue Team Explore For Level 0 | PowerShell ScriptBlock Logging | [PowerShell ScriptBlock Logging](/solo-purple-teaming/powershell-scriptblock-logging/)  |  |
| Blue Team Trace For Level 0 | Setting Up Wazuh for Trace Activities | [Setting Up Wazuh for Trace Activities](/solo-purple-teaming/setting-up-wazuh-for-trace-activities/)  |  |
| Blue Team Trace For Level 0 | Build Custom Rule for Unsigned ImageLoads | [Build Custom Rule for Unsigned ImageLoads](/solo-purple-teaming/build-custom-rule-for-unsigned-imageloads/)  |  |
| Blue Team Trace For Level 0 | Build Custom Rule for Network Connect to Common C2 Port | [Build Custom Rule for Network Connect to Common C2 Port](/solo-purple-teaming/build-custom-rule-for-network-connect-to-common-c2/)  |  |
| Blue Team Trace For Level 0 | Build Custom Correlation Rule | [Build Custom Correlation Rule](/solo-purple-teaming/build-custom-correlation-rule/)  |  |
| Blue Team Trace For Level 0 | Building Advanced Correlation Engine - Part 1 | [Building Advanced Correlation Engine - Part 1](/solo-purple-teaming/building-advanced-correlation-engine-part-1/)  |  |
| Blue Team Trace For Level 0 | Building Advanced Correlation Engine - Part 2 | [Building Advanced Correlation Engine - Part 2](/solo-purple-teaming/building-advanced-correlation-engine-part-2/)  |  |
| Blue Team Trace For Level 0 | Building Advanced Correlation Engine - Part 3 | [Building Advanced Correlation Engine - Part 3](/solo-purple-teaming/building-advanced-correlation-engine-part-3/)  |  |
| Blue Team Trace For Level 0 | Building Advanced Correlation Engine - Part 4 | [Building Advanced Correlation Engine - Part 4](/solo-purple-teaming/building-advanced-correlation-engine-part-4/)  |  |
| Blue Team Trace For Level 0 | Hunting For PowerShell IOCs and Building Detections | [Hunting For PowerShell IOCs and Building Detections](/solo-purple-teaming/hunting-for-powershell-iocs-and-building-detection/)  |  |
| Blue Team Trace For Level 0 | Creating Custom Rule for PowerShell Reflection | [Creating Custom Rule for PowerShell Reflection](/solo-purple-teaming/creating-custom-rule-for-powershell-reflection/)  |  |
| Blue Team Trace For Level 0 | Improving Solo Purple Teaming Detection Dashboard | [Improving Solo Purple Teaming Detection Dashboard](/solo-purple-teaming/improving-solo-purple-teaming-detection-dashboard/)  |  |
| Red Team Re-Engage For Level 0 | Red Team Re-Engage For Level - Testing All Detections | [Red Team Re-Engage For Level - Testing All Detections](/solo-purple-teaming/red-team-re-engage-for-level-testing-all-detecti/)  |  |
| Red Team Layout For Level 1  | Red Team Layout - Using Threat Intel to Formulate Next Attack Path | [Red Team Layout - Using Threat Intel to Formulate Next Attack Path](/solo-purple-teaming/red-team-layout-using-threat-intel-to-formulate/)  |  |
| Red Team Layout For Level 1  | Attack Path Level 1 | [Attack Path Level 1](/solo-purple-teaming/attack-path-level-1/)  |  |
| Red Team Layout For Level 1  | Using Run Box to Bypass UnSigned ImageLoads From Downloads | [Using Run Box to Bypass UnSigned ImageLoads From Downloads](/solo-purple-teaming/using-run-box-to-bypass-unsigned-imageloads-from-d/)  |  |
| Red Team Layout For Level 1  | Red Team Layout - Using Application Domain to Bypass Reflection Detection | [Red Team Layout - Using Application Domain to Bypass Reflection Detection](/solo-purple-teaming/red-team-layout-using-application-domain-to-bypa/)  |  |
| Red Team Layout For Level 1  | Red Team Layout - Writing A Service Enumeration Tool - Part 1 | [Red Team Layout - Writing A Service Enumeration Tool - Part 1](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to/)  |  |
| Red Team Layout For Level 1  | Red Team Layout - Writing A Service Enumeration Tool - Part 2 | [Red Team Layout - Writing A Service Enumeration Tool - Part 2](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-4/)  |  |
| Red Team Layout For Level 1  | Red Team Layout - Writing A Service Enumeration Tool - Part 3 | [Red Team Layout - Writing A Service Enumeration Tool - Part 3](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-3/)  |  |
| Red Team Layout For Level 1  | Red Team Layout - Writing A Service Enumeration Tool - Part 4 | [Red Team Layout - Writing A Service Enumeration Tool - Part 4](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-2/)  |  |
| Red Team Layout For Level 1  | Red Team Layout - Testing Service Enumeration Bypass | [Red Team Layout - Testing Service Enumeration Bypass](/solo-purple-teaming/red-team-layout-testing-service-enumeration-bypa/)  |  |
| Attack Path Level 1 Lab Setup | Adding sec_ecoin_sync to Domain Admins | [Adding sec_ecoin_sync to Domain Admins](/solo-purple-teaming/adding-sec-ecoin-sync-to-domain-admins/)  |  |
| Red Team Engage For Level 1 | Red Team Engage - Initial Access | [Red Team Engage - Initial Access](/solo-purple-teaming/red-team-engage-initial-access/)  |  |
| Red Team Engage For Level 1 | Red Team Engage - Privilege Escalation | [Red Team Engage - Privilege Escalation](/solo-purple-teaming/red-team-engage-privilege-escalation/)  |  |
| Red Team Engage For Level 1 | Red Team Engage - Lateral Movement | [Red Team Engage - Lateral Movement](/solo-purple-teaming/red-team-engage-lateral-movement/)  |  |
| Blue Team Explore For Level 1 | Blue Team Explore - What to Expect | [Blue Team Explore - What to Expect](/solo-purple-teaming/blue-team-explore-what-to-expect/)  |  |
| Blue Team Explore For Level 1 | Blue Team Explore - Learning About execute_assembly, Process Injection, and IPC | [Blue Team Explore - Learning About execute_assembly, Process Injection, and IPC](/solo-purple-teaming/blue-team-explore-learning-about-execute-assembl/)  |  |
| Blue Team Explore For Level 1 | Blue Team Explore - Enabling Sysmon Event IDs 10, 17 & 18 | [Blue Team Explore - Enabling Sysmon Event IDs 10, 17 & 18](/solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-10/)  |  |
| Blue Team Explore For Level 1 | Blue Team Explore: Hunting For Indications of execute_assembly | [Blue Team Explore: Hunting For Indications of execute_assembly](/solo-purple-teaming/blue-team-explore-hunting-for-indications-of-execu/)  |  |
| Blue Team Explore For Level 1 | Blue Team Explore - Enabling Sysmon Event IDs 12, 13 & 14 | [Blue Team Explore - Enabling Sysmon Event IDs 12, 13 & 14](/solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-12/)  |  |
| Blue Team Explore For Level 1 | Blue Team Explore  - Hunting For Initial Access Execution | [Blue Team Explore  - Hunting For Initial Access Execution](/solo-purple-teaming/blue-team-explore-hunting-for-initial-access-exe/)  |  |
| Blue Team Trace For Level 1 | Blue Team Trace - Reversing Execute Assembly and Customizing Mythic | [Blue Team Trace - Reversing Execute Assembly and Customizing Mythic](/solo-purple-teaming/reversing-execute-assembly-and-customizing-mythic/) |  |
| Blue Team Trace For Level 1  | Blue Team Trace - Extracting Payload | [Walkthrough: Extracting Payload](/solo-purple-teaming/walkthrough-extracting-payload/) |  |
| Blue Team Trace For Level 1  | Blue Team Trace - Basic Malware Analysis | [Walkthrough: Basic Malware Analysis](/solo-purple-teaming/walkthrough-basic-malware-analysis/) |  |
| Blue Team Trace For Level 1  | Build Detections For IWR and RunMRU | [Building Detections for IWR and RunMRU](/solo-purple-teaming/building-detections-for-iwr-and-runmru/) |  |
| Final Thoughts | Final Thoughts | [Final Thoughts](/solo-purple-teaming/final-thoughts/) |  |

[Walkthrough: What is Solo Purple Teaming?](/solo-purple-teaming/walkthrough-what-is-solo-purple-teaming/)

[Supplement: Scenario 1](/solo-purple-teaming/supplement-scenario-1/)

[Suplement: Initial Lab Setup](/solo-purple-teaming/suplement-initial-lab-setup/)

[Supplement: Choosing C2 Framework](/solo-purple-teaming/supplement-choosing-c2-framework/)

[Walkthrough: PowerShell Reverse Shell Without Defender](/solo-purple-teaming/walkthrough-powershell-reverse-shell-without-defen/)

[Supplement: Determining Root Cause](/solo-purple-teaming/supplement-determining-root-cause/)

[Supplement: What is AMSI?](/solo-purple-teaming/supplement-what-is-amsi/)

[When Patching Does Not Go As Planned](/solo-purple-teaming/when-patching-does-not-go-as-planned/)

[Managed VS Unmanaged](/solo-purple-teaming/managed-vs-unmanaged/)

[Installing Mythic C2](/solo-purple-teaming/installing-mythic-c2/)

[Installing Apollo](/solo-purple-teaming/installing-apollo/)

[Introduction to Reflection](/solo-purple-teaming/introduction-to-reflection/)

[Introduction to AV](/solo-purple-teaming/introduction-to-av/)

[Bypassing Signature-Based Detections](/solo-purple-teaming/bypassing-signature-based-detections/)

[Moving Tuned Apollo Agent to Mythic Server](/solo-purple-teaming/moving-tuned-apollo-agent-to-mythic-server/)

[Attack Path Level 0 Lab Setup](/solo-purple-teaming/attack-path-level-0-lab-setup/)

[Setting Up Edge Appliances](/solo-purple-teaming/setting-up-edge-appliances/)

[Moving Kali To Attack LAN](/solo-purple-teaming/moving-kali-to-attack-lan/)

[Configure Firewall Rules](/solo-purple-teaming/configure-firewall-rules/)

[Setting Up Assumed Breach](/solo-purple-teaming/setting-up-assumed-breach/)

[Testing Apollo on Assumed Breach Host](/solo-purple-teaming/testing-apollo-on-assumed-breach-host/)

[Setting Up ECORP Edge Appliance](/solo-purple-teaming/setting-up-ecorp-edge-appliance/)

[**Building the E Corp Domain Controller**](/solo-purple-teaming/building-the-e-corp-domain-controller/)

[Joining Assumed Breach Host to ECOIN Domain](/solo-purple-teaming/joining-assumed-breach-host-to-ecoin-domain/)

[Create Vulnerable Service Binary](/solo-purple-teaming/create-vulnerable-service-binary/)

[**Level Zero Attack Path Review**](/solo-purple-teaming/level-zero-attack-path-review/)

[Introduction to Mythic UI Basics](/solo-purple-teaming/introduction-to-mythic-ui-basics/)

[Mythic User Settings](/solo-purple-teaming/mythic-user-settings/)

[Create Mythic Operations ](/solo-purple-teaming/create-mythic-operations/)

[Generate Payload](/solo-purple-teaming/generate-payload/)

[Establish Foothold in ECOIN](/solo-purple-teaming/establish-foothold-in-ecoin/)

[Mythic Callbacks UI](/solo-purple-teaming/mythic-callbacks-ui/)

[Mythic Tasking](/solo-purple-teaming/mythic-tasking/)

[Enumeration](/solo-purple-teaming/enumeration/)

[Privilege Escalation](/solo-purple-teaming/privilege-escalation/)

[AllSafe Edge Device Setup](/solo-purple-teaming/allsafe-edge-device-setup/)

[Setup the Wazuh Server](/solo-purple-teaming/setup-the-wazuh-server/)

[Port Forwarding to Wazuh Server](/solo-purple-teaming/port-forwarding-to-wazuh-server/)

[Install Wazuh Agent](/solo-purple-teaming/install-wazuh-agent/)

[Install Sysmon](/solo-purple-teaming/install-sysmon/)

[Configuring Wazuh for Defender and Sysmon](/solo-purple-teaming/configuring-wazuh-for-defender-and-sysmon/)

[Sending Syslog Events From ECOIN Edge](/solo-purple-teaming/sending-syslog-events-from-ecoin-edge/)

[What is Detection Engineering?](/solo-purple-teaming/what-is-detection-engineering/)

[Pyramid of Pain](/solo-purple-teaming/pyramid-of-pain/)

[Balancing Accuracy VS Coverage](/solo-purple-teaming/balancing-accuracy-vs-coverage/)

[Balancing Telemetry VS Performance](/solo-purple-teaming/balancing-telemetry-vs-performance/)

[Detection Opportunities](/solo-purple-teaming/detection-opportunities/)

[Sysmon ProcessCreate Event ID 1 & NetworkConnect Event ID 3](/solo-purple-teaming/sysmon-processcreate-event-id-1-and-networkconnect-e/)

[Sysmon ImageLoad Event ID 7](/solo-purple-teaming/sysmon-imageload-event-id-7/)

[Sysmon FileCreate Event ID 11](/solo-purple-teaming/sysmon-filecreate-event-id-11/)

[PowerShell ScriptBlock Logging](/solo-purple-teaming/powershell-scriptblock-logging/)

[Setting Up Wazuh for Trace Activities](/solo-purple-teaming/setting-up-wazuh-for-trace-activities/)

[Build Custom Rule for Unsigned ImageLoads](/solo-purple-teaming/build-custom-rule-for-unsigned-imageloads/)

[Build Custom Rule for Network Connect to Common C2 Port](/solo-purple-teaming/build-custom-rule-for-network-connect-to-common-c2/)

[Build Custom Correlation Rule](/solo-purple-teaming/build-custom-correlation-rule/)

[Building Advanced Correlation Engine - Part 1](/solo-purple-teaming/building-advanced-correlation-engine-part-1/)

[Building Advanced Correlation Engine - Part 2](/solo-purple-teaming/building-advanced-correlation-engine-part-2/)

[Building Advanced Correlation Engine - Part 3](/solo-purple-teaming/building-advanced-correlation-engine-part-3/)

[Building Advanced Correlation Engine - Part 4](/solo-purple-teaming/building-advanced-correlation-engine-part-4/)

[Red Team Layout - Testing Service Enumeration Bypass](/solo-purple-teaming/red-team-layout-testing-service-enumeration-bypa/)

[Creating Custom Rule for PowerShell Reflection](/solo-purple-teaming/creating-custom-rule-for-powershell-reflection/)

[Improving Solo Purple Teaming Detection Dashboard](/solo-purple-teaming/improving-solo-purple-teaming-detection-dashboard/)

[Red Team Re-Engage For Level - Testing All Detections](/solo-purple-teaming/red-team-re-engage-for-level-testing-all-detecti/)

[Red Team Layout - Using Threat Intel to Formulate Next Attack Path](/solo-purple-teaming/red-team-layout-using-threat-intel-to-formulate/)

[Attack Path Level 1](/solo-purple-teaming/attack-path-level-1/)

[Using Run Box to Bypass UnSigned ImageLoads From Downloads](/solo-purple-teaming/using-run-box-to-bypass-unsigned-imageloads-from-d/)

[Red Team Layout - Using Application Domain to Bypass Reflection Detection](/solo-purple-teaming/red-team-layout-using-application-domain-to-bypa/)

[Red Team Layout - Writing A Service Enumeration Tool - Part 1](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to/)

[Red Team Layout - Writing A Service Enumeration Tool - Part 2](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-4/)

[Red Team Layout - Writing A Service Enumeration Tool - Part 3](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-3/)

[Red Team Layout - Writing A Service Enumeration Tool - Part 4](/solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-2/)

[Adding sec_ecoin_sync to Domain Admins](/solo-purple-teaming/adding-sec-ecoin-sync-to-domain-admins/)

[Red Team Engage - Initial Access](/solo-purple-teaming/red-team-engage-initial-access/)

[Red Team Engage - Privilege Escalation](/solo-purple-teaming/red-team-engage-privilege-escalation/)

[Red Team Engage - Lateral Movement](/solo-purple-teaming/red-team-engage-lateral-movement/)

[Blue Team Explore - What to Expect](/solo-purple-teaming/blue-team-explore-what-to-expect/)

[Blue Team Explore - Learning About execute_assembly, Process Injection, and IPC](/solo-purple-teaming/blue-team-explore-learning-about-execute-assembl/)

[Blue Team Explore - Enabling Sysmon Event IDs 10, 17 & 18](/solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-10/)

[Blue Team Explore - Enabling Sysmon Event IDs 12, 13 & 14](/solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-12/)

[Blue Team Explore  - Hunting For Initial Access Execution](/solo-purple-teaming/blue-team-explore-hunting-for-initial-access-exe/)

[Blue Team Explore: Hunting For Indications of execute_assembly](/solo-purple-teaming/blue-team-explore-hunting-for-indications-of-execu/)

[A Warning and a Challenge](/solo-purple-teaming/a-warning-and-a-challenge/)

[Hunting For PowerShell IOCs and Building Detections](/solo-purple-teaming/hunting-for-powershell-iocs-and-building-detection/)

[Prepping the Apollo Agent](/solo-purple-teaming/prepping-the-apollo-agent/)

[Full AMSI Bypass](/solo-purple-teaming/full-amsi-bypass/)

[Walkthrough: Bypassing AMSI Using x64dbg](/solo-purple-teaming/walkthrough-bypassing-amsi-using-x64dbg/)

[Walkthrough: Basic Malware Analysis](/solo-purple-teaming/walkthrough-basic-malware-analysis/)

[Walkthrough: Extracting Payload](/solo-purple-teaming/walkthrough-extracting-payload/)

[Final Thoughts](/solo-purple-teaming/final-thoughts/)

[Building Detections for IWR and RunMRU](/solo-purple-teaming/building-detections-for-iwr-and-runmru/)

[Reversing Execute Assembly and Customizing Mythic](/solo-purple-teaming/reversing-execute-assembly-and-customizing-mythic/)

</section>
</div>
