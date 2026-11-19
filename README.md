# Kitchen Consumption Analysis & Statistical Outlier Detection

**Python | MySQL | Power BI | pandas | DAX | Statistical Analysis**

An end-to-end analysis project that identifies unusual kitchen consumption patterns, checks serving quantities against approved operational standards, and helps operations teams prioritise records for investigation.

```
Excel/CSV files → Python ETL → MySQL staging tables → Statistical analysis → Power BI dashboard
```
<img width="1277" height="748" alt="image" src="https://github.com/user-attachments/assets/13550e11-6b82-4620-b13b-98533e263ab7" />
<img width="1282" height="747" alt="image" src="https://github.com/user-attachments/assets/67880393-afe4-499d-bbff-dcd816dec4a5" />



## Business problem

Kitchen consumption varies with the number of people served, item group, kitchen, session and operating conditions. Reviewing thousands of records by hand makes it hard to spot recurring deviations. This project provides a repeatable process to find exceptions and make them easy to investigate.

## What it does

- Reads multiple Excel and CSV files and loads them into MySQL staging tables
- Keeps source traceability on every row (`Excel_row`, `Relative path`, `Modified time`, `Folder`)
- Skips files that were already processed, using a processing log
- Calculates serving per person: `Out Qty × 1000 / No of Pax`
- Classifies each record against approved Min/Max serving ranges: Below, Within, Above, or Missing data
- Flags statistical outliers with the IQR method, calculated per month and item group: `Upper Limit = Q3 + 1.5 × IQR`
- Feeds a Power BI dashboard with outlier KPIs, trends by kitchen, item group and month, and record-level drill-down

## Operational breach vs statistical outlier

These are treated as separate indicators:

- **Operational breach:** a record exceeds the approved maximum serving per person.
- **Statistical outlier:** a record exceeds the IQR upper limit for its month and item group.

A record can be one without being the other, and neither proves an operational mistake or food wastage. Flagged records need verification with the kitchen team.

## Repository structure

```
src/
  config.py            settings read from environment variables
  etl.py               reusable file-based ETL with traceability and processing log
  load_to_mysql.py     loads consumption, sales and production data into MySQL
  outlier_analysis.py  serving per person, range check, IQR outliers
.env.example           template for local settings (copy to .env)
requirements.txt
```

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env        # fill in your MySQL URL and folder paths
python src/load_to_mysql.py
python src/outlier_analysis.py
```

Source data, the serving reference file and the Power BI report are not included in this repository.

## Data quality investigation

While validating results I traced one incorrect record from the source file through the ETL into MySQL and found that automatic date parsing had swapped day and month (`06/10/2026`, 6 October, was stored as 10 June). The problem arises in the ETL, before Power BI, so a Power BI locale setting cannot fix it.

The planned, more reliable design:

- Explicit date-format configuration per file and column
- Keep the original value alongside the parsed date
- Log ambiguous dates instead of guessing
- Validate conversions and plausible date ranges before loading

`src/etl.py` marks where this belongs (see the `TODO`). It is not implemented yet.

## Limitations and next steps

- The analysis identifies unusual patterns but does not establish their operational causes.
- Planned: total excess quantity across all operational breaches, consumption versus number of people served (regression and residuals), recurring-deviation monitoring, cost impact using reliable item costs, and investigation tracking.

## Skills demonstrated

Python ETL, pandas, SQL (MySQL), IQR outlier detection, descriptive statistics, Power BI, Power Query, DAX, data validation and traceability.
