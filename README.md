# SQL Injection Demo — Prisma vs Raw SQL

In this lesson we look at the difference between a **vulnerable** login endpoint (raw SQL) and a **safe** one (Prisma parameterized queries).

---

## What is SQL Injection?

Imagine a login form where you type your email address. Behind the scenes, the server builds a database query like this:

```sql
SELECT * FROM "User" WHERE email = 'you@example.com'
```

That looks fine — but what if someone types this as their email?

```
' OR '1'='1
```

The query becomes:

```sql
SELECT * FROM "User" WHERE email = '' OR '1'='1'
```

Since `'1'='1'` is always true, this returns **every user in the database**. The attacker just bypassed the login — without knowing any password.

This is SQL injection: tricking the database by sneaking extra SQL commands into user input.

---

## Tech Stack

- **Frontend:** React + Vite + Tailwind CSS
- **Backend:** Node.js + Express
- **Database:** PostgreSQL
- **ORM:** Prisma

---

## Getting Started

### Prerequisites

- Node.js
- Docker (for the database)

### 1. Start the database

```bash
cd backend
docker compose up -d
```

### 2. Set up the database

```bash
npx prisma migrate dev
npx prisma db seed
```

### 3. Start the backend

```bash
npm install
npm run dev
```

### 4. Start the frontend

```bash
cd ../frontend
npm install
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

---

## Example Attack Strings to Try

Paste these into the **Unsafe** panel to see SQL injection in action:

```
' OR '1'='1
```

Returns all users.

```
' OR admin = true --
```

Returns only admin users.

```
'; DROP TABLE "User"; --
```

Attempts to delete the entire users table.

---

## Why Prisma Prevents This

Prisma uses **parameterized queries**. Instead of building the SQL string with user input directly, it sends the query and the value separately:

```sql
-- Query template (sent first):
SELECT * FROM "User" WHERE email = $1

-- Value (sent separately):
$1 = 'whatever the user typed'
```

The database never interprets the value as SQL — it's always treated as plain text data. No matter what the user types, it cannot change the structure of the query.

---

## Further Reading

- [OWASP Top 10 — Injection](https://owasp.org/Top10/2025/)
- [W3Schools — SQL Injection](https://www.w3schools.com/sql/sql_injection.asp)
