### **Data Modeling** is:
A technique used to design and structure data in a database so it can be stored, organized, and analyzed efficiently.

### **Dimensional Modeling** is:
A data modeling technique used in data warehouses to organize data into **facts and dimensions** for easy analysis and fast querying.

### **Dimensional Models** are:
Database structures designed using dimensional modeling to organize data into **fact tables and dimension tables** for analysis.

example dimensional model ---> star / snow flake schema

### Dimensional modelling myths:

•Dimensional models are only for summary data
•Dimensional models are departmental not enterprise
•Dimensional models are not scalable
•Dimensional models are only for predictable usage
•Dimensional models can’t be integrated


### Agile:
**Agile Considerations (in DW/BI)** are guidelines for building data warehouse systems using **Agile methodology** instead of rigid, long development cycles


- managelbly sized increments in work
- both the kimball and agile says the same thing :
	- focus on delivering the business value
	- good collaboration should exist between IT/DEV team and business stakeholders
	- face-to-face communication , feed backs , prioritizing the business stakeholders
	- adapt to evolving changes
	- Tackle development in an iterative incremental manner


### Kimball architecture:

![[Pasted image 20260413111626.png]]

```
## 1. **Source Systems (Left side)**

- These are **operational systems** (databases, apps)
- They store **raw business data**
    - Example: transactions, sales, user data

**Problem:**  
Data is messy, scattered, not suitable for analysis.

---

## 2. **Back Room → ETL System**

(ETL = Extract, Transform, Load)

### What happens here:

### a) **Extract**

- Data is taken from source systems
- read data, understand data and take only the required data for ETL

### b) **Transform**

- Clean the data (remove errors)
- Combine data from multiple sources
- Standardize formats
- Remove redundancy → delete duplicates

### c) **Load**

- Store cleaned data into data warehouse as fact and dimension tables

### Key characteristics:

- No direct user access
- Focus on:
    - **Data quality**
    - **Consistency**
    - **High throughput**

---

## 3. **Front Room → Presentation Area**

This is the **actual Data Warehouse** used by users.

### What happens here:

- Data is stored in:
    - **Star Schema** OR
    - **OLAP Cubes**

### Data types:

- **Atomic data** → detailed (each transaction)
- **Summary data** → aggregated (monthly sales)

### Organized by:

- **Business processes**
    - Example: Sales, Orders

### Uses:

- **Conformed dimensions**
    - Same “Customer”, “Product” used everywhere

### Design goals:

- Easy to understand
- Fast query performance

---

## 4. **BI Applications (Right side)**

This is what users interact with.

### Tools:

- Reports
- Dashboards
- Analytics
- Data mining

### Example:

- “Sales this month vs last month”
- “Top-selling products”

---

## 5. **Overall Flow (Very Important)**

Source Data → ETL (Clean & Process) → Data Warehouse → BI Tools → Insights
```

restaurant metphor:

| ETL Concept    | Restaurant Meaning                |
| -------------- | --------------------------------- |
| Extraction     | Bringing ingredients into kitchen |
| Transformation | Cutting, cleaning, cooking        |
| Load           | Plating food for serving          |

```
Raw Ingredients → Kitchen → Dining Table → Customer Experience
(Source)        (ETL)     (DW)            (BI)
```


### example of a OLAP cube:

![[Pasted image 20260413124033.png]]
	
![[Pasted image 20260413124219.png]]


### Components of a dimension model:

> [!example]+ 
> 1. Fact
> Numerical measurements of a business process
> Stored in fact tables
> 
> Examples:
> 
> Sales amount, quantity, profit
> 
> Key point:
> Answers “How much?”
> 
> 2. Dimension
> They are nouns
> ==Provides context to facts / descriptive info of the business data==
> Describes who, what, where, when
> 
> Examples:
> 
> Product, Customer, Time, Location
> 
> 3. Attributes (of Dimension)
> Properties of a dimension
> Used for filtering and grouping
> 
> Examples:
> 
> Product → name, category
> Location → city, state
> 
> Key point:
> Helps in searching and analysis
> 
> 4. Fact Table
> Main table containing:
> Facts (numeric values)
> Foreign keys to dimensions
> 
> Example:
> 
> Product_ID	Time_ID	Sales
> 
> Key point:
> Central table in the model
> 
> 5. Dimension Table
> Contains:
> Dimension data
> Attributes
> 
> Example:
> | Product_ID | Name | Category |
> 
> Key point:
> Descriptive data, usually denormalized

### Steps of Dimensional Modelling:

•Identify Business Process - identify the process for which u r going to build a data model [eg : sales , marketing etc.. so in this business processes for which u r going to build a data model ]

•Identify Grain (level of detail) - Identifying the grain means , determining the level of  detail at which the data need to be captured
-deciding whether you want to track sales by the minute, by the hour, by the day, or by the month, ensuring you have just enough detail to answer your business questions

•Identify Dimensions - refer above qn

•Identify Facts - refer above qn (numerical, fact table ,foreign keys, central table )

•Build [Star/snowflake] Schema:
```
•In this step, you implement the Dimension Model.

•A schema is nothing but the database structure (arrangement of tables). There are two popular schemas

•Star Schema

–The star schema architecture is easy to design. It is called a star schema because diagram resembles a star, with points radiating from a center. The center of the star consists of the fact table, and the points of the star is dimension tables.

–The fact tables in a star schema which is third normal form whereas dimensional tables are de-normalized.

•Snowflake Schema

–The snowflake schema is an extension of the star schema. In a snowflake schema, each dimension are normalized and connected to more dimension tables.
```

#### Star schema:
![[Pasted image 20260413134305.png]]

#### Snowflake:
![[Pasted image 20260413134325.png]]


### Alternate archs:
- Independent data mart architecture 
- hub and spoke coporate information factory Inmon architecture
- hybrid hub and spoke and kimball architecture

	![[Pasted image 20260414231352.png|2700]]

![[Pasted image 20260414232243.png|1975]]

![[Pasted image 20260414234937.png]]
