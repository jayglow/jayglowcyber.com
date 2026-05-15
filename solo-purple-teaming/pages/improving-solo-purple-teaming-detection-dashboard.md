---
layout: default
title: "Improving Solo Purple Teaming Detection Dashboard"
permalink: /solo-purple-teaming/improving-solo-purple-teaming-detection-dashboard/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Improving Solo Purple Teaming Detection Dashboard</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Why Dashboards Matter in Solo Purple Teaming**

- **Purpose:** Dashboards turn raw logs and alerts into clear, actionable insights.
- **Benefits for solo purple teamers:**
    - Quickly spot anomalies and track attacker movements.
    - Instantly see if simulated attacks triggered alerts.
    - Monitor detection rule performance and false positives.
    - Save time by consolidating critical data in one place.
- **Goal:** Build a simple starting dashboard, then expand with more visualizations.

---

### **2. Lab Setup – Accessing Wazuh**

1. Log into the **Wazuh Manager**.
2. Begin by creating **saved searches** to feed our dashboard.

---

### **3. Creating Saved Searches**

We’ll create four searches based on **rule levels** (severity):

### **a. Rules above Level 12**

- Go to **Explore → Discover**.
- Search:
    
    ```
    rule.level > 12
    
    ```
    
- Adjust time filter to **Last 7 days**.
- Save search as:
    
    **solo purple teaming rules greater than twelve**.
    

### **b. Critical Alerts (Level 15)**

- Search:
    
    ```
    rule.level = 15
    
    ```
    
- Save as:
    
    **purple teaming critical alerts**.
    

### **c. High Alerts (Level 14)**

- Search:
    
    ```
    rule.level = 14
    
    ```
    
- Save as:
    
    **high alerts**.
    

### **d. Medium Alerts (Level 13)**

- Search:
    
    ```
    rule.level = 13
    
    ```
    
- Save as:
    
    **medium alerts**.
    

---

### **4. Creating the Dashboard**

1. Go to **Explore → Dashboards**.
2. **Create New Dashboard** → Name it:
    
    **Solo Purple Teaming Dashboard**.
    
3. **Add Existing Search** → Select:
    - **solo purple teaming rules greater than twelve**.
4. **Resize** as needed.

---

### **5. Improving Search Display**

1. Open **Dashboard Management → Saved Objects**.
2. Edit each saved search:
    - Add columns:
        - **Agent name**
        - **Rule level**
        - **Rule name/group**
        - **Rule description**
3. Save changes.
4. Refresh dashboard to see updated columns.

---

### **6. Adding Metrics to the Dashboard**

We’ll create **metric visualizations** for quick counts.

### **a. Critical Alerts Metric**

- Edit dashboard → **Create New → Metric**.
- Select: **purple teaming critical alerts** search.
- Options:
    - Add range (for color coding).
    - Reverse **green → red** scale.
    - Set font size to **90**.
- Save as: **Number of Critical Alerts**.
- Place and resize in dashboard.

### **b. High Alerts Metric**

- If missing, recreate saved search for Level 14.
- Create metric as above.
- Color scale: **yellow → red**.
- Save as: **Number of High Alerts**.
- Position next to Critical Alerts.

### **c. Medium Alerts Metric**

- Use Level 13 saved search.
- Color scale: **green → red**.
- Font size: **90**.
- Save as: **Number of Medium Alerts**.
- Position next to High Alerts.

---

### **7. Final Dashboard Layout**

- Three large metrics:
    - **Number of Critical Alerts**
    - **Number of High Alerts**
    - **Number of Medium Alerts**
- Table with rule details for deeper analysis.
- All elements resized for a clean, uniform look.

---

### **8. Saving and Testing**

1. Save the dashboard layout.
2. Refresh to ensure changes persist.
3. Prepare for the next lecture where we’ll **re-engage the attack** to watch alerts appear in real time.

---

✅ **End Result:** You now have a working Solo Purple Teaming Dashboard that gives you both **detailed rule data** and **quick severity counts**—a perfect starting point for monitoring attack simulations and tuning detections.

</section>
</div>
