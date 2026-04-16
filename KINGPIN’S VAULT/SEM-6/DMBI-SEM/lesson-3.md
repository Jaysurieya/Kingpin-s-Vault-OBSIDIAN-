
Kimball Life cycle:

![[Pasted image 20260414201442.png]]

```
### 1. **Project Planning**

A company decides: _“We want better reports and insights.”_  
So they plan what to build, how long it will take, and what resources are needed.

---

### 2. **Business Requirements Definition**

Business team explains:

- “We want sales reports”
- “We need customer analysis”
- “We want profit by region”

👉 At this stage, **no data is touched yet** — only _what is needed_ is defined.

---

### 3. **Now the actual data journey begins (3 parallel tracks)**

---

## 🔹 DATA FLOW TRACK (Main story of data)

### 3.1 **Dimensional Modeling**

Now designers decide:

- What data to store (Sales, Customers, Products)
- Structure (Fact table + Dimension tables)

👉 Example:

- Fact table → Sales (amount, quantity)
- Dimension → Customer, Product, Time

---

### 3.2 **Physical Design**

Deciding/creating the structure and which data will be stored where

Now they decide:
- Where data will be stored (tables, indexes)
- How to optimize performance

👉 Data is still not moved yet — just structure setup.

---

### 3.3 **Data Staging Design & Development (Actual Data Movement 🚀)**

Now real data comes in:

👉 Data flow:

- Data comes from **source systems** (DBs, files, apps)
- It goes to a **staging area**
- Data is:  **[ETL IS DONE]**
    - cleaned (remove errors)
    - transformed (format changes)
    - integrated (combine sources)

👉 Finally, data is loaded into the **data warehouse**

---

## 🔹 TECHNICAL TRACK (Supports data flow)

### 3.4 **Technical Architecture Design**

Decide:

- Will it be cloud or on-premise? ---> this is decided for handling/managing the data ware house
- What servers, pipelines, tools?

---

### 3.5 **Product Selection & Installation**

- identidfies the selections/tools in the tech arch and installs it

Install tools:

- Database (MySQL, Snowflake)
- ETL tools
- BI tools

👉 These tools help move and store data.

---

## 🔹 APPLICATION TRACK (Uses the data)

### 3.6 **Analytic Application Specification**

Business says:

- “We need dashboard showing sales trends”
- “We need filters by region”

---

### 3.7 **Analytic Application Development**

Develop:

- Dashboards
- Reports
- Charts

👉 Now data becomes **visual insights**

---

## 🔹 FINAL STAGE

### 4. **Deployment**

Everything is connected:

- Data pipelines running
- Warehouse ready
- Dashboards live

👉 Users can now see reports.

---

### 5. **Maintenance and Growth**

After release:

- Fix bugs
- Add new reports
- Handle more data
```


## Project Plan and management:
### **Assessing Readiness**

This step checks whether the organization is prepared to start a DW/BI project. It evaluates business support, motivation, and feasibility in terms of technical capability, resources, and data availability.

```
–Organization’s readiness factors

•To have a strong executive business sponsor

•To have a strong, compelling business motivation for tackling the DW/BI initiative and to handle critical situations

•Feasibility

–Technical

–Resource

–Data
```

### **Scoping and Justification**

- Scope - setting clear boundaries -> what should be and be not included in the project
- This is created by both IT and business people togather
- it should be good for business and also managable by the IT team
### **Staffing**

This involves forming a cross-functional team with members from both business and technical sides. 

eg : business ---> business sponsor , business lead , business user etc..
	IT ---> Business analyst , ETL architect, TECHNICAL architect , db admin etc..

### **Developing and Maintaining the Plan**

This step creates and manages the overall project plan, including tasks, timelines, costs, and effort. It also ensures proper communication and tracking throughout the project lifecycle.


==creates and maintains the plan ==
–Effort, time, cost required, communication


==**Business Requirements Definition** is the process of identifying and clearly documenting what the business needs from the DW/BI system. It involves interacting with stakeholders to understand goals, problems, data needs, and expected outcomes, and converting them into structured requirements.== This step ensures that the system being built aligns with business objectives and delivers meaningful insights.

![[Pasted image 20260414213126.png]]


### Tech track - all the headings

remaining refer the above for story