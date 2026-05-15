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

Owner: Mike Sterrett

### **Overview**

This walkthrough guides you through the initial setup for the *Attack Path Level Zero* lab environment. This lab is designed specifically for **solo purple teaming**, a methodology where a single individual plays both the attacker and defender to gain a complete understanding of offensive tactics and defensive detection.

By the end of this walkthrough, you’ll understand:

- The structure and purpose of the lab
- The systems involved
- The role of each network segment
- What comes next in the setup process

---

![image.png](Attack%20Path%20Level%200%20Lab%20Setup/image.png)

## **1. Purpose of the Lab**

This lab simulates a small but realistic enterprise environment. The goal is to allow you to:

- Develop and test attack payloads
- Deploy and manage implants
- Conduct post-compromise operations
- Create and tune detection mechanisms in a controlled and segmented network

This setup is ideal for:

- Practicing red and blue team skills
- Developing detection strategies
- Understanding the full attack kill chain

---

## **2. Lab Architecture**

The lab environment consists of **two isolated LANs**:

### **A. Attack LAN**

This network represents the attacker’s infrastructure and includes:

- **Reverse Engineering Workstation**
    - Purpose: Used to **develop and obfuscate custom payloads**
- **Kali Linux System**
    - Purpose: Hosts the **Mythic Command and Control (C2)** framework
    - Role: Enables **implant management** and **post-exploitation activities**

These two machines are already provisioned but must be moved to the appropriate virtual or physical network segment to be effective.

### **B. Target LAN (ECOIN)**

This network simulates a typical victim environment and currently includes:

- **One Windows 11 Host**
    - Role: Assumed **breach point** for the attacker
    - Purpose: Allows simulation of:
        - Privilege escalation
        - Credential harvesting
        - Lateral movement
        - Persistence mechanisms

Each LAN is **protected by a pfSense firewall**, which serves as the **edge device** and simulates perimeter defenses common in enterprise networks.

---

## **3. Future Expansion: The ECOIN Enterprise**

The lab will evolve into a more complex simulated environment:

- **Ecoin** will represent a growing enterprise network
- It will become a **subdomain of Ecorp**, supporting advanced attack paths such as:
    - Active Directory trust abuse
    - Multi-domain lateral movement
    - Advanced red/blue team scenarios

This future modularity supports realistic simulations and structured learning paths as you progress through the purple teaming program.

---

## **4. Solo Purple Teaming Benefits**

This lab setup is purpose-built for solo practitioners, allowing you to:

- Practice **both offensive and defensive techniques**
- Simulate **realistic enterprise conditions**
- Develop a **deep understanding** of tactics, techniques, and procedures (TTPs)
- Test **detections and evasion strategies** in a safe and repeatable environment

---

## **5. What’s Next**

In the next stage of the lab setup, you will:

- **Deploy the edge devices** (pfSense firewalls)
- Define the boundaries and routing between the attack and target LANs
- Control traffic flow and create a realistic network perimeter

These edge devices will:

- Simulate **real-world enterprise segmentation**
- Enable **traffic filtering and logging**
- Provide a **safe sandbox** for testing offensive and defensive actions

---

## **Summary**

This foundational setup gives you the infrastructure to:

- Launch attacks
- Analyze responses
- Build detections
- Iterate on both red and blue team capabilities

</section>
</div>
