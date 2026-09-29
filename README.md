# 🏭 Textile Mill Sensor Data Analyzer

A NumPy-based data analysis project that processes hourly temperature and humidity sensor readings from a textile mill, flags dangerous temperature conditions, and produces a daily monitoring report — while handling missing sensor data correctly.

---

## 📌 Overview

Textile manufacturing is highly sensitive to environmental conditions. Excess heat can damage fibers and equipment, and humidity affects yarn quality and machine performance. Sensors in real factories also fail or drop readings regularly.

This project simulates that reality. It analyzes **14 days of hourly readings (336 data points per sensor)** with **at least 5% missing values**, and produces a clear per-day report of averages, safety breaches, and data-quality issues.

## 🎯 Objectives

- Load and inspect a textile-engineering sensor dataset with **Pandas**
- Convert sensor columns into **NumPy arrays** for efficient computation
- Simulate realistic sensor dropouts (`np.nan`, ≥ 5% of values)
- Reshape flat hourly data into a **(days × 24 hours)** matrix
- Detect temperature threshold breaches using **boolean masking**
- Compute daily statistics safely with **NaN-aware functions**
- Verify robustness against a **fully-broken day** (all readings missing)

## 🧰 Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3 | Core language |
| NumPy | Array reshaping, boolean masking, NaN-safe aggregation |
| Pandas | CSV loading and dataset inspection |
| Google Colab / Jupyter | Development environment |

## 📂 Dataset

The notebook loads `textile_engineering_dataset.csv` (1,000 rows, 11 columns), which includes:

`machine_speed_rpm`, `temperature_c`, `humidity_percent`, `vibration_level`, `energy_usage_kwh`, `production_count`, `defect_count`, `hours_since_last_maintenance`, `maintenance_required`, `alert_triggered`, and related fields.

Only `temperature_c` and `humidity_percent` are used in this analysis.

> **Note:** The loaded CSV contains no missing values and is not structured as 14 days of hourly data. Because the project requires ≥ 14 days of hourly readings with ≥ 5% deliberately missing values, the notebook automatically **generates simulated data** when those conditions aren't met:
> - Temperature ~ Normal(μ = 30 °C, σ = 5)
> - Humidity ~ Normal(μ = 60 %, σ = 10)
> - 5% of readings randomly set to `np.nan` in each sensor

## ⚙️ Methodology

1. **Load & inspect** – Read the CSV, check structure, dtypes, and null counts.
2. **Prepare data** – Extract sensor arrays; simulate 336 hourly readings with missing values if the source data doesn't meet requirements.
3. **Reshape** – Convert 1-D arrays to shape `(14, 24)` (days × hours), padding with `NaN` if the length isn't a multiple of 24.
4. **Flag danger conditions** – Boolean mask `temp > 40.0 °C`, summed per day with `np.sum(..., axis=1)`.
5. **Track data quality** – Count missing readings per day using `np.isnan()`.
6. **Compute daily averages** – Use `np.nanmean(..., axis=1)` so missing values don't corrupt results.
7. **Stress test** – Force one entire day to `NaN` and confirm the pipeline still runs.

## 📊 Sample Output

```
Danger Temperature Threshold: 40.0°C
Number of danger breaches per day:  [0 1 2 0 0 0 0 0 0 0 0 0 0 0]
Missing temperature readings/day:   [3 2 3 0 1 1 1 3 2 0 0 0 0 0]
Missing humidity readings/day:      [0 1 1 1 0 1 1 3 1 3 0 2 1 1]
```

**Daily report (excerpt):**

```
Day 1:
  Average Temperature: 31.32°C
  Average Humidity: 61.51%
  Danger Breaches (Temp > 40.0°C): 0
  Missing Temperature Readings: 3
  Missing Humidity Readings: 0
```

**Broken-day test (Day 14, all readings missing):**

```
Day 14:
  Average Temperature: nan°C
  Average Humidity: nan%
  Danger Breaches (Temp > 40.0°C): 0
  Missing Temperature Readings: 24
  Missing Humidity Readings: 24
```

Days 2 and 3 were the only days with temperature breaches (1 and 2 respectively).

## 🔍 Key Takeaways

- `np.nanmean()` correctly ignores missing readings; plain `np.mean()` would return `NaN` for any day with a single dropout.
- A fully-missing day yields `NaN` (with a `RuntimeWarning: Mean of empty slice`) rather than a misleading number — the correct signal that the day has no usable data.
- Comparisons with `NaN` evaluate to `False`, so missing readings are never falsely counted as danger breaches.
- Reporting **missing-reading counts alongside averages** lets operators judge how trustworthy each day's numbers are.

## 🚀 Getting Started

### Prerequisites

```bash
pip install numpy pandas
```

### Run

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/textile-mill-sensor-analyzer.git
   cd textile-mill-sensor-analyzer
   ```
2. Place `textile_engineering_dataset.csv` in the project folder (or update `file_path` in the notebook; the Colab default is `/content/textile_engineering_dataset.csv`).
3. Open and run `Project_2_Textile_Mill_Sensor_Data_Analyzer.ipynb` in Jupyter or [Google Colab](https://colab.research.google.com/).

## 📁 Project Structure

```
├── Project_2_Textile_Mill_Sensor_Data_Analyzer.ipynb   # Main analysis notebook
├── textile_engineering_dataset.csv                     # Source dataset
└── README.md
```

## 🔮 Future Improvements

- Add visualizations (daily temperature/humidity trends, breach heatmap) with Matplotlib
- Impute missing values (linear interpolation) and compare against NaN-skipping
- Add configurable thresholds for humidity as well as temperature
- Suppress or handle the empty-slice warning explicitly for fully-missing days
- Use the real dataset's timestamps and additional features (vibration, energy usage) for correlation analysis
- Wrap the logic into a reusable function/CLI that exports the daily report to CSV or PDF

## 👤 Author

**Alia Maryam**
[GitHub](https://github.com/Alia Maryam) · [LinkedIn](www.linkedin.com/in/
alia-maryam-a59a47355)

