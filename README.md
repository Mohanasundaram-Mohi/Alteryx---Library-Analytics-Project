# 📚 Library Analytics Project

### PostgreSQL → Alteryx → Excel Data Pipeline

---

# 📌 Project Overview

This project demonstrates an **end-to-end data analytics workflow** starting from a relational database in PostgreSQL, processing the data using Alteryx Designer, and exporting analytical outputs to Excel.

The project focuses on analyzing library management data including books, members, employees, branches, book issues, and returns.

The workflow performs data preparation, joins, transformations, aggregations, and exports insights to Excel reports.

---

# 🛠 Technology Stack

| Tool             | Purpose                                     |
| ---------------- | ------------------------------------------- |
| PostgreSQL       | Database storage and SQL operations         |
| Alteryx Designer | Data transformation and workflow automation |
| Excel            | Final analytics output                      |
| ODBC             | Connection between PostgreSQL and Alteryx   |

---

# 🗄 1️⃣ Database Creation (PostgreSQL)

```sql
CREATE DATABASE library_project;

\c library_project;
```

---

# 🏗 2️⃣ Table Creation

```sql
CREATE TABLE branch (
    branch_id VARCHAR(10) PRIMARY KEY,
    manager_id VARCHAR(10),
    branch_address VARCHAR(100),
    contact_no VARCHAR(15)
);

CREATE TABLE employees (
    emp_id VARCHAR(10) PRIMARY KEY,
    emp_name VARCHAR(50),
    position VARCHAR(30),
    salary DECIMAL(10,2),
    branch_id VARCHAR(10),
    FOREIGN KEY (branch_id) REFERENCES branch(branch_id)
);

CREATE TABLE members (
    member_id VARCHAR(10) PRIMARY KEY,
    member_name VARCHAR(50),
    member_address VARCHAR(100),
    reg_date DATE
);

CREATE TABLE books (
    isbn VARCHAR(50) PRIMARY KEY,
    book_title VARCHAR(100),
    category VARCHAR(30),
    rental_price DECIMAL(10,2),
    status VARCHAR(10),
    author VARCHAR(50),
    publisher VARCHAR(50)
);

CREATE TABLE issues_status (
    issued_id VARCHAR(10) PRIMARY KEY,
    issued_member_id VARCHAR(10),
    issued_date DATE,
    issued_book_isbn VARCHAR(50),
    issued_emp_id VARCHAR(10)
);

CREATE TABLE return_status (
    return_id VARCHAR(10) PRIMARY KEY,
    issued_id VARCHAR(10),
    return_date DATE
);
```

---

# 🔗 3️⃣ Connecting PostgreSQL to Alteryx

Connection method used:

```
ODBC Connection
```

Steps:

1. Install PostgreSQL ODBC Driver
2. Create DSN in Windows ODBC Manager
3. Connect using **Input Data Tool** in Alteryx

Example DSN:

```
PostgreSQL_Library_DB
```

---

# 📥 4️⃣ Data Ingestion in Alteryx

Input Data Tools were used to import tables:

```
books
members
employees
branch
issues_status
return_status
```

Each table was loaded as a separate data stream.

---

# 🧹 5️⃣ Data Preparation

Tool used:

```
Select Tool
```

Purpose:

* Remove unnecessary columns
* Standardize column names
* Fix data types

Example:

| Column       | Type   |
| ------------ | ------ |
| isbn         | String |
| rental_price | Double |
| issued_date  | Date   |

---

# 🔗 6️⃣ Data Joining (Equivalent to SQL JOIN)

Join Tools were used to combine tables.

Equivalent SQL:

```sql
SELECT *
FROM issues_status ist
JOIN books b
ON ist.issued_book_isbn = b.isbn
JOIN members m
ON ist.issued_member_id = m.member_id
JOIN employees e
ON ist.issued_emp_id = e.emp_id
JOIN branch br
ON e.branch_id = br.branch_id;
```

Join Conditions used in Alteryx:

```
issued_book_isbn = isbn
issued_member_id = member_id
issued_emp_id = emp_id
branch_id = branch_id
issued_id = issued_id
```

---

# 📊 7️⃣ Data Transformations

## Revenue by Category

Tool Used:

```
Summarize Tool
```

Configuration:

```
Group By → category
Sum → rental_price
Count → issued_id
```

Equivalent SQL:

```sql
SELECT category,
SUM(rental_price),
COUNT(issued_id)
FROM issues_status ist
JOIN books b
ON ist.issued_book_isbn = b.isbn
GROUP BY category;
```

---

## Members with Multiple Issues

Summarize Tool configuration:

```
Group By → issued_member_id
Count → issued_id
```

Filter condition:

```
Count_issued_id > 1
```

Equivalent SQL:

```sql
SELECT issued_member_id,
COUNT(*)
FROM issues_status
GROUP BY issued_member_id
HAVING COUNT(*) > 1;
```

---

# ⚙ 8️⃣ Calculated Fields

Formula Tool was used to create derived fields.

Example formula:

```
Days_Overdue =
DateTimeDiff(DateTimeNow(), issued_date, "days")
```

Logic:

```
Days_Overdue > 30
```

This identifies overdue books.

Equivalent SQL:

```sql
SELECT CURRENT_DATE - issued_date AS overdue_days
FROM issues_status;
```

---

# 📤 9️⃣ Output Data

Final results were exported using **Output Data Tool**.

Output format:

```
Excel Files
```

Generated reports:

```
Rental_sum.xlsx
Book_category_rentalprice.xlsx
Members_with_Multiple_Issues.xlsx
```

Each dataset was written to Excel for reporting and visualization. 

---

# 🔄 Workflow Execution

Execution process:

1. Load data from PostgreSQL
2. Clean fields using Select Tool
3. Join related tables
4. Apply business logic
5. Aggregate results
6. Export outputs to Excel

Workflow execution was triggered using:

```
Run Workflow (Ctrl + R)
```

---

# 📊 Workflow Architecture

```
PostgreSQL Database
        │
        ▼
ODBC Connection
        │
        ▼
Alteryx Input Tools
        │
        ▼
Select Tools (Data Cleaning)
        │
        ▼
Join Tools (Data Integration)
        │
        ▼
Formula Tools (Derived Fields)
        │
        ▼
Summarize Tools (Aggregations)
        │
        ▼
Filter Tools
        │
        ▼
Output Data Tools
        │
        ▼
Excel Reports
```

---

# 🚀 Key Learning Outcomes

* PostgreSQL database design
* SQL joins and aggregations
* ODBC database connectivity
* Alteryx ETL workflow development
* Data transformation and aggregation
* Automated Excel report generation

---
![Alteryx_Workflow_page-0001](https://github.com/user-attachments/assets/f19d8024-8c07-4e56-b870-1d9deca3729e)
# 👨‍💻 Author
Mohan

End-to-End Data Analytics Project || PostgreSQL → Alteryx → Excel Pipeline
