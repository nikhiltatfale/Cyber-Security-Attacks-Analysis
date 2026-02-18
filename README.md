# 🛡️ Cyber Security Attack Analysis

> End-to-end analysis of 40,000 cyber attack records using Python (Pandas), MySQL, and Power BI — covering data ingestion, null treatment, feature extraction, and interactive dashboard reporting.

---

## 📁 Dataset

| Attribute | Detail |
|-----------|--------|
| Source | Kaggle |
| Records | 40,000 |
| Features | 25 columns |
| File | `cybersecurity_attacks.csv` |

**Key columns:** `Timestamp`, `Source/Destination IP`, `Protocol`, `Packet Length`, `Attack Type`, `Severity Level`, `Malware Indicators`, `Alerts/Warnings`, `Geo-location Data`, `Device Information`, `Firewall Logs`, `IDS/IPS Alerts`

---

## 🔧 Tech Stack

| Layer | Tool |
|-------|------|
| Data Processing | Python 3, Pandas |
| Environment | Google Colab |
| Visualization | Power BI |

---

## 🚀 Pipeline Overview

```
Raw CSV → EDA → Null Treatment → Column Standardization → Feature Extraction → Power BI Dashboard
```

---

## 📊 EDA & Preprocessing

### 1. Null Value Treatment

Five columns contained ~50% null values. Filled with descriptive defaults instead of dropping rows:

```python
df['Malware Indicators'].fillna('No Malware Detected', inplace=True)
df['Alerts/Warnings'].fillna('Not Triggered', inplace=True)
df['Proxy Information'].fillna('Unknown', inplace=True)
df['Firewall Logs'].fillna('Unknown', inplace=True)
df['IDS/IPS Alerts'].fillna('Unknown', inplace=True)
```

| Column | Null Count | Fill Value |
|--------|-----------|------------|
| Malware Indicators | 20,000 | `No Malware Detected` |
| Alerts/Warnings | 20,067 | `Not Triggered` |
| Proxy Information | 19,851 | `Unknown` |
| Firewall Logs | 19,961 | `Unknown` |
| IDS/IPS Alerts | 20,050 | `Unknown` |

### 2. Column Standardization

```python
df.columns = df.columns.str.lower().str.replace(' ', '_')
df.rename(columns={
    'alerts/warnings': 'alerts_or_warnings',
    'geo-location_data': 'geological_location_data',
    'ids/ips_alerts': 'ids_or_ips_alerts'
}, inplace=True)
```

### 3. Feature Extraction

**Timestamp → Date & Time**
```python
df['timestamp'] = pd.to_datetime(df['timestamp'])
df['date'] = df['timestamp'].dt.date
df['time'] = df['timestamp'].dt.time
df.drop('timestamp', axis=1, inplace=True)
```

**Geo-location → City & State**
```python
df[['city', 'state']] = df['geological_location_data'].str.split(',', expand=True)
df['city'] = df['city'].str.strip()
df['state'] = df['state'].str.strip()
df.drop('geological_location_data', axis=1, inplace=True)
```

**Device Information → Operating System**
```python
def extract_os(device_info):
    if pd.isna(device_info):
        return 'Unknown'
    os_list = ['Windows', 'Linux', 'Mac OS', 'iOS', 'Android', 'Windows CE']
    for os in os_list:
        if os.lower() in device_info.lower():
            return os
    return 'Other'

df['operating_system'] = df['device_information'].apply(extract_os)
```

---

## 📈 Power BI Dashboard

**DAX Measures:**
- `Hour` — extracted from `time` for hourly attack pattern analysis
- `Month`, `Year` — extracted from `date` for time-series trends
- `Total Alerts Triggered` — count where `alerts_or_warnings != 'Not Triggered'`
- `Total Malware Detected` — count where `malware_indicators != 'No Malware Detected'`

**Dashboard KPIs:**

| Metric | Value |
|--------|-------|
| Total Attacks | 40K |
| Alerts Triggered | 20K |
| Malware Detected | 20K |

**Filters:** Attack Type · Traffic Type · Network Segment · Year

---

## 🔍 Key Findings

- **Windows** accounts for **44.88%** of all targeted devices, followed by Linux (28.97%) and Mac OS (22.1%)
- **March** records peak attack volume (3.7K); November–December are lowest (2.7K)
- **DNS, HTTP, FTP** traffic types are near-evenly split (~13,300 each), indicating multi-vector exploitation
- **Severity distribution** is balanced — Medium (13,435), High (13,382), Low (13,183)
- **~50% of attacks** generated no malware indicator or alert signal, revealing a detection coverage gap
- **Manipur, Uttar Pradesh, Gujarat** are the top targeted states; **Ghaziabad** leads by city

---

## 📂 Repository Structure

```
├── 📁 data/
│   ├── cybersecurity_attacks.csv                                    # Raw dataset (40K records, 25 columns)
│   └── cybersecurity_attacks_cleaned.csv                           # Cleaned & feature-engineered dataset
│
├── Dashboard.png                                                    # Power BI dashboard screenshot
│
├── Project_Report.pdf                                               # Full project report
│
├── Presentation.pptx                                                # Project presentation slides
│
├── 📁 screenshots/
│   ├── df__head__.png                                               # Dataset sample view
│   ├── df_info__.png                                                # Column types & null counts
│   ├── df_describe_include__all__.png                               # Statistical summary
│   ├── null_value_check.png                                         # Null value detection
│   ├── null_replace_in_malware_indicator.png                        # Null fill — Malware Indicators
│   ├── null_replace_in_alerts_or_warnings.png                       # Null fill — Alerts/Warnings
│   ├── null_replace_in_proxy_information.png                        # Null fill — Proxy Information
│   ├── null_replace_in_firewall_logs.png                            # Null fill — Firewall Logs
│   ├── null_replace_in_ids_or_ips_alerts.png                        # Null fill — IDS/IPS Alerts
│   ├── replace_column_name_with_snake_cases.png                     # Column standardization
│   ├── extract_date_and_time_from_timestamp.png                     # Feature extraction — Timestamp
│   ├── extract_city_and_state_from_geological_location.png          # Feature extraction — Geo-location
│   └── extract_operating_system_from_device_information.png         # Feature extraction — OS
│
└── README.md
```

---

## 👤 Author

**Nikhil Mohan Tatfale**
