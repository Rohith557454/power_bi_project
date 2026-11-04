# 🏥 Hospital Patient Care Operations Analysis

An end-to-end **Power BI** project that analyses hospital appointments, treatments, doctors and rooms across 8 Indian cities. It turns raw operational data into a 6-page interactive report covering demand, patient behaviour, treatment performance, resource utilisation, problem areas and an executive summary.

> **Built during:** Innomatics Research Labs
> **Tool:** Microsoft Power BI (Power Query + DAX + Data Modelling)

---

## 📌 Table of Contents

1. [Business Problem](#-business-problem)
2. [Project Objectives](#-project-objectives)
3. [Dataset & Data Model](#-dataset--data-model)
4. [Data Preparation (Power Query)](#-data-preparation-power-query)
5. [DAX Measures](#-dax-measures)
6. [Dashboard Pages](#-dashboard-pages)
7. [Key Insights](#-key-insights)
8. [Recommendations](#-recommendations)
9. [Repository Structure](#-repository-structure)
10. [How to Use](#-how-to-use)
11. [Author](#-author)

---

## 🎯 Business Problem

Hospital management needs clear visibility into how patients book appointments, how efficiently treatments are delivered, and where operational problems (cancellations, no-shows, reschedules, repeated treatment attempts, long waiting times) occur. Without a single consolidated view, staffing, room allocation and scheduling decisions are made without data.

## ✅ Project Objectives

- Understand **appointment demand** by city, month, booking channel, service type and priority.
- Analyse **patient behaviour**: preferred time slots, appointments per patient, high-value patients.
- Measure **treatment performance**: completion rate, waiting time, treatment duration.
- Evaluate **doctor workload** and **room / equipment utilisation**.
- Identify **operational problems**: cancellations, no-shows, reschedules and multiple-attempt treatments.
- Provide an **executive summary** with findings and recommended actions.

---

## 🗂 Dataset & Data Model

The model contains **6 tables** (5 dimension/lookup tables + 1 fact-style treatments table) connected in a star-like schema.

| Table | Description | Key Columns |
|---|---|---|
| `appointments` | Appointment bookings | appointment_id, patient_id, doctor_id, appointment_date, booking_channel, service_type, priority, estimated_cost |
| `treatments` | Treatment records linked to appointments | treatment_id, appointment_id, doctor_id, room_id, actual_treatment_date, status, treatment_attempt, treatment_cost, treatment_duration_min, waiting_time_min |
| `patients` | Patient master data | patient_id, patient_name, age, gender, city, patient_type, preferred_time_slot, registration_date |
| `doctors` | Doctor master data | doctor_id, doctor_name, specialty, employment_type, hire_date, is_active, rating |
| `rooms` | Room & equipment master data | room_id, room_type, equipment_type, capacity, floor, is_available, last_maintenance_date |
| `date_table` | Calendar table | Date, Day, Day Name, Is Weekend, Month, Month Number, Quarter, Week Number, Year, Year-Month |

### Relationships

| From | To | Cardinality | Notes |
|---|---|---|---|
| `appointments[patient_id]` | `patients[patient_id]` | Many-to-One | Bi-directional |
| `appointments[appointment_date]` | `date_table[Date]` | Many-to-One | Active |
| `treatments[appointment_id]` | `appointments[appointment_id]` | Many-to-One | Bi-directional |
| `treatments[doctor_id]` | `doctors[doctor_id]` | Many-to-One | Bi-directional |
| `treatments[room_id]` | `rooms[room_id]` | Many-to-One | Bi-directional |
| `treatments[actual_treatment_date]` | `date_table[Date]` | Many-to-One | Inactive (used via `USERELATIONSHIP` for *Treatments by Actual Date*) |

---

## 🧹 Data Preparation (Power Query)

The following transformations were applied in Power Query Editor:

1. **Source** – loaded raw data
2. **Promoted Headers** – first row used as column names
3. **Changed Type** – correct data types for dates, numbers and text
4. **Trimmed Text** – removed leading/trailing spaces
5. **Capitalized Each Word** – standardised text casing (applied to multiple columns, e.g. city, service type, status)
6. **Filtered Rows** – removed invalid / blank records

A dedicated **date table** was created for time-intelligence and month-wise analysis.

---

## 🧮 DAX Measures

| Table | Measures |
|---|---|
| `appointments` | Appointments per Patient, Average_Waiting_time, Patient Appointment Value, Total_Treatment_Cost, Treatment Attempts |
| `doctors` | Active Doctors |
| `rooms` | Average Treatments per Room, Room Treatment Count, Room Treatments |
| `treatments` | Average Appointment Value, Average_Treatment_duration, Cancellation Rate, Cancelled Treatments, Completed Treatments, Multiple-Attempt Rate, Multiple-Attempt Treatments, No-Show Rate, No-Show Treatments, Rescheduled Rate, Rescheduled Treatments, Total Appointment Value, Total_Appointments, Total_Patients, Total_Treatments, Treatment Completion Rate, Treatments by Actual Date |

---

## 📊 Dashboard Pages

The report has **6 pages** with a consistent navigation bar (Page 1–6).

### Page 1 – Appointment Demand Dashboard
- **KPIs:** Total Appointments (3K), Total Patients (400), Total Treatment Cost (5.63M), Average Appointment Value (4.78K), Total Appointment Value (14.26M)
- **Visuals:** Appointments by City, Appointments by Booking Channel (donut), Monthly Appointment Volume (line), Appointment Value by Patient Type & Service Type (matrix)
- **Slicers:** Appointment date, City, Service type, Priority

### Page 2 – Appointment Behaviour Dashboard
- **KPIs:** Total Patients (400), Total Treatments (3K), Patient Appointment Value (14.26M), Appointments per Patient (7.46)
- **Visuals:** Monthly Bookings by Preferred Time Slot, Patient Appointment Activity by City & Type, Highest-Value Patients, Appointments per Patient
- **Slicers:** Appointment date, Patient type, Preferred time slot, City

### Page 3 – Treatment Performance Dashboard
- **KPIs:** Treatment Completion Rate (0.71), Total Treatments (3K), Average Waiting Time (14.51 min), Average Treatment Duration (28.74 min)
- **Visuals:** Treatment Outcomes across Cities & Service Types, Monthly Treatment Performance, Waiting Time vs Treatment Duration by Doctor, Treatment Outcomes by Status
- **Slicers:** City, Appointment date, Service type, Status

### Page 4 – Doctor and Room Performance Dashboard
- **KPIs:** Active Doctors (64), Total Patients (400), Average Waiting Time (14.53), Average Treatments per Room (69.17)
- **Visuals:** Treatments Handled by Doctor, Doctor Performance by Treatment Outcome, Treatment Usage by Room Type & Equipment, Room Usage Ranking
- **Slicers:** Doctor name, Equipment type, Specialty, Room type

### Page 5 – Appointment and Treatment Problems Dashboard
- **KPIs:** Multiple-Attempt Treatments (497), Cancellation Rate (0.08), No-Show Rate (0.07), Rescheduled Rate (0.10)
- **Visuals:** Treatment Problems by Status, Priority vs Waiting Time & Completion Rate, Appointments with Multiple Treatment Attempts, Appointment Problems by City or Service
- **Slicers:** City, Priority, Status, Service type

### Page 6 – Hospital Executive Summary
- **KPIs:** Total Appointments (3K), Total Patients (400), Treatment Completion Rate (0.70), Total Appointment Value (14.26M), Average Waiting Time (14.53), Multiple-Attempt Rate (0.17)
- **Visuals:** Monthly Appointment Trend, Appointments by City, Doctor Workload, Treatment Status Distribution — each paired with a written **Finding** and **Recommended Action**
- **Slicers:** Date, Doctor name, City, Service type

---

## 🔍 Key Insights

- **~3,000 appointments** from **400 patients** generated a total appointment value of **14.26M**, with an average of **7.46 appointments per patient**.
- **Kochi (439)** and **Mumbai (391)** have the highest appointment volumes; **Pune (332)** has the lowest.
- **App (29.5%)** is the most-used booking channel, followed by Website (20.4%) and Phone (18.5%).
- Monthly demand fluctuates between roughly **218 (February)** and **276 (July)** appointments.
- Treatment completion rate is about **70%** – about **68%** of treatments are completed, with the rest rescheduled, cancelled, no-show or still in progress.
- Average waiting time is about **14.5 minutes** and average treatment duration is about **28.7 minutes**.
- **Cancellation (0.08), No-Show (0.07) and Reschedule (0.10)** rates indicate room for improvement in scheduling.
- Doctor workload is uneven – the busiest doctor handles **66 treatments** versus ~46 for others in the top list.
- The multiple-attempt rate is **17%**, suggesting repeat treatments or follow-up gaps.

---

## 💡 Recommendations

1. **Plan staffing and resources** according to peak-demand months and high-volume cities.
2. **Review doctor workload distribution** and rebalance appointment allocation where needed.
3. **Reduce cancellations, no-shows and reschedules** with reminders, confirmations and better slot planning.
4. **Investigate repeated treatment attempts** to determine whether follow-up, treatment planning or process changes are required.
5. **Optimise room usage** by balancing load across highly and rarely used rooms.

---

## 📁 Repository Structure

```
├── Hospital_Patient_Care_Operations.pbix   # Power BI report file
├── data/                                   # Raw dataset files
├── screenshots/                            # Dashboard & data model images
│   ├── 01_appointment_demand.png
│   ├── 02_appointment_behaviour.png
│   ├── 03_treatment_performance.png
│   ├── 04_doctor_room_performance.png
│   ├── 05_problems_dashboard.png
│   ├── 06_executive_summary.png
│   └── data_model.png
└── README.md
```

> Update file names and folders to match your repository.

---

## ▶️ How to Use

1. Clone this repository:
   ```bash
   git clone https://github.com/Rohith557454/<your-repo-name>.git
   ```
2. Open the `.pbix` file in **Power BI Desktop** (latest version recommended).
3. If prompted, update the data source path under **Home → Transform data → Data source settings**.
4. Click **Refresh** and explore the report using the slicers and page navigation buttons.

---

## 🛠 Skills Demonstrated

`Power BI` · `Power Query` · `DAX` · `Data Modelling` · `Data Cleaning` · `KPI Design` · `Dashboard Design` · `Business Storytelling`

---

## 👤 Author

**Nareddy Rohith**
📧 nareddyrohith1@gmail.com
🔗 [LinkedIn](https://www.linkedin.com/in/nareddy-rohith-6b410525b) · [GitHub](https://github.com/Rohith557454)

---

⭐ If you found this project useful, consider giving the repository a star!
