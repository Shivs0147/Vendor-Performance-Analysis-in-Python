# 📊 CSV to SQLite Ingestion Pipeline

<p align = 'justify'>

This project uses a Python script (inside a Jupyter Notebook) to **automate the loading of multiple CSV files into a SQLite database**. It's useful for small ETL jobs or prepping data for analysis tools like Power BI or Tableau.</p>

---

## 📁 Folder Structure

```plaintext
.
├── Ingestion_db.ipynb        <- Jupyter notebook with all ingestion logic
├── data/                     <- Place your CSV files here
│   ├── begin_inventory.csv
│   ├── end_inventory.csv
│   ├── purchase_prices.csv
│   ├── purchases.csv
│   ├── Sales.csv
│   ├── vendor_invoice.csv
│   └── ...
├── logs/
│   └── ingestion_db.log      <- All logs about the ingestion process
└── inventory.db              <- SQLite database created automatically
```
---

## 🧠 How It Works (Line by Line Explanation)

### 🔹 Step 1: Import necessary libraries

```python
import pandas as pd                   # For handling CSV data
import os                             # For reading file names in folders
from sqlalchemy import create_engine  # For database connection
import logging                        # For logging messages
import time                           # For tracking how long it takes
```

> These are the essential Python libraries we'll use.  
> `pandas` for DataFrames, `os` to read file names, `sqlalchemy` to talk to the database.

---
### 🔹 Step 2: Setup the Logger

```python
logger = logging.getLogger("ingest_db")
logger.setLevel(logging.DEBUG)

file_handler = logging.FileHandler("logs/ingestion_db.log", mode="a")
formatter = logging.Formatter("%(asctime)s - %(levelname)s - %(message)s")
file_handler.setFormatter(formatter)

if not logger.hasHandlers():
    logger.addHandler(file_handler)
```

> This part **sets up logging** to a file called `ingestion_db.log`.  
> Every time something happens (like a file being ingested), it writes a timestamped log entry.

---
### 🔹 Step 3: Create the Database Connection

```python
engine = create_engine('sqlite:///inventory.db')
```

> This creates a **SQLite** database file named `inventory.db` if it doesn't exist.  
> It will be used to store your data from the CSV files.

---
### 🔹 Step 4: Function to Ingest DataFrame into the DB

```python
def ingest_db(df, table_name, engine):
    '''This function will ingest the dataframe into database table'''
    df.to_sql(table_name, con=engine, if_exists='replace', index=False)
```

> This function takes 3 things:
> - 1 `DataFrame[df]`
> - 2 `Table's Name[table_name]`
> - 3 `DB engine[engine]`
>  
> And then writes the data into the database table (replacing if it already exists).

---
### 🔹 Step 5: Function to Loop Through All CSV Files

```python
def row_data_load():
    '''This function will load the CSV files from the 'data' directory, 
    convert them into pandas DataFrames, and ingest them into the database.'''
    
    start = time.time()  # Record the start time of the process
    
    # Loop through all files in the 'data' directory
    for files in os.listdir('data'):
        # Process only files that end with '.csv'
        if files.endswith('.csv'):
            # Read the CSV file into a pandas DataFrame
            df = pd.read_csv(os.path.join('data', files))
            
            # Log which file is being ingested
            logger.info(f'Ingesting {files} into DB')
            
            # Ingest the DataFrame into the database
            # The table name is derived from the filename (excluding '.csv')
            ingest_db(df, files[:-4], engine)
    
    end = time.time()  # Record the end time of the process
    
    # Calculate total time taken in minutes
    total_time = (end - start) / 60
    
    # Log the completion of the ingestion process
    logger.info('----------- Ingestion Completed -----------')
    logger.info(f'Total Time Taken: {total_time:.2f} minutes')

```

> This function does the actual work:
> - It loops through each `.csv` file inside the `data/` folder.
> - Reads it into a DataFrame using `pandas.read_csv`
> - Calls `ingest_db()` to insert the data into the SQLite DB
> - Logs start time, completion, and total time taken

---
### 🔹 Step 6: Run the Script

```python
if __name__ == '__main__':
    row_data_load()
```

> This is a Python convention that means:  
> ➤ **Only run `row_data_load()` if this file is being executed directly.**

---
## ✅ Summary of Workflow

1. We place CSV files inside the `data/` folder.
2. Run the notebook or export it as a `.py` file and run:
   ```bash
   python Ingestion_db.py
   ```
3. All CSVs are read and loaded into `inventory.db`, with one table per file.
4. Logs are saved in `logs/ingestion_db.log`.

---
## 🔧 Technologies Used

| Tool         | Use Case                      |
|--------------|-------------------------------|
| Python       | Programming language           |
| pandas       | Reading & manipulating CSVs    |
| SQLAlchemy   | DB connection & write tables   |
| SQLite       | Lightweight embedded DB        |
| logging      | Tracks steps taken             |

---

## 📝 Example Log Output

```
2025-07-30 16:57:01,935 - INFO - Ingesting begin_inventory.csv into DB
2025-07-30 16:57:07,322 - INFO - Ingesting end_inventory.csv into DB
2025-07-30 16:57:22,446 - INFO - Ingesting purchases.csv into DB
2025-07-30 16:58:08,618 - INFO - Ingesting purchase_prices.csv into DB
2025-07-30 16:58:11,830 - INFO - Ingesting Sales.csv into DB
2025-07-30 16:58:50,223 - INFO - Ingesting vendor_invoice.csv into DB
2025-07-30 16:58:50,541 - INFO - ----------- Ingestion Completed -----------
2025-07-30 16:58:50,541 - INFO - Total Time Taken: 1.82 minutes
```

---

## 👤 Author & Contact

<ul>
  <li>Name - Shivam Gabani</li>
    
  [🖂](https://mail.google.com/mail/?view=cm&to=shivamgabani.744@outlook.com)
  [💬](https://www.linkedin.com/in/shivam-gabani-38192a36b/details/contact-info/)
  <li>📍 Surat, Gujarat.</li>
</ul>

## 🙌 Thanks for Scrolling!

If you liked this project, feel free to star ⭐ the repo or connect with me on LinkedIn.

I’m always open to feedback, learning, and new collaborations.

Cheers!  
**– Shivam Gabani**
