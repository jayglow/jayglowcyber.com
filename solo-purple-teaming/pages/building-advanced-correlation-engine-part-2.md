---
layout: default
title: "Building Advanced Correlation Engine - Part 2"
permalink: /solo-purple-teaming/building-advanced-correlation-engine-part-2/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building Advanced Correlation Engine - Part 2</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **Objective**

We’re enhancing our **advanced correlation engine** by extracting additional useful fields from Wazuh alerts.

In this lesson, we’ll focus on:

1. Parsing **host name** from the alert.
2. Parsing **image** from event data.
3. Updating the correlation logic to include these fields.

---

## **Step 1 – Why Parse Extra Fields?**

In the previous lecture, our alerts lacked critical context — most importantly, **which host** the alert originated from. Without this, correlation loses value. We also want to include other relevant details (like `image`) for better analysis.

---

## **Step 2 – Access the Script**

We’ll modify our Python correlation engine script:

```bash
cd /opt/wazuh-tools
sudo vi wazuh_ace.py

```

Locate the **`parse_alert()`** function.

---

## **Step 3 – Parsing the Host Name**

- In Wazuh event data, **host name** is stored under:
    - `agent` → `name`
- In Python, this means we first pull the **`agent`** dictionary, then get the **`name`** key.

**Example Code:**

```python
agent = alert.get("agent", {})
host_name = agent.get("name", "")

```

We:

- Use `.get()` to safely retrieve values.
- Default to `{}` (empty dict) or `""` to avoid errors if a key doesn’t exist.
- Add `host_name` to the return statement of `parse_alert()`.

---

## **Step 4 – Update `correlate_events()` to Include Host Name**

Inside **`correlate_events()`**:

- Capture `host_name` from the `parse_alert()` return values.
- Update any tuples or comprehensions to include it.
- Modify the alert string to use **string interpolation** for displaying the host name.

**Example Change:**

```python
alert_message = f"Correlated possible C2 activity on {host_name}"

```

---

## **Step 5 – Test Host Name Parsing**

1. Save and exit (`:wq`).
2. Run the script:
    
    ```bash
    python3 wazuh_ace.py
    
    ```
    
3. Trigger an alert (e.g., run the initial access payload).
4. Check output — if the host name shows correctly, parsing works.

---

## **Step 6 – Debugging Host Name Issues**

If you see an empty host name:

- Ensure you used:
    
    ```python
    agent = alert.get("agent", {})
    host_name = agent.get("name", "")
    
    ```
    
- **Do not** try to get `"name"` directly from `alert` — it lives under the `agent` key.

---

## **Step 7 – Parsing the Image Field**

The `image` field lives deeper in the structure:

- Path: `data` → `win` → `eventdata` → `Image`

We use chained `.get()` calls to safely navigate:

**Example Code:**

```python
event_data = alert.get("data", {}).get("win", {}).get("eventdata", {})
image = event_data.get("Image", "unknown")

```

- If any part is missing, we default to `{}` to prevent key errors.
- If `Image` isn’t found, we set `"unknown"`.

Add `image` to the **`parse_alert()`** return values.

---

## **Step 8 – Update `correlate_events()` to Include Image**

- Capture `image` from `parse_alert()`.
- Add it to the tuple and comprehension logic.
- Display it in alerts (e.g., in square brackets).

**Example:**

```python
alert_message = f"Correlated possible C2 activity on {host_name} [{image}]"

```

---

## **Step 9 – Test Image Parsing**

1. Save and exit the script.
2. Run:
    
    ```bash
    python3 wazuh_ace.py
    
    ```
    
3. Trigger an alert again.
4. Verify that the **image** value now appears alongside the host name.

---

## **Step 10 – Student Homework**

Enhance the correlation engine to parse **all other fields you think are important** for detection — for example:

- Process GUID
- Destination IP/Port
- Command line arguments
- User name

**Tip:** More fields give richer alerts, but too many can create noise. Aim for balance.

---

## **Next Steps**

In the next lecture, we’ll:

- Turn this correlation engine into a **service**.
- Make it monitor a file.
- Have Wazuh watch that file and ingest the correlation results into the dashboard.

</section>
</div>
