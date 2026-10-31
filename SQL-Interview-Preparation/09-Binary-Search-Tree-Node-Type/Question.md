# 📌 9) Binary Search Tree: Node Type

**Difficulty:** 🟡 Medium

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The bst table stores a Binary Search Tree with two columns: node and parent.

Write a query that returns every node and its type, ordered by node value in
ascending order. A node is one of three types:

  Root  - the node has no parent
  Inner - the node has a parent and at least one child
  Leaf  - the node has no children
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE bst (
    node    INT,
    parent  INT
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO bst (node, parent) VALUES
(1,2),(3,2),(6,8),(9,8),(2,5),(8,5),(5,NULL);
```

## ✅ Expected Output

```text
+------+-----------+
| node | node_type |
+------+-----------+
|    1 | Leaf      |
|    2 | Inner     |
|    3 | Leaf      |
|    5 | Root      |
|    6 | Leaf      |
|    8 | Inner     |
|    9 | Leaf      |
+------+-----------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
