---
layout: default
title: "Prepping the Apollo Agent"
permalink: /solo-purple-teaming/prepping-the-apollo-agent/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Prepping the Apollo Agent</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# 🧪 Preparing the Apollo Agent for Reflection-Based Loading

## 🧭 Overview

In this exercise, you'll modify the **Apollo agent** to include an `Execute()` method that can be called via reflection. Then, you'll generate a new payload, host it, and test it using the **Goodbye AMSI** stager. Finally, you’ll verify its execution and analyze issues related to Windows Defender blocking the agent.

---

## ✅ Step 1: Modify Apollo Agent to Add `Execute()` Method

1. **SSH into your Kali host.**
    
    ```bash
    ssh username@your-kali-host-ip
    
    ```
    

![image.png](Prepping%20the%20Apollo%20Agent/image.png)

1. **Navigate to the installed Apollo agent directory.**
    
    ```bash
    cd /opt/mythic/mythic/installed_services/Apollo/agent_code/Apollo
    
    ```
    

![image.png](Prepping%20the%20Apollo%20Agent/image%201.png)

1. **Open the `Program.cs` file for editing using `sudo`.**
    
    ```bash
    CopyEdit
    sudo nano Program.cs
    
    ```
    
2. **Note the namespace and class:**
    - Namespace: `Apollo`
    - Class: `Program`
    
    ![image.png](Prepping%20the%20Apollo%20Agent/image%202.png)
    
3. **Above the `Main` method, insert the following code:**

```csharp
public static void Execute()
{
    string[] args = {};
    Main(args);
}

```

![image.png](Prepping%20the%20Apollo%20Agent/image%203.png)

1. **Save and exit the file:**
    - Press `Ctrl + X`, then `Y`, then `Enter`.

---

## ✅ Step 2: Generate a New Apollo Payload

1. **Go to the Mythic Web UI.**
2. **Navigate to the `Payloads` section.**

![image.png](Prepping%20the%20Apollo%20Agent/image%204.png)

1. Click **Actions > Generate Payload**.

![image.png](Prepping%20the%20Apollo%20Agent/image%205.png)

1. **Select:**
    - **OS:** Windows
    - **Build Type:** WinExe (default)
    - Click **Next**

![image.png](Prepping%20the%20Apollo%20Agent/image%206.png)

1. **Choose C2 Profile:**
    - Select `http`
    - Update the host to match your Kali box IP address (e.g., `http://10.0.3.2`)
    - Click **Next**

![image.png](Prepping%20the%20Apollo%20Agent/image%207.png)

1. **Payload Name:** Leave as `apollo.exe` or rename if needed.

![image.png](Prepping%20the%20Apollo%20Agent/image%208.png)

1. **Click Generate.**

Wait for the build to complete.

![image.png](Prepping%20the%20Apollo%20Agent/image%209.png)

---

## ✅ Step 3: Download and Transfer the Payload to Kali

1. **Download the payload to your local machine (e.g., macOS).**
2. **Use `scp` to transfer it to your Kali box:**

```bash
scp ~/Downloads/apollo.exe kali@10.0.3.2:/home/kali/Downloads/

```

![image.png](Prepping%20the%20Apollo%20Agent/image%2010.png)

---

## ✅ Step 4: Host Payload Using Python HTTP Server

1. **SSH into your Kali box again (if needed).**

![image.png](Prepping%20the%20Apollo%20Agent/image%2011.png)

1. **Navigate to the Downloads directory.**
    
    ```bash
    cd ~/Downloads
    ls
    
    ```
    
    ![image.png](Prepping%20the%20Apollo%20Agent/image%2012.png)
    
    Confirm `apollo.exe` is present.
    
2. **Start a Python HTTP server:**
    
    ```bash
    python3 -m http.server 8000
    
    ```
    

![image.png](Prepping%20the%20Apollo%20Agent/image%2013.png)

Now the payload is being hosted at:

`http://10.0.3.2:8000/apollo.exe`

---

## ✅ Step 5: Update and Build Goodbye AMSI for Reflection

1. **On your reverse engineering Windows host, open the Goodbye AMSI source code.**
2. **Modify the file to:**
    - Change the payload to `apollo.exe`
    
    ![image.png](Prepping%20the%20Apollo%20Agent/image%2014.png)
    
    - Update the type name to `Apollo.Program`

![image.png](Prepping%20the%20Apollo%20Agent/image%2015.png)

1. **Save the changes and rebuild the solution (Build > Build Solution).**

---

## ✅ Step 6: Execute the Test

1. **Open a command prompt on your reverse engineering box.**
2. **Navigate to the Goodbye AMSI executable location.**
3. **Run it:**
    
    ```bash
    GoodbyeAMSI.exe
    
    ```
    

---

## 🚫 Troubleshooting: No Callback?

If you don’t receive a callback in Mythic:

1. **Check Windows Defender logs:**
    - Go to **Windows Security > Virus & threat protection > Protection history**
    - Look for a detection around the same time as your test
    - If you see something like "Mythic Agent Detected", it’s likely being blocked by real-time protection
    
    ![image.png](Prepping%20the%20Apollo%20Agent/image%2016.png)
    
2. **Disable Defender temporarily:**
    - Go to **Virus & threat protection > Manage settings**
    - Turn **Real-time protection** OFF
3. **Retry Goodbye AMSI execution.**
4. **Check Mythic UI for successful callback.**

![image.png](Prepping%20the%20Apollo%20Agent/image%2017.png)

---

## 🎉 Success Criteria

You have successfully completed the process if:

- Apollo agent was modified with an `Execute()` method
- You generated and hosted the payload
- You modified and built the Goodbye AMSI stager
- Upon execution, you saw a callback in Mythic UI

---

## 🧠 Challenge Before Next Lecture

Before moving forward:

> ✅ Ensure your modified Apollo agent can be reflectively loaded via the Goodbye AMSI stager.
> 

---

## 🔜 Next Steps

In the next lecture, you’ll dive into how to bypass **Windows Defender** more effectively by modifying the agent further to evade real-time protection.

</section>
</div>
