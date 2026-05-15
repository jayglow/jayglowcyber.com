---
layout: default
title: "Building Advanced Correlation Engine - Part 4"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-4/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 4</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **1. Roadmap Overview**

We’ve already:

- Built our advanced correlation engine.
- Logged correlation events to `correlation.json`.

Today we will:

1. Fix permissions on `/opt/wazuh-tools`.
2. Set up the Python script as a Linux `systemd` service.
3. Configure log rotation for `correlation.json`.
4. Instruct Wazuh to monitor `correlation.json`.
5. Write a custom decoder and rule so Wazuh can parse and alert on correlation events.

---

### **2. Fix Directory Permissions**

The Wazuh service user must own `/opt/wazuh-tools` so it can write logs.

```bash
cd /opt
ls -al
# Root currently owns wazuh-tools
sudo chown -R wazuh:wazuh wazuh-tools
ls -al wazuh-tools

```

✅ Now `wazuh` owns the directory and script.

---

### **3. Create a Systemd Service for the Engine**

We’ll run the correlation engine as a background service.

1. Create the service file:

```bash
sudo vi /etc/systemd/system/wazuh-ace.service

```

1. Add:

```
[Unit]
Description=Wazuh Advanced Correlation Engine
After=network.target

[Service]
WorkingDirectory=/opt/wazuh-tools
ExecStart=/usr/bin/python3 /opt/wazuh-tools/wazuh_ace.py
User=wazuh
Group=wazuh
Restart=always
RestartSec=5
StandardOutput=append:/var/log/wazuh-ace/ace.out
StandardError=append:/var/log/wazuh-ace/ace.out

[Install]
WantedBy=multi-user.target

```

---

### **4. Create Log Directory for the Service**

```bash
sudo mkdir -p /var/log/wazuh-ace
sudo chown wazuh:wazuh /var/log/wazuh-ace

```

---

### **5. Start and Enable the Service**

```bash
sudo systemctl daemon-reload
sudo systemctl start wazuh-ace
sudo systemctl status wazuh-ace

```

✅ Service is running.

---

### **6. Test Log Writing**

```bash
tail -f /opt/wazuh-tools/correlation.json

```

Trigger an event (e.g., run the initial access payload on the assumed breach host) and verify the log is updated.

---

### **7. Configure Log Rotation**

**Why:** Prevents `correlation.json` from consuming all disk space.

1. Create a config:

```bash
sudo vi /etc/logrotate.d/wazuh-ace

```

1. Add:

```
/opt/wazuh-tools/correlation.json {
    size 10M
    rotate 10
    compress
    copytruncate
}

```

1. Test rotation:

```bash
sudo logrotate -f /etc/logrotate.d/wazuh-ace

```

✅ `correlation.json` is now empty, backups stored as `.gz`.

---

### **8. Configure Wazuh to Monitor the Log**

1. Edit the main config:

```bash
sudo vi /var/ossec/etc/ossec.conf

```

1. Add inside `<localfile>` section:

```xml
<localfile>
    <log_format>json</log_format>
    <location>/opt/wazuh-tools/correlation.json</location>
</localfile>

```

1. Restart Wazuh Manager:

```bash
sudo systemctl restart wazuh-manager

```

---

### **9. Add Event Type to Python Script**

Modify `wazuh_ace.py` to tag correlation events:

```python
"event_type": "correlation"

```

Restart service:

```bash
sudo systemctl restart wazuh-ace

```

---

### **10. Verify Decoder Functionality**

- In Wazuh dashboard, go to **Server Management → Decoders**.
- Use the **JSON default decoder** (`0006-json_decoders.xml`) to parse events.
- Paste a sample correlation event in the decoder test and confirm all fields are parsed.

---

### **11. Write a Custom Rule**

1. Go to **Server Management → Rules**.
2. Create new file: `correlation-alerts.xml`.
3. Add:

```xml
<group name="correlation-alerts">
  <rule id="100500" level="12">
    <decoded_as>json</decoded_as>
    <field name="event_type">correlation</field>
    <match type="pcre2">.*c2.*</match>
    <description>Possible C2 activity detected</description>
    <group>attack,suspicious,c2</group>
  </rule>
</group>

```

1. Restart Wazuh Manager:

```bash
sudo systemctl restart wazuh-manager

```

---

### **12. Test the Rule**

- Trigger a correlation event.
- Check Wazuh dashboard — alert should appear as:
    
    **"Possible C2 activity detected"**
    
    with all parsed fields (`event_type`, `hostname`, `image`, etc.).
    

---

### **13. Refresh Field Mapping**

If you see a “no mapping” warning for `event_type`:

- Go to **Dashboards → Management → Index Patterns**.
- Refresh fields for the `alerts` index.

---

✅ **End Result:**

Your **custom advanced correlation engine** now:

- Runs automatically as a background service.
- Rotates logs to prevent disk overflow.
- Sends events to Wazuh for parsing.
- Generates alerts in the dashboard for correlation matches.

</section>
</div>
