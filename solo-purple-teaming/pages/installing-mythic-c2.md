---
layout: default
title: "Installing Mythic C2"
permalink: /solo-purple-teaming/installing-mythic-c2/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Installing Mythic C2</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# 🧰 Mythic C2 Installation Walkthrough

> This walkthrough installs Mythic on a Kali VM inside a **Proxmox lab;** however, Mythic also works on **Ubuntu 20.04 or later**.
> 

---

## 🔹 Step 1: Prepare Your Kali VM

> First, we need to prepare our system, to do that we need internet access."
> 
1. Open a terminal in Kali.
2. Navigate to `/opt`, the preferred location for optional tools.
    
    ```bash
    cd /opt
    
    ```
    
3. *(Optional)* You can group installs inside a dedicated directory:
    
    ```bash
    sudo mkdir -p /opt/mythic
    cd /opt/mythic
    
    ```
    

> You don’t need to make this directory. Cloning the repo will make it, but I like organizing things this way.
> 

![image.png](Installing%20Mythic%20C2/image.png)

---

## 🔹 Step 2: Clone the Mythic GitHub Repository

> The first thing we do is copy this clone command from the docs.
> 
1. Run the following command to clone the Mythic repo:
    
    ```bash
    sudo git clone https://github.com/its-a-feature/Mythic.git
    
    ```
    
2. Move into the cloned directory:
    
    ```bash
    cd Mythic
    
    ```
    

![image.png](Installing%20Mythic%20C2/image%201.png)

1. List the contents to verify the `Makefile` is present:
    
    ```bash
    ls
    
    ```
    

![image.png](Installing%20Mythic%20C2/image%202.png)

---

## 🔹 Step 3: Install Docker (If Needed)

> Ensure we have Docker installed.
> 
> 
> Mythic provides a helper script for Kali users:
> 
1. Run the install script provided in the repo:
    
    ```bash
    sudo ./install_docker_kali.sh
    
    ```
    
    ![image.png](Installing%20Mythic%20C2/image%203.png)
    

> If you're using a fresh Kali install, Docker might not be installed. This script handles it.
> 
> 
> If Docker is already installed, the script will exit cleanly.
> 

---

## 🔹 Step 4: Build Mythic with Make

> Now that we have Docker installed, we can use make in the Mythic directory where the makefile is located.
> 
1. Build Mythic using `make`:
    
    ```bash
    sudo make
    
    ```
    
2. Confirm the Mythic CLI binary was created:
    
    ```bash
    ls
    
    ```
    

> "You should now see the mythic CLI binary in the directory."
> 

![image.png](Installing%20Mythic%20C2/image%204.png)

---

## 🔹 Step 5: Start Mythic

> Now, when we run this, it will make a .env file with the passwords.
> 
> 
> We’re going to start Mythic with the CLI.
> 
1. Start Mythic using the CLI tool:
    
    ```bash
    sudo ./mythic-cli start
    
    ```
    

> It’s going to grab everything, download the containers, and then start them up. Wait until all Docker containers are downloaded and initialized.
> 

![image.png](Installing%20Mythic%20C2/image%205.png)

---

## 🔹 Step 6: Access the Mythic Web UI

> "We should be able to jump into our browser and go to localhost on port 7443..."
> 
1. In your Kali browser, navigate to:
    
    ```
    https://localhost:7443
    
    ```
    
2. If prompted, click **Advanced** and **Accept the Risk** (for the self-signed cert).

![image.png](Installing%20Mythic%20C2/image%206.png)

1. You’ll arrive at the Mythic login page.

![image.png](Installing%20Mythic%20C2/image%207.png)

---

## 🔹 Step 7: Get the Mythic Admin Password

> Now we need to get the default mythic admin password.
> 
1. Run the following to extract the password:
    
    ```bash
    cat .env | grep -i password
    
    ```
    

> The password is randomly generated. You’ll see something like MYTHIC_ADMIN_PASSWORD=XXXXXXXXXX.
> 
> 
> Copy this password.
> 
> ![image.png](Installing%20Mythic%20C2/image%208.png)
> 

---

## 🔹 Step 8: Log In to the Mythic UI

> The username is mythic_admin, and the password is the one we just copied.
> 
1. Use:
    - **Username**: `mythic_admin`
    - **Password**: (from the `.env` file)
2. Click **Log In**.

> Now we’re logged into Mythic. You can explore the UI, check out the documentation, and prepare for the next step.
> 

![image.png](Installing%20Mythic%20C2/image%209.png)

---

## ✅ Final Reminder Before the Next Lecture

> "Make sure you have Mythic installed and running in your Kali VM or whatever OS you're using."
> 
> 
> *"We’ll be using it in the next lecture to install the Apollo agent and generate payloads."*
> 
- Confirm you can:
    - Log into the web UI
    - View the interface
    - Browse through available options

---

##

</section>
</div>
