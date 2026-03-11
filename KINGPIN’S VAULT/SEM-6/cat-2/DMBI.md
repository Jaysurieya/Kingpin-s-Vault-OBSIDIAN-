![[Syllabus.png]]

DIMENSIONAL MODELLING :

![[Pasted image 20260222114009.png]]

- highly iterative and dynamic process

### Structure of Bus Matrix
- **Rows → Business Processes** (Sales, Orders, Inventory, etc.)
- **Columns →  Shared Dimensions** (Time, Product, Customer, Location, etc.)

A **Fact Table** is the central table in a dimensional model that stores:
- **Measurable numeric data** (facts)
- **Foreign keys** linking to dimension tables
#### What It Contains
1. **Foreign Keys**
    - Time_ID   
    - Product_ID
    - Customer_ID
2. **Measures (Facts)**
    - Quantity
    - Sales_Amount
    - Discount

### Structure (Star Schema)

Dimensional Model  
  → Fact Table (center)  
  → Dimension Tables (connected to fact table)
  
Dimension tables are connected to the fact table using foreign keys.


QNS :

lifecycle
scenario 


advanced dimension
ad fact table 
scenario based qns 

- one business process = one fact table only 
- a dimensional model can have multiple fact tables based on the number of  business processes  

> [!note]+ Fact and dimensional tables:[basic concept]
> How many fact tables can a single dimensional model have?
> 
> There is no fixed limit.
> 
> A dimensional model can have:
> 
> One fact table
> 
> Multiple fact tables
> 
> It depends on how many business processes you are modeling.
> 
> Rule
> 
> 👉 One fact table per business process
> 
> Example 1 — Simple Model (One Fact Table)
> 
> Retail system:
> 
> Sales process
> 
> Fact table:
> 
> Sales_Fact
> 
> Dimensions:
> 
> Date
> 
> Product
> 
> Customer
> 
> Store
> 
> Only one business process → one fact table.
> 
> Example 2 — Multiple Business Processes
> 
> Retail warehouse may have:
> 
> Sales
> 
> Returns
> 
> Inventory
> 
> Shipments
> 
> Each is a different process → separate fact tables:
> 
> Sales_Fact
> 
> Returns_Fact
> 
> Inventory_Fact
> 
> Shipment_Fact
> 
> All share common dimensions (Date, Product, etc.).
> 
> Important Concept
> 
> All fact tables together form the dimensional model.
> 
> They are connected through conformed dimensions.


### Atomic grain = lowest level of detail (per transaction).


## Advanced fact table techniques :

### 1. Fact Table Surrogate Keys — Simple Explanation

### What is It?

A **fact table surrogate key** is a system-generated unique number assigned to each fact row during ETL.
---------------------
It is:

- Not a foreign key
- Not related to any dimension
- Just a unique identifier for that row

### Example

|Fact_Sales_SK|Date_Key|Product_Key|Sales_Amount|
|---|---|---|---|
|1|20240101|P101|1000|

Here, `Fact_Sales_SK` is the surrogate key.
### Why Use It?

1. **Simplifies ETL restart**  
    If ETL fails, it’s easier to track processed rows.
    
2. **Unique row identification**  
    Helps debugging and auditing.
    
3. **Supports insert-delete strategy**  
    Instead of updating rows, delete and reinsert easily.

### 2. Centipede Fact Tables — Simple Explanation

### What is It?

A **centipede fact table** happens when too many hierarchical foreign keys are added to the fact table.

Example of bad design:

| Day_Key | Month_Key | Quarter_Key | Year_Key | Sales |

Many time-related keys in one fact table.

It looks like a “centipede” with too many legs (foreign keys).


### Why Is It a Problem?

- Too many joins    
- Poor performance
- Hard maintenance
- Redundant data

### Correct Design

Use a **single Date dimension** that already contains:

| Date_Key | Day | Month | Quarter | Year |

Fact table should store only:

| Date_Key | Sales |

Hierarchy comes from the dimension table.

### 3. Numeric Values as Attributes or Facts — Simple Explanation

### What is the Problem?

Some numeric values can be confusing.
Should they go in:

- Fact table?
- Dimension table?

### Decision Rule

1. Used for calculations → **Fact**
2. Used for filtering/grouping → **Dimension attribute**
3. Used for both → Store in both **places**

## 4. Lag or Duration facts reps:

> The time difference between two important events (milestones).
> eg: delivery time and processing time

## 5. header and line refer allocated 

## 6. Allocated Facts

Sometimes header-level facts (like freight) must be distributed to line-level rows.

**Method:** Allocate based on business rules such as quantity or value.

**Benefit:** Enables full dimensional analysis.

## 7. Profit and Loss Fact Tables

These fact tables implement the full profit equation:

**Profit = Revenue − Cost**
**Advantages:**
- Customer profitability
- Product profitability
- Channel profitability

They are usually stored at atomic transaction grain.

## 8. Multiple currencies:

Global companies operate in many countries.

Transactions may happen in:
- USD
- INR
- EUR
- GBP
Solution:
- Store value in **transaction currency**
- Store the same value in a **standard currency**
- Include a **Currency dimension**

![[Pasted image 20260222221144.png]]

## 9. Multiple Units of Measure

Facts may be required in different measurement units.

**Technique:**

- Store standard unit value
- Store conversion factors    

![[Pasted image 20260222212916.png]]

**Example:** weight in kg with kg-to-pound factor.

## 10  What is YTD?

**YTD (Year-to-Date)** means:

> Total value from the beginning of the year up to today (or a given date).

Example:  
If today is March 31, YTD Sales = Sales from Jan 1 → March 31.

Business users often request YTD values. [like Current year performance]

**Recommendation:**  
Avoid storing YTD in fact tables; compute during query time because stored YTD values are difficult to maintain.

## 11. Multipass SQL (Avoid Fact-to-Fact Joins)

Fact tables should never be directly joined because it causes incorrect results due to cardinality problems. [duplicate rows]

**Solution:**  
Use drilling across:

1. Aggregate each fact separately
2. Merge results
## 12. Timespan Tracking in Fact Tables

GRAIN : level of detail in a fact table --> what each row reps in a fact table
types:
transaction grain , periodic snap shot grain , accumulating snapshot grain

In special cases, fact tables include effective date, expiry date, and current flag to track the time validity of facts.

example: 
![[Pasted image 20260222211000.png]]
## 13. Late Arriving Facts

A late arriving fact is one that arrives after the dimensional context has changed. [fact arriving late after the occurance of the event]

**Solution:**  
ETL must link the fact to the dimension row that was effective when the event actually occurred.

### What should we do?
When loading the late fact:
- Search the dimension table
- Find the version of the row that was valid on event date
- Use that **dimension key**


UNIT-3:

life-cycle:

![[Pasted image 20260222230235.png]]

