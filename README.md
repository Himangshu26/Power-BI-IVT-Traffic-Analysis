# 🧠 IVT Traffic Analysis using Power BI

This project analyzes **ad traffic patterns** across six mobile apps — three valid and three invalid (marked IVT).  
The goal is to identify behavioral differences between **Valid** and **IVT-marked** apps and visualize these patterns through Power BI.

---

## 📊 Project Components

### 1. Dataset
- Source: Aggregated hourly data for six apps.
- Columns include:
  - `unique_idfas`, `unique_ips`, `unique_uas`
  - `total_requests`, `impressions`
  - Derived metrics such as `requests_per_idfa`, `idfa_ip_ratio`, `idfa_ua_ratio`, etc.

### 2. Metrics Calculated
| Metric | Formula | Purpose |
|---------|----------|----------|
| Requests per IDFA | total_requests / unique_idfas | Detect request anomalies |
| Impressions per IDFA | impressions / unique_idfas | Measure real ad delivery |
| IDFA/IP Ratio | unique_idfas / unique_ips | Detect proxy/data center traffic |
| IDFA/UA Ratio | unique_idfas / unique_uas | Identify device spoofing |

---

## 🧮 IVT Detection Logic

```DAX
IVT_Status =
VAR RequestsPerIDFA = DIVIDE('Master_IVT_Dataset'[total_requests], 'Master_IVT_Dataset'[unique_idfas], 0)
VAR IDFA_IP = DIVIDE('Master_IVT_Dataset'[unique_idfas], 'Master_IVT_Dataset'[unique_ips], 0)
VAR IDFA_UA = DIVIDE('Master_IVT_Dataset'[unique_idfas], 'Master_IVT_Dataset'[unique_uas], 0)
VAR ImpressionsPerIDFA = DIVIDE('Master_IVT_Dataset'[impressions], 'Master_IVT_Dataset'[unique_idfas], 0)
RETURN
IF(
    (RequestsPerIDFA > 1000 && IDFA_UA > 3) ||
    (ImpressionsPerIDFA < 0.8 && IDFA_IP > 2),
    "IVT",
    "Valid"
)
