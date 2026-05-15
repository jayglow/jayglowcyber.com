---
layout: default
title: "Managed VS Unmanaged"
permalink: /solo-purple-teaming/managed-vs-unmanaged/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Managed VS Unmanaged</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# Introduction

Let’s start by understanding the difference between **managed and unmanaged code**, and why this matters for red teamers.

---

### ✅ **Managed Code – Controlled by the CLR**

**Managed code** runs under the supervision of the **Common Language Runtime** (CLR). Think of the CLR as a safety net — it handles memory allocation, garbage collection, exception handling, and even enforces security policies.

This managed environment is designed to prevent crashes and protect memory from corruption.

From an attacker’s point of view, this structure is a **double-edged sword**:

- **On the downside**, traditional low-level exploits like arbitrary memory corruption are harder to pull off.
- **On the upside**, .NET-heavy environments open new doors — you can abuse trusted .NET assemblies, inject into CLR processes, and live off the land using built-in .NET tools.

Understanding how tightly managed code operates helps us know **where we're restricted** — and more importantly, **where we might exploit or blend in**.

---

### ✅ **Unmanaged Code – Native & Powerful**

In contrast, **unmanaged code** runs natively — **no CLR, no guardrails**.

This gives us two important advantages as attackers:

1. **Direct access to memory and system resources**.
2. The code is often written in **C or C++**, which are extremely fast but also vulnerable.

Since unmanaged code manages its **own memory**, we get a bigger attack surface:

- Buffer overflows
- Use-after-free vulnerabilities
- Dangling pointers

So, when we deal with unmanaged code, both the **opportunity** and the **risk** are much higher.

---

### 🎯 **Why This Matters for Red Teaming**

Knowing whether you're working in a managed or unmanaged environment is **not just theory** — it shapes your entire engagement:

- **Unmanaged code** is great for raw exploits: shellcode injection, memory corruption, and initial access.
- **Managed code**, however, becomes useful **after you’ve gained access** — you can abuse exposed .NET APIs, move laterally, or use living-off-the-land techniques.

But here’s the catch: .NET tools need the **CLR to be loaded**. And that’s something defenders can **detect**.

Modern blue teams are alert to processes that unexpectedly load the CLR. So, for **stealthy and long-lasting operations**, you must know when you're dealing with managed code, and how to **weaponize both** environments.

---

### 🧠 **CLR as an Attack Opportunity**

Once the CLR is active in a process, it’s **not just a defensive obstacle** — it's a **valuable resource**:

- You can **inspect loaded .NET assemblies** in memory to discover features and find weak points.
- You can **load and run your own .NET payloads** using reflective execution — no need to drop files to disk.
- You can **enumerate application domains and assemblies**, essentially mapping out the managed code landscape of the process.
- Advanced red teamers can even **hook managed methods at runtime**, allowing them to tamper with or bypass security tools like EDRs.

Bottom line: If the CLR is running, it gives you a **whole new toolkit** to work with.

---

### 🧪 **Lab Preview – Enumerating Assemblies in C#**

Let’s jump into a quick demo to solidify this.

We’ll use **Visual Studio** inside our **Windows 11 reverse engineering lab VM** to enumerate loaded assemblies in a running .NET process.

### 🔧 Step-by-Step

1. **Open Visual Studio**
2. Create a new **Console App (.NET Framework)** project
3. Name it something like `GoodbyeAMSI`
4. Choose **.NET Framework 4.7.2**

![image.png](Managed%20VS%20Unmanaged/image.png)

Once the project is set up:

1. Remove the default `using` statements we don’t need
2. Add the following snippet to `Main()`:

```csharp
static void Main(string[] args)
{
    foreach (var assembly in AppDomain.CurrentDomain.GetAssemblies())
    {
        Console.WriteLine(assembly.FullName);
        Console.WriteLine(); // add a few new lines for spacing
        Console.WriteLine();
    }

```

![image.png](Managed%20VS%20Unmanaged/image%201.png)

When we run this program, it will list all the assemblies currently loaded in the application domain. Since we haven’t included any extra dependencies, you’ll likely only see:

- `mscorlib`
- Your own `GoodbyeAMSI` assembly

![image.png](Managed%20VS%20Unmanaged/image%202.png)

This demonstrates how .NET reflects its own internal structure — and that’s exactly what we’ll **leverage next**.

---

### 🚀 **What’s Next? AMSI Bypass**

In the next lesson, we’ll use this knowledge to **bypass AMSI** (Anti-Malware Scan Interface) — a key defensive control in Windows.

We’ll do this by:

- Enumerating loaded assemblies
- Hooking into them using C#
- Disabling AMSI within a CLR process

This marks a pivot from understanding CLR… to **weaponizing it**.

---

### 📌 Summary

- Managed code = runs under CLR, safer, but can be abused via .NET features
- Unmanaged code = native execution, high-risk/high-reward, prime for exploits
- Red teamers can exploit both environments — but need to know **how and when**
- CLR gives you internal visibility and attack surface inside the memory of managed processes
- We’ll use this in the next lab to bypass AMSI

</section>
</div>
