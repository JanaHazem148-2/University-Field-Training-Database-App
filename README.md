# Field Training 2 — Internship Management System

A full-stack internship management system built as a database course project. It facilitates communication and workflow between three stakeholders — students, university mentors, and company mentors — through a Streamlit web application backed by a MySQL database.

---

## Team

| Name | ID | Phase |
|------|----|-------|
| Jana Hazem Hegazi Mohamed | 23101378 | Phase 3 |
| Hamdy Sameh Hamdy | 23101383 | Phase 4 |
| Noreen Osama Yosry Abdelwahab | 23102386 | Phase 1 |
| Hana Sherif Abdelmoniem | 23101499 | Phase 2 |
| Nour Ahmed Rashad | 23101495 | Phase 5 |

---

## Overview

Students upload their CVs and apply to companies. Applications pass through a two-stage review: first the university mentor approves or rejects, then the company mentor makes the final hiring decision. The system tracks the full application lifecycle, academic documents, and end-of-internship evaluations.

---

## Project Phases

| Phase | Deliverable |
|-------|-------------|
| Phase 1 | EERD (Extended Entity-Relationship Diagram) |
| Phase 2 | Relational schema + 3NF normalization |
| Phase 3 | SQL database creation and data population |
| Phase 4 | Sample dataset (CSV) |
| Phase 5 | Relational algebra, SQL views, stored procedure + Streamlit web app |

---

## Tech Stack

| Component | Technology |
|-----------|------------|
| Language | Python 3 |
| Web Framework | Streamlit |
| Database | MySQL (`field_training2`) |
| DB Connector | `mysql-connector-python` |
| Data Processing | Pandas |
| Visualizations | Plotly Express, Plotly Graph Objects |
| UI Extras | `streamlit-extras` (stylable containers, metric cards) |
| Export | xlsxwriter (Excel), CSV |

---

## Project Structure

```
field_training2/
├── field_training2_website.py   # Streamlit web application
├── Phase5.sql                   # Full SQL script: schema, data, views, stored procedure
├── Phase4_datasheets.csv        # Sample dataset
├── Phase1_Database.pdf          # EERD + relational schema + 3NF normalization
├── phase_3_update_.pdf          # SQL schema creation with screenshots
├── Phase_5_report.pdf           # Relational algebra + views + stored procedure report
├── Report_phase4_data.pdf       # Phase 4 data report
└── Phase2.jpg                   # ER diagram image
```

---

## Database Schema

**Database:** `field_training2`

| Table | Primary Key | Description |
|-------|-------------|-------------|
| `User` | `ID` | Shared base table for all users |
| `Student` | `Student_ID` (FK → User) | Student academic profile |
| `Mentor` | `Mentor_ID` (FK → User) | Shared mentor attributes |
| `Uni_Mentor` | `Uni_Mentor_ID` (FK → Mentor) | University mentor details |
| `Company_Mentor` | `Company_Mentor_ID` (FK → Mentor) | Company mentor details |
| `Company` | `Company_Logo` | Company info |
| `Company_Location` | (`Company_Logo`, `Locations`) | Multi-valued company locations |
| `Academic_Docs` | `Doc_ID` | Transcripts and recommendation letters |
| `Internship_Application` | `Application_ID` | Application records with status |
| `Has_a_Relation` | (`Company_Logo`, `Application_ID`) | M:N junction: company ↔ application |
| `Evaluation_Report` | `Report_ID` | Company mentor evaluations |
| `Performance_Score` | (`Report_ID`, `Student_ID`) | Per-student scores |
| `Application_Company_Mentor` | (`Application_ID`, `Company_Mentor_ID`) | M:N junction: application ↔ mentor |

All tables are normalised to **3NF**.

---

## SQL Views & Stored Procedure

| Object | Purpose |
|--------|---------|
| `StudentDashboard` | Full student overview: academics, application status, mentor names, performance score |
| `CompanyMentorView` | Mentor dashboard: assigned students, applications, evaluations |
| `UniversityCoordinatorView` | KPIs per coordinator: assigned students, placed count, avg performance, partner companies |
| `GetStudentReport(student_id)` | Stored procedure returning a complete report for a given student |

---

## Application Roles & Features

### Student
- View personal profile, CGPA, and application state
- Track internship applications with status visualization (pie & bar charts)
- View evaluation reports and performance trend over time
- Upload academic documents (transcript / recommendation letter)
- Submit new internship applications
- Export personal data as CSV

### Company Mentor
- View assigned students with metrics (count, avg CGPA, placement rate)
- Analyze student performance (bar and donut charts)
- Submit evaluations with score slider and feedback text
- Generate and export reports (CSV or Excel) for student performance or application status

### University Coordinator
- View all assigned students with placement statistics
- Monitor placement rate, avg CGPA, partner companies, avg performance score
- Manage and download academic documents
- Generate and export placement or company statistics reports

---

## Setup & Running

### 1. Install dependencies

```bash
pip install streamlit mysql-connector-python pandas plotly streamlit-extras xlsxwriter
```

### 2. Set up the MySQL database

```bash
mysql -u root -p < Phase5.sql
```

This creates the `field_training2` database, all tables, sample data, views, and the stored procedure.

### 3. Configure the database connection

In `field_training2_website.py`, update the connection credentials:

```python
connection = mysql.connector.connect(
    host='127.0.0.1',
    user='root',
    password='your_password',   # update this
    database='field_training2'
)
```

### 4. Run the app

```bash
streamlit run field_training2_website.py
```

---

## Login

The login screen accepts a **User ID** and a **Role**. The ID must exist in the corresponding table:

| Role | Verified against |
|------|-----------------|
| Student | `Student.Student_ID` |
| Company Mentor | `Company_Mentor.Company_Mentor_ID` |
| University Coordinator | `Uni_Mentor.Uni_Mentor_ID` |

Sample IDs from the seed data: `1`–`6` for all roles.

---

## Application Workflow

```
Student submits application (Status: Pending)
        ↓
University Mentor reviews → Approve / Reject
        ↓ (if approved)
Academic documents uploaded by Uni Mentor
        ↓
Application visible to Company Mentor
        ↓
Company Mentor reviews → Accept / Reject
        ↓ (if accepted)
Company Mentor submits Evaluation Report at end of internship
```

---

## Notes

- The `Company_Logo` field is used as the primary key for `Company` (assumed unique per company).
- Database credentials are hardcoded — use environment variables or a config file before deploying.
- The `Performance_Score` table stores individual scores separately from `Evaluation_Report` to support the composite-key 3NF decomposition.
