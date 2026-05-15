---
layout: default
title: "Installing Apollo"
permalink: /solo-purple-teaming/installing-apollo/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Installing Apollo</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# 🛠️ Installing Apollo Agent and C2 Profiles in Mythic

## 🧩 Prerequisites

- Mythic is already installed and running on your Kali VM.
- You have internet access (required to clone from GitHub).
- You are using the Mythic CLI for installing agents and profiles.
- You have access to the Mythic Web UI.

---

## ✅ Step 1: Clone and Install Apollo Agent

1. **Open Terminal in your Kali VM**
    
    Confirm Mythic is installed and running.
    
2. **Navigate to the Apollo GitHub Repo**
    
    Visit: [https://github.com/MythicAgents/apollo](https://github.com/MythicAgents/apollo)
    
    Scroll down to find the installation command.
    
3. **Stop Mythic (Optional but Recommended for Clean Install)**
    
    ```bash
    sudo ./mythic-cli stop
    
    ```
    

![image.png](Installing%20Apollo/image.png)

1. **Install Apollo Agent Using the CLI**
    
    Run the following:
    
    ```bash
    sudo ./mythic-cli install github https://github.com/MythicAgents/apollo
    
    ```
    

![image.png](Installing%20Apollo/image%201.png)

**Wait for Installation to Complete.** This step pulls the agent code from GitHub and may take some time.

1. **Start Mythic Again**
    
    ```bash
    sudo ./mythic-cli start
    
    ```
    

![image.png](Installing%20Apollo/image%202.png)

---

## ✅ Step 2: Verify Apollo Agent in Web UI

1. **Open Your Browser**
    
    Go to `https://localhost:7443`
    
2. **Log in to Mythic Web UI**
    
    If needed, get your login password using:
    
    ```bash
    grep "MythicAdminPassword" mythic-docker/.env
    
    ```
    
3. **Check Installed Agents**
    
    Go to the “Payload/ C2 Services” section.
    
    ![image.png](Installing%20Apollo/image%203.png)
    
    Apollo agent should now be listed as installed.
    

---

## ✅ Step 3: Install HTTP C2 Profile

1. **Get HTTP C2 Profile**
    
    Visit: [https://github.com/MythicC2Profiles/http](https://github.com/MythicC2Profiles/http)
    
2. **Stop Mythic (Before Adding Profile)**
    
    ```bash
    sudo ./mythic-cli stop
    
    ```
    

![image.png](Installing%20Apollo/image%204.png)

1. **Install the HTTP Profile**
    
    ```bash
    sudo ./mythic-cli install github https://github.com/MythicC2Profiles/http
    
    ```
    

![image.png](Installing%20Apollo/image%205.png)

**Wait for Installation to Finish**

---

## ✅ Step 4: Install SMB C2 Profile

1. **Get SMB C2 Profile**
    
    Visit: [https://github.com/MythicC2Profiles/smb](https://github.com/MythicC2Profiles/smb)
    
2. **Install the SMB Profile**
    
    ```bash
    sudo ./mythic-cli install github https://github.com/MythicC2Profiles/smb.git
    
    ```
    

![image.png](Installing%20Apollo/image%206.png)

**Wait for Installation to Finish**

---

## ✅ Step 5: Start Mythic and Verify Everything

1. **Start Mythic Again**
    
    ```bash
    sudo ./mythic-cli start
    
    ```
    

![image.png](Installing%20Apollo/image%207.png)

1. **Refresh Mythic Web UI in Browser**
- Check that the **Apollo Agent** is online.
- Confirm **HTTP Profile** is online and accepting connections.
- Confirm **SMB Profile** is online and active.

![image.png](Installing%20Apollo/image%208.png)

---

## 🎯 Summary – Before Moving to the Next Lecture

Make sure you have all of the following working:

✅ Mythic is running and accessible via the web UI

✅ Apollo agent is installed and listed

✅ HTTP C2 profile is installed and active

✅ SMB C2 profile is installed and active

Once everything is in place, you're ready for the next lecture on **reflection techniques**.

</section>
</div>
