---
layout: default
title: "Adding sec_ecoin_sync to Domain Admins"
permalink: /solo-purple-teaming/adding-sec-ecoin-sync-to-domain-admins/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Adding sec_ecoin_sync to Domain Admins</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Objective**

In this lab step, we’re going to make a small configuration change so we can easily pivot from our Linux (LIN) workstation to the Domain Controller (DC).

We’ll do this by temporarily adding the `svc_ecoin_sync` service account to the **Domain Admins** group.

> Note: This is not good security practice in a real environment. Adding a service account to Domain Admins significantly increases risk. Here, we’re only doing it to simplify our Level 1 exercise. In later levels, you’ll work with more realistic (and restricted) scenarios.
> 

---

### **Steps**

### **1. Run Command Prompt as Administrator**

- Click **Start** and type `cmd`.
- **Right-click** on Command Prompt and select **Run as administrator**.
- This gives you the elevated permissions needed to make changes in Active Directory.

---

### **2. Open the Microsoft Management Console (MMC)**

- In the Command Prompt window, type:
    
    ```bash
    mmc
    
    ```
    
    and press **Enter**.
    
- This opens the **Microsoft Management Console**.

---

### **3. Add the "Active Directory Users and Computers" Snap-in**

- Inside MMC, go to:
    - **File** → **Add/Remove Snap-in…**
- Select **Active Directory Users and Computers** from the left list.
- Click **Add →**, then **OK**.

---

### **4. Locate the `svc_ecoin_sync` Account**

- Expand your domain in the left-hand panel.
- Click on **Users**.
- Find the account named:
    
    ```
    svc_ecoin_sync
    
    ```
    
    (This is the service account we’ll modify.)
    

---

### **5. Add the Account to Domain Admins**

- **Right-click** `svc_ecoin_sync` → **Properties**.
- Go to the **Member Of** tab.
- Click **Add…**
- In the dialog box, type:
    
    ```
    Domain Admins
    
    ```
    
    - If needed, use the format:
        
        ```
        ecoin\Domain Admins
        
        ```
        
- Click **Check Names** to verify the group name.
- Click **OK**, then **Apply**, and finally **OK** again.

---

### **6. Verify Membership**

- Open Command Prompt (Administrator).
- To check your current logged-in account:
    
    ```bash
    net user <your-username> /domain
    
    ```
    
- To check the service account:
    
    ```bash
    net user svc_ecoin_sync /domain
    
    ```
    
- Confirm that **Domain Admins** is listed under **Group Memberships** for `svc_ecoin_sync`.

---

### **7. Proceed to Next Steps**

- You’ve now added `svc_ecoin_sync` to Domain Admins.
- This will make Level 1 challenges easier, since you won’t need to escalate privileges from this account.
- In later levels, this shortcut will be removed, and you’ll need to perform privilege escalation manually.

---

✅ **Checkpoint:**

Before moving on, ensure `svc_ecoin_sync` appears in the **Domain Admins** group when you run:

```bash
net user svc_ecoin_sync /domain

```

</section>
</div>
