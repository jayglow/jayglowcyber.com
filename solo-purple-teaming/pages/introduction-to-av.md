---
layout: default
title: "Introduction to AV"
permalink: /solo-purple-teaming/introduction-to-av/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Introduction to AV</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# Understanding Antivirus Software (Before Bypassing Windows Defender with Apollo)

## 🎯 Lecture Objectives

By the end of this walkthrough, you will:

- Understand how modern antivirus (AV) software functions
- Learn the differences between detection techniques (signature, heuristic, behavior-based)
- Recognize the strengths and limitations of AV
- Prepare for bypass techniques demonstrated in upcoming sessions

---

## 🛡️ What Is Antivirus Software?

Antivirus (AV) software is designed to **detect, prevent, and remove malicious software (malware)**. But it’s more than just a basic scanner.

AV solutions operate on multiple layers:

- **File system monitoring** for infected or suspicious files
- **Memory monitoring** for runtime anomalies
- **Network traffic inspection** to catch threats before arrival

Think of AV as the gatekeeper — protecting the operating system from attackers trying to gain a foothold.

---

## 🔧 Core Functions of Antivirus

1. **File Scanning**
    - On-demand or scheduled scans for known threats
2. **Real-Time Monitoring**
    - Actively scans as events happen (files opened, apps executed)
3. **Malware Detection & Removal**
    - Identifies, quarantines, and neutralizes threats
4. **Quarantine**
    - Isolates suspicious files for further analysis
5. **Automatic Updates**
    - Regularly pulls latest threat intel to remain effective

---

## 🧬 Signature-Based Detection

- **What it is**: Scans files against a **database of known malware "signatures"** — unique identifiers of malicious code.
- **Strengths**:
    - Fast and accurate for **known threats**
- **Weaknesses**:
    - **Useless against unknown or polymorphic malware**
    - Requires **frequent updates**

📌 **Polymorphic Malware**: Code that changes its structure to evade signature detection.

---

## 🔍 Heuristic-Based Detection

- **Goes beyond signatures** — looks for **suspicious characteristics** in code.
- Uses **rules and scoring systems** to evaluate potential threats.

### Two Main Techniques:

- **Static Analysis**: Inspects code without executing it.
- **Dynamic Analysis**: Executes code in a **sandbox** to observe behavior.

🟡 **Downside**: Can result in **false positives** (legitimate software flagged).

---

## ⏱️ Real-Time Protection

- Hooks directly into the operating system to monitor:
    - File access
    - Process execution
    - Registry changes

🔒 Especially valuable for **zero-day threats** — attacks with **no known signature**.

Real-time protection:

- **Stops threats at execution**
- **Blocks malicious behavior immediately**

---

## ☁️ Cloud-Based Scanning

- Sends files or behaviors to **cloud-based threat engines** for deeper analysis.

### Benefits:

1. **Speed & accuracy** — faster scanning, better decision-making
2. **Global telemetry** — learns from attacks around the world
3. **Reputation scoring** — checks how common or suspicious a file is

Example: If a file is rare, unsigned, and downloaded from a sketchy site, it may be flagged as high risk.

---

## 🧠 Behavior-Based Detection

- Focuses on **what a program does**, not how it looks.
- Catches:
    - **Code injection**
    - **Memory manipulation**
    - **Process hollowing**
    - **Privilege escalation**

💡 This technique powers **modern EDR (Endpoint Detection & Response)** tools.

✅ **Great at detecting obfuscated or zero-day malware.**

---

## ⚠️ Limitations of Antivirus Software

1. **It’s not foolproof**
    - Targeted or custom-built malware can evade detection
2. **Performance hits**
    - May slow down system during scans or heavy monitoring
3. **False positives**
    - Legitimate apps flagged as malware
4. **False negatives**
    - Real malware goes undetected

🛡️ **Best Practice**: AV should be just **one layer** in a broader **Defense-in-Depth** strategy, alongside:

- Endpoint protection
- Network segmentation
- User training

---

## 🔄 What’s Next?

In the next lecture:

- You'll learn how to **use ThreatCheck to bypass Windows Defender’s signature-based detections**.
- This hands-on demo illustrates **how attackers avoid AV detection**.

➡️ **Future Lectures**: Focus will shift to **defensive techniques**, including:

- Mitigating AV weaknesses
- Building multi-layered defenses
- Leveraging defense-in-depth strategies for modern threat landscapes

---

## 🧭 Recap

| Topic | Summary |
| --- | --- |
| AV Basics | Detects, prevents, removes malware via multiple layers |
| Signature Detection | Fast but weak against unknown threats |
| Heuristic Detection | Smarter, but risk of false positives |
| Real-Time Protection | Hooks OS functions to block threats instantly |
| Cloud-Based Scanning | Adds speed, reputation, and global intel |
| Behavior-Based Detection | Identifies malware by actions, not appearance |
| AV Limitations | Can be bypassed, needs layered security to be effective |

</section>
</div>
