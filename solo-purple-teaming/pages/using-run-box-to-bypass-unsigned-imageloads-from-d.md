---
layout: default
title: "Using Run Box to Bypass UnSigned ImageLoads From D"
permalink: /solo-purple-teaming/using-run-box-to-bypass-unsigned-imageloads-from-d/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Using Run Box to Bypass UnSigned ImageLoads From D</h1>
</section>
<section class="spt-content">

# Using Run Box to Bypass UnSigned ImageLoads From Downloads

Owner: Mike Sterrett

### **1. Introduction to the Technique**

- We are replacing our initial access method with a **Windows Run Dialogue execution**.
- This approach **reflectively loads the Apollo implant into memory**—no files are written to disk.
- Advantages:
    - Avoids file-based detections (AV, EDR file scan hooks, on-access scanners).
    - No phishing attachments for secure email gateways to scan.
    - Minimal user action—paste a one-liner into the run box and press Enter.
- From a defender’s view:
    - Execution appears **user-initiated**.
    - No new files appear on disk.
    - Code lives only in **live process memory**.
    - Detection requires memory telemetry or **advanced PowerShell Script Block Logging**.

---

### **2. Why PowerShell?**

- Cross-platform, built on .NET.
- Can automate tasks, query system info, and manage settings.
- Direct access to:
    - Windows APIs
    - .NET classes
    - WMI
- Commonly used by both administrators and red teamers.

---

### **3. Understanding the Command Structure**

We start with a **basic stealthy PowerShell command**:

- `w hidden` → launches PowerShell without a visible window.
- `c` (short for `command`) → executes the specified command/script block and exits.
- `& { ... }` → **call operator** invoking the enclosed script block.
- Inside the block, you can place any payload (e.g., `Get-Process` for testing).

---

### **4. Setting Up in the Lab**

1. Connect to your **reverse engineering VM** via RDP.
2. Open the **goodbye AMZ project** (initial access payload with Apollo reflective loading).
3. Copy the PowerShell script into **Notepad++**.

---

### **5. Converting to a One-Liner**

- Start with:
    
    ```
    powershell -c "& { ... }"
    
    ```
    
- Reduce size to fit into the run dialogue:
    - Replace double quotes with single quotes where possible.
    - Shorten variable names:
        - `bytes` → `b`
        - `loadedAssembly` → `l`
        - `instance` → `i`
        - `result` → `r`
        - `method` → `m`
        - `type` → `t`
    - Remove unnecessary spaces.
    - Inline method names instead of storing in separate variables.
    - Remove unused or redundant lines.

---

### **6. Testing in PowerShell**

- Paste the one-liner into a **PowerShell window** first.
- Run one line at a time if errors occur.
- Common troubleshooting:
    - Check renamed variables (e.g., `instance` → `i`).
    - Ensure method calls reference the shortened names correctly.

---

### **7. Adding Window Hiding**

- Add `w hidden` to run PowerShell without a visible window.
- Example:
    
    ```
    powershell -w hidden -c "& { ... }"
    
    ```
    

---

### **8. Run Box Length Limit**

- The run dialogue has a **character limit**.
- If the payload is still too long:
    - Further shorten variable names.
    - Replace `Invoke-WebRequest` with its alias `iwr`.
        - This also bypasses custom detection rules for `Invoke-WebRequest`.

---

### **9. Final Testing**

- Paste into the run dialogue (`Windows + R`).
- Ensure you receive the Apollo **callback** in your C2 server.
- If it fails, break it into lines again and debug.
- Verify it bypasses Windows Defender and previous custom detections.

---

### **10. Real-World Deployment Concept**

- Host the one-liner on a **compromised webpage**.
- Use JavaScript or other means to **hijack the clipboard** with the payload.
- Give the user instructions to:
    - Press `Windows + R`
    - Paste (`Ctrl + V`)
    - Press Enter
- This technique has proven surprisingly effective in real-world scenarios.

---

### **11. Before Moving On**

- Confirm you can:
    - Use the run dialogue to load Apollo into memory.
    - Bypass Windows Defender detections.
- **Next step:** Study the attached research on **PowerShell obfuscation**.
    - This will be necessary for bypassing reflection detection in the upcoming lecture.

</section>
</div>
