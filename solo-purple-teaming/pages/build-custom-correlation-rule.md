---
layout: default
title: "Build Custom Correlation Rule"
permalink: /solo-purple-teaming/build-custom-correlation-rule/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Build Custom Correlation Rule</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Understanding the Purpose of Rule Correlation**

- **Goal**: Combine two base detection rules into a single, higher-fidelity alert.
- **Why?**
    - A single event (e.g., unsigned binary from Downloads **or** a network connection to port 80) might not be suspicious on its own.
    - But if both occur within a short time window (e.g., 2 seconds), it may indicate **Command and Control (C2)** activity.
- **Benefit**:
    - Reduces noise and false positives.
    - Surfaces more meaningful alerts.
    - Especially useful for solo purple teamers wanting high-fidelity detections.

---

### **2. How Wazuh Correlation Works**

- **Wazuh’s built-in correlation is**:
    - **Time-based & coincidence-based** — it looks for events that occur within a certain time window from the same agent.
- **Limitations**:
    - Does **not** verify if events came from the same process (no matching on `ProcessGuid` or other fields).
    - Does **not** match on hostname, IP, or username unless explicitly included.
    - Does **not** perform field-level joins — only looks for events within the same timeframe.
- **Implication**:
    - Good for simple behavioral stacking.
    - Not enough for detecting highly advanced or stealthy activity — those require more sophisticated, field-aware correlation.

---

### **3. Defining the Correlation Detection Logic**

- **We want to trigger an alert when**:
    - **SID 100100** (Unsigned binary from Downloads)
        
        **AND**
        
    - **SID 100101** (Network connection to ports 80 or 443)
        
        occur within **2 seconds** from the same agent.
        
- **Outcome**: Possible C2 activity alert.

---

### **4. Writing the Correlation Rule**

- **Structure**:
    
    ```xml
    <group name="solo_purple_teaming_c2,windows,sysmon">
      <rule id="100200" level="13">
        <if_match_group>solo_purple_teaming_c2</if_match_group>
        <frequency>2</frequency>
        <timeframe>2</timeframe>
        <description>
          Correlation: 100100 and 100101 occurred within 2 seconds – Possible C2 activity
        </description>
      </rule>
    </group>
    
    ```
    
- **Key attributes**:
    - `group name`: Groups related rules together.
    - `rule id`: Jump to `100200` for correlation rules.
    - `level`: Set to **13** (Medium severity).
    - `frequency`: `2` means both events must occur within timeframe.
    - `timeframe`: `2` seconds.
    - `if_match_group`: Matches the **group** that contains both base rules.
    - `description`: Clearly states what’s being detected.

---

### **5. Implementing in Wazuh Manager**

1. **Open Wazuh Manager** → Navigate to **Server Management → Rules**.
2. **Find your custom rule file** (e.g., `solo_purple_teaming.xml`).
3. **Edit the file** and paste your new correlation `<group>` and `<rule>` block.
4. **Check syntax**:
    - Fix special quote characters if pasted from other sources.
    - Ensure XML is properly closed.
5. **Save changes**.
6. **Restart Wazuh Manager** → Wait for it to fully reload.

---

### **6. Testing the Correlation Rule**

1. **Run your initial access payload** that triggers both base rules.
2. **Confirm payload executes** (e.g., unsigned binary runs and makes network connection).
3. **Go to Wazuh → Discover → Search for Rule ID `100200`**.
4. If no alert appears:
    - Wait briefly for event ingestion.
    - Check that the `if_match_group` exactly matches your base rules’ group name.
    - Re-run the payload if needed.
5. **When successful**:
    - The correlation alert will fire.
    - This confirms Wazuh linked the two base rules within the defined timeframe.

---

### **7. Key Takeaways**

- **Correlation reduces noise** but relies on time-based coincidence, not deep relational logic.
- **For higher accuracy**, combine Wazuh correlation with:
    - Process-level matching (via Sysmon fields).
    - External enrichment or advanced analytics.
- **Lab checkpoint**:
    - Before moving on, ensure you have a **custom correlation alert** firing for unsigned binary + network connection combination.

---

In the **next lecture**, you’ll explore the **pitfalls of time-based correlation** and begin designing an **advanced alerting engine** that goes beyond Wazuh’s built-in capabilities.

</section>
</div>
