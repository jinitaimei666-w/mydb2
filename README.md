# 📚 Library Management System Database

一个完整的MySQL图书管理系统数据库项目，支持多角色管理（管理员、员工、成员）、图书管理、预留、罚款和供应商处理。

## 🎯 项目概述

本项目包含了一个功能完整的图书管理系统的数据库设计和示例数据，支持以下核心功能：

- ✅ 多角色系统：管理员、员工、成员
- ✅ 图书管理（CRUD）：作者、出版商、图书
- ✅ 借阅管理：图书发放、归还、预留、罚款
- ✅ 供应商管理
- ✅ 完整的外键关系和数据约束

## 📊 数据库结构

### 表清单

| 表名 | 说明 | 关键字段 |
|------|------|----------|
| `Author` | 作者信息 | id, name |
| `Publisher` | 出版社信息 | id, name |
| `Member` | 图书馆成员 | id, username, email, password |
| `Admin` | 管理员账户 | id, username, password |
| `Employee` | 员工账户 | id, username, email, password |
| `Book` | 图书信息 | id, title, author_id, publisher_id, quantity |
| `Vendor` | 供应商信息 | id, name, contact |
| `Reservation` | 图书预留记录 | id, book_id, member_id, reservation_date |
| `Fine` | 罚款记录 | id, member_id, amount, reason, date_assessed |
| `Issued_Books` | 图书发放记录 | id, book_id, member_id, issue_date, return_date, returned |

### 关系图

```
Author (1) ──┬──── (多) Book ──┬──── (多) Issued_Books
             │                  │
             │              Reservation ──── (1) Member
             │                  │
Publisher (1)┤                  └──── (多) Fine
             │
             └─── Vendor
```

## 🚀 快速开始

### 1. 创建数据库

```bash
mysql -u root -p
```

```sql
CREATE DATABASE librarys_management_system;
USE librarys_management_system;
SOURCE mysql.sql;
```

### 2. 导入SQL文件

```bash
mysql -u root -p librarys_management_system < mysql.sql
```

### 3. 验证安装

```sql
SHOW TABLES;
SELECT * FROM Author;
SELECT * FROM Book;
```

## 👥 默认测试账户

| 角色 | 用户名 | 密码 |
|------|--------|------|
| Admin | admin | admin123 |
| Member | user1 | user123 |
| Employee | employee1 | employee123 |

## 📖 数据库使用示例

### 示例1：查询所有可用图书

```sql
SELECT b.id, b.title, a.name AS author, p.name AS publisher, b.quantity
FROM Book b
JOIN Author a ON b.author_id = a.id
JOIN Publisher p ON b.publisher_id = p.id
WHERE b.quantity > 0
ORDER BY b.title;
```

### 示例2：记录图书发放

```sql
INSERT INTO Issued_Books (book_id, member_id, issue_date, returned)
VALUES (1, 1, CURDATE(), FALSE);
```

### 示例3：查询成员的借阅历史

```sql
SELECT m.username, b.title, ib.issue_date, ib.return_date
FROM Issued_Books ib
JOIN Member m ON ib.member_id = m.id
JOIN Book b ON ib.book_id = b.id
WHERE m.id = 1
ORDER BY ib.issue_date DESC;
```

### 示例4：查询未归还的图书

```sql
SELECT m.username, b.title, ib.issue_date
FROM Issued_Books ib
JOIN Member m ON ib.member_id = m.id
JOIN Book b ON ib.book_id = b.id
WHERE ib.returned = FALSE;
```

### 示例5：成员罚款记录

```sql
SELECT m.username, f.amount, f.reason, f.date_assessed
FROM Fine f
JOIN Member m ON f.member_id = m.id
ORDER BY f.date_assessed DESC;
```

## 🔧 常见操作

### 添加新作者和图书

```sql
INSERT INTO Author (name) VALUES ('新作者名字');
INSERT INTO Book (title, author_id, publisher_id, quantity) 
VALUES ('新图书', 11, 1, 5);
```

### 添加新成员

```sql
INSERT INTO Member (username, email, password) 
VALUES ('新用户', 'new@example.com', 'password123');
```

### 图书归还

```sql
UPDATE Issued_Books 
SET returned = TRUE, return_date = CURDATE() 
WHERE id = 1;
```

### 添加罚款

```sql
INSERT INTO Fine (member_id, amount, reason, date_assessed)
VALUES (1, 5.00, '逾期费用', CURDATE());
```

## 📋 数据约束

- **主键**：所有表都有自增主键 `id`
- **外键**：
  - `Book.author_id` → `Author.id` (ON DELETE SET NULL)
  - `Book.publisher_id` → `Publisher.id` (ON DELETE SET NULL)
  - `Reservation.book_id` → `Book.id` (ON DELETE CASCADE)
  - `Reservation.member_id` → `Member.id` (ON DELETE CASCADE)
  - `Fine.member_id` → `Member.id` (ON DELETE CASCADE)
  - `Issued_Books.book_id` → `Book.id` (ON DELETE CASCADE)
  - `Issued_Books.member_id` → `Member.id` (ON DELETE CASCADE)
- **唯一约束**：
  - `Member.username` 唯一
  - `Admin.username` 唯一
  - `Employee.username` 唯一

## 🔒 安全建议

⚠️ **重要**：本项目包含的示例密码仅用于测试目的。在生产环境中：
- 使用密码哈希（如 bcrypt、argon2）
- 不要存储明文密码
- 添加 CSRF 保护
- 实施速率限制和账户锁定
- 使用 SSL/TLS 加密数据传输

## 🐛 故障排查

### 问题："Table doesn't exist"
- **解决**：确保 `mysql.sql` 已完全导入

### 问题："Foreign key constraint fails"
- **解决**：检查引用的记录是否存在（例如 author_id）

### 问题："Duplicate entry"
- **解决**：检查唯一约束的字段（username、email 等）

## 📚 参考资源

- MySQL 官方文档：https://dev.mysql.com/doc/
- 外键关系：https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html
- 数据类型：https://dev.mysql.com/doc/refman/8.0/en/data-types.html

## 📝 许可证

MIT License

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

---

**创建日期**：2026年7月
**最后更新**：2026年7月8日
