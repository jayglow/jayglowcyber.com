---
layout: default
title: "Attack Path Level 1"
permalink: /solo-purple-teaming/attack-path-level-1/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Attack Path Level 1</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Overview**

Attack Path Level 1 builds directly on the detections and defenses you created during Level 0.

The objective here isn’t just to “break in again” — it’s to **bypass** those very defenses, sharpen adversarial thinking, and improve both offensive and defensive skills through iterative testing.

---

### **1. Initial Access – ClickFix Technique**

- **Purpose**: Test whether you can still gain execution despite previous detections for suspicious image loads.
- **Method**:
    - Use the **Windows Run dialog (`Win + R`)** to launch payloads.
    - This “ClickFix” technique is subtle and avoids some process creation traces seen in normal execution paths.
- **Challenge**:
    - Bypass both **Windows Defender** and your **custom blue team rules** for unsigned binaries in `\Temp` and `\Users` directories.
- **New Twist**:
    - Develop evasions for **reflection-based assembly loading** to directly target your earlier PowerShell reflection detection rules.

---

### **2. Enumeration – Advanced Recon**

- **Purpose**: See if your updated enumeration tactics can slip past the recon detections from Level 0.
- **Method**:
    - Perform host and network reconnaissance with adjusted commands, obfuscation, or timing.
- **Blue Team Test**:
    - Validate if your detections are catching behavioral patterns or just specific commands.
- **Goal**: Force adaptation — your enumeration must evade previous detection logic while still collecting useful intel.

---

### **3. Privilege Escalation – Modifiable Service Binary**

- **Purpose**: Go beyond just privilege escalation — bypass detections that flag service enumeration and abuse.
- **Method**:
    - Identify a service binary you can modify (replace or alter to execute your payload).
    - Avoid triggering service-related detection rules from Level 0.
- **Key Learning**:
    - This tests whether your detections are robust against *slightly altered tactics* or if they rely too heavily on exact matches.

---

### **4. Lateral Movement – Targeting the Domain Controller**

- **Purpose**: Take on a **high-value objective** while maintaining stealth.
- **Method**:
    - Attempt to pivot and access the **Domain Controller (DC)**.
    - Use lateral movement techniques that differ from Level 0 attempts.
- **Blue Team Test**:
    - Measure whether any traces are logged and caught by your rules.
    - Success here means achieving stealth against *your own tuned detections*.

---

### **5. Mindset & Takeaways**

- **Iterative Learning**: Each red team action feeds back into improving blue team logic, and vice versa.
- **Bypass Is Not the Goal Alone**: The real value lies in identifying gaps, understanding detection evasion, and preparing for **real-world adversaries**.
- **Continuous Improvement**: Solo purple teaming is a cycle — every level increases complexity, and every bypass teaches you how to close the gap.

---

**Next Step:**

In the following lab, you’ll start the **red team layout** for Attack Path Level 1, focusing first on using the **Run box technique** to bypass unsigned image load detections from the `Downloads` folder.

</section>
</div>
