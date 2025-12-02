# HealthKit ETL Pipeline

A Python-based ETL (Extract, Transform, Load) pipeline for processing Apple HealthKit data. This project extracts health and workout data from Apple Health XML exports, transforms it into a structured format, and loads it into BigQuery for analysis and visualization.

## Project Structure

```
healthkit/
├── src/                     # Core application code
│   ├── data_processing.py   # Health and workout data processing logic
│   └── config.py            # Configuration management
├── utils/                   # Utility modules
│   ├── bigquery_utils.py    # BigQuery data upload utilities
│   └── logging_config.py    # Logging configuration
├── scripts/                 # Standalone scripts
│   └── refresh.py           # Main script for data processing
├── config/                  # Configuration files (excluded from version control)
├── logs/                    # Log files (excluded from version control)
└── .gitignore               # Git ignore file
```

> **Note**: The `config/` and `logs/` directories are excluded from version control.

## Prerequisites

- Python 3.x
- Google Cloud Platform account with BigQuery access
- Apple Health data export (XML format)


## Usage

1. Export your Apple Health data:
   - Open the Health app on your iPhone
   - Go to your profile
   - Select "Export All Health Data"
   - Save the export file

2. Run the data processing script:
```bash
python scripts/refresh.py
```

The script will:
- Process the health data export
- Calculate daily metrics
- Upload the processed data to BigQuery

## Data Processing

The pipeline processes the following metrics:

### Health Metrics
- Active Calories Burned
- Basal Calories Burned
- Total Calories Burned
- Resting Heart Rate
- Sleep Analysis (Core, Deep, REM)

### Workout Metrics
- Workout Duration
- Distance (for running)
- Elevation Gain
- Workout Type Indicators

## Configuration

Configuration is managed through:
- `src/config.py`: Main configuration file
- `config/` directory: Additional configuration files

### Config directory and `config.json`

The `config/` directory is ignored by git and should contain your local runtime configuration:
- `config/config.json`: required. Holds the BigQuery targets and local paths the pipeline needs.
- (optional) any other environment-specific files you do not want in version control (e.g., a service account key if you point `GOOGLE_APPLICATION_CREDENTIALS` here).

Create `config/config.json` with these keys (replace placeholder values with your own):

```json
{
  "BQ_PROJECT": "your-gcp-project-id",
  "BQ_DATASET": "bigquery_dataset_name",
  "BQ_TABLES": {
    "health_record": "table_for_daily_metrics",
    "workouts_grouped": "table_for_workouts_grouped",
    "vo2max": "table_for_vo2max",
    "sleep_boxplots": "table_for_sleep_boxplots",
    "regimen_boxplots": "table_for_regimen_boxplots"
  },
  "PATHS": {
    "downloads": "/absolute/path/to/your/AppleHealth/exports"
  }
}
```

- `BQ_PROJECT` and `BQ_DATASET` tell the loader where to write.
- `BQ_TABLES` maps each dataset produced by the pipeline to the destination table name.
- `PATHS.downloads` must point to the directory containing your `export*.zip` Apple Health exports; the script picks the latest archive automatically.

## Logging

Logs are stored in the `logs/` directory with the following format:
- Daily log files
- Error tracking
- Processing statistics
