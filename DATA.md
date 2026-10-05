# Data

The dataset is not stored in this repository because the file is 124 MB and GitHub rejects files over 100 MB.

| | |
|---|---|
| Name | Pump Sensor Data |
| Source | https://www.kaggle.com/datasets/nphantawee/pump-sensor-data |
| File | `sensor.csv` |
| Size | about 124 MB |
| Rows | 220,320 (one per minute, 1 April 2018 to 31 August 2018) |
| Columns | 55: an unnamed index column, `timestamp`, 52 sensors (`sensor_00` to `sensor_51`), `machine_status` |
| Labels | NORMAL 205,836, RECOVERING 14,477, BROKEN 7 |

## Setup

1. Download `sensor.csv` from the Kaggle page above (a free Kaggle account is required).
2. Place it in the repository root, next to `notebook.ipynb`.
3. Do not rename it. The notebook reads `sensor.csv` from its working directory.

`sensor.csv` is listed in `.gitignore`, so it will not be committed by accident.

## Reference

Nphantawee. (2018). *Pump sensor data* [Data set]. Kaggle. https://www.kaggle.com/datasets/nphantawee/pump-sensor-data
