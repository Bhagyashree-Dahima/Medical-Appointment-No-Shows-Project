# 🏥 Medical Appointment No-Show Analysis

A SQL + Tableau project analyzing patient appointment no-show patterns using the [Medical Appointment No Shows dataset](https://www.kaggle.com/datasets/joniarroba/noshowappointments?resource=download) (110,527 appointments from public healthcare facilities in Brazil).

<img width="2099" height="1199" alt="Appointment No-Show Dashboard" src="https://github.com/user-attachments/assets/65c83887-8e65-49e7-83fd-88c4a834bcba" />


---

## 📌 Project Overview

Missed medical appointments ("no-shows") cost healthcare providers time, revenue, and reduce access to care for other patients. This project explores **why patients miss appointments** and builds a **risk-scoring model** to help clinics proactively identify high-risk patients — using MySQL for data cleaning and analysis, and Tableau for visualization.

**Key questions explored:**
- What is the overall no-show rate?
- Does the day of the week affect no-show behavior?
- Does lead time (days between scheduling and appointment) matter?
- Do age groups show different no-show patterns?
- Do SMS reminders reduce no-shows?
- Which neighborhoods have the highest no-show risk?
- Can a patient's appointment history predict future no-shows?

---

## 🛠️ Tools & Technologies

- **MySQL** — data cleaning, transformation, and exploratory analysis
- **Window Functions** (`RANK()`, `SUM() OVER`, `COUNT() OVER`) — patient-level risk scoring
- **SQL Views (CTEs)** — reusable risk-tier logic
- **Tableau** — interactive dashboard and visualization

---

## 🧹 Data Cleaning & Preparation

Key cleaning steps performed in MySQL:

1. **Renamed columns** for clarity and consistency:
   - `Hipertension` → `hypertension`
   - `Handcap` → `disability_count`
   - `No-show` → `no_show`

2. **Fixed date/time formatting** — converted ISO 8601 timestamp strings (`2016-04-29T18:38:08Z`) into proper `DATETIME`/`DATE` types using `STR_TO_DATE()` and `REPLACE()`.

3. **Removed data quality issues:**
   - Dropped records with invalid ages (`Age = -1`)
   - Dropped records with negative lead times (appointment scheduled after the appointment date — a data entry error)

4. **Engineered a new feature:** `lead_time_days` — the number of days between scheduling and the actual appointment (`DATEDIFF(AppointmentDay, ScheduledDay)`).

---

## 📊 Analysis Performed

| # | Analysis | Technique |
|---|----------|-----------|
| 1 | Overall no-show rate | Aggregate `COUNT`, `%` calculation |
| 2 | No-show rate by day of week | `DAYNAME()`, `GROUP BY` |
| 3 | No-show rate by lead-time bucket (Same Day / 1–3 / 4–7 / 8+ days) | `CASE WHEN`, `GROUP BY` |
| 4 | No-show rate by age group (Child / Teen / Young Adult / Adult / Senior) | `CASE WHEN`, `GROUP BY` |
| 5 | Impact of SMS reminders | `GROUP BY`, conditional aggregation |
| 6 | Highest-risk neighborhoods | `RANK() OVER()`, `HAVING` (min. 100 appointments) |
| 7 | Patient-level historical risk scoring | Window functions (`ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING`) |
| 8 | Reusable risk-tier classification | SQL `VIEW` with CTE (`v_appointment_risk`) |

### Risk Tier Logic
A view (`v_appointment_risk`) classifies each appointment into a risk tier based on the patient's **prior no-show history** and **lead time**:

- **New Patient – Monitor**: No appointment history yet
- **High Risk**: 50%+ historical no-show rate OR 8+ day lead time
- **Medium Risk**: 20–49% historical no-show rate OR 4–7 day lead time
- **Low Risk**: Everything else

---

## 📈 Dashboard Highlights (Tableau)

- **110,521** total appointments analyzed
- **20.19%** overall no-show rate
- **10.18 days** average lead time
- No-shows are highest on **Saturdays** and lowest mid-week
- No-show rate rises sharply with longer lead time — appointments booked **8+ days out** have the highest no-show rate, while **same-day** appointments have the lowest
- **Santos Dumont, Santa Cecilia, and Santa Clara** are among the highest-risk neighborhoods
- Risk-tier breakdown: **56.37%** New Patient, **20.08%** High Risk, **15.55%** Low Risk

*(See `Appointment_No-Show_Dashboard.png` for the full interactive dashboard layout.)*

---

## 📂 Repository Structure

```
├── appointment_analysis.sql        # Full SQL script (cleaning + analysis + views)
├── Appointment_No-Show_Dashboard.png  # Tableau dashboard screenshot
└── README.md                       # Project documentation
```

---

## 🚀 How to Reproduce

1. Download the dataset from [Kaggle](https://www.kaggle.com/datasets/joniarroba/noshowappointments?resource=download)
2. Import the CSV into a MySQL table named `medicalappointment`
3. Run `appointment_analysis.sql` sequentially — it handles cleaning, feature engineering, and all exploratory queries
4. Connect Tableau to the cleaned table / `v_appointment_risk` view to rebuild the dashboard

---

## 🔑 Key Takeaways

- **Lead time is the strongest predictor** of no-shows — patients booked far in advance are far more likely to forget or deprioritize their appointment.
- **Weekend scheduling** correlates with higher no-show rates.
- A significant share of appointments come from **first-time patients**, meaning risk scoring must combine historical behavior with contextual factors like lead time and neighborhood.
- These insights could support **targeted interventions** — e.g., extra SMS/call reminders for long-lead-time or high-risk-neighborhood appointments.

---

## 📬 Contact

Feel free to connect if you'd like to discuss this project or healthcare analytics in general!


Bhagyashree Dahima
linkdin: https://www.linkedin.com/in/bhagyashree-dahima-337282291/
