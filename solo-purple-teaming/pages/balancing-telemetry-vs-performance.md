---
layout: default
title: "Balancing Telemetry VS Performance"
permalink: /solo-purple-teaming/balancing-telemetry-vs-performance/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Balancing Telemetry VS Performance</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Introduction – Your Role as a Solo Purple Teamer**

- As a **solo purple teamer**, you’re responsible for **both detecting threats and simulating them**.
- **Telemetry** (logs, events, metrics) is your **primary source of truth** for investigations.
- Collecting too much telemetry:
    - Can slow tools and searches.
    - Wastes time processing irrelevant data.
    - Risks obscuring critical indicators.
- For training purposes, we will **lean towards over-collection** to ensure we identify the best detection opportunities.
- **Goal:** Strike a balance — enough data for detection and investigation without making the environment unmanageable.
- Note: In the lab, you won’t feel these constraints heavily, but in the **real world SOC**, they are significant.

---

### **2. Key Terms**

1. **Telemetry** – Logs, events, and metrics from endpoints, networks, and applications.
2. **Search Performance** – The **speed and responsiveness** of queries in your SIEM or EDR platform.
3. **Signal-to-Noise Ratio** – The **quality** of your data versus the **quantity** of irrelevant or low-value data.

---

### **3. Over-Collection in Solo Purple Teaming**

- Over-collection ensures **full visibility** during simulations.
- **Trade-offs** of more telemetry:
    - Increased **storage** needs.
    - Greater **processing load** on systems.
    - Slower **query performance**.
- **Best Practice:** Build your pipeline so **high-value data** is easy to access and analyze.
- **Mindset shift:** It’s not about "more logs," it’s about **the right logs**.

---

### **4. Operational Costs of Over-Collection**

While over-collection helps visibility, it introduces challenges, especially in SOC environments:

1. **Query Timeouts** – Large data volumes slow investigations.
2. **Alert Fatigue** – Excess non-actionable alerts hide real threats.
3. **Increased Costs** – Storage and licensing expenses in commercial SIEMs.

**Lab vs. SOC:**

- In your lab — not a big problem.
- In a SOC — can cause **investigation delays**, **missed detections**, and **budget issues**.

---

### **5. Skills Developed Through Solo Purple Teaming**

By designing your own telemetry pipeline, you will:

- **Think critically** about what data matters.
- Gain **hands-on experience** filtering noise and prioritizing high-value logs.
- Learn to **support detection use cases** with relevant telemetry.
- Enter a SOC with a **signal-focused mindset** instead of a “collect everything” approach.

---

### **6. The Value of Baselining**

- **Baselining**: Identifying what “normal” looks like in your environment.
- Benefits:
    - Distinguishes **expected behavior** from **potential threats**.
    - Helps **fine-tune detections** and reduce false positives.
    - Improves **alert fidelity** and signal-to-noise ratio.
- **Examples of normal patterns to baseline:**
    - Regular user logon behavior.
    - Routine DNS queries.
    - Benign, repetitive processes.

---

### **7. Looking Ahead**

In the **next lecture**:

- Begin the **Blue Team Explore Phase**:
    - Search and analyze telemetry.
    - Identify telemetry gaps.
    - Investigate alerts.
    - Validate detection logic.

</section>
</div>
