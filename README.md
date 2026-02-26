# 🏥 HealthSure Clinics — Reducing Missed Appointments
### Turning 110,000+ raw health records into actionable insights that improve access, equity, and operational efficiency

![SQL](https://img.shields.io/badge/SQL-MySQL-blue?logo=mysql&logoColor=white) ![Excel](https://img.shields.io/badge/Excel-Data%20Preparation-217346?logo=microsoft-excel&logoColor=white) ![Power BI](https://img.shields.io/badge/BI-PowerBI-F2C811?logo=powerbi&logoColor=black) ![Data Modeling](https://img.shields.io/badge/Model-Star%20Schema-orange) ![Status](https://img.shields.io/badge/Project-Completed-brightgreen)

![Dashboard](Project%20Files/HealthSure%20Appointments%20Dashboard%20PNG.png)

Click below to view the live report:  

[![View Dashboard](https://img.shields.io/badge/Power%20BI-View%20Report-F2C811?logo=powerbi&logoColor=black)](https://app.powerbi.com/view?r=eyJrIjoiNWU5OGFhMDUtYmYyOS00ZDA3LWFkMDUtYzU0ZDllMDQ0Zjk1IiwidCI6IjNjMzJjMjljLTg1YmEtNDVlZi05YTA2LTkyZmNkNjEwYThkMiJ9)

---

## 📑 Table of Contents
- [Executive Summary](#-executive-summary)
- [Context & Stakeholders](#-context--stakeholders)
- [Business Problem](#business-problem)
- [Business Goals](#business-goals)
- [Tools & Skills](#tools--skills)
- [Data Model](#data-model)
- [Data Cleaning & Transformation](#data-cleaning--transformation)
- [SQL & DAX Highlights](#sql--dax-highlights)
- [Key Insights](#key-insights)
- [Financial Impact Model](#financial-impact-model)
- [Recommendations](#recommendations)
- [Business Value](#business-value)
- [Conclusion](#conclusion)
- [Next Steps](#next-steps)
- [Data Limitations](#data-limitations)

---

## 🚀 Executive Summary

HealthSure Clinics was facing persistently high appointment no-shows (~80%), resulting in lost revenue, idle clinical capacity, and delayed patient care.

I built an end-to-end BI solution — from SQL database design to Power BI storytelling — to help the Head of Operations answer one question:
> **“Who is missing appointments, where, and why - and what should we do about it?”**

### 🔍 What the data revealed
- 📍 **Three (3) neighborhoods drive most of the problem** — Santa Martha & Tabuazeiro exceed **84% no-shows**
- 👵 **60–79 year olds have the highest no-show rates (~85%)**
- 🫀 **Patients with chronic conditions miss more appointments**
- 🕒 **Longer wait times = higher no-shows**  
- 🌆 **Evening clinics perform better — but are under-utilized**
- 📲 **Single SMS reminders ≠ effective engagement**

### 💡 Business Impact
By reallocating appointment capacity & improving outreach, HealthSure could:

✅ Recover **5,000–11,000 missed visits annually** (depending on adoption & scheduling constraints)  
✅ Improve access for vulnerable communities  
✅ Increase staff productivity  
✅ Reduce wasted capacity  

This project demonstrates business-first analytics — ensuring insights translate directly into operational and financial outcomes.

### 👨‍💻 My Role
I delivered this project end-to-end, including:

✔ data cleaning & modeling  
✔ SQL analytics  
✔ DAX measures  
✔ dashboard design  
✔ business storytelling  
✔ financial impact analysis

---

## 🏥 Context & Stakeholders
**Organization:** HealthSure Clinics (Brazil)  

**Function:** Outpatient care  

**Primary Stakeholder:** Head of Operations  

**Secondary Stakeholders:** Clinic Managers, Patient Outreach Teams, Finance & Strategy

**Objective:** Improve appointment attendance & operational efficiency

**Dataset:** 110,527 appointment records across 80+ neighborhoods. It reflects a high no-show environment commonly seen in underserved communities.

---

## ❗ Business Problem
Missed appointments were causing:

❌ Lost revenue  
❌ Idle clinical capacity  
❌ Reduced patient access  
❌ Lower continuity of care  
❌ Staff scheduling inefficiency  

Yet leadership lacked:

⚠ clear visibility into **who & where**  
⚠ root-cause understanding  
⚠ measurable recommendations  

---

## 🎯 Business Goals
✔ Reduce missed appointments  
✔ Improve access for low-income patients  
✔ Optimize staff scheduling  
✔ Build a reusable reporting model  

---

## 🛠 Tools & Skills
- **SQL (MySQL)** — staging, cleaning, star schema modeling  
- **Excel** — mapping & enrichment  
- **Power BI** — KPIs, storytelling visuals  
- **Power Query / DAX** — transformations & metrics  
- **Data Modelling** — fact/dimension design  
- **Business Storytelling** — insight-to-action framing  

---

## ❄️ Data Model
I built a **star schema** optimized for BI performance.

**Fact Table**
`appointments_fact`

**Dimensions**
`patient_dim`   `location_dim`  

> ![ERD](Project%20Files/ERD_HealthSure_Appointments_Dashboard_PNG.png)

This separates **measures** from **descriptors** — enabling fast slicing by patient, time, and location.

---

## 🧹 Data Cleaning & Transformation
Cleaning wasn’t just technical — it ensured **business-ready accuracy.**

### 1️⃣ **Load raw data → staging table**  
*Goal: Preserve original integrity*

### 2️⃣ Remove duplicates  
*Goal: Ensure each appointment is unique*

### 3️⃣ Standardize formats  
*Goal: Eliminate ambiguity so fields are query-ready and consistent across tools*

### 4️⃣ Normalize binary flags (0/1 → Yes/No)  
*Goal: Improve business readability*

### 5️⃣ Enrich locations with socioeconomic data  
*Goal: Support equity analysis*

### 6️⃣ Split tables into fact & dimensions  
*Goal: Create scalable BI structure*

### 7️⃣ Create surrogate keys & indexes  
*Goal: Improve query performance and consistency*

---

## SQL & DAX Highlights

### 📌 Duplicate Check (SQL)
```sql
SELECT
  *
FROM (
   SELECT *,
   ROW_NUMBER() OVER (
      PARTITION BY AppointmentID, PatientID
   ) rn
   FROM stg_appointments
) sub
WHERE rn > 1;
```

### 📌 No-show rate by Neighborhood (SQL)
```sql
SELECT 
   location_dim.Neighbourhood,
   COUNT(*) AS TotalAppointments,
   SUM(CASE WHEN Status='No-Show' THEN 1 END) AS NoShows,
   ROUND(
      100 * SUM(CASE WHEN Status='No-Show' THEN 1 END) / COUNT(*),
      2
   ) AS NoShowRate
FROM appointments_fact a
JOIN location_dim l
   ON a.LocationKey = l.LocationKey
GROUP BY Neighbourhood
ORDER BY NoShowRate DESC;
```

---

### 📌 Show Rate % (DAX)

```DAX
Show Rate % =
DIVIDE(
    [Show Count],
    [Total Appointments]
)
```

### 📌 Repeat Visit % (DAX)

```DAX
Repeat Visit % =
VAR Total =
    DISTINCTCOUNT(Appointments[PatientID])
VAR Repeaters =
    CALCULATE(
        DISTINCTCOUNT(Appointments[PatientID]),
        Appointments[VisitCount] > 1
    )
RETURN
DIVIDE(Repeaters, Total)
```

---

## ⭐ Key Insights

### 1️⃣ Geography drives risk
Santa Martha & Tabuazeiro miss **84%+ of appointments**

👉 Targeting JUST these areas could recover **8,000+ visits/year**

---

### 2️⃣ Older / Chronic patients are most vulnerable
- Age **60–79 → 85% miss rate**
- Diabetes / Hypertension → **82%+**

👉 These are patients who **need** continuity of care

---

### 3️⃣ Time-of-day matters
Morning: **83% no-show**  
Evening: **70%**

👉 Demand exists — but scheduling is misaligned

---

### 4️⃣ Wait time increases risk
Attendees wait: **12.3 days**  
No-shows wait: **21.5 days**

👉 Reducing backlog ≈ improved attendance

---

### 5️⃣ Patient loyalty is low
Only **39% of patients return**

👉 Outreach & journey design opportunity

---

## 💰 Financial Impact Model
Financial impact estimates are directional and based on conservative assumptions.

> *Assumption:*
- Average consultation revenue = **$35**
- Annual appointments = **110,527**
- Show rate improvement target = **5%**

### Baseline
No-show rate ≈ **79%**

### If we improve attendance by 5% or 10%:
✔ Recovered visits =  
110,527 × 5% = **5,526 visits per year**
110,527 × 10% = **11,053 visits per year**

💰 Revenue recovered =  
5,526 × $35 = **$193,410 per year**
11,052 × $35 = **$386,820 per year**

### Additional benefits
✔ Better staff utilization  
✔ Lower overtime requirements  
✔ Improved patient health outcomes  

**This delivers measurable business value.**

---

## 🎯 Recommendations

### Highest-Impact Actions
1️⃣ **Target top 3 high-risk neighborhoods first**  
2️⃣ **Introduce multi-touch reminders (SMS + call)**  
3️⃣ **Shift 10–15% of capacity to evenings**  
4️⃣ **Cap wait time at ≤ 14 days for high-risk groups**  
5️⃣ **Schedule chronic-care follow-ups before checkout**

---

## 💼 Business Value
This solution enables leadership to:

✅ See operational risk hotspots
✅ Align staffing to demand
✅ Improve continuity of care
✅ Protect revenue
✅ Scale reporting across clinics

---

## 🏁 Conclusion

With targeted intervention, HealthSure Clinics can:

✔ Improve access
✔ Reduce wasted capacity
✔ Protect revenue
✔ Strengthen financial performance
✔ Deliver patient-centered care
✔ Build a data-driven scheduling strategy

---

## 🔮 Next Steps

- Automate data refresh  
- Build predictive risk scoring  
- Track financial leakage monthly
- A/B test reminder strategies
- Add real-time monitoring
- Expand patient journey analytics

---

## 🔮 Data Limitations
> ***Note: Dataset does not include reason-codes for cancellations, so behavioral drivers are inferred rather than directly reported.***

---

## 👤 Author
**Oluwatobi Moses Ojo**  
Data & BI Analyst  
*Turning data into business clarity.*

