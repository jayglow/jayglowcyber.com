---
layout: default
title: "Enumeration"
permalink: /solo-purple-teaming/enumeration/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Enumeration</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **Overview**

In this phase, we move into the **second stage** of Attack Path Level Zero — **Enumeration**.

After gaining **initial access** to a compromised system, the goal is to gain **situational awareness**. This involves discovering:

- What system we’re on
- Who we are (username and privileges)
- What else is running in the environment

Enumeration gives us the context needed to decide whether to proceed with:

- **Privilege escalation**
- **Lateral movement**
- **Target identification**

For a **solo purple teamer**, this simulates the reconnaissance an attacker would perform immediately after compromise, and helps train you to think like an adversary.

---

## **Step 1 – Starting Enumeration in Mythic**

1. **Open Mythic UI** and navigate to your active callback.
    - This is your communication channel with the compromised system.
2. **Check current user and privileges**:
    - Task Name: `shell`
    - Command:
        
        ```
        whoami /priv
        
        ```
        
    - This tells us:
        - The logged-in user
        - The privileges associated with that account
    - In this case, nothing overly interesting is found — but executing these commands simulates attacker behavior and creates telemetry artifacts for later detection.

---

## **Step 2 – Identify Local Administrators**

1. **Find members of the local Administrators group**:
    - Task Name: `shell`
    - Command:
        
        ```
        net localgroup administrators
        
        ```
        
    - Review the results for unusual accounts.
        - Here, we see **`svc_ecoin_sync`** — a service account in the Administrators group.

---

## **Step 3 – Enumerate Services**

We want to see if that `svc_ecoin_sync` account is tied to a service.

1. **List all services with start names** using WMI:
    - Task Name: `shell`
    - Command:
        
        ```
        wmic service get name,displayname,startname
        
        ```
        
    - Look for services running as **`svc_ecoin_sync`**.
    - We find:
        - **Service Name:** `ecoin sync`
        - **Display Name:** `Ecoin Sync`
        - **Runs as:** `ecoin\svc_ecoin_sync` (local admin privileges)

---

## **Step 4 – Investigate the Service**

We need more details about the vulnerable service.

1. **Query service configuration**:
    - Task Name: `shell`
    - Command:
        
        ```
        sc qc "ecoin sync"
        
        ```
        
    - Review:
        - **Binary Path:**
            
            ```
            C:\Users\rlyn\Desktop\ecoin_sync.exe
            
            ```
            
        - This binary is located in the current user’s desktop directory.

---

## **Step 5 – Check Service Permissions**

We want to see if the current user can modify or control this service.

1. **View the service’s security descriptor**:
    - Task Name: `shell`
    - Command:
        
        ```
        sc sdshow "ecoin sync"
        
        ```
        
    - Look for **Access Control Entries (ACEs)** that grant permissions to the current user.
2. **Find the current user’s SID** to match against ACEs:
    - Task Name: `shell`
    - Command:
        
        ```
        wmic useraccount where name="%username%" get name,sid
        
        ```
        
    - Compare the SID with entries from the `sc sdshow` output.
        - Here, the SID matches, confirming **`rlyn`** can **start and stop** the `ecoin sync` service.

---

## **Step 6 – Attacker Logic**

From an attacker’s perspective, the findings are:

1. **`svc_ecoin_sync`** is in the **Administrators group**.
2. This account runs the **`Ecoin Sync`** service.
3. The service binary is located in a directory **writable by the current user**.
4. The current user has **permissions to start/stop the service**.

This makes the service a **privilege escalation target**.

---

## **Key Takeaways**

- **Purpose:** Simulate realistic post-compromise enumeration to create telemetry artifacts for later detection engineering.
- **Findings in This Lab:**
    - `svc_ecoin_sync` (local admin)
    - Service binary located in writable path
    - Current user (`rlyn`) can control the service
- **Next Step:** Use this misconfiguration for **privilege escalation**.

---

✅ **Lab Checklist Completed:**

- [x]  Verified current user and privileges
- [x]  Enumerated local administrators
- [x]  Identified services and start accounts
- [x]  Located vulnerable binary path
- [x]  Confirmed user permissions on service

---

Next lecture: **Privilege Escalation with Vulnerable Service**

</section>
</div>
