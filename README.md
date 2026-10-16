# GCP Flight Data ETL

A hands-on Google Cloud data-ingestion exercise that loads flight records from Cloud Storage into BigQuery. The repository includes two newline-delimited JSON (NDJSON) flight-data batches and the BigQuery schema used for a typed load.

## What I did

1. Verified the local Google Cloud CLI environment and its active project configuration.
2. Used the BigQuery CLI (`bq`) to create the `etl_process_gcp.tb_voos` table.
3. Loaded the `2023-07-30` flight-data object from Cloud Storage with schema autodetection.
4. Loaded the same NDJSON source using the repository's explicit schema in `datasets/schema.json`.
5. Initialized this Git repository and documented the workflow for reproducibility.

## Dataset

| File | Format | Records | Flight date |
| --- | --- | ---: | --- |
| `datasets/2023-07-30.json` | NDJSON | 100,000 | 2023-07-30 |
| `datasets/2023-07-31.json` | NDJSON | 100,001 | 2023-07-31 |
| `datasets/schema.json` | BigQuery JSON schema | 12 fields | — |

Each flight record contains the flight date, airline and flight identifiers, origin and destination airports, departure and arrival times/delays, airtime, distance, and an ID. Both data files were validated as valid NDJSON, and each contains unique IDs within its batch.

## Tools

- Google Cloud CLI (`gcloud`)
- BigQuery CLI (`bq`)
- Google Cloud Storage
- BigQuery
- Git

## Reproducible workflow

Set a project without embedding personal account details:

```bash
gcloud config set project <PROJECT_ID>
```

Create the destination table:

```bash
bq mk -t etl_process_gcp.tb_voos
```

Load NDJSON with automatic schema inference:

```bash
bq load --autodetect \
  --source_format=NEWLINE_DELIMITED_JSON \
  etl_process_gcp.tb_voos \
  gs://gcp-etl-flights-dataset/2023-07-30.json
```

Load NDJSON using the versioned schema in this repository:

```bash
bq load \
  --schema datasets/schema.json \
  --source_format=NEWLINE_DELIMITED_JSON \
  etl_process_gcp.tb_voos \
  gs://gcp-etl-flights-dataset/2023-07-30.json
```

> **Note:** BigQuery CLI options require two dashes. For example, use `--source_format=NEWLINE_DELIMITED_JSON`, not `source_format=NEWLINE_DELIMITED_JSON`.

## BigQuery schema

The explicit schema declares 12 required columns:

| Column | Type |
| --- | --- |
| `flight_date` | `DATE` |
| `airline_code` | `STRING` |
| `flight_num` | `INTEGER` |
| `source_airport` | `STRING` |
| `destination_airport` | `STRING` |
| `departure_time` | `INTEGER` |
| `departure_delay` | `INTEGER` |
| `arrival_time` | `INTEGER` |
| `arrival_delay` | `INTEGER` |
| `airtime` | `INTEGER` |
| `distance` | `INTEGER` |
| `id` | `INTEGER` |

## Repository layout

```text
.
├── README.md
└── datasets/
    ├── 2023-07-30.json
    ├── 2023-07-31.json
    └── schema.json
```
