## 34-ETL-subsystem:

### Subsystem - 1: DATA PROFILING 

- focus is on data quality

- Data Profiling is the process of examining the source data to understand its quality, structure, and content before using it in the data warehouse. It helps identify issues like missing values, errors, and inconsistencies, and decides whether the data is suitable to use and what cleaning or processing steps will be needed later.

- we are also identifying what kind of mess you'll need to clean up later.

### Subsystem - 2: Change Data Capture [CDC]

- Change Data Capture (CDC) is the process of identifying only the data that has changed in the source system, such as new, updated, or deleted records, instead of processing the entire dataset

- ==when there is a change in the source systems , then instead of processing the whole data system again , this CDC identifies and sends only the changed data to the ETL process==

> [!info]+ Ways to capture data changes
> ==Audit columns==
> ==Timed extracts==
> ==Full Diff Compare==
> ==Database log scraping==
> ==Message queue monitoring==

### Subsystem - 3:  Extract System

- The Extract System is responsible for taking data from source systems (like databases, files, or APIs) and moving it into the further ETL process

---

### ==4. Data Cleansing (The Filter)==
*   ==**What it does:** This step scans the incoming data for errors and fixes them. It looks for things like missing values, wrong formats (e.g., a phone number with== 
==letters), or impossible dates (e.g., a birthdate in the future).==
*   ==**Why it matters:** If you put bad data into your system, you get bad reports ("Garbage In, Garbage Out").==
*   ==**Simple Example:** Imagine importing a list of emails. This system automatically fixes "gmail.con" to "gmail.com" or removes entries that have no email address at== 
==all.==

goals:
- early diagnosis of the data errors / problems 
- using the errors and problems source systems can be updated

### ==5. Error Event Tracking (The Logbook)==
*   ==**What it does:** When the Cleansing system (Subsystem 4) finds a mistake, it doesn't just fix it silently; it writes down what happened. It creates a log of every== 
==error found.==
*   ==**Why it matters:** This helps the IT team spot patterns. If the same error happens every day, they can go to the source and fix the root problem so it stops== 
==happening.==
*   ==**Simple Example:** If 500 customer records arrive without a "Country" code today, the system logs this event. The manager sees the log and calls the sales team to== 
==ask why they aren't collecting country codes.==

[just for ref]
![[Pasted image 20260413234837.png|500]]

### ==6. Audit Dimension Creation (The Label)==
*   ==**What it does:** This attaches a "digital sticker" or tag to every single row of data. This tag records **who** loaded the data, **when** it was loaded, and **which== 
==version** of the process was used.==
*   ==**Why it matters:** It creates trust. If a number in a report looks weird, you can check the "label" to see where that data came from and when it was processed.==
*   ==**Simple Example:** Like a food product having a "Packaged On" date and a "Batch ID" on the box. If the food goes bad, you know exactly which batch to recall.==

- it is the meta data about a dimension
![[Pasted image 20260413234939.png]]



### ==7. Deduplication (The Merge)==
*   ==**What it does:** This finds duplicate records and combines them. Often, the same customer or product appears in multiple source systems with slight differences== 
==(e.g., "John Smith" vs. "J. Smith"). This system merges them into one unique profile.==
*   ==**Why it matters:** It prevents double-counting. You don't want to think you have two customers when you only have one.==
*   ==**Simple Example:** Your phone contacts might have "Mom" and "Mom - Home." Deduplication merges these into one contact, keeping the most recent phone number (this is== 
==called a "survivorship rule").==

### ==8. Data Conformance (The Standardizer)==
*   ==**What it does:** This ensures everyone uses the same language. It makes sure that "Gender" is always coded as "M/F" everywhere, not "Male/Female" in one table and== 
=="1/0" in another. It also ensures metrics (like Revenue) are calculated the same way across the whole company.==
*   ==**Why it matters:** It allows different departments to compare apples to apples.==
*   ==**Simple Example:** If the US team reports money in **Dollars** and the UK team reports in **Pounds**, you can't add them together. Conformance converts everything== 
==to Dollars so the total is accurate.==


![[Pasted image 20260413235558.png]]

### Summary Analogy
Imagine you are running a **Library**:
1.  **Cleansing:** You repair torn pages and fix typos in books before shelving them.
2.  **Tracking:** You write down a note every time a book arrives damaged so you can complain to the publisher.
3.  **Audit:** You stamp every book with the date it arrived and the librarian's initials.
4.  **Deduplication:** You realize you have three copies of the same book; you keep the best one and return the others.
5.  **Conformance:** You ensure every book uses the same Dewey Decimal System so they can all be found on the same shelves.

---
### Subsystem 9 : SCD Manager

- SCD are dimension tables in the DW , which contains all the data attributes that going to slowly change over time

- 0 - 7 types with handling 

### Subsystem 10 : Surrogate key generator

- It will generate the surrogate keys for all the dimensions independently
- any example of u r own

### Subsystem 11 : Hierarchy Manager

- for a single / same dimension [data] this manager will deliver different structures [structural representations]

- example : One hierarchy shows employees by geographical location, and another shows them by functional department

### Subsystem 12 : Special Dimension Manager:

- some attributes will not set/suits in the normal table , so they are put into special dimensions [eg : junk , mini dimensions]

- this subsystem manages this kind of special dimensions
- in case of repetition , it will create placeholder in the ETL system

### Subsystem 13 : Fact Table Builder:

