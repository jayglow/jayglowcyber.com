---
layout: default
title: "Red Team Layout - Writing A Service Enumeration To"
permalink: /solo-purple-teaming/red-team-layout-writing-a-service-enumeration-to-2/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Writing A Service Enumeration To</h1>
</section>
<section class="spt-content">

# Red Team Layout - Writing A Service Enumeration Tool - Part 4

Owner: Mike Sterrett

### **1. Introduction**

In this final stage of building our **Service Scan Utility**, we’re going to:

- Review some additional **.NET classes** that will help us complete the functionality.
- Write the code to:
    - Retrieve the binary path of a Windows service.
    - Check whether the current user can **modify the service binary**.
- Test the utility on a vulnerable target.

---

### **2. Key .NET Classes for Our Service Scan**

### **WindowsIdentity (System.Security.Principal)**

- Represents the identity of a Windows user.
- Can be instantiated using:
    - The current process token
    - A UPN (User Principal Name)
    - An explicit token handle
- **Key Properties:**
    - `Name` – Username
    - `IsAuthenticated` – Boolean flag for authentication status
    - `User` – Security Identifier (SID) of the current user
    - `Groups` – List of group SIDs the user belongs to
- **Why we use it:**
    
    We’ll retrieve the current user’s SID and group SIDs to compare against the Access Control Entries (ACEs) in the Discretionary Access Control List (DACL) of the service binary.
    

---

### **ManagementObjectSearcher (System.Management)**

- Executes **WMI queries** using WQL (WMI Query Language).
- Returns a **ManagementObjectCollection** to iterate over.
- **Why we use it:**
    
    To query the `Win32_Service` class and extract the `PathName` property, which gives the full path to the service binary.
    

---

### **FileInfo (System.IO)**

- Provides an object-oriented way to work with files.
- Properties: full path, size, timestamps, etc.
- Can also perform file operations (copy, delete, open stream).
- **Why we use it:**
    
    To target the service binary file and retrieve its `FileSecurity` object for DACL inspection.
    

---

### **FileSecurity (System.Security.AccessControl)**

- Represents a file’s **security descriptor**, including its DACL.
- `GetAccessRules` returns a collection of ACEs.
- ACEs specify:
    - A SID
    - Granted/denied access rights (read, write, full control)
- **Why we use it:**
    
    To compare current user/group SIDs against ACEs to see if write permissions are allowed.
    

---

### **3. Coding the Service Binary Path Retrieval**

**Function:** `GetServiceBinaryPath(string serviceName)`

**Steps:**

1. Initialize a `path` string to `null`.
2. Build a **WQL query**:
    
    ```csharp
    SELECT PathName FROM Win32_Service WHERE Name = 'ServiceName';
    
    ```
    
3. Create a `ManagementObjectSearcher` with this query.
4. Retrieve results with `searcher.Get()`.
5. Iterate over results and extract the `PathName`:
    - Use a **null-conditional check** with `?` to avoid errors.
    - Convert to string if not null.
6. Return the `path`.

---

### **4. Checking if the User Can Modify the Service Binary**

**Function:** `CanModifyServiceBinary(string pathName)`

**Steps:**

1. Wrap the logic in **error handling** — if anything fails, return `false`.
2. Create a `FileInfo` object for the `pathName`.
3. If the file exists:
    - Retrieve its DACL with `GetAccessControl()`.
    - Get all ACEs using:
        
        ```csharp
        dacl.GetAccessRules(true, true, typeof(SecurityIdentifier))
        
        ```
        
4. Get the current user SID via:
    
    ```csharp
    WindowsIdentity currentUser = WindowsIdentity.GetCurrent();
    
    ```
    
5. **Compare permissions:**
    - Iterate over ACEs:
        - Cast `IdentityReference` to `SecurityIdentifier`.
        - If SID matches **current user** or any **current user’s groups**:
            - Perform a **bitwise AND** on `ace.FileSystemRights` with `FileSystemRights.WriteData`.
            - Ensure `AccessControlType` is `Allow`.
            - If both conditions are true → return `true`.
6. If no match found → return `false`.

---

### **5. Integrating Into the Main Function**

- After retrieving the service path with `GetServiceBinaryPath`:
    - Call `CanModifyServiceBinary(path)`.
    - If **both**:
        - The service can be **started**.
        - The binary can be **modified**.
        - → Display the service as vulnerable.

---

### **6. Running and Testing**

1. **Build the project** and fix any syntax errors (often missing braces or misplaced parentheses).
2. Run the utility:
    - It will iterate through **all services**.
    - For each:
        - Check if the current user can **start** it.
        - Retrieve the binary path.
        - Check if the user can **modify** the binary.
    - This process can be **slow** because it’s thorough.
