---
layout: default
title: "Pyramid of Pain"
permalink: /solo-purple-teaming/pyramid-of-pain/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Pyramid of Pain</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Overview**

In this session, we explore the **Pyramid of Pain** — a strategic model developed by **David Bianco** to help security professionals understand which indicators of compromise (IOCs) are most valuable for defenders and most difficult for attackers to change.

![image.png](Pyramid%20of%20Pain/image.png)

The pyramid organizes IOCs from **easiest to hardest for adversaries to alter**, and from **least to most valuable for defenders**.

---

## **1. Base of the Pyramid – Hash Values**

- **Definition**: Unique fingerprints of files, often generated via hashing algorithms like MD5, SHA1, or SHA256.
- **Detection**:
    - Commonly used in **Antivirus**, **EDR tools**, and **YARA scans**.
    - Effective for identifying **known malware**.
- **Attacker Evasion**:
    - Extremely easy for attackers to bypass.
    - A **single byte change** in a file generates a **completely new hash**.
- **Defensive Value**: Low against skilled adversaries; useful for known threats but not resilient.

---

## **2. Second Level – IP Addresses**

- **Role in Detection**:
    - Used to block or monitor connections to **malicious infrastructure**.
    - Often appears in threat intelligence feeds.
- **Attacker Evasion**:
    - Attackers can rotate IPs quickly using:
        - VPS hosting
        - Bulletproof hosting services
        - Compromised legitimate servers
- **Defensive Value**: Moderate short-term effectiveness, but poor for long-term blocking due to rapid IP changes.

---

## **3. Third Level – Domains**

- **Importance**:
    - Central to **phishing campaigns**, **malware delivery**, and **C2 communications**.
- **Attacker Evasion**:
    - Can register new domains easily, but this costs **time** and **money**.
- **Defensive Enhancements**:
    - **Domain age checks** – flag newly registered sites.
    - **Registrar and hosting provider analysis** – identify suspicious service usage.
    - **Traffic/Ranking analysis** – separate legitimate domains from those created for attacks.
- **Defensive Value**: Higher than IPs due to added cost/delay for attackers to replace.

---

## **4. Fourth Level – Network Host Artifacts**

- **Definition**: Evidence left behind on a compromised system such as:
    - File paths
    - Registry keys
    - Mutex names
- **Attacker Evasion**:
    - Changing these may **break malware functionality**.
- **Defensive Techniques**:
    - **Deep malware analysis** required.
    - Monitor for common paths, registry entries, or artifacts tied to specific tools.
- **Impact**: Detecting these disrupts attacker operations significantly.

---

## **5. Fifth Level – Tools**

- **Description**:
    - Software adversaries use for exploitation, persistence, or data exfiltration.
    - Can be **open-source** or **custom-built**.
- **Attacker Evasion**:
    - Replacing tools means extra **development time**, **cost**, and **risk**.
- **Defensive Advantage**:
    - Early detection of known tools can signal sophisticated intrusions.
    - Forces attackers to abandon or rebuild preferred toolkits.

---

## **6. Top of the Pyramid – Tactics, Techniques, and Procedures (TTPs)**

- **Definition**:
    - **Tactics** – the high-level objectives (e.g., persistence, privilege escalation).
    - **Techniques** – the general methods (e.g., credential dumping).
    - **Procedures** – the specific implementations.
- **Attacker Evasion**:
    - Changing TTPs often requires **retraining teams** and **redesigning attack playbooks**.
- **Defensive Value**:
    - Highest in the pyramid.
    - Detection at this level causes **maximum disruption** and **long-term impact**.
    - More resilient than indicators tied to specific infrastructure or files.

---

## **Key Takeaways**

- The **higher** you detect on the pyramid, the **more pain** you inflict on adversaries.
- Lower levels (hashes, IPs) are quick to change — good for short-term but weak in persistence.
- Higher levels (artifacts, tools, TTPs) are **hard to change** and **disrupt attacker operations** more effectively.
- Effective defenders combine **multiple levels** for balanced detection.

---

## **Next Steps**

In the next session, we’ll cover **balancing accuracy vs. coverage** — understanding how to build detection strategies that avoid excessive false positives while maintaining broad visibility.

</section>
</div>
