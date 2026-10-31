# 💡 9) Binary Search Tree: Node Type: Solution

**Difficulty:** 🟡 Medium &nbsp;|&nbsp; **Concepts used:** CASE WHEN (checked in order), IS NULL, subquery with IN, the NOT IN + NULL trap

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
SELECT node,
       CASE
           WHEN parent IS NULL THEN 'Root'
           WHEN node IN (SELECT parent FROM bst WHERE parent IS NOT NULL) THEN 'Inner'
           ELSE 'Leaf'
       END AS node_type
FROM bst
ORDER BY node;
```

## 🪜 Step-by-Step Explanation

The sample data is this tree:

```text
        5
      /   \
     2     8
    / \   / \
   1   3 6   9
```

**Step 1: understand what each type means in the table**
- **Root**: its `parent` column is NULL (5).
- **Inner**: it appears in somebody else's `parent` column, so it has a child (2 and 8).
- **Leaf**: it never appears in the `parent` column (1, 3, 6, 9).

**Step 2: find every node that is a parent (the subquery)**

```sql
SELECT parent FROM bst WHERE parent IS NOT NULL
```
returns `2, 2, 8, 8, 5, 5`: the nodes that have children.

**Step 3: decide the type with `CASE`**
- `CASE` checks the `WHEN` conditions **from top to bottom** and stops at the first one that is true.
- `WHEN parent IS NULL THEN 'Root'` comes first. Node 5 is also a parent, but checking Root first labels it correctly as Root, not Inner.
- `WHEN node IN (…subquery…) THEN 'Inner'`: `IN` is true if the node appears in the list from Step 2.
- `ELSE 'Leaf'`: anything left over has no children.
- `IS NULL` is used because `parent = NULL` never works: NULL can't be compared with `=`.

**Step 4: `ORDER BY node`** sorts the result by node value.

> **Common trap:** finding leaves with `WHERE node NOT IN (SELECT parent FROM bst)` returns **no rows at all**. The subquery contains a NULL (the root's parent), and `x NOT IN (…, NULL)` is never true. That's why the subquery filters `WHERE parent IS NOT NULL`.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
