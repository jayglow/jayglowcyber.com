---
layout: default
title: "Generate Payload"
permalink: /solo-purple-teaming/generate-payload/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Generate Payload</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **Objective**

In this exercise, we will:

1. Configure the **reflective loader** to retrieve the Apollo payload from the correct IP address.
2. Generate an **Apollo agent payload** in Mythic configured to call back over HTTP.
3. Stage all files for initial access in the lab.

---

## **Part 1 – Configuring the Reflective Loader**

1. **Understand the Setup**
    - Our project is called **Goodbye AMSI**, used to reflectively load the Apollo agent.
    - In previous tests, we reached the Kali host using:
        
        ```
        10.0.3.2:8000
        
        ```
        
        Both the reverse engineering box and Kali host were on the **Attack LAN**.
        
    - Now, we must use the **WAN interface** so the payload can reach the Mythic server from **inside the Ecoin network**.
    - WAN interface address:
        
        ```
        192.168.100.101:8000
        
        ```
        
2. **Edit the Loader**
    - In the reflective loader source code, replace the old IP/port with:
        
        ```
        192.168.100.101:8000
        
        ```
        
    - Save the file (**Ctrl + S**).
3. **Build the Solution**
    - Compile the project in **x64 Debug** mode.
    - Output file:
        
        ```
        goodbyeAMSI.exe
        
        ```
        
4. **Locate and Transfer the File**
    - Navigate to:
        
        ```
        /source/repos/GoodbyeAMSI/x64/Debug/
        
        ```
        
    - Transfer the executable to the Kali host:
        - If SSH isn’t running on Kali, start it:
            
            ```bash
            sudo service ssh start
            
            ```
            
        - Use SCP from the Reverse Engineering box to copy the file:
            
            ```bash
            scp goodbyeAMSI.exe user@10.0.3.2:/home/user/Downloads
            
            ```
            
        - Enter your password when prompted.

---

## **Part 2 – Generating the Apollo Payload in Mythic**

1. **Open Mythic and Start Payload Creation**
    - In Mythic’s UI, click the **Payloads** icon (hazard/radioactive symbol).
    - Select **Actions → Generate New Payload**.
2. **Payload Configuration**
    - **Platform:** Windows
    - **Agent:** Apollo
    - **Format:** Windows Binary
    - **Commands:** Add all available commands (→ button).
    - **C2 Profile:** HTTP
        - Host: `192.168.100.101` (WAN interface of Attack LAN edge device)
    - **Description:**
        
        ```
        Assumed breach payload – Attack Level Zero
        
        ```
        
3. **Generate and Download**
    - Click **Create Payload**.
    - Once generated, download it to your current machine (Mac outside the lab in this example).
4. **Rename and Transfer**
    - Rename the payload:
        
        ```bash
        mv apollo2.exe apollo.exe
        
        ```
        
    - Use SCP to transfer the file into Kali’s Downloads directory:
        
        ```bash
        scp apollo.exe user@192.168.100.101:/home/user/Downloads
        
        ```
        
    - Enter your password when prompted.

---

## **Part 3 – Staging the Payload for Delivery**

1. **Verify Files on Kali**
    - SSH into the Kali host:
        
        ```bash
        ssh user@192.168.100.101
        
        ```
        
    - Go to the Downloads directory and confirm:
        
        ```bash
        ls
        
        ```
        
        You should see:
        
        ```
        goodbyeAMSI.exe
        apollo.exe
        
        ```
        
2. **Start a Python HTTP Server**
    - In the Downloads directory, run:
        
        ```bash
        python3 -m http.server 8000
        
        ```
        
    - If the server is already running, you’ll see a message indicating the port is in use.

---

## **Before Moving On**

✅ Ensure you have:

- `goodbyeAMSI.exe` staged in Kali Downloads.
- `apollo.exe` payload staged in Kali Downloads.
- Python HTTP server running on port 8000 to serve the payload during initial access.

---

## **Next Step**

In the next lecture, we will:

- Launch the staged payload.
- Establish an initial foothold inside the **ecoin domain**.

</section>
</div>
