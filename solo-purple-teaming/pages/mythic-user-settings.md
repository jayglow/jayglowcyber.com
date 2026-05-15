---
layout: default
title: "Mythic User Settings"
permalink: /solo-purple-teaming/mythic-user-settings/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Mythic User Settings</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

In this lesson, we’ll cover how to **add and manage users** in Mythic, as well as how to **adjust personal preferences** such as font size and display settings.

---

### **1. Logging into Mythic**

- You can log into Mythic using either:
    - The **mythic admin** account, or
    - Another account you’ve created for daily use.
- In this example, the instructor is logging in with a **non-admin** account to demonstrate user management.

![image.png](Mythic%20User%20Settings/image.png)

---

### **2. Retrieving and Resetting the Mythic Admin Password**

If you’ve forgotten the admin password or want to set a new one, you can use the Mythic CLI tool.

**Steps:**

1. SSH into your Kali VM where Mythic is installed.
2. Navigate to the Mythic installation directory.
    
    Example:
    
    ```bash
    bash
    cd /opt/mythic/mythic
    
    ```
    
3. Use the Mythic CLI to retrieve the current admin credentials:
    
    ```bash
    sudo ./mythic-cli config get mythic_admin
    
    ```
    
    ![image.png](Mythic%20User%20Settings/image%201.png)
    
    - This will display:
        - `mythic_admin_user`
        - `mythic_admin_password`
4. To change the password, run:
    
    ```bash
    sudo ./mythic-cli config set mythic_admin_password mythicpassword
    
    ```
    
    ![image.png](Mythic%20User%20Settings/image%202.png)
    
    - Replace `mythicpassword` with your desired password.
5. Verify the change:
    
    ```bash
    sudo ./mythic-cli config get mythic_admin
    
    ```
    
6. You can now log into Mythic using the updated admin credentials.

---

### **3. Accessing User Settings in the UI**

Once logged in:

1. In the **top right corner** of the Mythic interface, click the **person icon with a gear**.

![image.png](Mythic%20User%20Settings/image%203.png)

1. Click your username.
    - This will open the **Users** page.

---

### **4. Managing Users**

- The Users page lists:
    - Bot accounts
    - The `mythic_admin` account
    - Your currently logged-in user
    
    ![image.png](Mythic%20User%20Settings/image%204.png)
    
- From here, you can:
    - **Add new users** (recommended: create a secondary admin account so you don’t always need to use `mythic_admin`).
    - **Assign different roles and permissions**.

---

### **5. Adjusting Time and Font Settings**

- **Time Setting:**
    - Toggle **UTC** time display on or off depending on your preference.
- **Font Settings:**
    1. Increase the font size for easier readability (e.g., set to `16`).
    2. Keep or change the font family.
    3. Adjust other visual settings such as **color schema**.

> Tip: Larger fonts can significantly improve readability, especially if you’re working long hours in the UI.
> 

---

### **6. Preparing for the Next Module**

Before moving on:

- Ensure you have a Mythic user ready for **Operation: Attack Path Level Zero**.
    - You can use the `mythic_admin` account or a custom account you created.
- Customize the UI to match your preferences:
    - Fonts
    - Colors
    - Time format

---

**Next Step:**

In the next lecture, we’ll cover **operations** in Mythic — including command blocks, lists, and how to create your new operation for Attack Path Level Zero.

</section>
</div>
