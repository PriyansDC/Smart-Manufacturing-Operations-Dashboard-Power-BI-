# Smart Manufacturing Operations Dashboard (Power BI)

An interactive Power BI report that analyses **12,000 smart-factory machine readings** to track machine health, energy use, load, edge-network performance, faults and maintenance needs.

## Project Overview

Factories generate large volumes of sensor, energy and network data, but it is hard to see where faults start and what drives them. This dashboard brings the data into one report so a plant or operations manager can:

- see overall fault, warning and maintenance levels at a glance
- find which operating conditions (temperature, vibration, load, workload) go with faults
- check how edge-network quality (packet loss, response time) changes when machines fault
- drill down to individual machines

**Authors:** Priyanshu Kumar

## Dataset

- **Source:** Kaggle, *Digital Infrastructure & Smart Manufacturing* dataset ([add dataset link here])
- **Size:** 12,000 records, 300 machines, 6 production lines, 4 factory zones, 3 shifts
- **Period:** 1 Jan 2024 to 24 Mar 2024
- **Columns:** 40 in the raw sheet; 33 kept in the cleaned sheet used by the report
- **Files in this repo:** the original dataset (raw) and a cleaned version with only the required columns, both in the `data/` folder
- **Groups of columns:** machine sensors (temperature, vibration, pressure, torque, load, energy), environment, edge infrastructure (CPU, memory, latency, packet loss, throughput), and labels (`workload_status`, `operation_state`, `maintenance_status`, `infrastructure_operation_level`)

| Column | Meaning |
|---|---|
| `workload_status` | How busy the machine is: Light, Balanced, Heavy, Extreme |
| `operation_state` | Machine condition: Normal, Warning, Overload, Fault, Idle |
| `infrastructure_operation_level` | Health of the digital infrastructure: Optimized, Stable, Degraded, Critical |
| `maintenance_status` | Whether maintenance is required |

## Dashboard Pages

| # | Page | What it shows |
|---|---|---|
| 1 | **Executive Overview** | KPI cards, operation-state split, fault rate by machine type, monthly energy, infrastructure level by line. Holds all slicers, synced to every page. |
| 2 | **Machine Health & Operation Conditions** | Vibration, pressure, torque, temperature and load by operation state, and maintenance status by machine type. |
| 3 | **Energy Consumption Analysis** | Energy by workload, operation state, factory zone and production line. |
| 4 | **Edge Infrastructure Analysis** | Packet loss, CPU, memory and response time by state, level and workload. |
| 5 | **Load Analysis** | Load by workload and state, load bands, and a zone/line/machine-type matrix. |
| 6 | **Machine Details (Drillthrough)** | Record-level details for a selected machine. |

## Key Findings

- **Faults:** 17.6% of readings (2,110) are faults; 38.3% are warnings; 11.5% need maintenance.
- **Maintenance:** 85.8% of maintenance-required readings come from Fault or Overload states. Maintenance is required in 24.2% of Extreme-workload readings versus 2.3% for Light.
- **Thresholds:** Normal and Warning readings never exceed about 84 °C or 7.5 mm/s; beyond these limits machines are in Overload or Fault. About 76% of faults, however, occur inside these limits.
- **Network:** Fault readings average 14.82% packet loss versus about 5.5% in Normal.
- **Energy:** Overload readings use the most energy (24.6 kWh vs 22.5 kWh in Normal). Lines A and B account for 37.4% of total energy, mainly because they record more readings; energy per reading is similar across lines.
- **Load:** Overload makes up 37.4% of readings at 75%+ load, against 21.0% overall.
- **Little difference** in fault, load and maintenance rates across production lines, zones, machine types and shifts.

## Tools & Techniques

- **Power BI Desktop:** report pages, synced slicers, drillthrough, field parameters, custom theme
- **DAX:** measures such as fault rate, maintenance %, load bands and threshold-based readings
- **Microsoft Excel:** raw and cleaned dataset sheets

### Sample DAX

```DAX
Fault Rate % =
DIVIDE(
    CALCULATE(COUNTROWS(cleaned_data), cleaned_data[operation_state] = "Fault"),
    COUNTROWS(cleaned_data)
)

Maintenance Required % =
DIVIDE(
    CALCULATE(COUNTROWS(cleaned_data), cleaned_data[maintenance_status] = "Required"),
    COUNTROWS(cleaned_data)
)
```

## Repository Structure

```
.
├── README.md
├── data/
│   ├── Clean_dataset_only_required_columns.xlsx            # cleaned data used by the report
│   ├── digital_infrastructure_smart_manufacturing_dataset.xlsx   # original dataset (raw + cleaned sheets)
│   └── digital_infrastructure_smart_manufacturing_dataset.csv    # original dataset (CSV)
├── powerbi/
│   ├── smart_manufacturing_dashboard.pbix                  # Power BI report
│   └── SmartManufacturing_PowerBI_Theme.json               # custom Power BI theme
├── presentation/
│   └── Smart_Manufacturing_Dashboard_Presentation.pptx     # project presentation
└── Theme and wallpaper/
    └── powerbi_wallpaper_1920x1080.png                     # page wallpaper image
```

## How to Use

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Windows).
2. Download or clone this repository.
3. Open `powerbi/smart_manufacturing_dashboard.pbix`.
4. If Power BI cannot find the data, go to **Home → Transform data → Data source settings** and point it to the Excel file in `data/`.
5. Use the slicers on Page 1 to filter all pages. Right-click a machine and choose **Drill through** to see its details.

### Theme and wallpaper (optional)
- **Theme:** in Power BI Desktop go to **View → Themes → Browse for themes** and select `powerbi/SmartManufacturing_PowerBI_Theme.json`.
- **Wallpaper:** select a page, then **Format → Page → Wallpaper → Image**, and choose the image from `Theme and wallpaper/`.

## Limitations

- March covers only 1 to 24 March, so monthly totals for March are for a partial month.
- Thresholds used in some measures (84 °C, 7.5 mm/s, 75% load, 10% packet loss) were chosen from the data distribution, not from machine specifications.
- Labels such as `infrastructure_operation_level` are relative categories from the dataset; "Critical" does not mean the system has failed.
- The dataset has few readings for some groups (Light workload: 44, Optimized level: 8), so avoid drawing conclusions from them.

## Acknowledgements

Dataset from Kaggle. Please check the dataset page for its license and terms of use.
