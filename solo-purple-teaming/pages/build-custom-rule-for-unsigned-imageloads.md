---
layout: default
title: "Build Custom Rule for Unsigned ImageLoads"
permalink: /solo-purple-teaming/build-custom-rule-for-unsigned-imageloads/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Build Custom Rule for Unsigned ImageLoads</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Objective**

In this lecture, we’ll create a **custom Wazuh rule** to detect unsigned binaries loaded from risky locations like **Downloads** or **Temp** directories.

This rule will act as:

- A **base detection rule** now
- A **correlation rule** in future lectures

---

### **2. Recap – Indicators of Compromise (IoCs) in Attack Path Level Zero**

Before building the rule, here’s what we saw in the attack path:

1. An **unsigned binary** loaded from the Downloads directory.
2. That binary made **network connections to port 80**.
3. Followed by **user enumeration commands**.
4. Then **service enumeration commands**.

We’ll focus on the **unsigned binary** as our starting detection point.

---

### **3. Rule Design – Suspicious Image Load**

We’ll create a **suspicious image load rule** with these key components:

### **Group Tags**

- `solo_purple_teaming_c2` (custom group for solo purple teaming rules)
- Add to **Windows** and **Sysmon** groups for proper categorization.

### **Rule Metadata**

- **Rule ID range** for custom rules: `100000–120000`
- Start with: **100100** (offset from start of range for clarity)
- **Level**:
    - 12 = Low severity
    - 13 = Medium
    - 14 = High
    - 15 = Critical
    - For this rule: **Level 12**

### **Event Type Matching**

- We will check for **Sysmon Event ID 7** (Image Load event)
- Corresponding Wazuh SID: **616009**

### **Field Checks**

- **Image Loaded Path** matches:
    - `C:\Users\...` through `Z:\Users\...`
    - OR `C:\Windows\Temp\...` through `Z:\Windows\Temp\...`
- **Signed field** must be `false` (indicating unsigned binary)

### **Description**

- `"Unsigned image loaded from user or temp directory"`

---

### **4. Finding the Sysmon Event SID**

Before writing the rule, find the SID for Event ID 7:

1. Go to **Server Management → Rules**.
2. Filter by **"Sysmon"**.
3. Scroll until you find **Event ID 7** description.
4. Note the SID: **616009**.

---

### **5. Creating the Custom Rule File**

1. In the Wazuh Manager, **add a new rules file**.
2. Paste the custom rule (ensure no bad characters – use standard double quotes, remove trailing commas).
3. Example file name: `solo_purple_teaming.xml`

**Example Rule Structure**

```xml
<group name="solo_purple_teaming_c2,windows,sysmon">
  <rule id="100100" level="12">
    <if_sid>616009</if_sid>
    <field name="ImageLoaded" type="pcre2">^[c-zC-Z]:\\Users\\|^[c-zC-Z]:\\Windows\\Temp\\</field>
    <field name="Signed">false</field>
    <description>Unsigned image loaded from user or temp directory</description>
  </rule>
</group>

```

1. Save the rule file.

---

### **6. Restarting the Manager**

1. Restart the Wazuh Manager to load the new rules.
2. Confirm the restart completes successfully.

---

### **7. Testing the Rule**

1. On the **assumed breach host**, run the malicious payload.
2. Wait for the Sysmon event to be generated and sent to Wazuh.
3. In Wazuh, go to **Explore → Discover**.
4. Change the time filter to the **last 5 minutes**.
5. In the **alerts index**, filter by:
    
    ```
    rule.id:100100
    
    ```
    
6. Confirm you see the event triggered by the unsigned binary.

---

### **8. Success Criteria**

✅ Rule created in Wazuh Manager

✅ Event triggers on payload execution

✅ Visible in Discover with correct **rule ID** and description

---

### **9. Before Moving On**

Make sure you have:

- A **working custom rule** detecting unsigned image loads from **Downloads** or **Temp**
- Verified that it triggers in a test scenario

---

### **10. Next Lecture**

We’ll build another custom rule for **detecting network connections** related to this attack chain.

</section>
</div>
