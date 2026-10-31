# 📌 5) Convert Product Attribute Columns into Rows

**Difficulty:** 🟢 Easy

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The products table stores each product's attributes in separate columns:
category, description, color and size.

Write a query that converts these columns into rows, so that every product has
one row per attribute, with the columns:
    product_id | name (attribute name) | value (attribute value)

Order the output by product_id, and within each product show the attributes in
the order: Category, Description, Color, Size.
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE products (
    product_id   INT,
    category     VARCHAR(30),
    description  VARCHAR(50),
    color        VARCHAR(20),
    size         VARCHAR(10)
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO products (product_id, category, description, color, size) VALUES
(123,'Shoes','Men shoes','Black','40'),
(124,'Shirts','Cotton shirt','Blue','L'),
(125,'Watches','Analog watch','Silver','M');
```

## ✅ Expected Output

```text
+------------+-------------+--------------+
| product_id | name        | value        |
+------------+-------------+--------------+
|        123 | Category    | Shoes        |
|        123 | Description | Men shoes    |
|        123 | Color       | Black        |
|        123 | Size        | 40           |
|        124 | Category    | Shirts       |
|        124 | Description | Cotton shirt |
|        124 | Color       | Blue         |
|        124 | Size        | L            |
|        125 | Category    | Watches      |
|        125 | Description | Analog watch |
|        125 | Color       | Silver       |
|        125 | Size        | M            |
+------------+-------------+--------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
