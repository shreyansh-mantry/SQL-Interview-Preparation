# 💡 5) Convert Product Attribute Columns into Rows: Solution

**Difficulty:** 🟢 Easy &nbsp;|&nbsp; **Concepts used:** Unpivot (columns to rows), UNION ALL, string literals as columns, derived table

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
SELECT product_id,
       name,
       value
FROM (
    SELECT product_id, 1 AS attr_order, 'Category'    AS name, category    AS value FROM products
    UNION ALL
    SELECT product_id, 2 AS attr_order, 'Description' AS name, description AS value FROM products
    UNION ALL
    SELECT product_id, 3 AS attr_order, 'Color'       AS name, color       AS value FROM products
    UNION ALL
    SELECT product_id, 4 AS attr_order, 'Size'        AS name, size        AS value FROM products
) AS attributes
ORDER BY product_id, attr_order;
```

## 🪜 Step-by-Step Explanation

This is called **unpivoting**: turning columns into rows. MySQL has no `UNPIVOT` keyword, so we build it with `UNION ALL`.

**Step 1: write one small SELECT per attribute**

```sql
SELECT product_id, 1 AS attr_order, 'Category' AS name, category AS value FROM products
```
- `'Category' AS name` is a **string literal**: the same fixed text is printed on every row. It becomes the attribute name.
- `category AS value` takes the real value from the `category` column.
- `1 AS attr_order` is a helper number we'll use later to sort the attributes in the required order.
- This one SELECT returns 3 rows (one per product), all about *Category*. We write the same thing for Description (2), Color (3) and Size (4).

**Step 2: stack the four results with `UNION ALL`**
- `UNION ALL` places the rows of each SELECT one under another: 4 attributes × 3 products = **12 rows**.
- Every SELECT in a UNION must have the **same number of columns, in the same order, with compatible types**. That's why `size` is a `VARCHAR`: it holds both `'40'` and `'L'`, and all values go into the one text column `value`.
- We use `UNION ALL` instead of `UNION` because `UNION` also removes duplicate rows, which costs extra work and could drop rows we want.

**Step 3: wrap it as a derived table and sort**
- The stacked query sits inside `FROM ( … ) AS attributes`. A query in `FROM` is called a *derived table* and must have an alias (`attributes`).
- `ORDER BY product_id, attr_order` sorts by product first, then by our helper number, so each product lists Category → Description → Color → Size.
- The outer `SELECT` shows only `product_id, name, value`. The helper column `attr_order` is used for sorting but not displayed.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
