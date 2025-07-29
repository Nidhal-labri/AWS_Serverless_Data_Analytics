<img width="2668" height="789" alt="Blank diagram - Page 1 (2)" src="https://github.com/user-attachments/assets/4d33632e-828e-4afe-b411-2cbb29cf3bd3" /># 🚀 AWS Project: Cloud-Native Data Analytics Workflow
**Building a Serverless Data Pipeline for Spotify Music Insights**

This project demonstrates how I’ve built a fully cloud-native data engineering pipeline to analyze Spotify music data using AWS services.
In this project, we designed and implemented a serverless, end-to-end cloud data workflow using Amazon S3, Glue, Athena, and QuickSight. The goal is to transform raw Spotify datasets into actionable business insights through dynamic dashboards—leveraging modern, scalable, and cost-efficient AWS technologies.

---

## 💡 Solution Description

I designed a cloud-native architecture where each AWS service is connected in a pipeline, with every step preparing the data for the next one.

It all starts with **Amazon S3**, which acts as the staging area where we upload our raw dataset. Then I used **AWS Glue** services to perform the ETL process (Extract, Transform, Load). This service extracts the raw CSV files from S3, applies transformations, and loads the cleaned data back into a different S3 location—our **data warehouse**.

After that, we launch a **Glue Crawler**, which scans the transformed data and generates a metadata catalog that describes the schema and structure of our dataset.

Once cataloged, the data becomes queryable via **Amazon Athena**, using an OLAP (ROLAP) approach. Finally, the Athena query results are connected to **Amazon QuickSight**, which allows us to build interactive and personalized dashboards with clear business KPIs.


---

## 🗺️ Architecture Diagram

<img width="1920" height="1080" alt="architecture" src="https://github.com/user-attachments/assets/20525b85-af98-473f-bcb7-c80455d0fc1b" />

---

## 🧱 Key AWS Services Used

- **Amazon S3**  
  Used for storing both the raw input files (staging area) and the transformed output files (data warehouse).
- **AWS Glue (ETL + Data Catalog)**
  - **Glue ETL Job**: Extracts CSV files, transforms them via a visual data flow (no-code), and loads cleaned results back to S3.
  - **Glue Crawler**: Automatically scans and catalogs the transformed data structure.
- **Amazon Athena**  
  A serverless SQL query engine that performs fast, scalable analysis directly on S3 data without requiring a traditional database.
- **Amazon QuickSight**  
  A BI visualization tool connected to Athena to create dashboards and data visualizations.

---

## 🛠️ Deployment Steps

### 📂 Step 1 – Prepare the Dataset and Upload to Amazon S3

We first download our dataset from Kaggle via this link:  [Spotify Dataset 2023 – Kaggle](https://www.kaggle.com/datasets/tonygordonjr/spotify-dataset-2023)

<img width="1396" height="697" alt="image" src="https://github.com/user-attachments/assets/c9daf48b-34f4-444b-99da-6c4e38cc4fcb" />

For this project, I selected only the following CSV files:
- `Spotify_artist_data_2023.csv`
- `Spotify_tracks_data_2023.csv`
- `Spotify_albums_data_2023.csv`

Then, in AWS S3, I created a cloud storage bucket to serve as the **staging area**. The downloaded files were uploaded into this bucket.

<img width="1415" height="331" alt="image" src="https://github.com/user-attachments/assets/a020dbd9-819b-4847-8574-d6af7688637d" />

### 📂 Step 2 – Extract, Transform & Load Using AWS Glue

I started by creating an ETL job using AWS Glue Studio, which provides a zero-code visual interface focused on data flows.
<img width="1920" height="828" alt="image" src="https://github.com/user-attachments/assets/55580d70-8df1-4c7d-a397-c50c0a889529" />

#### 🟢 Extract:
We extract the needed CSV files from the S3 staging area (artist, album, and track datasets).

#### 🟡 Transform:
We clean, filter, and standardize the data:
- First, we join the album and artist tables based on `artist_id`, then join the result with the tracks table on `track_id`.
- We drop unnecessary columns (like `artist_0` to `artist_11`) which are mostly empty.
- We standardize data types: convert IDs to integers, `explicit` to boolean, `duration` to float, and `release_date` to date.

#### 🔵 Load:
After transformation, the clean data is saved to a new folder in the same S3 bucket, now serving as our **data warehouse**.

✅ This completes the ETL pipeline, from ingestion to cleaned storage.
<img width="932" height="724" alt="image" src="https://github.com/user-attachments/assets/00df58b6-d249-4247-b538-352eed15c200" />

Finally, we **run the ETL job** to process and move the data.

<img width="1226" height="116" alt="image" src="https://github.com/user-attachments/assets/2c05b7d4-7868-4d18-af3e-abc5a35dfa32" />

### 📂 Step 3 – Store and Partition Transformed Data

After running the ETL job, the cleaned data is exported and stored in the S3 data warehouse.

<img width="1269" height="630" alt="image" src="https://github.com/user-attachments/assets/42fb6767-c30d-4d89-84dc-90c1f2fd28b2" />

The output consists of **16 partitioned files**, also called packets. Each file contains a slice of the full dataset.
This partitioning:
- Significantly boosts query performance.
- Reduces scan costs.
- Ensures scalability for large data growth.

### 📂 Step 4 – Run AWS Glue Crawler

To make the transformed dataset queryable, I launched an AWS Glue **Crawler**.
<img width="1400" height="510" alt="image" src="https://github.com/user-attachments/assets/3545b0da-82ad-4d93-a2d1-842817126fed" />

The crawler:
- Automatically detects schema (columns, types, formats).
- Registers the dataset into the **AWS Glue Data Catalog**.

This step bridges our stored data with analytics services like **Athena**.
With one click, our dataset becomes fully structured, documented, and ready to query.


### 📂 Step 5 – Query Data Using Amazon Athena

With the data catalog ready, we move to **Athena** to run analytical SQL queries.
Athena uses a **ROLAP (Relational OLAP)** approach to directly query the data in S3. There is no need to move or duplicate data into a separate database.
Because our dataset is partitioned, Athena can scan only the necessary packets—making queries fast and cost-effective.

<img width="1894" height="756" alt="image" src="https://github.com/user-attachments/assets/b3245874-5b12-417e-8a6f-580bbde689ad" />
<img width="1357" height="583" alt="image" src="https://github.com/user-attachments/assets/372c0e34-a6ef-4c88-935b-92b71479279e" />

### 📂 Step 6 – Visualize with Amazon QuickSight

Our dataset is now ready for visualization.
Using **Amazon QuickSight**, I connected directly to Athena and created a dashboard. This dashboard brings together all business KPIs and answers core analytical questions about the Spotify dataset.
It's fully interactive, scalable, and can be embedded into web or mobile applications.

<img width="1899" height="927" alt="image" src="https://github.com/user-attachments/assets/2685c84f-59c5-488a-8e40-cce19a2ae882" />


---

## 📌 Conclusion

Through this project, we successfully built a fully managed, cost-effective, and scalable cloud-native analytics pipeline using **only serverless AWS services**.
The solution demonstrates how raw data can be turned into business value with minimal infrastructure management and zero code ETL development.
This project can be extended to multiple domains: e-commerce analytics, sales forecasting, IoT metrics, and more.

---

## ✍️ Author

**Made with 💻 by Nidhal Labri**  
🔗 [LinkedIn](https://www.linkedin.com/in/nidhal-labri/)

