---
layout: default
title: "Building Advanced Correlation Engine - Part 1"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-1/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 1</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Understanding the Problem: Correlation by Coincidence**

- **Definition**: Correlation by coincidence happens when events are tied together just because they occur close in time or share superficial similarities.
- **Example**: Detecting a process start + network connection within 2 seconds could flag suspicious behavior — but also normal system updates.
- **Issue**:
    - Leads to false positives.
    - Causes alert fatigue.
    - Misses real threats.

**Key takeaway**: Build correlation based on *meaningful relationships* between events, not just time proximity.

---

### **2. Correlation by Relationships**

- **Better methods**:
    - **Process GUID correlation** – Link events from the same process to reconstruct execution flows.
    - **Parent-child process relationships** – e.g., `cmd.exe` spawned from `winword.exe` → possible malicious macro.
    - **File hash correlation** – Tie together events involving the same file.
    - **User-based correlation** – Track a single account across multiple hosts or activities.

**Example**:

If the same process GUID triggered:

- **Rule 100100** – Unsigned binary loaded from Downloads.
- **Rule 100101** – Network connection to suspicious port.
    
    …within 2 seconds → High-fidelity detection.
    

---

### **3. Advanced Time-Based and User-Based Correlation**

- **Time-based**:
    - Group repeated failed logins → successful compromise.
    - Detect rapid lateral movement.
- **User-based**:
    - Service accounts doing unusual actions.
    - Non-admin suddenly using admin tools like PsExec.

---

### **4. Advanced Correlation Engine (ACE) Architecture**

- **Purpose**: Extend Wazuh’s built-in detection capabilities.
- **How it works**:
    1. Monitors `alerts.json` in real-time.
    2. Extracts key fields: `rule_id`, `timestamp`, `process_guid`.
    3. Buffers alerts in memory grouped by process GUID.
    4. Correlates if **Rule A (100100)** and **Rule B (100101)** fire for the same process within a time window.
    5. Generates a correlated alert → feeds it back into Wazuh.

---

### **5. Setting Up the Python Script**

**Location**: `/opt/wazuh_tools/wazuh_ace.py`

**Imports**:

```python
import json
import time
from collections import defaultdict, deque
from dateutil import parser as dt_parser

```

- `json` → parse Wazuh alert data.
- `time` → sleep & timing.
- `defaultdict` + `deque` → store recent events.
- `dt_parser` → convert timestamps into comparable objects.

**Constants**:

```python
ALERTS_FILE = "/var/ossec/logs/alerts/alerts.json"
RULE_A = 100100
RULE_B = 100101
TIME_WINDOW = 2  # seconds

```

**Event Buffer**:

```python
event_buffer = defaultdict(lambda: deque(maxlen=50))

```

Stores tuples of `(timestamp, rule_id)` for each process GUID.

---

### **6. Parsing Alerts**

Function: `parse_alert(line)`

1. Parse JSON from each log line.
2. Extract:
    - `rule_id`
    - `process_guid` (`ProcessGuid` in `win.eventdata`)
    - `timestamp`
3. Return `(rule_id, process_guid, timestamp)` or `(None, None, None)` if invalid.
4. Use `try/except` to handle malformed JSON without crashing.

---

### **7. Main Correlation Function**

Function: `correlate_events()`

1. Open `alerts.json` in **read mode**.
2. Seek to the **end of file**.
3. Loop indefinitely (`while True`):
    - Read a new line.
    - If no new line → `time.sleep(0.5)` and retry.
    - Parse the alert.
    - If `rule_id` or `guid` missing → skip.
4. Append `(timestamp, rule_id)` to the buffer for that GUID.
5. Retrieve all events for that GUID → filter those within `TIME_WINDOW`.
6. If both **RULE_A** and **RULE_B** exist in recent events → print correlated alert.

---

### **8. Running the Script**

In `__main__` block:

```python
if __name__ == "__main__":
    print(f"Monitoring {ALERTS_FILE} for correlations...")
    correlate_events()

```

Make executable:

```bash
sudo chmod +x wazuh_ace.py
python3 wazuh_ace.py

```

---

### **9. Debugging & Troubleshooting**

- **Issue**: Script not detecting correlation.
- **Method**: Use print statements to check:
    - If alerts are being parsed.
    - If `rule_id` and `guid` are captured.
- **Bug found**: Time window too strict (`2s` with `0.5s` sleep missed some matches).
- **Fix**: Increase `TIME_WINDOW` to `5s` for better matching.

---

### **10. Final Testing**

- Re-run simulated attack payload on assumed breached host.
- Confirm correlated alerts appear:

```
[ALERT] Correlated possible C2 activity: Rule 100100 & Rule 100101 seen for GUID <process_guid> within 5 seconds at <timestamp>

```

- Remove debug print statements once working.

---

### **Key Lessons for Students**

- Avoid **coincidence-based** correlation; use meaningful event relationships.
- **Process GUID correlation** is powerful for reconstructing attack chains.
- **Small time windows** risk missing events; balance precision vs. detection success.
- **Print debugging** is simple but effective in script troubleshooting.
- Feeding correlated results back into Wazuh enhances detection capabilities.

</section>
</div>
