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

## 🌳 Understanding the Tree

A **tree** is a set of nodes connected from top to bottom. Every node except the top one hangs under exactly one **parent**. The nodes hanging under a node are its **children**. Here is a small example (letters, not the sample data):

```text
        A            <-- ROOT  : the top node, it has no parent
      /   \
     B     C         <-- INNER : has a parent above AND a child below
    / \     \
   D   E     F       <-- LEAF  : has no children below it
```

In a table, each row stores a node and its parent, so the tree above is stored as:
`(A, NULL)`, `(B, A)`, `(C, A)`, `(D, B)`, `(E, B)`, `(F, C)`.

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
