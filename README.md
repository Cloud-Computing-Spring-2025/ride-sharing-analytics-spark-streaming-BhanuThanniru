# Real-Time Ride-Sharing Analytics with Apache Spark

## Overview
This project processes real-time ride-sharing data using Apache Spark Structured Streaming. It consists of three tasks that ingest, aggregate, and analyze streaming data, writing results to CSV files.

---

## Setup

### Prerequisites
- Apache Spark 3.5+
- Python 3.8+ with dependencies:
  ```bash
  pip install pyspark faker
  ```

### Start Data Generator
Run the Python script to simulate ride-sharing events:
```bash
python data_generator.py
```
**Output** (example):
```
Streaming data to localhost:9999...
New client connected: ('127.0.0.1', 45872)
Sent: {'trip_id': '64f666bb-ddc4...', 'driver_id': 1, ...}
```

---

## Tasks

### Task 1: Raw Data Ingestion
**Purpose**: Parse and store raw ride events from the socket stream.  
**Command**:
```bash
spark-submit task_1_streaming_ingestion.py
```
**Output Directory**: `output/task1/data`  
**Sample Output** (`part-00000-*.csv`):
```
trip_id,driver_id,distance_km,fare_amount,timestamp
7a72b6eb-d6ff-45f4-90ef-eaff5864c621,93,17.97,97.69,2025-04-01 23:29:50
cb7a5d4c-faa1-47a6-b60c-3ada99c3dc7a,36,32.96,26.3,2025-04-01 23:29:52
d71c4d42-cfb5-4ea7-9500-7b97f67aeb8f,36,20.23,45.69,2025-04-01 23:29:54
```

---

### Task 2: Driver-Level Aggregations
**Purpose**: Calculate total fare and average distance per driver.  
**Command**:
```bash
spark-submit task_2_real_time.py
```
**Output Directory**: `output/task2`  
**Sample Output** (`part-00000-*.csv`):
```
driver_id,total_fare,avg_distance
87,174.94,28.203333333333337
64,56.08,47.72
30,67.68,2.04
...
```

---

### Task 3: Windowed Fare Analytics
**Purpose**: Compute total fares in 5-minute sliding windows (1-minute interval).  
**Command**:
```bash
spark-submit task_3_window_time.py
```
**Output Directory**: `output/task3`  
**Sample Output** (`part-00000-*.csv`):
```
window_start,window_end,total_fare
2025-04-02T00:08:00.000Z,2025-04-02T00:13:00.000Z,4094.18
2025-04-02T00:10:00.000Z,2025-04-02T00:15:00.000Z,777.16
2025-04-02T00:05:00.000Z,2025-04-02T00:10:00.000Z,3317.02
...
```

---

## Folder Structure
```
ride-sharing-analytics-spark-streaming/
├── data_generator.py       # Simulates ride events
├── task_1_streaming_ingestion.py                # Ingests raw data
├── task_2_real_time.py                # Driver-level aggregations
├── task_3_window_time.py                # Windowed analytics
├── output/
│   ├── ride_data/         # Raw ride data (CSV)
│   ├── output_task2/              # Driver aggregates (CSV)
│   └── output_task3/              # Windowed totals (CSV)
└── checkpoint/            # Spark metadata (not for processing)
    ├── commits
    ├── chk_task2/
    └── chk_task3/
```

---

## Notes
1. **Timestamp Format**: Outputs use ISO-8601 format (`2025-04-01T22:49:00.000Z`) for window boundaries.  
2. **Future Dates**: Timestamps appear in 2025 due to the synthetic data generator.  
3. **Checkpoints**: Metadata is stored in `checkpoint/` – do not modify/delete while jobs are running.  
4. **Output Files**: CSV files are partitioned and may contain multiple `part-*.csv` files.