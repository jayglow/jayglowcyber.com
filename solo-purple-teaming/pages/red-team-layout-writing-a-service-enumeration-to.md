---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Writing A Service Enumeration To</h1>
</section>
<section class="spt-content">

# Red Team Layout - Writing A Service Enumeration Tool - Part 1

Owner: Mike Sterrett

### **Objective**

In this phase, we will begin building a custom C# utility called **Service Scan** that enumerates all services on a Windows endpoint. The goal is to:

1. Identify services our current user can **start or stop**.
2. Identify services where we can **write to the associated binary**.

These checks are critical for detecting **privilege escalation opportunities** via service abuse.

---

### **Key Concepts**

### **Why Services Matter in Privilege Escalation**

- Many Windows services run with **elevated privileges**.
- If an attacker can:
    - Modify the service binary
    - Or control its start/stop behavior
        
        → They can escalate privileges and execute malicious code with higher permissions.
        

### **Technology Used**

- **C#** with the **.NET Framework**
- **ServiceController** class for service interaction
- **Windows Management Instrumentation (WMI)** for advanced permission checks
- **Apollo Execute-Assembly** (later phase) for **in-memory execution** to evade disk-based detections

---

### **Step 1 – Understanding the ServiceController Class**

The `ServiceController` class allows:

- Enumerating existing services
- Querying properties like:
    - **DisplayName** – Friendly name of the service
    - **ServiceType** – Type of service (e.g., Win32 service)
    - **StartType** – How the service starts (Automatic, Manual, Disabled)
    - **Status** – Current state (Running, Stopped, Paused)
- Using **GetServices()** to retrieve all **non-driver services** on a machine

---

### **Step 2 – Preparing the Development Environment**

1. **Remote into Reverse Engineering VM**
    - Connect via Remote Desktop.
2. **Open Visual Studio Code**
3. **Create a New Project**
    - Type: `Console App (.NET Framework)`
    - Name: `ServiceScanDemo`

---

### **Step 3 – Adding Required References**

The `ServiceController` class requires a reference to **System.ServiceProcess.dll**.

**In Visual Studio Code:**

1. Remove unused `using` statements.
2. Add:
    
    ```csharp
    using System.ServiceProcess;
    
    ```
    
3. If IntelliSense shows it’s unavailable:
    - **Right-click** the project → **Add Reference**
    - Go to **.NET** → Find **System.ServiceProcess**
    - Check the box → Click **OK**
4. The error disappears, meaning the reference is active.

---

### **Step 4 – Writing the Enumeration Code**

1. **Retrieve All Services**
    
    ```csharp
    ServiceController[] services = ServiceController.GetServices();
    
    ```
    
2. **Loop Through Services**
    
    ```csharp
    foreach (ServiceController service in services)
    {
        Console.WriteLine($"Service: {service.DisplayName}");
        Console.WriteLine($"Start Type: {service.StartType}");
        Console.WriteLine($"Status: {service.Status}");
        Console.WriteLine();
    }
    
    ```
    

---

### **Step 5 – Running the Initial Version**

- Press **Run** in Visual Studio Code.
- Output:
    - Iterates through all services on the host
    - Displays:
        - Service Display Name
        - Start Type
        - Status
- A blank line separates each service entry for readability.

---

### **Step 6 – Next Steps**

Before moving on:

1. **Verify** that your code:
    - Successfully enumerates all services
    - Prints the desired properties
2. **Review**:
    - The Microsoft documentation for **ServiceController**
    - The research docs for:
        - `ServiceController`
        - `ManagementObjectSearcher` (used later for deeper WMI queries)
3. Prepare for the next lecture where:
    - We’ll **expand** the Service Scan to check permissions
    - Then use **Apollo’s execute-assembly** to run it **in-memory** for stealth execution

---

✅ **By the end of this lecture:**

You now have a functional **service enumeration utility** in C# that prints detailed metadata about all services. This is the foundation for privilege escalation checks we’ll add next.

</section>
</div>
