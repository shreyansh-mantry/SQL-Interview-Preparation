# SQL-Interview-Preparation


# 📊 SQL Interview Preparation

Real-style SQL interview questions, ordered **Easy → Medium → Hard**, each with sample data, the expected output and a step-by-step solution.

## 🚀 How to Use This Repository

1. Pick a question from the **[list below](#questions)** and open its **📄 Question** file.
2. Run the **Create Table** and **Insert Sample Data** blocks in MySQL 8.0+. No MySQL installed? Use **[DB Fiddle](#db-fiddle)**, explained below.
3. Write your own query and compare your result with the **Expected Output**.
4. Open the **💡 Solution** file to check your answer and read the step-by-step explanation.

---

## 🧩 Question Format

Every problem has its own folder with **two files**:

**📄 `…-Question.md`**: try the problem yourself

| Section | What it contains |
|---|---|
| 📝 **Question** | The problem statement, written inside a `/* … */` SQL comment |
| 🗂️ **Create Table** | The `CREATE TABLE` statements for the tables used |
| 📥 **Insert Sample Data** | The `INSERT` statements that load the sample rows |
| ✅ **Expected Output** | The exact result your query should return |

**💡 `…-Solution.md`**: check your answer

| Section | What it contains |
|---|---|
| 🧾 **Solution Code** | A working MySQL 8.0+ query |
| 🪜 **Step-by-Step Explanation** | Every step and function explained in plain words, with the data shown at key steps |

**Difficulty levels**
- 🟢 **Easy**: one core concept (joins, basic window functions, UNION ALL)
- 🟡 **Medium**: CTEs, window functions with `PARTITION BY`, conditional aggregation
- 🔴 **Hard**: multi-step logic (gaps and islands, recursive CTEs, cohort analysis)

> 💡 Every block is ready to copy: hover over any code block and click the **copy icon** in its top-right corner.

---

<a id="db-fiddle"></a>
## ▶️ How to Run the Code Online (DB Fiddle, no installation needed)

[DB Fiddle](https://www.db-fiddle.com/) is a free website where you can run MySQL in your browser. You don't need to sign up or install anything.

### Step 1: Open DB Fiddle and choose MySQL
1. Go to **https://www.db-fiddle.com/**.
2. Click the **Database** dropdown in the top-left corner and select **MySQL v8.0** (or any newer MySQL version).

> ⚠️ Don't choose MySQL 5.x. The solutions use CTEs (`WITH`) and window functions (`ROW_NUMBER()`, `RANK()`, `LAG()` …), which need **MySQL 8.0 or later**.

### Step 2: Create the tables (left panel: **Schema SQL**)
1. Delete any example text already in the **Schema SQL** panel on the left.
2. From the question file, copy the **🗂️ Create Table** block and paste it in.
3. Below it, paste the **📥 Insert Sample Data** block.

### Step 3: Write your query (right panel: **Query SQL**)
1. Delete any example text in the **Query SQL** panel on the right.
2. Write your own query to answer the question. To check an answer, you can paste the query from the solution file instead.

### Step 4: Run it
1. Click the **Run** button at the top-left.
2. DB Fiddle builds the tables from the left panel, runs your query from the right panel, and shows the **result below the panels**.
3. Compare your result with the **✅ Expected Output** in the question file.

<img width="1464" height="750" alt="Screenshot 2026-10-31 at 2 12 39 PM" src="https://github.com/user-attachments/assets/7b12622f-4e6c-480d-8b55-a5ef379feb59" />

