---
layout: default
title: "Balancing Accuracy VS Coverage"
permalink: /solo-purple-teaming/balancing-accuracy-vs-coverage/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Balancing Accuracy VS Coverage</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Introduction to the Trade-Off**

In this lecture, we examine one of the most important challenges in detection engineering:

**Balancing coverage vs accuracy** in detection systems.

- These two goals are often in tension.
- Finding the right balance is essential for building effective and sustainable detection capabilities.

---

### **2. Understanding Detection Outcomes**

Before addressing coverage and accuracy, it’s important to understand the **four possible outcomes** when a detection rule triggers:

1. **True Positive (TP)** – Malicious activity that is correctly detected.
2. **True Negative (TN)** – Benign activity that is correctly ignored.
3. **False Positive (FP)** – Benign activity that is incorrectly flagged as malicious.
4. **False Negative (FN)** – Malicious activity that goes undetected.

> Why this matters:
> 
> - **False Positives** → Alert fatigue, wasted SOC time.
> - **False Negatives** → Missed detection opportunities, allowing threats to persist.

![image.png](Balancing%20Accuracy%20VS%20Coverage/image.png)

---

### **3. Key Metrics for Evaluating Detections**

Detection engineers commonly use three metrics to evaluate performance:

- **Precision** – Of all the alerts generated, what percentage were *actual threats*?
    
    *Focus:* Correctness of alerts.
    
- **Recall** – Of all the *actual threats*, how many did we detect?
    
    *Focus:* Detection coverage.
    
- **Accuracy** – The overall correctness (TP + TN) ÷ total cases.
    
    *Caution:* In security, accuracy can be misleading because true negatives (benign activities) can dominate the results and make detection appear better than it actually is.
    

---

### **4. Accuracy vs Coverage**

### **Accuracy**

- High accuracy means detections trigger **only when something truly malicious happens**.
- **Advantages:**
    - Low false positive rates.
    - Builds trust in SOC alerts.
- **Risk:**
    - Can result in missed threats (higher false negatives) if rules are too narrow.

### **Coverage**

- Measures how much of the **attack surface** your detections monitor.
- **Advantages:**
    - Increases chances of catching novel or evasive threats.
- **Risk:**
    - More false positives → Analyst fatigue.

---

### **5. The Constant Push and Pull**

In detection engineering:

- **Improving accuracy** often **reduces coverage**.
- **Increasing coverage** often **reduces accuracy**.
- The “right” balance depends on:
    - Your **organization’s risk tolerance**.
    - **Available SOC resources**.
    - **Threat landscape** (what attackers you expect to face).

---

### **6. Example Scenarios**

- **High Accuracy:**
    - Minimal false positives.
    - Higher miss rate for new or unusual threats.
- **High Coverage:**
    - Broad visibility into activity.
    - More false positives → SOC must handle higher workload.

---

### **7. Solo Purple Teaming Challenges**

Solo purple teaming can be powerful, but simulating a real-world environment introduces limitations:

- Lack of **real user behavior** or **network noise** → Hard to simulate realistic false positives.
- May miss techniques you don’t emulate yourself.
- No incident response team or production telemetry → No feedback loop to improve detections.
- Risk of **overly optimistic results** or **blind spots** in coverage.

---

### **8. Preparing for the Next Lesson**

In the next lecture, we’ll discuss:

- **Balancing telemetry volume** vs **search performance**.
- Why too much telemetry can have a performance and cost impact.
- How to apply these lessons to production environments.

</section>
</div>
