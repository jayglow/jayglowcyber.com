---
layout: default
title: "Detection Opportunities"
permalink: /solo-purple-teaming/detection-opportunities/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Detection Opportunities</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### 1. **Purpose of the Lecture**

- This session is **foundational** for becoming highly effective at **red teaming** or **blue teaming**.
- The concepts here help you excel in:
    - **Offense** – as a Red Team Operator.
    - **Defense** – as a Detection Engineer.
- In **solo purple teaming**, you must operate **self-sufficiently** without relying on a separate team to share intelligence.

---

### 2. **The Solo Purple Teaming Context**

- Since you control the simulation:
    - You **know the attack path**.
    - You know **every command, payload, and technique** used.
- Tools like **Mythic** store **full task history**, letting you:
    - Retrace each action.
    - Compare it against collected telemetry.
- **Primary task**: Identify **telemetry gaps** — where visibility breaks down.

---

### 3. **Telemetry Verification Process**

- **Check native Windows Event Logs**:
    - May require **enabling extra audit settings**.
- **Review Sysmon configuration**:
    - Ensure it is tuned for **events you care about**.
- **Evaluate EDR logs**:
    - Verify if events are recorded and easily searchable.
- **When in doubt**:
    - Research what events a technique *should* produce.
    - Compare with what is actually being collected.
- **Cycle for improvement**:
    1. Simulate
    2. Verify
    3. Adjust

---

### 4. **Initial Access – Attack Path Level Zero**

**Scenario:** C2 payload delivered as **unsigned executable** downloaded from the internet.

### Key Telemetry Indicators

- **Mark of the Web (MOTW)** present (downloaded from internet).
- **Unsigned binary** execution.
- **Outbound HTTP connections** to C2 infrastructure.

### Relevant Sysmon Event IDs

1. **Event ID 7 – Image Load**
    - Tracks unsigned executables loading from **user directories** (e.g., Downloads).
    - Combine with file reputation services to reduce false positives.
2. **Event ID 15 – File Create Stream Hash**
    - Detects first write of a file from the internet.
    - Strong indicator of malicious download.
3. **Event ID 3 – Network Connect**
    - Shows outbound network activity.
    - Detects suspicious HTTP connections to **non-standard destinations**.

---

### 5. **Situational Awareness (Post-Compromise Reconnaissance)**

- **Attacker’s Goals**:
    - Identify **host** and **user privileges**.
    - Discover other users.
    - Check for exploitable software.
    - Plan **lateral movement**.
- **Example Recon Commands**:
    - `whoami`
    - `hostname`
    - `net user`
    - `tasklist`

### Defensive Detection

- **Sysmon Event ID 1 – Process Creation**
    - Captures **full command-line arguments**.
    - Reveals reconnaissance and enumeration tools.
    - Crucial for spotting **post-compromise behavior**.

---

### 6. **Understanding Sysmon**

- **Definition**: System Monitor from **Microsoft Sysinternals**.
- **Role**:
    - Provides **high-fidelity event data** to Windows Event Log.
    - Runs as a **Windows service** with a **driver** for low-level monitoring.
    - Operates as a **Protected Process Light (PPL)** for tamper resistance.
- **Limitations**:
    - Not an EDR (no real-time blocking).
- **Strengths**:
    - Free, actively maintained, highly configurable.
    - Ideal for **threat hunting**, **detection engineering**, and **incident response**.

---

### 7. **Sysmon Capabilities Useful for Detection**

- **Process Creation Events**:
    - Includes **parent process** for execution chain analysis.
- **File Hashing**:
    - SHA1, MD5, SHA256, and imphash.
- **Unique Process GUIDs**:
    - Allows correlation even if PID is reused.
- **Session GUIDs**:
    - Tracks actions in the same user session.
- **File & Disk Events**:
    - Detect creation, timestamp tampering.
- **Network Connection Logging**:
    - Captures IPs, hostnames, ports, originating process.
- **Dynamic Rule Filtering**:
    - Include/exclude events as needed.
- **Auto-Reload Config**:
    - Quick iteration during tuning.
- **Early Boot Data Capture**:
    - Detects low-level threats.

---

### 8. **Next Steps in the Lab**

- Begin reviewing **each Sysmon Event ID** required for **high-fidelity detections**.
- Implement or enable these events in the **lab environment**.
- Validate that all critical telemetry is being collected for **Attack Path Level Zero**.

---

✅ **Outcome for Students**: By the end of this exercise, you’ll know exactly **what telemetry to expect** for specific attack stages, how to **verify its presence**, and how to **tune Sysmon** to close visibility gaps.

</section>
</div>
