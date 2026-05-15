---
layout: default
title: "Build Custom Rule for Network Connect to Common C2"
permalink: /solo-purple-teaming/build-custom-rule-for-network-connect-to-common-c2/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Build Custom Rule for Network Connect to Common C2</h1>
</section>
<section class="spt-content">

# Build Custom Rule for Network Connect to Common C2 Port

Owner: Mike Sterrett

### 1. **Purpose of the Rule**

In this lab, we’ll create a **base detection rule** for spotting Command-and-Control (C2) activity. Specifically, we’ll focus on **network connections to known C2 ports** (HTTP/HTTPS) originating from suspicious directories.

We’ve already created a rule for detecting unsigned binaries loaded from the Downloads folder in a previous exercise. Now, we’ll add another rule to detect when that unsigned binary makes outbound network connections.

---

### 2. **Indicators of Compromise (IoCs) for Attack Path Level Zero**

- **Unsigned binary** loaded from the Downloads folder.
- **Network connections** made by that unsigned binary to **port 80 (HTTP)** or **port 443 (HTTPS)**.
- **Suspicious directories** involved:
    - `C:\Users\...`
    - `C:\Windows\Temp`

---

### 3. **Rule Definition**

Our new rule will:

- Have **Rule ID**: `100101` (incremented from the previous rule’s ID).
- Set **Level**: `12` (low-level security event).
- Reference **SID 61605**, which corresponds to **Sysmon Event ID 3** (network connect).
- Match the following fields:
    - **Destination Port**: `80` or `443`
    - **Image Path**: from `C:\Users\` or `C:\Windows\Temp`
- **Description**: “Network connection to common C2 ports from Downloads, Users, or Windows Temp directories.”

---

### 4. **Steps to Create the Rule in Wazuh Manager**

1. **Identify the Correct SID**:
    - Search for Sysmon network connect events.
    - Confirm that **Event ID 3** maps to **SID 61605**.
2. **Open Rules Management**:
    - Navigate to **Server Management → Rules**.
    - Open your **solo_purple_teaming** custom rules file for editing.
3. **Add the New Rule**:
    - Inside your custom group, paste the new rule block.
    - Make sure it references **SID 61605**.
    - Align and format the XML properly.
4. **Save Changes**:
    - Save the rule file.
    - Restart the Wazuh manager to apply the new rule.

---

### 5. **Testing the Rule**

1. **Restart the Manager**:
    - Wait for it to fully restart before testing.
2. **Trigger the Rule**:
    - On your assumed breached host, run the initial access payload again.
    - Confirm you receive a callback.
3. **Check for Alerts**:
    - In Wazuh, go to **Explore → Discover**.
    - Search within the last 5 minutes for **Rule ID 100101**.
    - Be patient—events can take a few moments to arrive.
4. **Verify Detection**:
    - Ensure that network connect events from the suspicious directories are appearing in the Wazuh manager.

---

### 6. **Before Moving On**

- Confirm that you have:
    - A **custom alert** for network connections to ports 80/443 from `Downloads`, `Users`, or `Windows Temp`.
    - Successfully tested it in your environment.
    - Verified that the alerts appear in Wazuh.

---

### 7. **Next Steps**

In the next lecture, we’ll:

- Build our **first correlation alert**.
- Discuss the **weaknesses of correlation alerts in Wazuh**.
- Work toward a **more advanced custom detection solution**.

</section>
</div>
