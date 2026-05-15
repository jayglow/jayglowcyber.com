---
layout: default
title: "Create Vulnerable Service Binary"
permalink: /solo-purple-teaming/create-vulnerable-service-binary/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Create Vulnerable Service Binary</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **1. Lab Context**

![image.png](Create%20Vulnerable%20Service%20Binary/image.png)

- **Domains**:
    - **E Corp** – parent domain
    - **E Coin** – child domain of E Corp
- **Assumed Breach Host**:
    - Hostname: `WRK-RLIN`
    - IP: `10.0.2.100`
- **Lab Networking**:
    - Three edge devices (E Corp, E Coin, Attack LAN)

---

### **2. Objective**

You will:

1. Create a **service account**.
2. Create a **Group Policy** to give that account admin privileges and log-on rights.
3. Write a **Windows service binary** (placeholder) that we can later replace with a malicious payload.
4. Configure the service permissions so another user can start/stop it.

---

### **3. Remote Administration Setup**

- **Why**: RSAT tools allow remote Active Directory and Group Policy management.
- **Install RSAT Tools** via PowerShell:

```powershell
Add-WindowsCapability -Online -Name RSAT:ActiveDirectory.DS-LDS.Tools

```

- Launch **MMC** as Administrator.
- Add **Active Directory Users and Computers** snap-in.

---

### **4. Create the Service Account**

1. In **Active Directory Users and Computers**, right-click `Users` → **New → User**.
2. **Name**:
    - First: `ecoin`
    - Last: `sync`
    - Username: `svc_ecoin_sync`
3. **Password Settings**:
    - Uncheck: *User must change password at next logon*
    - Check: *Password never expires*
4. Verify account creation in the `Users` container.

---

### **5. Grant Admin Privileges via Group Policy**

- Install GPO RSAT tools:

```powershell
Add-WindowsCapability -Online -Name RSAT:GroupPolicy.Management.Tools

```

- Add **Group Policy Management** snap-in to MMC.
- Edit **Default Domain Policy**:
    1. Navigate: `Computer Configuration → Preferences → Control Panel Settings → Local Users and Groups`.
    2. Add **New → Local Group**: `Administrators`.
    3. Add the service account:
        
        ```
        Ecoin\svc_ecoin_sync
        
        ```
        
    4. Apply and close.
- **Force GPO Update** on WRK-RLIN:

```bash
gpupdate /force

```

- Verify membership:

```bash
net localgroup administrators

```

Confirm `Ecoin\svc_ecoin_sync` is listed.

---

### **6. Allow "Log on as a Service"**

1. Edit **Default Domain Policy** again.
2. Navigate: `Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment`.
3. Add `Ecoin\svc_ecoin_sync` to **Log on as a service**.
4. Force GPO update:

```bash
gpupdate /force

```

---

### **7. Create the Vulnerable Service Binary**

1. Open **Visual Studio / Visual Studio Code** on WRK-RLIN (or any dev machine).
2. Create a **New Project**:
    - Type: **Windows Service (.NET Framework)**
    - Name: `EcoinSync`
3. Leave default `OnStart` and `OnStop` methods in `Service1.cs`.
4. Change build configuration to:
    - **Release**
    - **x64 Architecture**
5. Build solution → locate `.exe` in output folder → copy to Desktop.
6. **Note**: Running it directly will fail — Windows service binaries must be started via Service Control Manager.

---

### **8. Create the Service**

In an **elevated Command Prompt**:

```bash
sc create "EcoinSync" binPath= "C:\Users\rlin\Desktop\EcoinSync.exe" start= demand obj= "Ecoin\svc_ecoin_sync" password= "PasswordHere"

```

- Verify:

```bash
sc qc EcoinSync

```

- Start/Stop the service:

```bash
sc start EcoinSync
sc stop EcoinSync

```

---

### **9. Delegate Start/Stop Rights to Another User**

We want `Ecoin\rlin` to start/stop the service.

1. **Get Current SDDL**:

```bash
sc sdshow EcoinSync

```

1. **Find SID for rlin** in PowerShell:

```powershell
(New-Object System.Security.Principal.NTAccount("Ecoin\rlin")).Translate([System.Security.Principal.SecurityIdentifier]).Value

```

1. **Build ACE**:
    - `RP` = Start permission
    - `WP` = Stop permission
        
        Format: `(A;;RPWP;;;SID_HERE)`
        
2. **Set New SDDL**:

```bash
sc sdset EcoinSync "SDDL_PART1(A;;RPWP;;;SID_HERE)SDDL_PART2"

```

---

### **10. Test Delegation**

- **As Admin**:

```bash
sc start EcoinSync

```

- **As rlin** (non-admin):

```bash
sc stop EcoinSync

```

Confirm it works.

---

### **11. Lab Completion Checklist**

✅ Service account `svc_ecoin_sync` created.

✅ Added to Local Admins via GPO.

✅ Granted "Log on as a Service" rights.

✅ Placeholder Windows service binary created.

✅ Service created with SC pointing to binary.

✅ Delegated start/stop permissions to a non-admin user.

---

In the **next phase**, you’ll replace this placeholder binary with a malicious payload to escalate privileges from a standard domain account to local administrator.

</section>
</div>
