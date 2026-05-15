---
layout: default
title: "Blue Team Explore - Learning About execute_assembl"
permalink: /solo-purple-teaming/blue-team-explore-learning-about-execute-assembl/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Learning About execute_assembl</h1>
</section>
<section class="spt-content">

# Blue Team Explore - Learning About execute_assembly, Process Injection, and IPC

Owner: Mike Sterrett

## **1. Introduction to Execute Assembly in Apollo**

- **Purpose:** Execute a .NET assembly entirely in memory without writing it to disk.
- **Execution Flow:**
    1. Apollo launches a **sacrificial process** — a clean host process determined by the `spawnto_x64` setting.
    2. Into this process, Apollo injects a **Common Language Runtime (CLR) loader**.
    3. The actual .NET assembly is injected directly into the process memory — **never written to disk**.
    4. Using `System.Reflection.Assembly.Load`, the loader **reflectively loads** the assembly.
    5. The specified **entry point** of the assembly is invoked with any provided arguments.
    6. As it runs, **standard output** is streamed back to the Apollo agent over a **named pipe**.
- **Key Advantage:**
    - Stealthy, fileless execution avoids many traditional detection mechanisms.
    - However, it still presents **operational risks**.

---

### **2. Primer on Process Injection**

- **Definition:** Technique allowing an attacker to execute code inside another running process’s memory space.
- **Basic Steps:**
    1. **Select a legitimate process** to blend in with normal activity.
    2. **Obtain a handle** to that process with permissions to manipulate it.
    3. **Allocate memory** inside the target process for the malicious payload.
    4. **Write the payload** into the allocated memory.
    5. **Create a remote thread** inside the target process pointing to the payload address.
    6. Payload **executes within the trusted process**, making detection harder.
- **Note:**
    - Creating a remote thread is just one method; there are other process injection techniques.
    - Students should research and learn multiple injection methods for **both offensive and defensive purposes**.

---

### **3. Operational Risks of Execute Assembly**

- **Risk Origin:**
    - Process access events
    - Inter-process communication (IPC) via named pipes
- **Detection Opportunities with Sysmon:**
    - **Event ID 10 – Process Access**
        - Logs when one process opens a handle to another.
        - Useful fields:
            - **SourceProcessGUID / SourceImage** – who initiated the access.
            - **TargetProcessGUID / TargetImage** – which process was targeted.
            - **GrantedAccess** – level of access given (e.g., `PROCESS_ALL_ACCESS`).
    - **Event ID 17 – Pipe Created**
        - Triggered when a named pipe is created.
        - Logs **ProcessGUID**, **Image**, and **PipeName**.
    - **Event ID 18 – Pipe Connected**
        - Triggered when a connection is made to a named pipe.
        - Captures similar fields to Event ID 17.
- **Why Important:**
    - These events can reveal stealthy command-and-control channels or tool staging via named pipes.
    - Combining process access logs with pipe creation/connection data helps map attacker activity.

---

### **4. Next Steps in the Lab**

1. **Enable Sysmon logging** for Event IDs 10, 17, and 18.
2. **Re-engage** and run `execute-assembly` in Apollo.
3. **Audit the logs**:
    - Note **Granted Access** values.
    - Track process relationships (who accessed who).
4. Begin **formulating detection logic** for execute assembly activity.

---

This walkthrough takes students from understanding **how execute assembly works**, through **its relationship with process injection**, to **how defenders can detect it** with Sysmon telemetry, before setting them up for hands-on analysis in the next lab.

</section>
</div>
