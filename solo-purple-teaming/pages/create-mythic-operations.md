---
layout: default
title: "Create Mythic Operations"
permalink: /solo-purple-teaming/create-mythic-operations/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Create Mythic Operations</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **Creating a Mythic Operation for Attack Path Level Zero**

In this walkthrough, we will create a new operation in Mythic for our **Attack Path Level Zero** exercise. Operations in Mythic manage the lifecycle of red team campaigns, allowing us to track, switch between, and eventually complete engagements while preserving their details for later analysis.

---

### **1. Understanding Mythic Operations**

- **Create Operation** – Allows you to define a new operation, including selecting the agent profile and associated payloads.
- **Mark as Complete** – Archives the operation so you can still analyze its data, but prevents creating new active callbacks in it.
- **Make Current** – Switches the active focus to the selected operation, useful when juggling multiple engagements.
- **Command Block Lists** – Restrict the use of risky or easily detectable commands.
    - Improves operational security.
    - Forces you to find alternative, stealthier methods to accomplish objectives.
    - Acts as a training tool for solo purple teaming.

---

### **2. Navigating to the Operations Section**

1. Log in to Mythic.
2. You can access operations in two ways:
    - Click on the **Current Operation** name at the top of the Mythic interface.
    - OR navigate to **Operation Config → Modify Operations**.

![image.png](Create%20Mythic%20Operations/image.png)

---

### **3. Reviewing Existing Operations**

- In our example, there is an existing operation called **test Apollo reflection**, which we previously used for payload testing.
- To close it out:
    1. Click **Edit** on the operation.
    2. Change the name if needed.
    3. Click **Mark as Complete**.
    4. Click **Update** to save changes.

This allows you to keep the data for analysis but prevents adding new active callbacks to that operation.

---

### **4. Creating a New Operation**

1. Click **New Operation**.
2. Enter a name for the operation:
    - For this example: `ecoin attack path level zero`.
3. Click **Submit**.
4. If the name already exists, adjust it slightly and try again.
5. Once created, click **Make Current** to set it as your active operation.

---

### **5. Creating a Command Block List**

As you progress and mature in solo purple teaming, you can use command block lists to train yourself to work around restricted commands.

1. Click **New Block List**.
2. Select the installed agent (in this case, **Apollo**).
3. Give the block list a descriptive name — e.g., `Beginner Solo Purple Teamer`.
4. Choose commands to block:
    - Example: Select **get_system** and move it to the **Blocked Commands** list.
5. Click **Submit** to save the block list.

---

### **6. Applying the Block List to an Operation or User**

1. Edit the operation or user profile.
2. Select your new block list (`Beginner Solo Purple Teamer`).
3. Click **Update**.
4. Now, if you attempt to run the blocked command (e.g., `get_system`) with the Apollo agent, Mythic will prevent it.

---

### **7. Summary**

You have now:

- Marked an old operation as complete.
- Created a new operation for **ecoin attack path level zero**.
- Created and applied a command block list for training and operational security.

---

**✅ Before moving to the next lecture:**

Make sure your new operation for **Attack Path Level Zero** is created and set as your current operation.

**Next Step:** In the upcoming lecture, we will generate the payload for Attack Path Level Zero.

</section>
</div>
