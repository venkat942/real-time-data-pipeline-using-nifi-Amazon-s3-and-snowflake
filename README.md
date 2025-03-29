

# Real-Time Data Pipeline using Apache NiFi, AWS S3 & Snowflake

This project demonstrates a **real-time data streaming architecture** that automates the process of generating, ingesting, loading, and processing customer data using modern data engineering tools. The pipeline uses **Apache NiFi**, **AWS S3**, **Snowpipe**, **Snowflake Streams & Tasks**, and implements both **SCD Type 1 and Type 2** logic for handling slowly changing dimensions.

---

##  Tech Stack

- **Jupyter Notebook** – For generating fake customer data using Python  
- **Apache NiFi** – For automating file ingestion and upload to AWS S3  
- **AWS S3** – Cloud storage to stage uploaded customer data  
- **Snowflake** – Cloud data warehouse for processing and storing final datasets  
- **Snowpipe** – To automatically load new data from S3 into Snowflake  
- **Streams & Tasks** – For real-time data change detection and automation  
- **SQL (Snowflake)** – To implement transformation logic (SCD1 & SCD2)

---

## 🧩 Architecture Overview

![Architecture Diagram](images/architecture_diagram.png)

1. **Data Generation**  
   - Fake customer data is generated using Faker in Jupyter Notebook and saved as CSV files.

2. **Apache NiFi**  
   - NiFi detects and reads the CSV files.  
   - It uploads them to the AWS S3 bucket (`stream_data` folder) using ListFile → FetchFile → PutS3Object processors.

3. **AWS S3**  
   - Stores the incoming files from NiFi.  
   - Triggers Snowpipe on file arrival.

4. **Snowpipe (Snowflake)**  
   - Automatically loads new files from the `stream_data` folder into the `customer_raw` table in Snowflake.

5. **Snowflake Stream & Task Automation**  
   - A **Stream** tracks changes in `customer_raw`.  
   - Two **Tasks** run every minute:
     - `SCD1 Task`: Updates the `customer` table with only the latest values (no history).  
     - `SCD2 Task`: Updates the `customer_history` table, preserving all versions of records with `START_TIME`, `END_TIME`, and `IS_CURRENT`.

---

## 📁 Repository Structure

```
Real-Time-Data-Pipeline/
├── README.md
├── images/
│   └── architecture_diagram.png
├── jupyter_notebook/
│   └── fake_data_generator.ipynb
├── nifi_templates/
│   └── nifi_flow.xml
├── snowflake_sql/
│   ├── 1_table_creation.sql
│   ├── 2_data_loading.sql
│   ├── 3_scd1.sql
│   └── 4_scd2.sql
├── data/
│   └── sample_fake_data.csv
├── requirements.txt
└── LICENSE
```

---

## ⚙️ Setup Instructions

### 1. Clone the repository
```bash
git clone https://github.com/your-username/real-time-data-pipeline.git
cd real-time-data-pipeline
```

### 2. Generate Fake Data
- Open the notebook in `jupyter_notebook/fake_data_generator.ipynb`  
- Run the code to generate customer data into the `FakeDataset/` folder

### 3. Set Up Apache NiFi
- Import the `nifi_templates/nifi_flow.xml` into your NiFi instance  
- Start the processors to detect and upload new data to AWS S3

### 4. Set Up AWS S3
- Create a bucket (e.g., `your-bucket-name`)  
- Set up the `stream_data/` folder  
- Ensure Snowflake has access via a Storage Integration

### 5. Set Up Snowflake
- Use the scripts in `snowflake_sql/`:
  - `1_table_creation.sql` – Creates all required tables  
  - `2_data_loading.sql` – Creates stage, file format, and Snowpipe  
  - `3_scd1.sql` – Logic for SCD1 transformations  
  - `4_scd2.sql` – Logic for SCD2 transformations

---

## 📌 Key Features

- **End-to-End Automation** – From data generation to SCD transformations  
- **Real-Time Processing** – Files are loaded automatically without batch scheduling  
- **Scalable & Modular** – Easily extensible for new sources or destinations  
- **SCD1 & SCD2 Support** – Tracks both current and historical changes in customer data  

---

##  SCD Logic Overview

### 🔹 **SCD Type 1**
- Updates the `customer` table with the **latest values only**  
- Overwrites existing records when changes are detected  
- Does **not maintain historical data**

### 🔹 **SCD Type 2**
- Tracks **every insert, update, and delete** made to customer records  
- Maintains a **versioned history** in the `customer_history` table  
- Uses:  
  - `START_TIME` – When the version becomes active  
  - `END_TIME` – When the version is no longer active (updated or deleted)  
  - `IS_CURRENT` – Flag to indicate if the record is the latest  
- Allows **full historical tracking**, enabling **audit logs, rollback**, and **trend analysis**

---

## 📷 Screenshots 


## ✨ Future Enhancements

- Add alerting/monitoring for failed Snowflake tasks  
- Connect Snowflake to Tableau or Power BI for live reporting  
- Add Kafka or Kinesis for enhanced real-time ingestion  
- Add unit tests for data validation  

---

## 👤 Author

- **Venkat sathish pidikiti** –| [GitHub](https://github.com/venkat942
)

---

## 📄 License

This project is licensed under the MIT License.
```

---

✅ You're now ready to paste this into your `README.md` on GitHub. Let me know if you'd like a downloadable `.md` file too!
