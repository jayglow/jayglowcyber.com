---
layout: default
title: "Setting Up Wazuh for Trace Activities"
permalink: /solo-purple-teaming/setting-up-wazuh-for-trace-activities/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up Wazuh for Trace Activities</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### 1. **Why Wazuh Is Used in This Course**

- **Purpose**: Wazuh is chosen because it’s a professional-grade **open-source SIEM** ideal for learning and experimentation.
- **Role in the course**:
    - Provides a foundation for **detection engineering** and **threat hunting**.
    - Not the sole focus, but central to blue team tracing labs.
    - Students may adapt the lab for a different SIEM if desired.
- **Core capabilities**:
    - Log collection, correlation, and real-time alerting.
    - File integrity monitoring, vulnerability detection, and threat intel integration.
    - Tight integration with **Elastic Stack** for visualization and analysis.

---

### 2. **Wazuh Rules and Their Importance**

- **Detection logic**: Rules are written in XML and work with decoders to parse logs.
- **Components**:
    - **Conditions**: Keyword matches, thresholds, patterns.
    - **Severity levels**: Higher levels indicate more urgent alerts.
- **Course focus**:
    - We’ll work with **levels 12–15** (serious/actionable alerts).
    - All **custom rules** start at **level 12**, adjusted based on severity and confidence.
- **Solo purple teaming value**:
    - Translate red team actions directly into blue team detections.
    - Test and tune for high-fidelity alerts.

---

### 3. **Saved Searches for Blue Team Tracing**

- **Definition**: Pre-built queries in Wazuh/Kibana that quickly filter specific events.
- **Purpose**:
    - Useful for dashboards, recurring investigations, and monitoring.
    - Can feed alerts or hunting workflows.
- **Goal for this step**:
    - Create a saved search to view alerts with **rule.level > 12**.

---

### 4. **Creating the Saved Search**

**Steps**:

1. In Wazuh Manager, click the **hamburger menu** → **Explore** → **Discover**.
2. **Select index pattern**: `wazuh-alerts-*`.
3. In the search bar (DQL), enter:
    
    ```
    rule.level > 12
    
    ```
    
4. **Add useful columns**:
    - Agent name
    - Rule description
    - Rule ID
    - Rule level
    - Rule groups
5. **Reorder columns** using the arrow buttons in the column headers.
6. Review the results — e.g., **33 alerts above level 12**.
7. **Save search**:
    - Click **Save** → Title it `Solo Purple Teaming - Alerts Above 12`.

---

### 5. **Identifying and Tuning a Noisy Rule**

- In the saved search, notice many alerts are **false positives** from **Rule ID: 92213**.
- Decision: Lower this rule’s severity to reduce noise before custom rule building.

---

### 6. **Overriding the Rule**

**Steps**:

1. In Wazuh Manager UI:
    - Go to **Hamburger Menu** → **Server Management** → **Rules**.
    - Search for `92213`.
    - Open the rule file containing it.
2. **Copy the rule** into your local override file:
    - SSH into the Wazuh Manager:
        
        ```bash
        cd /var/ossec/etc/rules
        sudo vi local_rules.xml
        
        ```
        
    - Paste the **group** definition and the **92213 rule**.
    - Change:
        
        ```xml
        <level>15</level>
        
        ```
        
        to:
        
        ```xml
        <level>12</level>
        
        ```
        
    - Save and exit: `Esc :wq`.
3. **Restart Wazuh Manager**:
    
    ```bash
    sudo systemctl restart wazuh-manager
    
    ```
    

---

### 7. **Verifying the Change**

1. Open **Explore → Discover** again.
2. Load the saved search.
3. Review alerts — noisy rule now shows level 12 instead of 15.
4. Confirm you have a cleaner baseline before writing custom rules.

---

### 8. **Why This Matters**

- A **clean baseline** prevents false positives from interfering with your purple teaming.
- You can now **focus on detecting** your own simulated attacker activities.
- You’ll build **custom rules** that:
    - Match your offensive simulation.
    - Belong to clearly defined groups.
    - Maintain high detection fidelity.

---

### 9. **Next Steps**

Before moving on:

- Ensure:
    - Wazuh is running.
    - Saved search for alerts above level 12 is available.
    - Rule 92213 has been tuned.
- Next lecture:
    - Build your **first custom detection rule** for attack level 0 activities.
    - Later: Learn to **bypass your own detections** for red team skill growth.

</section>
</div>
