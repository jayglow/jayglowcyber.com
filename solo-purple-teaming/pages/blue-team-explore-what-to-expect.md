---
layout: default
title: "Blue Team Explore - What to Expect"
permalink: /solo-purple-teaming/blue-team-explore-what-to-expect/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - What to Expect</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **1. Purpose of the Explore Phase**

The **Blue Team Explore Phase** focuses on researching the **specific TTPs** (Tactics, Techniques, and Procedures) you encountered during the **Engage Phase**. Your job here is to:

- Understand the techniques used in your **attack path**.
- Learn how they typically operate in real-world attacks.
- Identify how they are detected in live environments.

---

### **2. Step-by-Step Process**

### **Step 1 – Start with the Techniques Used**

- Review the exact TTPs from your **Engage Phase**.
- Ask yourself:
    - *What are these techniques?*
    - *How do they work in practice?*
    - *What does a real-world detection look like for these techniques?*

---

### **Step 2 – Research from Credible Sources**

- **Threat Hunting Blogs** – Look for posts that break down detection logic.
- **Detection Rule Repositories** – Explore Sigma rules, Splunk queries, or YARA rules related to the TTPs.
- **Incident Response Write-ups** – Real-world cases show both attacker behaviors and defender responses.
- **Offensive Security Blogs** – While attacker-focused, they often reveal evasion strategies you need to know about.

---

### **Step 3 – Examine Your Existing Security Tools**

- Check the **documentation** for your SIEM, EDR, Sysmon config, or other monitoring tools.
- Confirm they **generate telemetry** relevant to your researched TTPs.
    - Example: If the TTP involves **process injection**, verify if your tools log **Sysmon Event ID 10**.
- If your telemetry is missing something, note the gap.

---

### **Step 4 – Identify and Document Visibility Gaps**

- If something isn’t being logged or detected:
    - Record the gap in your notes.
    - Consider **what extra tools, logging configurations, or data sources** could close it.

---

### **3. Course Simplification**

In this training, the Explore Phase has been **streamlined**:

- You’ll still **research TTPs** from your Engage Phase.
- You’ll still **check your telemetry stack** for coverage.
- **Omitted for brevity:**
    - Deep dives into every detection tool.
    - Full threat hunting walk-throughs.

*These omitted steps are for you to practice later on your own.*

---

### **4. Goal of This Phase**

- Build the **mindset and workflow** for TTP research.
- Avoid being overwhelmed with too much detail at once.
- Gain a **solid foundation** to expand upon in real-world or advanced lab scenarios.

---

### **5. Next Step**

- Begin your **Blue Team Explore Phase** research now.
- Document each TTP, its detection methods, and any visibility gaps you find.

</section>
</div>
