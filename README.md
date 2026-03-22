# Run Spark cluster using Docker

**Note:** Do _not_ use Python 3.14 with this setup. Several dependencies (e.g. `duckdb==1.0.0` via `ibis-bench`) do not have prebuilt wheels for Python 3.14, causing C++ compilation failures during install. A `.python-version` file is included to default to Python 3.12.

```docker compose up -d```

## 1.Generate 20GB of data

### 1.1 Create a Virtual Environemnt and activate .venv

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 1.2 Install package

```pip install -r requirements.txt```

### Generate TCPH data

Note: If you running out of memory, you can change the -s to lower value, such as 5

```bench tpch gen -s 10```

## Copy into the folder data

```cp ./tpch_data/parquet/sf=10/n=1/lineitem/0000.parquet ./data/0000.parquet```

Clean up the generated TPC-H data to free disk space:

```rm -rf tpch_data```

## Run the script to reach the 20GB data size

```chmod +x duplicate.sh && ./duplicate.sh 20```

Verify the data size:

```du -sh data/```

## Run the Spark application

```docker exec -it spark-master /opt/spark/bin/spark-submit --master spark://spark-master:7077 /opt/spark/work-dir/spark-warehouse/data/main.py```
