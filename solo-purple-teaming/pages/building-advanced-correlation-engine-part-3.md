---
layout: default
title: "Building Advanced Correlation Engine - Part 3"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-3/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 3</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **Objective**

In this lab, we will modify our **Advanced Correlation Engine** so that correlation alerts are written to a log file in **JSON format**.

This will allow us to:

- Monitor the alerts with the Wazuh Manager.
- Create a **custom decoder**.
- Parse these fields to generate custom log events.
- Visualize correlation alerts in a dashboard.

---

### **1. Preparing the Environment**

1. Connect to the **Wazuh Manager** via SSH.
2. Navigate to the tools directory:
    
    ```bash
    cd /opt/wazuh-tools
    
    ```
    
3. Open the **Advanced Correlation Engine** script for editing:
    
    ```bash
    sudo vi waza_ace.py
    
    ```
    

---

### **2. Adding JSON Log Output**

We’ll modify the section of the script where the alert is matched and printed to also write it to a file.

### **2.1 Open the file for writing**

- Use Python’s `with open()` to create or append to a log file:
    
    ```python
    with open("correlation.json", "a") as log_file:
    
    ```
    
- We are placing the log in the current directory for now (shortcut), though `/var/log` would be the ideal location.
- **Note:** This directory is protected — running the script will require `sudo`.

### **2.2 Write the JSON object**

- Use `json.dump()` to write a dictionary of values:
    
    ```python
    json.dump({
        "timestamp": ts.isoformat(),
        "processGuid": guid,
        "ruleA": rule_a,
        "ruleB": rule_b,
        "hostName": host_name,
        "image": image,
        "message": f"Correlated possible C2 activity on host {host_name} from image {image} with process GUID {guid}"
    }, log_file)
    log_file.write("\n")
    
    ```
    
- **Why these fields?**
    - `timestamp` – When the alert occurred.
    - `processGuid` – Unique process identifier.
    - `ruleA` and `ruleB` – Both base rules must have fired.
    - `hostName` – System involved.
    - `image` – The process image file.
    - `message` – Readable correlation description.
- **Note on fields:**
    
    Destination IP and port are excluded for now because some alerts (like image loads) don’t have them.
    
    We’ll handle that in a future refactor.
    

---

### **3. Saving and Running the Script**

1. Save your changes and exit `vi`.
2. Run the script with sudo:
    
    ```bash
    sudo python3 waza_ace.py
    
    ```
    
3. The script will now listen for events and append correlation alerts to `correlation.json`.

---

### **4. Testing the Setup**

1. Trigger a known correlation event by running the **initial access payload** on the assumed-breached host.
2. Check Wazuh’s Discover/Overview tabs for the correlated rule IDs.
    - Remember: In a **lab environment**, there can be lag before events appear.
3. If no events show, press **CTRL+C** to stop the script, then check the file:
    
    ```bash
    cat correlation.json
    
    ```
    
4. You should see a JSON entry for the correlation event.

---

### **5. Verifying JSON Format**

- Confirm that:
    - The log entry is valid JSON.
    - Special characters like `{}` inside strings are enclosed in quotes.
    - Each event is on its own line (due to `\n` after `json.dump()`).

---

### **6. Possible Refactoring for Future**

- Instead of passing tuples, store event data in a **dictionary** and iterate over it to merge more fields (e.g., destination IP, additional image loads) from both base events.
- This will allow for richer correlation data in the alert.

---

### **7. Next Steps**

In the next lecture, we will:

1. Set up the correlation engine script as a **service** so it runs automatically on server start.
2. Configure the Wazuh Manager to monitor the `correlation.json` file.
3. Write a **custom decoder** to ingest these JSON alerts into Wazuh.
4. Create a dashboard visualization for correlation alerts.

---

✅ **Before continuing**: Make sure your script is correctly writing to `correlation.json` and that at least one test event has been logged.

</section>
</div>
