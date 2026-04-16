## SCD:

A Slowly Changing Dimension is a dimension table in a data warehouse that stores data which, while generally stable, may change over time in an unpredictable manner

Type 0 - Retain Original: Attributes never change (e.g., date of birth, original signup source)
eg: DOB

Type 1 - Overwrite: Old data is replaced with new data (no history preserved)
eg: Customer Address changes

Type 2 :
> [!example]+ Type 2:
> ```
> 
> Step-by-Step Process for Type 2 SCD
> 1. Initial Table Structure
> Let's start with a customer dimension table:
> 
> CREATE TABLE dim_customer (
>     customer_sk INT PRIMARY KEY,          -- Surrogate key
>     customer_id VARCHAR(20),              -- Natural/business key
>     customer_name VARCHAR(100),
>     city VARCHAR(50),
>     state VARCHAR(10),
>     valid_from DATE,
>     valid_to DATE,
>     is_current BOOLEAN
> );
> 2. Initial Data Load
> When we first load customer data:
> 
> customer_sk	customer_id	customer_name	city	state	valid_from	valid_to	is_current
> 1001	C1001	John Smith	Austin	TX	2024-01-01	9999-12-31	TRUE
> Key points:
> 
> valid_from: When this record became effective
> valid_to: 9999-12-31 (a future date) indicates this is the current record
> is_current: TRUE indicates this is the active version
> 3. Customer Moves - The Change Occurs
> John Smith moves from Austin, TX to Seattle, WA on March 15, 2024.
> 
> 4. ETL Process Steps
> Step 4.1: Expire the current record
> 
> UPDATE dim_customer 
> SET valid_to = '2024-03-14',  -- Day before the change
>     is_current = FALSE
> WHERE customer_id = 'C1001' 
>   AND is_current = TRUE;
> Result after this update:
> 
> customer_sk	customer_id	customer_name	city	state	valid_from	valid_to	is_current
> 1001	C1001	John Smith	Austin	TX	2024-01-01	2024-03-14	FALSE
> Step 4.2: Insert the new record
> 
> INSERT INTO dim_customer (
>     customer_sk, customer_id, customer_name, 
>     city, state, valid_from, valid_to, is_current
> )
> VALUES (
>     1002, 'C1001', 'John Smith', 
>     'Seattle', 'WA', '2024-03-15', '9999-12-31', TRUE
> );
> 5. Final Result
> After the change, the table contains:
> 
> customer_sk	customer_id	customer_name	city	state	valid_from	valid_to	is_current
> 1001	C1001	John Smith	Austin	TX	2024-01-01	2024-03-14	FALSE
> 1002	C1001	John Smith	Seattle	WA	2024-03-15	9999-12-31	TRUE
> 
> ```

==To maintain :==
- ==natural==
- ==surrogate==
- ==valid_from==
- ==valid_to==
- ==current==

Type 3 - Add New Attribute: Adds columns to store limited history (current + previous values) 
-also called ==alternate reality==
eg: –A product price change is stored in "Previous Price" and "Current Price" columns.


•Type 4: Add Mini-Dimension - ==new row creation in mini dimension==
–Slowly changing dimension type 4 is used when a group of attributes in a dimension rapidly changes and is split off to a mini–dimension.
-rapidly changing monster dimension - {==history table==}
-both will have seperate primary key [surrogate keys] and fact table contains it both

```
### Step 1: Original Table Structure (Before Mini-Dimension)


CREATE TABLE dim_employee (
    employee_sk INT PRIMARY KEY,      -- Surrogate key
    employee_id VARCHAR(20),          -- Natural key
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    hire_date DATE,
    job_title VARCHAR(100),           -- This changes frequently!
    department VARCHAR(50),
    salary_band VARCHAR(20)           -- This also changes frequently!
);


### Step 2: Split Into Two Tables

Main Employee Dimension (slow-changing attributes):


CREATE TABLE dim_employee (
    employee_sk INT PRIMARY KEY,
    employee_id VARCHAR(20) UNIQUE,
    first_name VARCHAR(50),
    last_name VARCHAR(50), 
    hire_date DATE,
    department VARCHAR(50)
);


Mini-Dimension for Job Profiles (rapidly changing attributes):


CREATE TABLE dim_job_profile (
    profile_sk INT PRIMARY KEY,       -- Mini-dimension surrogate key
    job_title VARCHAR(100),
    salary_band VARCHAR(20)
);


### Step 3: Fact Table Includes Both Keys


CREATE TABLE fact_salary (
    salary_id INT PRIMARY KEY,
    employee_sk INT,                  -- Links to main dimension
    profile_sk INT,                   -- Links to mini-dimension  
    salary_amount DECIMAL(10,2),
    effective_date DATE,
    FOREIGN KEY (employee_sk) REFERENCES dim_employee(employee_sk),
    FOREIGN KEY (profile_sk) REFERENCES dim_job_profile(profile_sk)
);
```


type - 5:

