---
layout: default
title: "Level Zero Attack Path Review"
permalink: /solo-purple-teaming/level-zero-attack-path-review/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Level Zero Attack Path Review</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **1. Understanding the Scenario**

Before diving into the technical execution, it’s critical to define the simulation parameters for both the **target environment** and the **attacker profile**.

### **Target Environment (Low Maturity)**

- **Security Awareness:** No formal user training or awareness program.
- **Telemetry:** Limited to non-existent logging and endpoint visibility.
- **Preventative Controls:** Minimal or poorly configured.
- **Patch Management:** Neglected, leaving systems vulnerable.
- **Business Priorities:** Heavy focus on availability; security is an afterthought.

**Implication:** This environment is more susceptible to basic attack techniques, making it ideal for measuring quick improvements through purple teaming.

### **Attacker Profile (Low Skill Threat Actor)**

- **TTP Knowledge:** Little to no understanding of tactics, techniques, and procedures.
- **Operational Security:** None — actions are noisy and easily detected by competent defenders.
- **Approach:** Relies on straightforward, unsophisticated attack steps.

**Opportunity for Purple Teaming:**

This simulation allows a single operator to play both roles — attacker and defender — to:

- Simulate realistic threats.
- Measure detection and prevention effectiveness.
- Identify immediate, high-value improvements.

---

## **2. Attack Path Phases**

### **Phase 1: Initial Access**

- Simulate a user downloading and executing malware on the **assumed breach host**.
- **Primary Goal:** Bypass Microsoft Defender to establish an initial foothold.

---

### **Phase 2: Enumeration**

- Conduct **situational awareness** to map the target environment.
- Identify:
    - Logged-in users.
    - Privileges and group memberships.
    - Active network connections.
    - Possible escalation paths.

---

### **Phase 3: Privilege Escalation**

- Search for **modifiable service binaries** as an escalation vector.
- Tools & Commands:
    - `sc` → List services and associated executable paths.
    - `icacls` → Check file permissions for modification rights.
- **Objective:** Replace a legitimate service binary with a malicious one to gain elevated privileges.

---

## **3. Transition to Mythic C2 Operations**

Before performing the hands-on attack, familiarize yourself with the Mythic Command and Control (C2) framework.

### **Mythic UI Preparation**

In upcoming lectures, we will:

1. **Mythic UI Basics** – Overview of the interface.
2. **User Settings** – Customizing preferences.
3. **Operation Creation** – Set up a dedicated **Level Zero Operation**.
4. **Payloads & Listeners** – Understand the relationship and configuration.
5. **Payload Generation** – Create the payload for our initial access stage.

---

## **4. Execution Plan**

Once the Mythic setup is complete:

1. Deploy the generated payload on the **assumed breach host**.
2. Gain initial access.
3. Begin enumeration and privilege escalation testing.
4. Record detection and prevention gaps for follow-up defense improvements.

</section>
</div>
