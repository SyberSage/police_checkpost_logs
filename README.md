# 🚓 SecureCheck: Police Checkpost Logs Analytics

A Python, SQL and Streamlit based dashboard that stores **police traffic-stop records** in a MySQL database and turns them into live insights, covering stop outcomes, violations, driver demographics, time patterns and individual vehicle look-ups.

The application is built with **Python, MySQL and Streamlit**. Every chart, table and KPI on the dashboard is produced by an SQL query run against the database, and users can also look up a vehicle or enter the details of a stop to get a clear outcome summary.

## 📌 Project Overview

**SecureCheck** is a digital ledger for police check post logs. Manual logging and scattered databases slow down security processes, so this project centralizes the stop records in one SQL database and puts a Python dashboard on top of it for fast, searchable insights.

The project covers the complete pipeline:

* 🧹 Data Cleaning & Preprocessing (pandas)
* 🗄️ SQL Database Design (schema, indexes) and bulk data loading
* 🔎 20 SQL queries, from simple aggregations to window functions, CTEs and joins
* 📊 Interactive Streamlit Dashboard with KPIs, charts and tables
* 🔍 Parameterized vehicle search
* 🧾 Stop Outcome & Violation summary generator

## ✨ Features

* **Overview** page with live KPI cards: total stops, arrests, searches and DUI stops
* **Visual Insights**: stops by violation, driver gender distribution and stop outcome distribution (interactive Plotly charts)
* **Insights Dashboard**: 14 SQL questions grouped by vehicle, demographic, time and duration, violation and location
* **Advanced Insights**: 6 analyses using subqueries, joins, CTEs and window functions
* **Vehicle Search**: look up any vehicle number, full or partial, using a safe parameterized query
* **Stop Outcome & Violation**: enter the details of a stop and get a written summary that includes the outcome, arrest status, DUI, search, drug-related flag and duration
* Dropdown values in the summary form are read straight from the database
* Database password kept out of the code (loaded from a `.env` file)
* Top navigation bar for quick movement between sections

## 🗄️ Database Design

Data is stored in a single MySQL table, `traffic_stops`, with indexes on the most searched columns (`vehicle_number`, `stop_date`, `country_name + violation`, `stop_outcome`).

| Column               | Type          | Description                                  |
| -------------------- | ------------- | -------------------------------------------- |
| `id`                 | INT (PK)      | Auto-increment record ID                     |
| `stop_date`          | DATE          | Date of the stop                             |
| `stop_time`          | TIME          | Time of the stop                             |
| `country_name`       | VARCHAR       | Country where the stop took place            |
| `driver_gender`      | VARCHAR       | M or F                                       |
| `driver_age`         | INT           | Driver age (after cleaning)                  |
| `driver_race`        | VARCHAR       | Driver race / ethnicity                      |
| `violation`          | VARCHAR       | Speeding, DUI, Seatbelt, Signal, Other       |
| `search_conducted`   | BOOLEAN       | Whether a search was conducted               |
| `search_type`        | VARCHAR       | Frisk or Vehicle Search (NULL if blank)      |
| `stop_outcome`       | VARCHAR       | Warning, Ticket or Arrest                    |
| `is_arrested`        | BOOLEAN       | Whether the driver was arrested              |
| `stop_duration`      | VARCHAR       | 0-15 Min, 16-30 Min or 30+ Min               |
| `drugs_related_stop` | BOOLEAN       | Whether the stop was drug-related            |
| `vehicle_number`     | VARCHAR       | Vehicle registration number                  |

## 🔎 SQL Analysis

| Level   | Category           | Questions answered                                                                                                  |
| ------- | ------------------ | ------------------------------------------------------------------------------------------------------------------- |
| Medium  | Vehicle-based      | Top 10 vehicles in drug-related stops; vehicles most frequently searched                                            |
| Medium  | Demographic-based  | Arrest rate by age group; gender distribution by country; race + gender with the highest search rate                |
| Medium  | Time and duration  | Stops by hour of day; average stop duration by violation; are night stops more likely to lead to arrests?           |
| Medium  | Violation-based    | Violations linked to searches or arrests; most common violations among drivers under 25; violations that rarely lead to a search or arrest |
| Medium  | Location-based     | Drug-related stop rate by country; arrest rate by country and violation; countries with the most searches           |
| Complex | Advanced           | Yearly stops and arrests by country (subquery + window functions); violation trends by age and race (join with subquery); stops by year, month and hour; violations with high search and arrest rates (window functions); driver demographics by country; top 5 violations by arrest rate |

