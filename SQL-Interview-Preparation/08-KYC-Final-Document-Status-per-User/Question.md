# 📌 8) KYC: Final Document Status per User

**Difficulty:** 🟡 Medium

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The kyc_documents table logs every status update of a user's KYC documents
(PAN and AADHAAR). A document can be updated several times.

Write a query that shows, for each user, the final (latest) status of each
document as separate columns:

  user_id | pan_status | aadhaar_status

If a user never uploaded a document, show 'Not uploaded' for it.
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE kyc_documents (
    user_id     VARCHAR(5),
    updated_at  DATE,
    doc_type    VARCHAR(10),
    status      VARCHAR(30)
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO kyc_documents (user_id, updated_at, doc_type, status) VALUES
('u1','2020-01-01','PAN','Verified'),
('u1','2020-02-01','AADHAAR','Verified'),
('u2','2020-03-01','PAN','Rejected'),
('u3','2020-01-01','PAN','Rejected'),
('u3','2020-01-15','PAN','Pending on verification'),
('u3','2020-01-25','AADHAAR','Rejected');
```

## ✅ Expected Output

```text
+---------+-------------------------+----------------+
| user_id | pan_status              | aadhaar_status |
+---------+-------------------------+----------------+
| u1      | Verified                | Verified       |
| u2      | Rejected                | Not uploaded   |
| u3      | Pending on verification | Rejected       |
+---------+-------------------------+----------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
