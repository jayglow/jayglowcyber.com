---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-4/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Writing A Service Enumeration To</h1>
</section>
<section class="spt-content">

# Red Team Layout - Writing A Service Enumeration Tool - Part 2

Owner: Mike Sterrett

### **1. Introduction**

In this phase, we’re expanding our custom **Service Scan** utility to retrieve the **Security Descriptor Definition Language (SDDL)** string for Windows services.

To do this, we’ll use **P/Invoke** in C# to call unmanaged Windows API functions from our managed .NET code.

---

### **2. Understanding P/Invoke**

- **What is P/Invoke?**
    - Allows managed .NET code to call unmanaged Windows API functions.
    - Lets you work directly with **native Windows DLLs** like `advapi32.dll`.
- **Why is it advanced?**
    - Requires knowledge of both managed (.NET) and unmanaged (Win32 API) environments.
    - Involves **manual memory management** (allocating, copying, freeing).
    - **Data marshaling** is critical — incorrect marshaling can cause:
        - Memory corruption
        - Runtime crashes
        - Architecture-specific bugs
    - Struct layout **must match** native memory layout exactly.

---

### **3. Why We Need P/Invoke for Services**

- .NET does **not** provide a built-in way to query a service’s **security descriptor**.
- Security descriptors tell us:
    - Which users/groups can control a service.
    - Potential privilege escalation vectors.
- P/Invoke allows us to:
    1. Connect to the Service Control Manager (SCM) with `OpenSCManager`
    2. Open a handle to a service with `OpenService`
    3. Query the service’s security descriptor with `QueryServiceObjectSecurity`

---

### **4. Setting Up the C# Project**

1. **Code cleanup**
    - Remove unused `using` statements.
    - Replace string concatenation with **string interpolation** (`$"..."`).
2. **Capture service names**
    - Ensure we store the **service name** for later use.

---

### **5. Creating `GetServiceSDDL` Function**

We’ll create a **public static** function that:

- Accepts a `serviceName` string.
- Returns the SDDL string.
- Steps:
    1. Open SCM connection (`OpenSCManager`).
    2. Get a handle to the target service (`OpenService`).
    3. Query the service’s security descriptor (`QueryServiceObjectSecurity`).
    4. Convert it to a string (`ConvertSecurityDescriptorToStringSecurityDescriptor`).
    5. Return the string.

---

### **6. Declaring External Entry Points**

- Add `using System.Runtime.InteropServices;`
- Use `[DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Auto)]` for P/Invoke.
- Declare:
    - `OpenSCManager`
    - `OpenService`
    - `QueryServiceObjectSecurity`
    - `ConvertSecurityDescriptorToStringSecurityDescriptor`
    - `CloseServiceHandle`

---

### **7. Creating Enums for Access Rights**

- Define **Access Rights** as `[Flags] enum` so they can be combined with bitwise OR (`|`):
    - `SC_MANAGER_CONNECT` (0x0001)
    - `SC_MANAGER_ENUMERATE_SERVICE` (0x0004)
    - `READ_CONTROL` (0x00020000) — needed for `OpenService`

---

### **8. Using `OpenSCManager`**

- Parameters:
    - `lpMachineName` → `null` (local machine)
    - `lpDatabaseName` → `null` (default active services DB)
    - Desired access → combine `SC_MANAGER_CONNECT` and `SC_MANAGER_ENUMERATE_SERVICE`
- Check return value:
    - If `IntPtr.Zero`, call `Marshal.GetLastWin32Error()` to get error code.

---

### **9. Using `OpenService`**

- Parameters:
    - SCM handle
    - Service name
    - Desired access → `READ_CONTROL`
- Handle error **code 5 (Access Denied)** gracefully by skipping the service.

---

### **10. Querying the Security Descriptor**

- Call `QueryServiceObjectSecurity` **twice**:
    1. First with:
        - Null pointer for security descriptor
        - Buffer size = 0
        - Out `bytesNeeded`
            
            → This tells us how much memory to allocate.
            
    2. Allocate memory with `Marshal.AllocHGlobal(bytesNeeded)`.
    3. Call again, passing the allocated buffer.
- Flags used:
    - `OWNER_SECURITY_INFORMATION`
    - `GROUP_SECURITY_INFORMATION`
    - `DACL_SECURITY_INFORMATION`

---

### **11. Converting to SDDL String**

- Call `ConvertSecurityDescriptorToStringSecurityDescriptor`:
    - Pass pointer to security descriptor
    - Pass same security information flags
    - Provide a `StringBuilder` to hold the result
- Return `stringBuilder.ToString()` as the SDDL.

---

### **12. Memory and Handle Cleanup**

- Always close:
    - Service handle
    - SCM handle
- Free allocated memory with `Marshal.FreeHGlobal()`.

---

### **13. Error Handling**

- Wrap operations in `try/catch/finally`
    - **Catch** → handle specific errors like `Access Denied`.
    - **Finally** → ensure memory and handles are freed.

---

### **14. Testing the Utility**

- Compile and run the service scan tool.
- Confirm it:
    - Iterates through all services.
    - Prints the SDDL for those accessible.
    - Matches output from `sc sdshow <service>`.

---

### **15. Next Steps**

In the next lesson:

- We’ll **parse the SDDL** to determine what privileges the current user has.
- We’ll also integrate the tool into Apollo’s `execute-assembly` for in-memory execution.

---

✅ **End of Walkthrough** — At this point, students should:

- Understand why P/Invoke is necessary for this task.
- Know how to open SCM, open services, and retrieve security descriptors.
- See the importance of memory management in C# interop with Win32 APIs.

</section>
</div>