```
## Type 5 SCD: The Hybrid Approach

### What's Happening in Type 5:

Type 5 = Type 4 (Mini-Dimension) + Type 1 (Overwrite)

1. Mini-Dimension: Like Type 4, we create a separate table for rapidly changing attributes
2. Type 1 Outrigger: We add a "current reference" column in the main dimension that points to the current mini-dimension record
3. Two Ways to Access:
    - Historical: Through fact table joins (like Type 4)
    - Current: Directly from main dimension (like Type 1)

### Step-by-Step Process:

Step 1: Create main dimension with slowly changing attributes Step 2: Create mini-dimension for rapidly changing attributes  
Step 3: Add a Type 1 column in main dimension pointing to current mini-dimension Step 4: Fact table stores both keys for historical tracking Step 5: When attributes change, update the Type 1 reference in main dimension

---

## Detailed Example: Customer Loyalty Program

### Table Structure:

Main Customer Dimension:


CREATE TABLE dim_customer (
    customer_sk INT PRIMARY KEY,          -- Surrogate key
    customer_id VARCHAR(20) UNIQUE,       -- Natural key
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    signup_date DATE,
    -- TYPE 1 OUTRIGGER: Points to CURRENT mini-dimension
    current_profile_sk INT,               -- This gets overwritten!
    FOREIGN KEY (current_profile_sk) REFERENCES dim_loyalty_profile(profile_sk)
);


Mini-Dimension (Loyalty Profiles):


CREATE TABLE dim_loyalty_profile (
    profile_sk INT PRIMARY KEY,           -- Mini-dimension key
    loyalty_tier VARCHAR(20),             -- Bronze, Silver, Gold, Platinum
    benefits_level VARCHAR(20),           -- Basic, Premium, VIP
    discount_percentage DECIMAL(5,2)      -- 5%, 10%, 15%, etc.
);


Fact Table (Purchases):


CREATE TABLE fact_purchases (
    purchase_id INT PRIMARY KEY,
    customer_sk INT,                      -- Links to main dimension
    profile_sk INT,                       -- Links to mini-dimension AT TIME OF PURCHASE
    purchase_amount DECIMAL(10,2),
    purchase_date DATE,
    FOREIGN KEY (customer_sk) REFERENCES dim_customer(customer_sk),
    FOREIGN KEY (profile_sk) REFERENCES dim_loyalty_profile(profile_sk)
);


### Sample Data:

Loyalty Profiles Mini-Dimension:

|profile_sk|loyalty_tier|benefits_level|discount_percentage|
|---|---|---|---|
|101|Bronze|Basic|5.00|
|102|Silver|Premium|10.00|
|103|Gold|Premium|15.00|
|104|Platinum|VIP|20.00|

Main Customer Dimension:

|customer_sk|customer_id|first_name|last_name|signup_date|current_profile_sk|
|---|---|---|---|---|---|
|1001|C1001|Sarah|Johnson|2023-01-15|101|
```

TYPE-6 :
type1 + type2
- multiple rows - history
- overwrite col - current value

> [!example]+ 
> Example: Product Pricing
> Step 1: Initial Data
> 
> Product_ID	Price (Type 2)	Current Price (Type 1)
> P1	100	100
> 
> Step 2: Price changes to 120
> Type 2 action:
> New row created
> 
> Type 1 action:
> Current price updated in all rows
> 
> Final Table:
> Product_ID	Price (Type 2 - Historical)	Current Price (Type 1)
> P1	100	120
> P1	120	120


Type 7 :

```
## Type 7: The "Two Different Glasses" Approach

Imagine you have special glasses that let you see the same data in two different ways:

### 🕶️ Glass #1: "Current Vision"

- Shows everything as it is TODAY
- All customers have their current addresses
- All products have their current prices

### 👓 Glass #2: "Historical Vision"

- Shows everything as it was BACK THEN
- Customers have addresses they had when the sale happened
- Products have prices they had when purchased

## How It Works Technically:

### 1. Dimension Table (One table, both views):

|surrogate_key|natural_key|customer_name|city|valid_from|valid_to|is_current|
|---|---|---|---|---|---|---|
|1001|C1001|John Smith|Austin|2023-01-01|2023-12-31|FALSE|
|1002|C1001|John Smith|Seattle|2024-01-01|9999-12-31|TRUE|

### 2. Fact Table (Has both keys):

|sale_id|surrogate_key|natural_key|amount|sale_date|
|---|---|---|---|---|
|5001|1001|C1001|$100|2023-06-15|
|5002|1002|C1001|$150|2024-02-20|

## The Magic: Two Different Ways to Query

### Option 1: "Current View" (Through NATURAL_KEY)


SELECT s.sale_date, s.amount, c.customer_name, c.city
FROM fact_sales s
JOIN dim_customer c ON s.natural_key = c.natural_key 
                     AND c.is_current = TRUE


Shows: All sales with customers' CURRENT cities

- 2023-06-15: $100, John Smith, Seattle ← Uses current city!
- 2024-02-20: $150, John Smith, Seattle

### Option 2: "Historical View" (Through SURROGATE_KEY)


SELECT s.sale_date, s.amount, c.customer_name, c.city
FROM fact_sales s
JOIN dim_customer c ON s.surrogate_key = c.surrogate_key


Shows: All sales with customers' cities AS THEY WERE

- 2023-06-15: $100, John Smith, Austin ← Actual city at time!
- 2024-02-20: $150, John Smith, Seattle
```

2 - basic table techniques