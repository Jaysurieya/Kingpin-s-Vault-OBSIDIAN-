### what is big data ?

- Big Data refers to extremely large volumes of **structured, semi-structured, and unstructured data** that cannot be processed using traditional systems and require scalable, distributed technologies for storage and analysis
- It has high **volume, velocity, and variety (3Vs)**

### Use cases:

- Build sensors
- CRM
- social media data analysis [user preferences]
- recommendation systems
- fraud detection

### Challenges and capabilities for Big Data:

“Challenges and capabilities for Big Data” refers to the **difficulties in handling large-scale data (challenges)** and the **system features required to effectively store, process, and analyze it (capabilities)**.


```
### **1. Data Arrives Fast**

- (10) **High-speed data loading**
- (11) **Multi-source integration**
- (12) **Schema-less loading**
- (13) **Real-time streaming analytics**

👉 _Story:_ Data comes **very fast from many sources**, even without structure, and is analyzed in real time.

---

### **2. Store Everything**

- (3) **Store raw data (no conversion)**
- (9) **Support multiple data types**
- (1) **Scalability (petabytes)**

👉 _Story:_ Store **all types of data as-is**, and handle **huge volumes**.

---

### **3. Spread Across Systems**

- (2) **Distributed system**

👉 _Story:_ Data is stored across **many machines worldwide**.

---

### **4. Process Smartly**

- (5) **Complex UDF support**
- (6) **Multi-language UDFs**
- (7) **Reusable UDF libraries**
- (8) **Fast execution on large data**

👉 _Story:_ Apply **custom logic (UDFs)** efficiently on massive data.

---

### **5. Fast Query & Analysis**

- (4) **Fast query response**
- (15) **Large-scale joins**

👉 _Story:_ Get **quick answers**, even for very large datasets.

---

### **6. Keep Data Updated**

- (14) **High-speed data updates**

👉 _Story:_ Data is **continuously updated in real time**.

---

### **7. Manage Complex Workflows**

- (16) **Complex workflow execution**

👉 _Story:_ Many processes run **together across systems**.

---

### **8. Ensure Reliability**

- (17) **No single point of failure**
- (18) **Failover support**

👉 _Story:_ System **never stops**, even if parts fail.

---

### **9. Handle Real-World Load**

- (19) **Mixed workloads & many users**

👉 _Story:_ Supports **thousands of users and different tasks at once**.
```

### Extended RDBMS :

Traditional **RDBMS systems are being enhanced to handle Big Data** by supporting **new data types (unstructured, complex data)** and allowing **custom processing using UDFs inside the database**.

![[Pasted image 20260414181547.png]]

### Hadoop / Mapreduce:

- A **company needs to process huge data**, so it uses an **open-source Apache system**.
	- It uses **MapReduce**, a **framework designed for big data processing**.
- This idea was **originally created by Google** for web search.
- The system runs on **thousands of machines working together**.
- It is **very flexible**, so it can solve many types of problems.
- map reduce [logic] can be implemented in **different languages (mainly Java)** .
- It works like a **UDF execution framework/system**, executing complex custom functions.
- The most popular implementation is **Apache Hadoop**.
- Hadoop stores data in **HDFS** and can also connect to **cloud storage like Amazon S3**.

### difference:

| **Extended RDBMS**                        | **Hadoop (MapReduce)**                         |
| ----------------------------------------- | ---------------------------------------------- |
| Proprietary (paid)                        | Open source (free/cheap)                       |
| Expensive                                 | Less expensive                                 |
| Needs **structured data**                 | Handles **any data (no structure needed)**     |
| Fast for **small queries (index lookup)** | Good for **large data processing (full scan)** |
| Strong **SQL/relational support**         | Limited SQL (uses tools like Hive)             |
| Limited support for complex data          | Strong support for complex data                |
| Limited for complex logic (loops)         | Strong for complex processing                  |
| Strong **transaction support (ACID)**     | Little/no transaction support                  |

### Best Big data practices:

MADG:
•Management
•Architecture
•Data modeling
•Governance

#### Management :
- **Structure Big Data Environments Around Analytics** – Design the system mainly for **data analysis**, including steps like loading, cleaning, integrating, and finally using BI tools.
- **Delay Building Legacy Environments** – Don’t build fixed long-term systems now because **big data technologies keep changing rapidly**.
- **Build From Sandbox Results** – First **experiment and test ideas**, then convert successful ones into proper production systems.
- **Try Simple Applications First** – Start with **small, easy use cases** before moving to complex ones.
- **Backup and Archiving** – Always **store and protect old data safely** for future use or recovery.

#### Architecture:
- **Plan a Data Highway** – Organize data flow in **multiple layers (caches)** from raw to processed for efficient access.
- **Build a Fact Extractor from Big Data** – Convert raw data into **useful measurable facts (like trends, metrics)**.
- **Build Comprehensive Ecosystems** – Integrate **different data sources (RDBMS, social media, documents, etc.)** into one system.
- **Plan for Data Quality** – Ensure **accuracy and correctness of data**, balancing speed and quality.
- **Add Value to Data as Soon as Possible** – Clean and process data **early to improve usefulness and reduce unnecessary data**.
- **Implement Backflow to Earlier Caches** – Send **processed/important data back to earlier stages** for reuse.
- **Implement Streaming Data** – Process data **in real time as it arrives**.
- **Avoid Boundary Crashes** – Design the system to **handle very large scale without failure**.

#### Data modelling:
Use dimensional modeling (facts & dimensions)
Combine data using conformed dimensions
Use surrogate keys
Handle both:
Structured data
Unstructured data
Use Slowly Changing Dimensions (SCD)

#### Data governance:

- **There is no such thing as big data governance** – Governance principles are **same for all data**, big data doesn’t need completely new rules.
- **Dimensionalize the data before applying governance** – First **organize data (facts & dimensions)**, then apply governance rules properly.
- **Privacy is the most important governance perspective** – Protecting **user data and sensitive information** is the top priority.
- **Don’t choose big data over governance** – Don’t ignore rules just for big data; **governance is always important**.