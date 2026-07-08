# 📊 图书管理系统 - 数据库详细设计文档

## 目录

1. [表结构详解](#表结构详解)
2. [关系说明](#关系说明)
3. [数据流程](#数据流程)
4. [SQL查询示例](#sql查询示例)

---

## 表结构详解

### 1. Author 表 (作者表)

```sql
CREATE TABLE Author (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);
```

| 字段 | 类型 | 说明 | 示例 |
|------|------|------|------|
| id | INT | 作者ID（主键） | 1 |
| name | VARCHAR(255) | 作者名字 | J.K. Rowling |

**示例数据**：
```
id | name
---|--------
1  | J.K. Rowling
2  | George Orwell
3  | Agatha Christie
```

---

### 2. Publisher 表 (出版社表)

```sql
CREATE TABLE Publisher (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);
```

| 字段 | 类型 | 说明 | 示例 |
|------|------|------|------|
| id | INT | 出版社ID（主键） | 1 |
| name | VARCHAR(255) | 出版社名字 | Penguin Books |

---

### 3. Member 表 (成员表) ⭐

```sql
CREATE TABLE Member (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) NOT NULL,
    password VARCHAR(255) NOT NULL
);
```

| 字段 | 类型 | 约束 | 说明 | 示例 |
|------|------|------|------|------|
| id | INT | PRIMARY KEY | 成员ID | 1 |
| username | VARCHAR(255) | UNIQUE NOT NULL | 用户名（唯一） | user1 |
| email | VARCHAR(255) | NOT NULL | 邮箱 | user1@example.com |
| password | VARCHAR(255) | NOT NULL | 密码 | user123 |

**示例数据**：
```
id | username       | email                | password
---|----------------|----------------------|---------
1  | user1          | user1@example.com    | user123
2  | Himanshukumar  | himanshu@gmail.com   | himu123
```

---

### 4. Admin 表 (管理员表)

```sql
CREATE TABLE Admin (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL
);
```

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | INT | PRIMARY KEY | 管理员ID |
| username | VARCHAR(255) | UNIQUE NOT NULL | 用户名 |
| password | VARCHAR(255) | NOT NULL | 密码 |

---

### 5. Employee 表 (员工表)

```sql
CREATE TABLE Employee (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL,
    password VARCHAR(100) NOT NULL
);
```

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | INT | PRIMARY KEY | 员工ID |
| username | VARCHAR(100) | UNIQUE NOT NULL | 用户名 |
| email | VARCHAR(100) | NOT NULL | 邮箱 |
| password | VARCHAR(100) | NOT NULL | 密码 |

---

### 6. Book 表 (图书表) ⭐ 核心表

```sql
CREATE TABLE Book (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author_id INT,
    publisher_id INT,
    quantity INT NOT NULL,
    FOREIGN KEY (author_id) REFERENCES Author(id) ON DELETE SET NULL,
    FOREIGN KEY (publisher_id) REFERENCES Publisher(id) ON DELETE SET NULL
);
```

| 字段 | 类型 | 约束 | 说明 | 示例 |
|------|------|------|------|------|
| id | INT | PRIMARY KEY | 图书ID | 1 |
| title | VARCHAR(255) | NOT NULL | 图书标题 | The Shining |
| author_id | INT | FOREIGN KEY | 作者ID | 4 |
| publisher_id | INT | FOREIGN KEY | 出版社ID | 4 |
| quantity | INT | NOT NULL | 库存数量 | 10 |

**示例数据**：
```
id | title                    | author_id | publisher_id | quantity
---|--------------------------|-----------|--------------|----------
1  | The Shining              | 4         | 4            | 10
2  | Pride and Prejudice      | 5         | 1            | 15
3  | The Hobbit               | 6         | 2            | 20
```

---

### 7. Vendor 表 (供应商表)

```sql
CREATE TABLE Vendor (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    contact VARCHAR(100)
);
```

| 字段 | 类型 | 说明 |
|------|------|------|
| id | INT | 供应商ID |
| name | VARCHAR(255) | 供应商名称 |
| contact | VARCHAR(100) | 联系方式 |

---

### 8. Reservation 表 (预留表)

```sql
CREATE TABLE Reservation (
    id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    member_id INT,
    reservation_date DATE,
    FOREIGN KEY (book_id) REFERENCES Book(id) ON DELETE CASCADE,
    FOREIGN KEY (member_id) REFERENCES Member(id) ON DELETE CASCADE
);
```

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | INT | PRIMARY KEY | 预留ID |
| book_id | INT | FOREIGN KEY | 图书ID |
| member_id | INT | FOREIGN KEY | 成员ID |
| reservation_date | DATE | | 预留日期 |

**关系**：一个成员可以预留多本书，一本书可被多个成员预留

---

### 9. Fine 表 (罚款表)

```sql
CREATE TABLE Fine (
    id INT AUTO_INCREMENT PRIMARY KEY,
    member_id INT,
    amount DECIMAL(10, 2),
    reason TEXT,
    date_assessed DATE,
    FOREIGN KEY (member_id) REFERENCES Member(id) ON DELETE CASCADE
);
```

| 字段 | 类型 | 说明 | 示例 |
|------|------|------|------|
| id | INT | 罚款ID | 1 |
| member_id | INT | 成员ID | 1 |
| amount | DECIMAL(10,2) | 罚款金额 | 5.00 |
| reason | TEXT | 罚款原因 | 逾期费用 |
| date_assessed | DATE | 罚款日期 | 2025-05-20 |

---

### 10. Issued_Books 表 (图书发放记录) ⭐ 核心表

```sql
CREATE TABLE Issued_Books (
    id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    member_id INT,
    issue_date DATE,
    return_date DATE,
    returned BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (book_id) REFERENCES Book(id) ON DELETE CASCADE,
    FOREIGN KEY (member_id) REFERENCES Member(id) ON DELETE CASCADE
);
```

| 字段 | 类型 | 说明 | 示例 |
|------|------|------|------|
| id | INT | 发放记录ID | 1 |
| book_id | INT | 图书ID | 1 |
| member_id | INT | 成员ID | 1 |
| issue_date | DATE | 发放日期 | 2025-05-20 |
| return_date | DATE | 归还日期 | 2025-06-03 |
| returned | BOOLEAN | 是否已归还 | FALSE |

---

## 关系说明

### 1对多关系

#### Author → Book
- 一个作者可以写多本书
- 多本书属于一个作者
- 外键：`Book.author_id` → `Author.id`
- 删除策略：SET NULL（删除作者时，图书的 author_id 设为 NULL）

#### Publisher → Book
- 一个出版社可以出版多本书
- 多本书由一个出版社出版
- 外键：`Book.publisher_id` → `Publisher.id`
- 删除策略：SET NULL

#### Member → Issued_Books
- 一个成员可以借多本书
- 多条借阅记录属于一个成员
- 外键：`Issued_Books.member_id` → `Member.id`
- 删除策略：CASCADE（删除成员时，删除其所有借阅记录）

#### Member → Reservation
- 一个成员可以预留多本书
- 多条预留记录属于一个成员

#### Member → Fine
- 一个成员可以有多条罚款记录
- 多条罚款记录属于一个成员

---

## 数据流程

### 流程1：成员借书

```
1. 成员(Member)存在
   ↓
2. 图书(Book)存在且有库存(quantity > 0)
   ↓
3. 创建借阅记录(Issued_Books)
   INSERT INTO Issued_Books (book_id, member_id, issue_date, returned)
   VALUES (book_id, member_id, CURDATE(), FALSE)
   ↓
4. 减少图书库存(可选)
   UPDATE Book SET quantity = quantity - 1 WHERE id = book_id
   ↓
5. 借阅完成
```

### 流程2：成员归还书籍

```
1. 查找未归还的借阅记录(returned = FALSE)
   ↓
2. 更新记录
   UPDATE Issued_Books 
   SET returned = TRUE, return_date = CURDATE()
   WHERE id = issue_id
   ↓
3. 恢复图书库存(可选)
   UPDATE Book SET quantity = quantity + 1 WHERE id = book_id
   ↓
4. 检查是否逾期
   IF return_date > due_date THEN 计算罚款
   ↓
5. 归还完成
```

### 流程3：罚款处理

```
1. 成员借阅的书逾期未归还
   ↓
2. 系统计算逾期天数和罚款金额
   ↓
3. 创建罚款记录(Fine)
   INSERT INTO Fine (member_id, amount, reason, date_assessed)
   VALUES (member_id, fine_amount, 'Overdue: X days', CURDATE())
   ↓
4. 通知成员
```

---

## SQL查询示例

### 查询1：获取所有图书及其作者和出版社

```sql
SELECT 
    b.id,
    b.title,
    a.name AS author,
    p.name AS publisher,
    b.quantity AS stock
FROM Book b
LEFT JOIN Author a ON b.author_id = a.id
LEFT JOIN Publisher p ON b.publisher_id = p.id
ORDER BY b.title;
```

### 查询2：查询某成员的所有借阅记录

```sql
SELECT 
    m.username,
    b.title,
    ib.issue_date,
    ib.return_date,
    CASE 
        WHEN ib.returned = TRUE THEN '已归还'
        ELSE '未归还'
    END AS status
FROM Issued_Books ib
JOIN Member m ON ib.member_id = m.id
JOIN Book b ON ib.book_id = b.id
WHERE m.username = 'user1'
ORDER BY ib.issue_date DESC;
```

### 查询3：查询库存不足的图书

```sql
SELECT 
    b.id,
    b.title,
    b.quantity
FROM Book b
WHERE b.quantity <= 5
ORDER BY b.quantity ASC;
```

### 查询4：查询有罚款的成员

```sql
SELECT 
    m.username,
    COUNT(f.id) AS fine_count,
    SUM(f.amount) AS total_fine
FROM Fine f
JOIN Member m ON f.member_id = m.id
GROUP BY m.id, m.username
HAVING total_fine > 0
ORDER BY total_fine DESC;
```

### 查询5：查询未归还的图书（可能逾期）

```sql
SELECT 
    m.username,
    b.title,
    ib.issue_date,
    DATEDIFF(CURDATE(), ib.issue_date) AS days_borrowed
FROM Issued_Books ib
JOIN Member m ON ib.member_id = m.id
JOIN Book b ON ib.book_id = b.id
WHERE ib.returned = FALSE
ORDER BY days_borrowed DESC;
```

---

**更新日期**: 2026年7月