1. Transaction Grain : One row = one event / one transaction
2. Periodic Snapshot : One row = status of an entity at/for a fixed time interval
3. Accumulating Snapshot : One row = full lifecycle of a particular process /recaord / item [from begenning to end all combined]

### Subsystem 14 : Surrogate Key pipeline

- it convert the natural keys in the fact table to its appropriate dimensional surrogate keys

```
Example

•A sales fact record comes with customer_id = "CUST456", product_code = "PRD123".

•The surrogate key pipeline looks up these in the dimension tables:

•"CUST456" → customer_sk = 301

•"PRD123" → product_sk = 101

•Fact table stores customer_sk and product_sk instead of natural keys.
```

### Subsystem 15: Multivalued bridge table buillder

```
example :

## Step 1: Customer Table

customer_id | name  
------------|------  
101         | Ram

---

## Step 2: Segment Table

segment_id | segment_name  
-----------|--------------  
1          | Premium  
2          | Loyalty  
3          | Online

---

## Step 3: Bridge Table (IMPORTANT)

customer_id | segment_id  
------------|------------  
101         | 1  
101         | 2  
101         | 3

```


### Subsystem 16: Late Arriving Data Handler

Standard processing procedures -> apply special modifications --to handle--> late ariving fact and dimension data


```
example :

## Problem (Late Data)

An order arrives **before** the customer data:

### Step 1: Order comes first

order_id | customer_id | amount  
---------|-------------|--------  
1        | C101        | 500

But **customer C101 is not yet in Customer table** ❌

---

## What this subsystem does

### Step 2: Insert with dummy key

order_id | customer_sk | amount  
---------|-------------|--------  
1        | -1 (unknown)| 500

➡️ Uses a **temporary/dummy surrogate key**

---

### Step 3: Later, customer data arrives

customer_id | customer_sk | name  
------------|-------------|------  
C101        | 101         | Ram

---

### Step 4: Update the fact table

order_id | customer_sk | amount  
---------|-------------|--------  
1        | 101         | 500

➡️ Replace dummy key with correct one
```

### Subsystem 17 : Dimension Manager

it ensures all the dimensions should be present only once and it should be a centrailzed dimension and all the tables need to access the dimension should access this centralized dimension only 

example : X (wrong)-> sales --> seperate product dimension
			  marketing --> seperate product dimension

It is the central authority ---> that will create and publish the conformed dimensions to the dw community 

### Subsystem 18: Fact Table Provider

- Own the administration of all the fact tables 
- responsible for monitoring / controlling the creation of the fact table by fact table builder 
```
### 2. Creation Control

- Decides:
    - What columns should exist
    - What grain (transaction level)
- Approves any schema changes
```

- manages / maintains the fact table 
```
### 3. Maintenance

- Ensures:
    - Daily data is loaded correctly
    - No missing or corrupt data
    - Performance is good (indexes, partitions)
```
- usage control
```
### 4. Usage Control

- Provides this table to:
    - BI tools (Power BI, Tableau)
    - Analysts
- Defines:
    - Who can access it
    - How it should be used
```

### Subsystem 19 : Aggregate Builder

- It will Build and maintains aggregate data 

- example: total sales per month , year etc

### Subsystem 20 : OLAP cubes builder

- It will take the data from the DW and creates and maintains the OLAP cubes 
- it will include the dimension like ---> time , product , region and measures like sales and product

### Subsystem 21 : Data propagation manager

- Distributes the data to multiple other systems from the data ware house 
- example : HR asks data and this system gives the data via API's

### Subsystem 22 : Job Scheduler

- ETL process have many jobs , that are schedules by this system 
- it will start the jobs based on fixed time intervals or on some trigger like occuring of an event

### Subsystem 23 : backup system

- The Backup Subsystem makes backup the whole ETL system [for future recoveries]
- eg : ETL settings, dictionaries, job schedules, etc.

### Subsystem 24: recovery and restart

- finds the failed jobs and restarts those jobs in ETL

### Subsystem 25: Version control system

- It keeps track of **different versions of your ETL code/configuration** and records **when and where each version was used**.

### Subsystem 26 : Version control migration 

- The version control migration subsystem manages the development, testing and deployment of ETL pipelines.

### Subsystem 27: workflow monitor

- monitors:
	- ETL pipelines /processes/workflow
	- CPU and disk resources used by ETL
	- which ongoing and which are completed ETL workflows

### Subsystem 28 : Sorting subsystem

- The sorting subsystem sorts data (usually by keys) so that **joins and loading become faster and efficient**.

### Subsystem 29 : Lineage and dependency

- From source systems ----> DW , tracking what are all the things / steps happened to the data is called data lineage

### Subsystem 30 : Problem Escalation

- When an ETL job fails, this subsystem **alerts the right people immediately** so they can fix it.

### Subsystem 31: Parallel processing and pipelining

- Run **multiple ETL tasks at the same time** (instead of one by one) to save time.
- it identifies the part of the pipeline that can be processed parallely

### Subsystem 32 : Security

- It controls **who can access what** in the ETL system and keeps a record of all actions.

> [!example]+ 
> Data Engineer → full access
> Analyst → read-only access
> Unauthorized user → no access

### Subsystem 33 : Compliance manager

- It ensures that **ETL SYSTEM follows legal and company rules** 
- tracks **who accessed the data at every step**.
- What is “Chain of Custody”?
- A record of:
    - **Who accessed data**
    - **When**
    - **What they did**

### Subsystem 34 : Metadata repository

- It is a database that contains the info's like :
	- source data
	- destination info / data
	- DW structure/schema
	- ETL details
	- Intermediate preprocessing info etc