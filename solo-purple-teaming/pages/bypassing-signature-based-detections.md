---
layout: default
title: "Bypassing Signature-Based Detections"
permalink: /solo-purple-teaming/bypassing-signature-based-detections/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Bypassing Signature-Based Detections</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# 🎯 Objective

Learn how to modify and test a payload using **ThreatCheck** to evade **signature-based detection** by Windows Defender. The goal is to get a **customized Apollo agent** past Defender without detection.

---

## 📖 **Part 1: Introduction to Signature-Based Detection**

- Antivirus engines often use **signatures** to detect known threats (e.g., specific byte patterns, hashes).
- If you upload a payload to VirusTotal, it may become **flagged and distributed**, making it ineffective.
- Tools like **ThreatCheck** automate payload testing against Defender without sending it to public AV engines.

---

## ⚙️ **Part 2: Lab Setup and Precautions**

### 🔒 Before Going Online

1. **Disable the following in Windows Defender:**
    - **Cloud-delivered protection**
    - **Automatic sample submission**
2. **Create an exclusion folder**:
    - Go to **Windows Security > Virus & threat protection > Manage settings > Exclusions**.
    - Click **"Add or remove exclusions"** → Add a **folder** named `research`.
    - This will be your safe zone for payloads.
    
    ![image.png](Bypassing%20Signature-Based%20Detections/image.png)
    

---

## ⬇️ **Part 3: Download Required Projects from GitHub**

### 🛠 Tools Needed

- **ThreatCheck** by RastaMouse
    
    GitHub: `https://github.com/rasta-mouse/ThreatCheck`
    

![image.png](Bypassing%20Signature-Based%20Detections/image%201.png)

- **Apollo Agent**
    
    GitHub: Search for `MythicAgents/apollo`
    

![image.png](Bypassing%20Signature-Based%20Detections/image%202.png)

> Recommendation: Review all code before running it. Take a snapshot of your VM before downloading.
> 

---

## 📦 **Part 4: Extract and Build Projects**

### A. Build ThreatCheck

1. **Extract `ThreatCheck-master.zip`** into your `research` folder.
2. Open the `.sln` (solution) file in **Visual Studio**.

![image.png](Bypassing%20Signature-Based%20Detections/image%203.png)

1. Go to `Tools > NuGet Package Manager > Manage NuGet Packages for Solution` and install:
    - `CommandLineParser`
2. **Set configuration** to `Release | x64` and build the project.

![image.png](Bypassing%20Signature-Based%20Detections/image%204.png)

---

### B. Build Apollo Agent

1. Extract `Apollo-master.zip`.
2. Navigate to:
    
    ```
    Apollo-master > Payload_Type > Apollo > Apollo
    
    ```
    

![image.png](Bypassing%20Signature-Based%20Detections/image%205.png)

1. Open the Apollo solution in Visual Studio.
2. Set configuration to `Release | x64`.

![image.png](Bypassing%20Signature-Based%20Detections/image%206.png)

1. Fix any missing namespaces:
    - If there's an error like missing `Task`,
    
    ![image.png](Bypassing%20Signature-Based%20Detections/image%207.png)
    
    - navigate to:
        
        ```
        Apollo > Management > Task.cs
        
        ```
        
        and correct the namespace.
        
    
    ![image.png](Bypassing%20Signature-Based%20Detections/image%208.png)
    
2. Rebuild the solution. It should build all 22 projects.
3. Locate the output:

```
Apollo > bin > x64 > Release > net451 > Apollo.exe

```

![image.png](Bypassing%20Signature-Based%20Detections/image%209.png)

---

## 🔄 **Part 5: Prepare for ThreatCheck Testing**

1. Disconnect your VM from the Internet.
2. Create a **testing** folder inside your `research` directory.
3. Copy the **Apollo agent binary** and the following from the `ThreatCheck` build:
    - `ThreatCheck.exe`
    - `CommandLine.dll` (dependency)
    - `System.Management.Automation.dll` (dependency)
4. Paste them into the `testing` folder.

![image.png](Bypassing%20Signature-Based%20Detections/image%2010.png)

---

## 🧪 **Part 6: Run ThreatCheck**

1. Open **Command Prompt**.
2. Navigate to the testing folder:
    
    ```bash
    cd \path\to\research\testing
    
    ```
    
3. Run ThreatCheck:
    
    ```bash
    ThreatCheck.exe -f Apollo.exe
    
    ```
    
4. Observe:
    - Windows Defender will likely **flag** the file.
    - Output will show **detected strings** like `mythic`.
    
    ![image.png](Bypassing%20Signature-Based%20Detections/image%2011.png)
    

---

## 🧬 **Part 7: Modify Payload to Evade Detection**

1. Go back to **Apollo project** in Visual Studio.
2. Use **Find and Replace**:
    - Press `Ctrl + Shift + F`.
    - Find: `Mythic` (case-sensitive)
    - Replace with: `My`
    - Replace all (should be ~509 occurrences).
    - Repeat for lowercase `mythic` → `my` (~73 occurrences).

![image.png](Bypassing%20Signature-Based%20Detections/image%2012.png)

1. Rebuild the Apollo solution.

---

## 🔁 **Part 8: Re-Test Modified Payload**

1. Copy the **new Apollo.exe** from:
    
    ```
    bin > x64 > Release > net451
    
    ```
    
    into your `testing` folder, replacing the old one.
    
2. Run ThreatCheck again:
    
    ```bash
    ThreatCheck.exe -f Apollo.exe
    
    ```
    
3. **Success!** If no threats are found, the payload has **bypassed Defender**.

![image.png](Bypassing%20Signature-Based%20Detections/image%2013.png)

1. Optional: Paste it onto the desktop to verify it's not flagged.

---

## ✅ **Part 9: Required Outcome**

To proceed:

- Your **customized Apollo agent** must successfully bypass Windows Defender.
- Make sure:
    - **Automatic sample submission remains OFF**
    - You **do not upload** the payload to public services like VirusTotal

---

## 🚀 **Next Steps**

In the next lecture:

- You will **replace** the Apollo agent code in your **Mythic C2 installation** with the modified version.

> ⚠️ Note: You must complete the bypass yourself. This walkthrough provides guidance, but the actual evasion is a required hands-on task for this course.
>

</section>
</div>
