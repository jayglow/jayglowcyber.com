---
layout: default
title: "Mythic Callbacks UI"
permalink: /solo-purple-teaming/mythic-callbacks-ui/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Mythic Callbacks UI</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **1. Introduction to Callbacks**

Before starting enumeration on a target host, it’s important to understand **Mythic callbacks**.

In Mythic, a **callback** is an active communication channel between the C2 server and a deployed agent on a compromised system.

- When a payload is executed on a target, it “calls back” to the Mythic server, creating a callback entry on the **Callbacks** page.
- This callback acts as your **control point** for:
    - Sending tasks to the agent.
    - Receiving output from those tasks.
    - Viewing valuable metadata (e.g., username, hostname, parent process).

---

### **2. Callback Lifecycle**

![image.png](Mythic%20Callbacks%20UI/image.png)

Callbacks go through a natural life cycle:

1. **Payload execution** – The agent initiates its first callback.
2. **Beaconing** – The agent sends periodic “I’m alive” signals to the server.
3. **Tasking** – The operator sends commands through the callback, and results are returned.
4. **Loss of communication** – If the process is killed or blocked, the callback eventually becomes stale or dies.

💡 **Why persistence matters**:

Attackers often set up persistence immediately after gaining access so that callbacks are re-established after reboots.

---

### **3. Callback Metadata**

Each callback contains rich details, such as:

- Process ID (PID)
- Username
- Privilege level
- Integrity level

You can **rename** callbacks or apply **tags** to organize targets—especially useful in engagements with many hosts.

---

### **4. Interacting with Callbacks**

Once active, you can:

- **Send tasks** (commands or actions) to the agent.
- View **real-time results** in the callback interface.
- **Pin** important results for easy reference.

⚠ **Note**: “Real-time” depends on the **sleep time** and **jitter** settings of the agent—longer sleep times mean slower responses.

---

### **5. Managing Multiple Callbacks**

In real-world operations, you may have dozens of callbacks.

Mythic provides:

- **Filtering** (e.g., by user, host, operation)
- **Tags** for grouping systems
- **Sorting** by any column

---

### **6. Exploring the Callbacks Page**

![image.png](Mythic%20Callbacks%20UI/image%201.png)

1. **Navigate to the Callbacks page** in Mythic.
2. **View callback details**:
    - Callback ID
    - Associated operators
    - Hostname, user, domain
    - PID, last check-in time
    - Agent type, IP addresses
3. **Lock/Unlock callbacks**:
    - Locking restricts interaction to the lead and the operator who locked it.
    - Unlock to allow others to issue tasks.
4. **Resize columns**:
    - **Double-click** column borders to auto-resize.
    - **Click and drag** to adjust width manually.
5. **Customize visible metadata**:
    - **Right-click** a header → *Reorder Columns / Adjust Visibility*
    - Toggle fields (e.g., External IP) → Click **Submit**
    - Verify changes in the table.
6. **Relating metadata to your network diagram**:
    - Example: External IP `192.168.100.102` matches the WAN interface on the Ecoin edge device.
    - Internal IP `10.0.2.100` matches the static IP assigned to the R-LIN host.
7. **Sorting and filtering**:
    - Sort by clicking a column header.
    - Filter by entering criteria in a column filter field.

---

### **7. Student Action**

Before moving on:

- Review the **Callbacks** page in Mythic UI.
- Adjust **visible columns** and **metadata** to suit your preferences.
- Add any fields you find useful for tracking hosts.

---

### **8. Next Steps**

In the next lecture, you’ll learn:

- The basics of **Mythic tasking**.
- How tasking is used for host enumeration.
- Steps to begin working toward **privilege escalation**.

</section>
</div>