3. On the **Zoom breach host**:
    - Upload the binary via SCP to the staging server, then to the Downloads folder.
    - Run the service scan utility.
    - Example result:
        
        Found `ecoin sync` service → can both start and modify binary → **vulnerable service**.
        

---

### **7. Student Task**

- Run your own service scan utility.
- Confirm it:
    - Iterates through all services.
    - Checks start permission.
    - Checks modify permission on service binaries.
- Identify at least **one vulnerable service**.

---

### **8. Next Steps**

In the next lecture, we’ll cover **Apollo’s `execute-assembly`** command so we can:

- Register our service scan utility.
- Run it **in-memory** for stealth.

Final Code:

```csharp
using System;
using System.ServiceProcess;
using System.Runtime.InteropServices;
using System.Text;
using System.Security.Principal;
using System.Security.AccessControl;
using System.Management;
using System.Diagnostics.Eventing.Reader;
using System.IO
using System.Security;

namespace ServiceScanDemo
{
    internal class Program
    {
        [Flags]
        private enum AccessRights : uint
        {
            SC_MANAGER_CONNECT = 0x00000001,
            SC_MANAGER_ENUMERATE_SERVICE = 0x00000004,
            READ_CONTROL = 0x0020000
        }

        [Flags]
        enum SECURITY_INFORMATION : uint
        {
            OWNER_SECURITY_INFORMATION = 0x00000001,
            GROUP_SECURITY_INFORMATION = 0x00000002,
            DACL_SECURITY_INFORMATION = 0x00000004,
            SACL_SECURITY_INFORMATION = 0x00000008,
            UNPROTECTED_SACL_SECURITY_INFORMATION = 0x10000000,
            UNPROTECTED_DACL_SECURITY_INFORMATION = 0x20000000,
            PROTECTED_SACL_SECURITY_INFORMATION = 0x40000000,
            PROTECTED_DACL_SECURITY_INFORMATION = 0x80000000
        }

        // CloseServiceHandle
        [DllImport("advapi32.dll", SetLastError = true)]
        [return: MarshalAs(UnmanagedType.Bool)]
        private static extern bool CloseServiceHandle(IntPtr hSCObject);

        [DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Auto)]
        private static extern IntPtr OpenSCManager(
          string lpMachineName,
          string lpDatabaseName,
          uint dwDesiredAccess
        );

        [DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Auto)]
        private static extern IntPtr OpenService(
          IntPtr hSCManager,
          string lpServiceName,
          uint dwDesiredAccess
        );

        [DllImport("advapi32.dll", SetLastError = true)]
        [return: MarshalAs(UnmanagedType.Bool)]
        private static extern bool QueryServiceObjectSecurity(
            IntPtr hService,
            uint dwSecurityInformation,
            IntPtr lpSecurityDescriptor,
            uint cbBufSize,
            out uint pcbBytesNeeded
        );

        private const uint SDDL_REVISION_1 = 1;
        [DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Auto)]
        [return: MarshalAs(UnmanagedType.Bool)]
        private static extern bool ConvertSecurityDescriptorToStringSecurityDescriptor(
            IntPtr SecurityDescriptor,
            uint RequestedStringSDRevision,
            uint SecurityInformation,
            out StringBuilder StringSecurityDescriptor,
            out uint StringSecurityDescriptorLen
        );

        public static string GetServiceSDDL(string serviceName)
        {
            // open connection to the SCM using OpenSCManager Win API call that lives advapi32.dll
            IntPtr scmHandle = OpenSCManager(null, null, (uint)(AccessRights.SC_MANAGER_ENUMERATE_SERVICE | AccessRights.SC_MANAGER_CONNECT));
            if (scmHandle == IntPtr.Zero)
            {
                throw new Exception($"Failed to connect to SCM. ERROR: {Marshal.GetLastWin32Error()}");
            }

            // Get handle to service using OpenService Win API call that lives in advapi32.dll
            IntPtr serviceHandle = IntPtr.Zero;
            IntPtr sdPtr = IntPtr.Zero;
            StringBuilder sddlStringBuilder = null;
            try
            {
                serviceHandle = OpenService(scmHandle, serviceName, (uint)AccessRights.READ_CONTROL);
                // Query the service object for the security descriptor and copy into memory
                // Query serviceHandle object for security descriptor using QueryServiceObjectSecurity
                uint bytesNeeded = 0;
                QueryServiceObjectSecurity(serviceHandle, (uint)(SECURITY_INFORMATION.DACL_SECURITY_INFORMATION | SECURITY_INFORMATION.OWNER_SECURITY_INFORMATION | SECURITY_INFORMATION.GROUP_SECURITY_INFORMATION), IntPtr.Zero, 0, out bytesNeeded);
                sdPtr = Marshal.AllocHGlobal((int)bytesNeeded);

                QueryServiceObjectSecurity(serviceHandle, (uint)(SECURITY_INFORMATION.DACL_SECURITY_INFORMATION | SECURITY_INFORMATION.OWNER_SECURITY_INFORMATION | SECURITY_INFORMATION.GROUP_SECURITY_INFORMATION), sdPtr, bytesNeeded, out bytesNeeded);

                // Convert the security descriptor to string and return
                // Get Security Descriptor

                uint sddlStringLen = 0;
                ConvertSecurityDescriptorToStringSecurityDescriptor(
                    sdPtr,
                    SDDL_REVISION_1,
                    (uint)(SECURITY_INFORMATION.DACL_SECURITY_INFORMATION | SECURITY_INFORMATION.OWNER_SECURITY_INFORMATION | SECURITY_INFORMATION.GROUP_SECURITY_INFORMATION),
                    out sddlStringBuilder,
                    out sddlStringLen
                    );

            }
            catch
            {
                int error = Marshal.GetLastWin32Error();
                if (error != 5)
                {
                    throw new Exception($"Could not get handle to service. ERROR: {error}");
                }
                else
                {
                    Console.WriteLine($"Access Denied to service {serviceName}");
                    return null;
                }
            }
            finally
            {
                // Clean UP
                if (sdPtr != IntPtr.Zero)
                {
                    Marshal.FreeHGlobal(sdPtr);
                }
                if (serviceHandle != IntPtr.Zero)
                {
                    CloseServiceHandle(serviceHandle);
                }
                if (scmHandle != IntPtr.Zero)
                {
                    CloseServiceHandle(scmHandle);
                }
            }
            if (sddlStringBuilder != null)
            {
                return sddlStringBuilder.ToString();
            }
            else
            {
                return null;
            }

        }

        public static bool CanStartService(string sddl)
        {
            if (sddl != null)
            {
                WindowsIdentity currentUser = WindowsIdentity.GetCurrent();
                RawSecurityDescriptor rsd = new RawSecurityDescriptor(sddl);
                foreach (CommonAce ace in rsd.DiscretionaryAcl)
                {
                    if (ace.AceType == AceType.AccessAllowed)
                    {
                        if (currentUser.User == ace.SecurityIdentifier || currentUser.Groups.Contains(ace.SecurityIdentifier))
                        {
                            return true;
                        }
                    }
                }
            }

            return false;
        }

        public static string GetServiceBinaryPath(string serviceName)
        {
            string path = null;
            string query = $"SELECT PathName FROM Win32_Service WHERE Name = '{serviceName}'";
            ManagementObjectSearcher searcher = new ManagementObjectSearcher(query);
            ManagementObjectCollection results = searcher.Get();
            foreach (ManagementObject result in results)
            {
                path = result["PathName"]?.ToString();
            }
            return path;
        }

        public static bool CanModifyServiceBinary(string pathName)
        {
            try
            {
                FileInfo fileInfo = new FileInfo(pathName);
                if (fileInfo.Exists)
                {
                    FileSecurity dacl = fileInfo.GetAccessControl();
                    WindowsIdentity currentUser = WindowsIdentity.GetCurrent();
                    AuthorizationRuleCollection aces = dacl.GetAccessRules(true, true, typeof(SecurityIdentifier));
                    foreach(FileSystemAccessRule ace in aces)
                    {
                        var sid = (SecurityIdentifier)ace.IdentityReference.Translate(typeof(SecurityIdentifier));
                        if(currentUser.User != null && sid == currentUser.User)
                        {
                            if((ace.FileSystemRights & FileSystemRights.WriteData) == FileSystemRights.WriteData 
                                   && ace.AccessControlType == AccessControlType.Allow) {
                                return true;
                            }
                            if (currentUser.Groups.Contains(sid))
                            {
                                if ((ace.FileSystemRights & FileSystemRights.WriteData) == FileSystemRights.WriteData
                                   && ace.AccessControlType == AccessControlType.Allow)
                                {
                                    return true;
                                }
                            }
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                return false;
            }

            return false;
        }

        static void Main(string[] args)
        {
            ServiceController[] services = ServiceController.GetServices();
            foreach (ServiceController service in services)
            {
                string sddl = GetServiceSDDL(service.ServiceName);
                if (CanStartService(sddl))
                {
                    
                    if (CanModifyServiceBinary(GetServiceBinaryPath(service.ServiceName))){
                        Console.WriteLine($"Display Name: {service.DisplayName}");
                        Console.WriteLine($"Start Type: {service.StartType}");
                        Console.WriteLine($"Status: {service.Status}");
                        Console.WriteLine($"Service Name: {service.ServiceName}");
                        Console.WriteLine(sddl);
                        Console.WriteLine("Can Start Service!");
                        Console.WriteLine("Can Modify Service Binary");
                        Console.WriteLine();
                    }
                }
            }
        }
    }
}

```

</section>
</div>
