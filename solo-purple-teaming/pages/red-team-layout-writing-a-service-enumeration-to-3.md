---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-3/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Writing A Service Enumeration To</h1>
</section>
<section class="spt-content">

# Red Team Layout - Writing A Service Enumeration Tool - Part 3

Owner: Mike Sterrett

### **1. Lecture Objective**

In this session, we continue developing our **Service Scan Utility**.

Our focus: **Check if the current user has permission to start a service** as we iterate through all services on a target system.

---

## **2. Understanding Security Descriptors in .NET**

Before coding, we need a solid understanding of the **RawSecurityDescriptor** class in .NET, as it gives low-level access to Windows object permissions.

- **DACL (Discretionary Access Control List)**
    
    Defines which users or groups are allowed or denied access to the object.
    
- **Group Field**
    
    Sets or retrieves the **primary group** associated with the object.
    
    Relevant in systems where **group ownership** impacts access.
    
- **Owner Field**
    
    Stores the **Security Identifier (SID)** of the object owner.
    
    Ownership is critical because owners often have permission-modifying rights.
    

---

### **3. RawSecurityDescriptor Constructors**

.NET provides multiple ways to create a **RawSecurityDescriptor**:

1. **Byte Array Constructor**
    
    Initialize from raw binary data (often from unmanaged Windows API calls).
    
2. **Detailed Constructor**
    
    Provide explicit control flags, owner/group, and both system & discretionary ACLs.
    
3. **SDDL String Constructor**
    
    Use **Security Descriptor Definition Language** strings.
    
    - Human-readable
    - Great for importing/exporting permissions
    - **We will use this** for our utility.

---

### **4. Understanding Service Permissions**

Windows stores service permissions as a **bitmask** (an integer where each bit is a permission flag).

- **SERVICE_START permission** = `0x00010` (binary `00010000`)
- An ACE (**Access Control Entry**) can have **multiple permissions** combined.
- To check if **SERVICE_START** is granted:
    
    ```csharp
    if ((accessMask & SERVICE_START) == SERVICE_START)
    
    ```
    
    A match means the permission is present.
    

---

## **5. Coding the `CanStartService` Function**

### **Step 1 — Create the Function**

- Place it **below** the existing `GetServiceSDDL` method.
- Signature:
    
    ```csharp
    public static bool CanStartService(string sddl)
    {
        return false; // placeholder
    }
    
    ```
    

---

### **Step 2 — Null Check**

Ensure the SDDL is not null before processing:

```csharp
if (sddl != null)

```

---

### **Step 3 — Get Current User Identity**

```csharp
WindowsIdentity currentUser = WindowsIdentity.GetCurrent();

```

This retrieves a **WindowsIdentity** object for the currently logged-in user.

---

### **Step 4 — Create Raw Security Descriptor from SDDL**

```csharp
RawSecurityDescriptor rsd = new RawSecurityDescriptor(sddl);

```

---

### **Step 5 — Iterate Through the DACL**

```csharp
foreach (CommonAce ace in rsd.DiscretionaryAcl)

```

We focus on **AccessAllowed** ACE types:

```csharp
if (ace.AceType == AceType.AccessAllowed)

```

---

### **Step 6 — Check User or Group Match**

We check if:

- The ACE’s **SID** matches the current user’s SID
    
    **OR**
    
- The ACE’s SID is in one of the user’s group memberships.

```csharp
if (currentUser.User.Equals(ace.SecurityIdentifier) ||
    currentUser.Groups.Contains(ace.SecurityIdentifier))
{
    return true; // Permission granted
}

```

---

## **6. Integrating Permission Check Into Service Scan**

- **Move SDDL retrieval** up in the loop.
- Call:
    
    ```csharp
    if (CanStartService(sddl))
    {
        Console.WriteLine(serviceName);
        Console.WriteLine(sddl);
        Console.WriteLine("Current user can start this service.");
    }
    
    ```
    

---

## **7. Testing in the Lab**

### **Step 1 — Run in Reverse Engineering VM**

- Execute the utility to see which services the current user can start.

### **Step 2 — Test on Assume Breach Host**

- Use `scp` to copy the executable to the Kali host’s downloads.
- SSH in and start the staging server.
- Download the service scan demo from the assume breach host’s browser.
- Run the tool and observe output.

**Expected Result:**

The utility lists several services, including the **ecoin** service, as startable by the current user.

---

## **8. Student Task Before Next Lecture**

- Iterate through all services with the Service Scan Utility.
- Verify that the output correctly identifies services you can start.
- Read **ReAttach research** mentioned in the walkthrough.
- Prepare for the next session, where we’ll **finish the custom utility** and learn to execute it in memory using **Apollo’s Execute-Assembly**.

</section>
</div>
