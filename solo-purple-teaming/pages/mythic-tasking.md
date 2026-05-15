---
layout: default
title: "Mythic Tasking"
permalink: /solo-purple-teaming/mythic-tasking/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Mythic Tasking</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **Mythic Tasking Basics**

### **1. Understanding Tasking in Mythic**

![image.png](Mythic%20Tasking/image.png)

In Mythic, *tasking* is the process of sending commands to agents deployed inside the target environment.

- This is the primary way you interact with compromised systems.
- Mythic supports both **manual** and **automated** tasking:

- Through the **web UI**
- Using **prebuilt scripts**
- Via the **Mythic API**

The Mythic UI includes features that make tasking more efficient and safer:

![image.png](Mythic%20Tasking/image%201.png)

- **Autocomplete** and **tool tips** for accurate commands
- Ability to **preview commands** before dispatching
- **OPSEC warnings** for potentially noisy actions
- Command **tagging** and **grouping** for organized operation tracking

---

### **2. Navigating to Callbacks**

1. Log in to Mythic.
2. Go to the **Callbacks** page.
3. Locate **Callback 1** (our initial foothold).
4. You can rename the callback tab for clarity:
    - Right-click the tab → **Set Tab Description**
    - Example: Rename to *Initial Foothold – LAN*

---

### **3. Discovering Available Commands**

You can find available tasks/commands in several ways:

**Method 1 – Using the `help` command:**

- Type `help` and execute it.
- Mythic will list all available commands for that agent.

**Method 2 – Help on a specific command:**

- Type `help <command_name>` (e.g., `help assembly_inject`).
- Mythic will display detailed information about that specific command.

**Method 3 – Command modal for parameters:**

- If a command takes parameters (e.g., `run`), type the command and press **Shift + Enter**.
- This opens a dialog box where you can fill out parameters such as:
    - Executable path or filename
    - Arguments

**Method 4 – Viewing command metadata:**

- Click the arrow next to the **Callback ID** → **Meta** → **View Metadata**
- This shows all commands with direct **documentation links**.
- Clicking a link opens a new tab with usage details.

---

### **4. Running Your First Task**

Example: Running the `ps` command to list processes.

1. Type `ps` and execute.
2. **Task lifecycle:**
    - **Submitted** – Waiting for the agent to check in.
    - **Agent Processing** – Agent picks up the task.
    - **Data Returned** – Output is displayed in the UI.

The results use a **browser script** to format the output as a table for easier viewing.

---

### **5. Taking Actions on Returned Results**

For each task result:

- Click the **Action** button.
- Possible actions include:
    - Kill the process
    - Inject a keylogger
    - Capture a screenshot
    - Steal a token
    - View more details about the process

---

### **6. Before Moving to Enumeration**

Before continuing to the enumeration phase in the next lecture:

- Run **at least one task** (e.g., `ps`)
- Ensure you can see the task’s lifecycle and returned results

---

### **Next Steps**

In the next phase, you will:

- Enumerate local admin group members
- Identify installed services
- Check for vulnerable services
- Simulate attacker enumeration to later create **detections** for these activities

</section>
</div>
