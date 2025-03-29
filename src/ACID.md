# ACID Properties in PostgreSQL

PostgreSQL is ACID-compliant by default, meaning it ensures transactions are atomic, consistent, isolated, and durable.

## **1. Atomicity in PostgreSQL**

**Ensures "All or Nothing" execution of a transaction.**

- Uses `BEGIN`, `COMMIT`, and `ROLLBACK`.  
- If any part of the transaction fails, everything **rolls back**.

### **Example: Bank Transfer**

```sql
BEGIN;  -- Start a transaction

UPDATE accounts SET balance = balance - 500 WHERE account_id = 1; -- Debit
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2; -- Credit

COMMIT;  -- Save changes
```

If a **system crash** occurs before `COMMIT`, PostgreSQL **automatically rolls back** to ensure no incomplete data.

**If an error occurs, rollback the transaction:**

```sql
BEGIN;
UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
-- an error (e.g., incorrect column name)
UPDATE accounts SET balanc = balance + 500 WHERE account_id = 2;
ROLLBACK;  -- Undo all changes
```

## **2. Consistency in PostgreSQL**

**Ensures the database remains in a valid state** by enforcing constraints.

### **Example: Foreign Key Constraint**

```sql
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name TEXT UNIQUE NOT NULL
);

CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    student_name TEXT NOT NULL,
    department_id INT REFERENCES departments(department_id) ON DELETE CASCADE
);
```

Now, **students cannot be assigned a non-existent department**, ensuring **referential integrity**.

**If you try inserting invalid data, PostgreSQL rejects it:**

```sql
INSERT INTO students (student_name, department_id) VALUES ('John Doe', 100);
-- ERROR: Key (department_id)=100 is not present in the departments table
```

## **3. Isolation in PostgreSQL**

**Controls how transactions interact with each other.**  
PostgreSQL supports four **Isolation Levels**:

| Isolation Level      | Prevents Dirty Reads? | Prevents Non-Repeatable Reads? | Prevents Phantom Reads? |
|----------------------|----------------------|--------------------------------|--------------------------|
| **Read Uncommitted** | ❌ No                | ❌ No                           | ❌ No                     |
| **Read Committed**   | ✅ Yes               | ❌ No                           | ❌ No                     |
| **Repeatable Read**  | ✅ Yes               | ✅ Yes                          | ❌ No                     |
| **Serializable**     | ✅ Yes               | ✅ Yes                          | ✅ Yes                    |

**Default Isolation Level in PostgreSQL = READ COMMITTED**  

### **Example: Preventing Dirty Reads**

#### **Transaction 1 (T1) starts but does not commit immediately**

```sql
BEGIN;
UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
-- (Transaction still running, not committed)
```

#### **Transaction 2 (T2) tries to read the uncommitted balance**:

```sql
SELECT balance FROM accounts WHERE account_id = 1;
```

**In Read Committed Mode**, T2 **waits** until T1 is committed, ensuring no dirty reads.

### **Change Isolation Level**

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

Prevents **phantom reads** and ensures full transaction isolation.

## **4. Durability in PostgreSQL**

**Ensures committed transactions are saved permanently**, even after crashes.  
PostgreSQL achieves durability through:

- **WAL (Write-Ahead Logging)**  
- **Checkpoints & Automatic Recovery**

### **Example: WAL (Write-Ahead Logging)**

```sql
SHOW wal_level;
-- Default is 'replica', ensuring durability
```

PostgreSQL **writes changes to a WAL log** before committing, ensuring no data loss.

### **Example: Ensuring Data is Durable**

```sql
BEGIN;
UPDATE orders SET status = 'Shipped' WHERE order_id = 10;
COMMIT;  -- This change is now permanent
```

Even if the system **crashes immediately after COMMIT**, PostgreSQL **recovers** and maintains data integrity.