## 🔄 Workflow

The general workflow followed in the project is:

```text
Raw Traffic Stop Dataset (Excel)
   ↓
Data Cleaning & Preprocessing (pandas)
   ↓
cleaned_traffic_stops.csv
   ↓
MySQL Database + Table Creation (with indexes)
   ↓
Bulk Data Load
   ↓
SQL Queries (aggregations, window functions, CTEs, joins)
   ↓
Streamlit Dashboard (KPIs, charts, tables, vehicle search, outcome summary)
```

### Data cleaning steps

* Dropped columns that contained only missing values
* Filled missing `driver_age` with the median, `violation` and `search_type` with `Unknown`, and missing `search_conducted`, `is_arrested` and `drugs_related_stop` with `False`
* Removed duplicate rows
* Converted `stop_time` to a time and `stop_date` to a date
* Dropped the raw helper columns `driver_age_raw` and `violation_raw`
* Aligned `is_arrested` with `stop_outcome` (arrested = the outcome is `Arrest`), because the raw column did not agree with the outcome
* Exported the result as `cleaned_traffic_stops.csv`

## 🛠️ Technologies Used

### Programming

* Python

### Data Processing

* Pandas
* NumPy

### Database

* MySQL
* mysql-connector-python

### Visualization

* Plotly

### Dashboard

* Streamlit

### Configuration

* python-dotenv

### Development Tools

* Jupyter Notebook
* VS Code
* GitHub

## 📂 Project Structure

```text
SecureCheck/
│
├── 1_Data_cleaning.ipynb
├── 2_Police_mysql.ipynb
│
├── app.py
│
├── cleaned_traffic_stops.csv
│
├── requirements.txt
├── .env                (not committed, holds your MySQL password)
├── .gitignore
│
└── README.md
```

## ⚙️ Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-username/your-repository-name.git
```

### 2. Navigate to the project directory

```bash
cd your-repository-name
```

### 3. Create a virtual environment

```bash
python -m venv .venv
```

### 4. Activate the virtual environment

**Windows:**

```bash
.venv\Scripts\activate
```

**Mac/Linux:**

```bash
source .venv/bin/activate
```

### 5. Install dependencies

```bash
pip install -r requirements.txt
```

### 6. Install and start MySQL

Install MySQL Server and make sure it is running on `localhost:3306`.

### 7. Add your MySQL password

Create a file named `.env` in the project folder:

```text
mysql_password=your_mysql_password
```

Add `.env` to your `.gitignore` so the password is never pushed to GitHub.

### 8. Load the data into MySQL

Open and run `2_Police_mysql.ipynb`. It creates the database and the `traffic_stops` table, then loads `cleaned_traffic_stops.csv`. The database name in the notebook and in `app.py` must be the same.

### 9. Run the Streamlit application

```bash
streamlit run app.py
```

The application will open in your browser.

## 📦 Requirements

Example `requirements.txt`:

```text
streamlit>=1.50
pandas
plotly
mysql-connector-python
python-dotenv
openpyxl
notebook
```

`openpyxl` is only needed to re-run the cleaning notebook on the raw Excel file.

## 🌐 Deployment

The dashboard queries a live MySQL database, so it runs as-is on a local machine. **Streamlit Community Cloud cannot reach a MySQL server running on your own computer.** To deploy online:

1. Host the database on a cloud MySQL service and load the data into it.
2. Update the connection details (`host`, `user`, `database`) in `app.py`, and remove `ssl_disabled=True`, which is only meant for a local server.
3. Store the database password in Streamlit **Secrets** instead of a `.env` file.
4. Push the project to GitHub, connect it to Streamlit Community Cloud, select `app.py` as the main file and deploy.

## ⚠️ Disclaimer

This project is intended **for educational and demonstration purposes only**.

The dataset appears to be synthetic. Records are split almost evenly across categories (for example, roughly one third of stops end in each outcome), every vehicle number appears only once, and all stops fall within a 46-day window (1 Jan to 15 Feb 2020). Because of this, rankings between categories differ by very small margins and should be read as demonstrations of the SQL and dashboard, not as real-world findings. Results from this application should not be used as the sole basis for any law-enforcement or policy decision.

## 👨‍💻 Author

Yogaprabhu Ramesh Kanna

Data Science | Machine Learning | Python

Portfolio: https://sybersage.github.io/Portfolio/

GitHub: https://github.com/sybersage

LinkedIn: https://www.linkedin.com/in/yoga-prabhu

---

⭐ If you found this project useful, consider giving the repository a star!
