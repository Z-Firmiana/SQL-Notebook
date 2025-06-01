# DDL 语法

## 什么是 DDL

DDL (Data Definition Language) 数据定义语言，用于定义和管理数据库结构。

## 主要 DDL 语句

### CREATE - 创建数据库对象

```sql
-- 创建数据库
CREATE DATABASE company;

-- 创建表
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    age INT,
    department VARCHAR(30)
);

-- 创建索引
CREATE INDEX idx_name ON employees(name);
```

### ALTER - 修改数据库对象

```sql
-- 添加列
ALTER TABLE employees ADD COLUMN salary DECIMAL(10,2);

-- 修改列
ALTER TABLE employees MODIFY COLUMN name VARCHAR(100);

-- 删除列
ALTER TABLE employees DROP COLUMN age;

-- 添加约束
ALTER TABLE employees ADD CONSTRAINT fk_dept 
FOREIGN KEY (department_id) REFERENCES departments(id);
```

### DROP - 删除数据库对象

```sql
-- 删除表
DROP TABLE employees;

-- 删除数据库
DROP DATABASE company;

-- 删除索引
DROP INDEX idx_name;
```

### TRUNCATE - 清空表数据

```sql
-- 清空表（保留结构）
TRUNCATE TABLE employees;
```

## 常用约束

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE,
    age INT CHECK (age >= 18),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 注意事项

- DDL 语句通常会自动提交事务
- 删除操作不可恢复，需谨慎使用
- 修改表结构可能影响现有数据

# DML 语法

## 什么是 DML

DML (Data Manipulation Language) 数据操作语言，用于对数据库中的数据进行增删改查操作。

## 主要 DML 语句

### INSERT - 插入数据

```sql
-- 插入单行数据
INSERT INTO employees (id, name, age, department) 
VALUES (1, '张三', 25, '技术部');

-- 插入多行数据
INSERT INTO employees (id, name, age, department) 
VALUES 
    (2, '李四', 30, '销售部'),
    (3, '王五', 28, '人事部');

-- 从其他表插入数据
INSERT INTO employees_backup 
SELECT * FROM employees WHERE department = '技术部';
```

### SELECT - 查询数据

```sql
-- 基本查询
SELECT * FROM employees;

-- 条件查询
SELECT name, age FROM employees WHERE age > 25;

-- 排序查询
SELECT * FROM employees ORDER BY age DESC;

-- 分组查询
SELECT department, COUNT(*) as count 
FROM employees GROUP BY department;

-- 连接查询
SELECT e.name, d.department_name 
FROM employees e 
JOIN departments d ON e.department_id = d.id;
```

### UPDATE - 更新数据

```sql
-- 更新单条记录
UPDATE employees 
SET age = 26 
WHERE id = 1;

-- 更新多列
UPDATE employees 
SET age = 31, department = '管理部' 
WHERE name = '李四';

-- 批量更新
UPDATE employees 
SET salary = salary * 1.1 
WHERE department = '技术部';
```

### DELETE - 删除数据

```sql
-- 删除特定记录
DELETE FROM employees WHERE id = 1;

-- 条件删除
DELETE FROM employees WHERE age < 20;

-- 删除所有记录（保留表结构）
DELETE FROM employees;
```

## 高级查询语法

### 子查询

```sql
-- 标量子查询
SELECT name FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);

-- EXISTS 子查询
SELECT * FROM departments d
WHERE EXISTS (SELECT 1 FROM employees e WHERE e.department_id = d.id);
```

### 窗口函数

```sql
-- 排名函数
SELECT name, salary, 
       ROW_NUMBER() OVER (ORDER BY salary DESC) as rank
FROM employees;

-- 分组排名
SELECT name, department, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dept_rank
FROM employees;
```

### 聚合函数

```sql
-- 基本聚合
SELECT 
    COUNT(*) as total_count,
    AVG(salary) as avg_salary,
    MAX(salary) as max_salary,
    MIN(salary) as min_salary,
    SUM(salary) as total_salary
FROM employees;
```

## 事务控制

```sql
-- 开始事务
BEGIN;

-- 执行DML操作
INSERT INTO employees VALUES (4, '赵六', 27, '财务部');
UPDATE employees SET salary = 8000 WHERE id = 4;

-- 提交事务
COMMIT;

-- 或回滚事务
-- ROLLBACK;
```

## 注意事项

- DML 操作可以回滚（在事务中）
- 大量数据操作时注意性能影响
- 使用 WHERE 条件避免误操作
- 建议先用 SELECT 验证条件再执行 UPDATE/DELETE

# DQL 语法

## 什么是 DQL

DQL (Data Query Language) 数据查询语言，是 SQL 中专门用于查询数据的语句集合，主要包括 SELECT 语句及其各种子句。

## 基本查询语法

### SELECT 基础语法

```sql
-- 查询所有列
SELECT * FROM employees;

-- 查询指定列
SELECT name, age, salary FROM employees;

-- 使用别名
SELECT name AS 员工姓名, age AS 年龄 FROM employees;

-- 去重查询
SELECT DISTINCT department FROM employees;
```

### WHERE 条件筛选

```sql
-- 比较运算符
SELECT * FROM employees WHERE age > 25;
SELECT * FROM employees WHERE salary BETWEEN 5000 AND 10000;
SELECT * FROM employees WHERE name IN ('张三', '李四');
SELECT * FROM employees WHERE name LIKE '张%';

-- 逻辑运算符
SELECT * FROM employees WHERE age > 25 AND department = '技术部';
SELECT * FROM employees WHERE age < 20 OR age > 60;
SELECT * FROM employees WHERE NOT department = '销售部';

-- 空值判断
SELECT * FROM employees WHERE phone IS NULL;
SELECT * FROM employees WHERE phone IS NOT NULL;
```

## 排序与分页

### ORDER BY 排序

```sql
-- 单列排序
SELECT * FROM employees ORDER BY age DESC;

-- 多列排序
SELECT * FROM employees ORDER BY department ASC, salary DESC;

-- 使用表达式排序
SELECT * FROM employees ORDER BY salary * 12 DESC;
```

### LIMIT 分页

```sql
-- 限制返回行数
SELECT * FROM employees LIMIT 10;

-- 分页查询
SELECT * FROM employees LIMIT 10 OFFSET 20;

-- MySQL 简化语法
SELECT * FROM employees LIMIT 20, 10;
```

## 聚合查询

### 聚合函数

```sql
-- 基本聚合
SELECT 
    COUNT(*) AS 总人数,
    COUNT(phone) AS 有电话人数,
    AVG(salary) AS 平均工资,
    MAX(salary) AS 最高工资,
    MIN(salary) AS 最低工资,
    SUM(salary) AS 工资总和
FROM employees;

-- 字符串聚合
SELECT GROUP_CONCAT(name) AS 员工列表 FROM employees;
```

### GROUP BY 分组

```sql
-- 基本分组
SELECT department, COUNT(*) AS 人数
FROM employees 
GROUP BY department;

-- 多列分组
SELECT department, position, AVG(salary) AS 平均工资
FROM employees 
GROUP BY department, position;

-- 分组后筛选
SELECT department, COUNT(*) AS 人数
FROM employees 
GROUP BY department
HAVING COUNT(*) > 5;
```

## 连接查询

### 内连接 INNER JOIN

```sql
-- 显式内连接
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;

-- 隐式内连接
SELECT e.name, d.department_name
FROM employees e, departments d
WHERE e.department_id = d.id;
```

### 外连接

```sql
-- 左外连接
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;

-- 右外连接
SELECT e.name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;

-- 全外连接
SELECT e.name, d.department_name
FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.id;
```

### 自连接

```sql
-- 查找同部门员工
SELECT e1.name AS 员工1, e2.name AS 员工2
FROM employees e1
JOIN employees e2 ON e1.department_id = e2.department_id
WHERE e1.id < e2.id;
```

## 子查询

### 标量子查询

```sql
-- 查询高于平均工资的员工
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

### 列子查询

```sql
-- 查询技术部门的所有员工
SELECT name FROM employees
WHERE department_id IN (
    SELECT id FROM departments WHERE name LIKE '%技术%'
);
```

### 行子查询

```sql
-- 查询与张三同部门同职位的员工
SELECT * FROM employees
WHERE (department_id, position_id) = (
    SELECT department_id, position_id 
    FROM employees 
    WHERE name = '张三'
);
```

### 表子查询

```sql
-- 查询各部门工资最高的员工
SELECT * FROM employees e1
WHERE salary = (
    SELECT MAX(salary) 
    FROM employees e2 
    WHERE e1.department_id = e2.department_id
);
```

## 窗口函数

### 排名函数

```sql
-- 行号
SELECT name, salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num
FROM employees;

-- 排名（有并列）
SELECT name, salary,
       RANK() OVER (ORDER BY salary DESC) AS rank_num,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank_num
FROM employees;

-- 分组排名
SELECT name, department, salary,
       ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM employees;
```

### 聚合窗口函数

```sql
-- 累计求和
SELECT name, salary,
       SUM(salary) OVER (ORDER BY hire_date) AS running_total
FROM employees;

-- 移动平均
SELECT name, salary,
       AVG(salary) OVER (ORDER BY hire_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM employees;
```

### 位置函数

```sql
-- 前后行数据
SELECT name, salary,
       LAG(salary, 1) OVER (ORDER BY hire_date) AS prev_salary,
       LEAD(salary, 1) OVER (ORDER BY hire_date) AS next_salary
FROM employees;

-- 首尾值
SELECT name, salary,
       FIRST_VALUE(salary) OVER (PARTITION BY department ORDER BY hire_date) AS first_salary,
       LAST_VALUE(salary) OVER (PARTITION BY department ORDER BY hire_date) AS last_salary
FROM employees;
```

## 高级查询技巧

### CASE WHEN 条件表达式

```sql
-- 简单 CASE
SELECT name, salary,
       CASE 
           WHEN salary >= 10000 THEN '高薪'
           WHEN salary >= 5000 THEN '中薪'
           ELSE '低薪'
       END AS salary_level
FROM employees;
```

### UNION 联合查询

```sql
-- 合并查询结果
SELECT name, 'employee' AS type FROM employees
UNION
SELECT name, 'manager' AS type FROM managers;

-- 保留重复记录
SELECT department FROM employees
UNION ALL
SELECT department FROM temp_employees;
```

### EXISTS 存在性查询

```sql
-- 查询有员工的部门
SELECT * FROM departments d
WHERE EXISTS (
    SELECT 1 FROM employees e WHERE e.department_id = d.id
);

-- 查询没有员工的部门
SELECT * FROM departments d
WHERE NOT EXISTS (
    SELECT 1 FROM employees e WHERE e.department_id = d.id
);
```

## 注意事项

- SELECT 语句不会修改数据，是只读操作
- 使用索引优化查询性能
- 避免使用 SELECT * 查询所有列
- 大数据量查询时使用 LIMIT 分页
- 复杂查询建议使用 EXPLAIN 分析执行计划

# DCL 语法

## 什么是 DCL

DCL (Data Control Language) 数据控制语言，用于控制数据库的访问权限和安全性，主要包括权限管理和用户管理相关的语句。

## 主要 DCL 语句

### GRANT - 授权

```sql
-- 授予表级权限
GRANT SELECT, INSERT, UPDATE ON employees TO user1;

-- 授予数据库级权限
GRANT ALL PRIVILEGES ON company.* TO user1;

-- 授予系统级权限
GRANT CREATE, DROP ON *.* TO admin_user;

-- 授予特定列权限
GRANT SELECT (name, age), UPDATE (salary) ON employees TO hr_user;

-- 授予执行存储过程权限
GRANT EXECUTE ON PROCEDURE calculate_bonus TO user1;

-- 授权时指定密码
GRANT SELECT ON employees TO 'user1'@'localhost' IDENTIFIED BY 'password123';

-- 授权并允许转授
GRANT SELECT ON employees TO user1 WITH GRANT OPTION;
```

### REVOKE - 撤销权限

```sql
-- 撤销表级权限
REVOKE INSERT, UPDATE ON employees FROM user1;

-- 撤销所有权限
REVOKE ALL PRIVILEGES ON company.* FROM user1;

-- 撤销授权权限
REVOKE GRANT OPTION FOR SELECT ON employees FROM user1;

-- 级联撤销权限
REVOKE SELECT ON employees FROM user1 CASCADE;
```

## 用户管理

### 创建用户

```sql
-- 创建本地用户
CREATE USER 'user1'@'localhost' IDENTIFIED BY 'password123';

-- 创建可从任意主机连接的用户
CREATE USER 'user1'@'%' IDENTIFIED BY 'password123';

-- 创建指定主机用户
CREATE USER 'user1'@'192.168.1.100' IDENTIFIED BY 'password123';

-- 创建用户并设置密码过期
CREATE USER 'user1'@'localhost' 
IDENTIFIED BY 'password123' 
PASSWORD EXPIRE;
```

### 修改用户

```sql
-- 修改用户密码
ALTER USER 'user1'@'localhost' IDENTIFIED BY 'newpassword123';

-- 重命名用户
RENAME USER 'old_user'@'localhost' TO 'new_user'@'localhost';

-- 锁定用户账户
ALTER USER 'user1'@'localhost' ACCOUNT LOCK;

-- 解锁用户账户
ALTER USER 'user1'@'localhost' ACCOUNT UNLOCK;

-- 设置密码过期策略
ALTER USER 'user1'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
```

### 删除用户

```sql
-- 删除用户
DROP USER 'user1'@'localhost';

-- 删除多个用户
DROP USER 'user1'@'localhost', 'user2'@'%';
```

## 角色管理

### 创建角色

```sql
-- 创建角色
CREATE ROLE 'developer';
CREATE ROLE 'analyst';
CREATE ROLE 'admin';

-- 给角色授权
GRANT SELECT, INSERT, UPDATE ON company.* TO 'developer';
GRANT SELECT ON company.* TO 'analyst';
GRANT ALL PRIVILEGES ON company.* TO 'admin';
```

### 分配角色

```sql
-- 将角色分配给用户
GRANT 'developer' TO 'user1'@'localhost';
GRANT 'analyst', 'developer' TO 'user2'@'localhost';

-- 设置默认角色
ALTER USER 'user1'@'localhost' DEFAULT ROLE 'developer';

-- 激活所有角色
SET DEFAULT ROLE ALL TO 'user1'@'localhost';
```

### 撤销角色

```sql
-- 从用户撤销角色
REVOKE 'developer' FROM 'user1'@'localhost';

-- 删除角色
DROP ROLE 'developer';
```

## 权限类型详解

### 数据库对象权限

```sql
-- 表权限
GRANT SELECT ON table_name TO user1;           -- 查询权限
GRANT INSERT ON table_name TO user1;           -- 插入权限
GRANT UPDATE ON table_name TO user1;           -- 更新权限
GRANT DELETE ON table_name TO user1;           -- 删除权限
GRANT ALTER ON table_name TO user1;            -- 修改表结构权限

-- 数据库权限
GRANT CREATE ON database_name.* TO user1;      -- 创建表权限
GRANT DROP ON database_name.* TO user1;        -- 删除表权限
GRANT INDEX ON database_name.* TO user1;       -- 创建索引权限
GRANT REFERENCES ON database_name.* TO user1;  -- 外键引用权限
```

### 管理权限

```sql
-- 系统管理权限
GRANT RELOAD ON *.* TO admin_user;              -- 重载权限
GRANT SHUTDOWN ON *.* TO admin_user;            -- 关闭服务器权限
GRANT PROCESS ON *.* TO admin_user;             -- 查看进程权限
GRANT SUPER ON *.* TO admin_user;               -- 超级用户权限

-- 用户管理权限
GRANT CREATE USER ON *.* TO admin_user;         -- 创建用户权限
GRANT GRANT OPTION ON *.* TO admin_user;        -- 授权权限
```

## 权限查询

### 查看权限信息

```sql
-- 查看当前用户权限
SHOW GRANTS;

-- 查看指定用户权限
SHOW GRANTS FOR 'user1'@'localhost';

-- 查看所有用户
SELECT User, Host FROM mysql.user;

-- 查看用户表权限
SELECT * FROM mysql.tables_priv WHERE User = 'user1';

-- 查看数据库权限
SELECT * FROM mysql.db WHERE User = 'user1';

-- 查看列权限
SELECT * FROM mysql.columns_priv WHERE User = 'user1';
```

### 权限检查

```sql
-- 检查当前用户权限
SELECT USER(), CURRENT_USER();

-- 刷新权限
FLUSH PRIVILEGES;
```

## 安全最佳实践

### 密码策略

```sql
-- 设置密码复杂度要求
SET GLOBAL validate_password.policy = STRONG;
SET GLOBAL validate_password.length = 12;

-- 强制密码过期
ALTER USER 'user1'@'localhost' PASSWORD EXPIRE;

-- 设置密码历史
ALTER USER 'user1'@'localhost' PASSWORD HISTORY 5;
```

### 连接限制

```sql
-- 限制用户连接数
ALTER USER 'user1'@'localhost' 
WITH MAX_CONNECTIONS_PER_HOUR 100
    MAX_QUERIES_PER_HOUR 1000
    MAX_UPDATES_PER_HOUR 500;
```

### 审计和监控

```sql
-- 启用审计日志
SET GLOBAL audit_log_policy = ALL;

-- 查看连接信息
SHOW PROCESSLIST;

-- 查看用户连接状态
SELECT USER, HOST, COMMAND, TIME, STATE 
FROM INFORMATION_SCHEMA.PROCESSLIST;
```

## 实际应用示例

### 开发环境权限配置

```sql
-- 创建开发者角色
CREATE ROLE 'developer';
GRANT SELECT, INSERT, UPDATE, DELETE ON dev_db.* TO 'developer';
GRANT CREATE, DROP, ALTER ON dev_db.* TO 'developer';

-- 创建开发者用户
CREATE USER 'dev_user'@'%' IDENTIFIED BY 'DevPassword123!';
GRANT 'developer' TO 'dev_user'@'%';
SET DEFAULT ROLE 'developer' TO 'dev_user'@'%';
```

### 生产环境权限配置

```sql
-- 创建只读用户（用于报表）
CREATE USER 'report_user'@'%' IDENTIFIED BY 'ReportPass123!';
GRANT SELECT ON prod_db.employees TO 'report_user'@'%';
GRANT SELECT ON prod_db.departments TO 'report_user'@'%';

-- 创建应用用户（限制权限）
CREATE USER 'app_user'@'192.168.1.%' IDENTIFIED BY 'AppPassword123!';
GRANT SELECT, INSERT, UPDATE ON prod_db.* TO 'app_user'@'192.168.1.%';
```

## 注意事项

- DCL 语句通常需要管理员权限才能执行
- 权限变更后建议执行 FLUSH PRIVILEGES 刷新权限缓存
- 遵循最小权限原则，只授予必要的权限
- 定期审查和清理不必要的用户和权限
- 使用强密码策略和定期密码更新
- 限制用户连接来源和连接数量

# SQL 函数 - 字符串函数

## 什么是字符串函数

字符串函数是 SQL 中用于处理和操作字符串数据的内置函数集合。这些函数可以用来查询、修改、格式化和分析文本数据，是数据处理中非常重要的工具。

## 基本字符串函数

### 长度和位置函数

```sql
-- LENGTH() / LEN() - 获取字符串长度
SELECT LENGTH('Hello World') AS str_length;        -- 返回: 11
SELECT name, LENGTH(name) AS name_length FROM employees;

-- CHAR_LENGTH() - 获取字符数（多字节字符计为1）
SELECT CHAR_LENGTH('你好世界') AS char_count;       -- 返回: 4

-- POSITION() / LOCATE() - 查找子字符串位置
SELECT POSITION('World' IN 'Hello World') AS pos;  -- 返回: 7
SELECT LOCATE('o', 'Hello World') AS first_o;      -- 返回: 5
SELECT LOCATE('o', 'Hello World', 6) AS second_o;  -- 从第6位开始查找，返回: 8
```

### 大小写转换函数

```sql
-- UPPER() / UCASE() - 转换为大写
SELECT UPPER('hello world') AS uppercase;          -- 返回: HELLO WORLD
SELECT name, UPPER(name) AS upper_name FROM employees;

-- LOWER() / LCASE() - 转换为小写  
SELECT LOWER('HELLO WORLD') AS lowercase;          -- 返回: hello world
SELECT email, LOWER(email) AS lower_email FROM users;

-- INITCAP() - 首字母大写（部分数据库支持）
SELECT INITCAP('hello world') AS title_case;       -- 返回: Hello World
```

### 截取和提取函数

```sql
-- SUBSTRING() / SUBSTR() - 截取子字符串
SELECT SUBSTRING('Hello World', 1, 5) AS sub1;     -- 返回: Hello
SELECT SUBSTRING('Hello World', 7) AS sub2;        -- 返回: World
SELECT SUBSTR(phone, 1, 3) AS area_code FROM customers;

-- LEFT() - 从左侧截取指定长度
SELECT LEFT('Hello World', 5) AS left_part;        -- 返回: Hello

-- RIGHT() - 从右侧截取指定长度
SELECT RIGHT('Hello World', 5) AS right_part;      -- 返回: World

-- MID() - 从中间位置截取（MySQL）
SELECT MID('Hello World', 3, 3) AS mid_part;       -- 返回: llo
```

### 修剪和填充函数

```sql
-- TRIM() - 去除两端空格
SELECT TRIM('  Hello World  ') AS trimmed;         -- 返回: Hello World
SELECT TRIM(BOTH ' ' FROM '  Hello  ') AS both_trim;

-- LTRIM() - 去除左侧空格
SELECT LTRIM('  Hello World') AS left_trimmed;     -- 返回: Hello World

-- RTRIM() - 去除右侧空格
SELECT RTRIM('Hello World  ') AS right_trimmed;    -- 返回: Hello World

-- LPAD() - 左侧填充
SELECT LPAD('123', 6, '0') AS padded;              -- 返回: 000123
SELECT LPAD(id, 6, '0') AS formatted_id FROM products;

-- RPAD() - 右侧填充
SELECT RPAD('Hello', 10, '*') AS right_padded;     -- 返回: Hello*****
```

## 查找和替换函数

### 字符串替换

```sql
-- REPLACE() - 替换字符串
SELECT REPLACE('Hello World', 'World', 'SQL') AS replaced;  -- 返回: Hello SQL
SELECT REPLACE(description, 'old', 'new') AS updated_desc FROM products;

-- 多重替换示例
SELECT REPLACE(REPLACE(phone, '-', ''), ' ', '') AS clean_phone FROM customers;
```

### 字符串匹配

```sql
-- LIKE 模式匹配
SELECT * FROM employees WHERE name LIKE 'J%';      -- 以J开头
SELECT * FROM employees WHERE email LIKE '%@gmail.com';  -- 以@gmail.com结尾
SELECT * FROM products WHERE code LIKE 'A_B%';     -- A开头，第三位是B

-- REGEXP / RLIKE - 正则表达式匹配（MySQL）
SELECT * FROM employees WHERE name REGEXP '^[A-Z][a-z]+$';
SELECT * FROM emails WHERE email REGEXP '[0-9]+@[a-z]+\\.com$';
```

## 字符串连接函数

### 基本连接

```sql
-- CONCAT() - 连接字符串
SELECT CONCAT('Hello', ' ', 'World') AS combined;   -- 返回: Hello World
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM employees;

-- CONCAT_WS() - 使用分隔符连接
SELECT CONCAT_WS('-', year, month, day) AS date_str; -- 返回: 2024-03-15
SELECT CONCAT_WS(', ', last_name, first_name) AS name_format FROM employees;

-- || 操作符（部分数据库）
SELECT first_name || ' ' || last_name AS full_name FROM employees;
```

### 高级连接

```sql
-- GROUP_CONCAT() - 分组连接（MySQL）
SELECT department, GROUP_CONCAT(name) AS employee_list 
FROM employees 
GROUP BY department;

-- 指定分隔符和排序
SELECT department, 
    GROUP_CONCAT(name ORDER BY name SEPARATOR '; ') AS sorted_employees
FROM employees 
GROUP BY department;

-- STRING_AGG() - 分组连接（PostgreSQL, SQL Server）
SELECT department, 
    STRING_AGG(name, ', ' ORDER BY name) AS employee_list
FROM employees 
GROUP BY department;
```

## 格式化函数

### 数字格式化

```sql
-- FORMAT() - 格式化数字
SELECT FORMAT(1234567.89, 2) AS formatted_number;  -- 返回: 1,234,567.89
SELECT FORMAT(salary, 0) AS formatted_salary FROM employees;

-- 货币格式化示例
SELECT CONCAT('$', FORMAT(salary, 2)) AS currency FROM employees;
```

### 字符串格式化

```sql
-- PRINTF() / FORMAT() - 格式化字符串
SELECT PRINTF('Employee: %s, Salary: $%.2f', name, salary) AS info
FROM employees;

-- 使用 CASE 进行条件格式化
SELECT name,
    CASE 
        WHEN salary >= 10000 THEN CONCAT(name, ' (高薪)')
        WHEN salary >= 5000 THEN CONCAT(name, ' (中薪)')
        ELSE CONCAT(name, ' (一般)')
    END AS formatted_name
FROM employees;
```

## 高级字符串函数

### 字符串分析

```sql
-- ASCII() - 获取字符的ASCII值
SELECT ASCII('A') AS ascii_value;                   -- 返回: 65
SELECT ASCII(LEFT(name, 1)) AS first_char_ascii FROM employees;

-- CHAR() - 根据ASCII值生成字符
SELECT CHAR(65) AS char_value;                      -- 返回: A

-- REVERSE() - 反转字符串
SELECT REVERSE('Hello') AS reversed;                -- 返回: olleH
```

### 字符串编码

```sql
-- HEX() - 转换为十六进制
SELECT HEX('Hello') AS hex_value;                   -- 返回: 48656C6C6F

-- UNHEX() - 十六进制转换为字符串
SELECT UNHEX('48656C6C6F') AS original;             -- 返回: Hello

-- BASE64 编码/解码
SELECT TO_BASE64('Hello World') AS encoded;
SELECT FROM_BASE64('SGVsbG8gV29ybGQ=') AS decoded;
```

### 字符串比较

```sql
-- STRCMP() - 字符串比较
SELECT STRCMP('abc', 'abc') AS equal;               -- 返回: 0
SELECT STRCMP('abc', 'def') AS less;                -- 返回: -1
SELECT STRCMP('def', 'abc') AS greater;             -- 返回: 1

-- SOUNDEX() - 语音匹配算法
SELECT SOUNDEX('Smith') AS soundex1;                -- 返回: S530
SELECT SOUNDEX('Smyth') AS soundex2;                -- 返回: S530
SELECT * FROM customers 
WHERE SOUNDEX(last_name) = SOUNDEX('Johnson');
```

## 实际应用示例

### 数据清理

```sql
-- 清理电话号码格式
SELECT phone,
    REPLACE(REPLACE(REPLACE(phone, '-', ''), '(', ''), ')', '') AS clean_phone
FROM customers;

-- 标准化邮箱格式
SELECT email,
    LOWER(TRIM(email)) AS normalized_email
FROM users
WHERE email IS NOT NULL;

-- 提取域名
SELECT email,
    SUBSTRING(email, POSITION('@' IN email) + 1) AS domain
FROM users;
```

### 数据验证

```sql
-- 验证邮箱格式
SELECT email,
    CASE 
        WHEN email LIKE '%@%.%' AND email NOT LIKE '%@%@%' 
        THEN '有效'
        ELSE '无效'
    END AS email_status
FROM users;

-- 验证手机号格式（中国）
SELECT phone,
    CASE 
        WHEN phone REGEXP '^1[3-9][0-9]{9}$' 
        THEN '有效'
        ELSE '无效'
    END AS phone_status
FROM customers;
```

### 报表生成

```sql
-- 生成员工信息摘要
SELECT 
    CONCAT(
     UPPER(LEFT(first_name, 1)), 
     LOWER(SUBSTRING(first_name, 2)), 
     ' ', 
     UPPER(last_name)
    ) AS formatted_name,
    CONCAT('$', FORMAT(salary, 0)) AS formatted_salary,
    CONCAT(
     UPPER(LEFT(department, 1)),
     LOWER(SUBSTRING(department, 2))
    ) AS formatted_dept
FROM employees;

-- 生成缩写代码
SELECT company_name,
    UPPER(CONCAT(
        LEFT(company_name, 1),
        SUBSTRING(company_name, POSITION(' ' IN company_name) + 1, 1)
    )) AS company_code
FROM companies
WHERE company_name LIKE '% %';
```

### 搜索优化

```sql
-- 模糊搜索优化
SELECT * FROM products
WHERE LOWER(REPLACE(product_name, ' ', '')) 
      LIKE LOWER(REPLACE('%search term%', ' ', ''));

-- 全文搜索准备
SELECT product_id,
    CONCAT_WS(' ', product_name, description, category) AS search_text
FROM products;
```

## 数据库差异说明

### MySQL 特有函数

```sql
-- FIELD() - 返回值在列表中的位置
SELECT FIELD('b', 'a', 'b', 'c') AS position;      -- 返回: 2

-- INSERT() - 在指定位置插入字符串
SELECT INSERT('Hello World', 7, 5, 'SQL') AS result; -- 返回: Hello SQL

-- ELT() - 返回指定位置的字符串
SELECT ELT(2, 'first', 'second', 'third') AS result; -- 返回: second
```

### SQL Server 特有函数

```sql
-- CHARINDEX() - 查找子字符串位置
SELECT CHARINDEX('World', 'Hello World') AS pos;    -- 返回: 7

-- STUFF() - 替换指定位置的字符
SELECT STUFF('Hello World', 7, 5, 'SQL') AS result; -- 返回: Hello SQL

-- QUOTENAME() - 添加引号
SELECT QUOTENAME('table name') AS quoted;           -- 返回: [table name]
```

### PostgreSQL 特有函数

```sql
-- SPLIT_PART() - 分割字符串并返回指定部分
SELECT SPLIT_PART('a,b,c', ',', 2) AS part;        -- 返回: b

-- REPEAT() - 重复字符串
SELECT REPEAT('Ha', 3) AS repeated;                 -- 返回: HaHaHa

-- TRANSLATE() - 字符替换
SELECT TRANSLATE('Hello', 'elo', '310') AS result;  -- 返回: H311o
```

## 性能优化建议

### 索引使用

```sql
-- 避免在WHERE子句中对列使用函数
-- 不推荐
SELECT * FROM employees WHERE UPPER(name) = 'JOHN';

-- 推荐 - 使用函数索引或存储计算值
CREATE INDEX idx_name_upper ON employees(UPPER(name));
-- 或者存储大写名称在单独列中
```

### 批量处理

```sql
-- 批量字符串操作
UPDATE employees 
SET email = LOWER(TRIM(email))
WHERE email IS NOT NULL;

-- 使用CASE避免多次UPDATE
UPDATE products
SET product_code = CASE 
    WHEN category = 'Electronics' THEN CONCAT('E-', id)
    WHEN category = 'Clothing' THEN CONCAT('C-', id)
    ELSE CONCAT('O-', id)
END;
```

## 注意事项

- 字符串函数可能区分大小写，取决于数据库配置
- 处理NULL值时要小心，大多数字符串函数遇到NULL会返回NULL
- 长字符串操作可能影响性能，建议在应用层处理复杂逻辑
- 不同数据库的字符串函数语法可能略有差异
- 使用字符串函数时注意字符编码和排序规则
- 在生产环境中测试字符串函数的性能影响

# SQL 函数 - 数值函数

## 什么是数值函数

数值函数是 SQL 中用于处理和计算数值数据的内置函数集合。这些函数可以执行数学运算、统计计算、数值格式化和类型转换等操作，是数据分析和业务计算中不可缺少的工具。

## 基本数学函数

### 四则运算函数

```sql
-- ABS() - 绝对值
SELECT ABS(-15) AS absolute_value;                  -- 返回: 15
SELECT ABS(profit) AS absolute_profit FROM sales;

-- SIGN() - 符号函数
SELECT SIGN(-10) AS negative_sign;                  -- 返回: -1
SELECT SIGN(0) AS zero_sign;                        -- 返回: 0
SELECT SIGN(10) AS positive_sign;                   -- 返回: 1

-- MOD() / % - 取模运算
SELECT MOD(10, 3) AS remainder;                     -- 返回: 1
SELECT id % 2 AS is_even FROM products;            -- 判断奇偶

-- POWER() / POW() - 幂运算
SELECT POWER(2, 3) AS power_result;                 -- 返回: 8
SELECT POW(salary/1000, 2) AS salary_squared FROM employees;

-- SQRT() - 平方根
SELECT SQRT(16) AS square_root;                     -- 返回: 4
SELECT SQRT(area) AS side_length FROM squares;
```

### 舍入和截断函数

```sql
-- ROUND() - 四舍五入
SELECT ROUND(123.456, 2) AS rounded_two;           -- 返回: 123.46
SELECT ROUND(123.456, 0) AS rounded_int;           -- 返回: 123
SELECT ROUND(123.456, -1) AS rounded_ten;          -- 返回: 120
SELECT ROUND(salary, -3) AS rounded_thousands FROM employees;

-- CEIL() / CEILING() - 向上取整
SELECT CEIL(4.3) AS ceiling_value;                 -- 返回: 5
SELECT CEILING(-4.3) AS ceiling_negative;          -- 返回: -4
SELECT CEIL(price) AS min_price FROM products;

-- FLOOR() - 向下取整
SELECT FLOOR(4.8) AS floor_value;                  -- 返回: 4
SELECT FLOOR(-4.3) AS floor_negative;              -- 返回: -5
SELECT FLOOR(discount_rate * 100) AS discount_percent FROM promotions;

-- TRUNCATE() / TRUNC() - 截断
SELECT TRUNCATE(123.456, 2) AS truncated;          -- 返回: 123.45
SELECT TRUNC(salary, -2) AS truncated_hundreds FROM employees;
```

## 三角函数

### 基本三角函数

```sql
-- SIN() - 正弦
SELECT SIN(PI()/2) AS sine_90;                     -- 返回: 1
SELECT SIN(angle * PI()/180) AS sine_degrees FROM measurements;

-- COS() - 余弦
SELECT COS(0) AS cosine_0;                         -- 返回: 1
SELECT COS(angle * PI()/180) AS cosine_degrees FROM measurements;

-- TAN() - 正切
SELECT TAN(PI()/4) AS tangent_45;                  -- 返回: 1
SELECT TAN(slope_angle) AS slope_ratio FROM terrain;

-- PI() - 圆周率
SELECT PI() AS pi_value;                           -- 返回: 3.141593
SELECT 2 * PI() * radius AS circumference FROM circles;
```

### 反三角函数

```sql
-- ASIN() - 反正弦
SELECT ASIN(1) AS arcsine_1;                       -- 返回: 1.5708 (π/2)
SELECT ASIN(sine_value) * 180/PI() AS angle_degrees FROM calculations;

-- ACOS() - 反余弦
SELECT ACOS(0) AS arccosine_0;                     -- 返回: 1.5708 (π/2)

-- ATAN() - 反正切
SELECT ATAN(1) AS arctangent_1;                    -- 返回: 0.7854 (π/4)

-- ATAN2() - 双参数反正切
SELECT ATAN2(y_coord, x_coord) AS angle_radians FROM coordinates;
```

### 双曲函数

```sql
-- SINH() - 双曲正弦
SELECT SINH(1) AS hyperbolic_sine;                 -- 返回: 1.1752

-- COSH() - 双曲余弦
SELECT COSH(0) AS hyperbolic_cosine;               -- 返回: 1

-- TANH() - 双曲正切
SELECT TANH(1) AS hyperbolic_tangent;              -- 返回: 0.7616
```

## 对数和指数函数

### 对数函数

```sql
-- LOG() - 自然对数
SELECT LOG(2.718281828) AS natural_log;            -- 返回: 1
SELECT LOG(population) AS log_population FROM cities;

-- LOG10() - 以10为底的对数
SELECT LOG10(100) AS log_base_10;                  -- 返回: 2
SELECT LOG10(revenue) AS log_revenue FROM companies;

-- LOG(base, number) - 指定底数的对数
SELECT LOG(2, 8) AS log_base_2;                    -- 返回: 3
SELECT LOG(2, value) AS binary_log FROM data_points;
```

### 指数函数

```sql
-- EXP() - e的幂
SELECT EXP(1) AS e_power_1;                        -- 返回: 2.718281828
SELECT EXP(growth_rate) AS growth_factor FROM investments;

-- EXP10() - 10的幂（部分数据库支持）
SELECT EXP10(2) AS ten_power_2;                    -- 返回: 100
```

## 随机数函数

### 生成随机数

```sql
-- RAND() / RANDOM() - 生成0-1之间的随机数
SELECT RAND() AS random_value;                     -- 返回: 0.123456
SELECT RAND(123) AS seeded_random;                 -- 使用种子值

-- 生成指定范围的随机整数
SELECT FLOOR(RAND() * 100) AS random_0_to_99;
SELECT FLOOR(RAND() * 10) + 1 AS random_1_to_10;

-- 随机排序
SELECT * FROM products ORDER BY RAND() LIMIT 5;

-- 随机采样
SELECT customer_id, RAND() AS random_value
FROM customers
ORDER BY RAND()
LIMIT 1000;
```

## 聚合数值函数

### 基本统计函数

```sql
-- COUNT() - 计数
SELECT COUNT(*) AS total_records FROM sales;
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM orders;

-- SUM() - 求和
SELECT SUM(amount) AS total_sales FROM orders;
SELECT SUM(quantity * price) AS total_revenue FROM order_items;

-- AVG() - 平均值
SELECT AVG(salary) AS average_salary FROM employees;
SELECT AVG(CASE WHEN gender = 'M' THEN salary END) AS male_avg_salary FROM employees;

-- MIN() / MAX() - 最小值/最大值
SELECT MIN(price) AS min_price, MAX(price) AS max_price FROM products;
SELECT MIN(order_date) AS first_order, MAX(order_date) AS last_order FROM orders;
```

### 高级统计函数

```sql
-- STDDEV() / STDDEV_POP() - 标准差
SELECT STDDEV(salary) AS salary_stddev FROM employees;
SELECT department, STDDEV(salary) AS dept_salary_stddev 
FROM employees GROUP BY department;

-- VARIANCE() / VAR_POP() - 方差
SELECT VARIANCE(score) AS score_variance FROM test_results;

-- MEDIAN() - 中位数（部分数据库支持）
SELECT MEDIAN(salary) AS median_salary FROM employees;
```

## 数值转换函数

### 类型转换

```sql
-- CAST() - 类型转换
SELECT CAST('123.45' AS DECIMAL(10,2)) AS converted_decimal;
SELECT CAST(price AS INTEGER) AS price_integer FROM products;

-- CONVERT() - 数据类型转换（SQL Server）
SELECT CONVERT(INT, '123') AS converted_int;
SELECT CONVERT(DECIMAL(10,2), price_string) AS converted_price FROM temp_data;

-- 数值验证
SELECT 
    CASE 
        WHEN price REGEXP '^[0-9]+\.?[0-9]*$' 
        THEN CAST(price AS DECIMAL(10,2))
        ELSE 0
    END AS safe_price
FROM imported_data;
```

### 进制转换

```sql
-- BIN() - 转换为二进制
SELECT BIN(10) AS binary_value;                     -- 返回: 1010

-- OCT() - 转换为八进制
SELECT OCT(8) AS octal_value;                       -- 返回: 10

-- HEX() - 转换为十六进制
SELECT HEX(255) AS hex_value;                       -- 返回: FF

-- CONV() - 进制转换
SELECT CONV('A', 16, 10) AS hex_to_decimal;         -- 返回: 10
SELECT CONV(10, 10, 2) AS decimal_to_binary;        -- 返回: 1010
```

## 条件数值函数

### 条件计算

```sql
-- IF() / IIF() - 条件函数
SELECT IF(score >= 60, score, 0) AS passed_score FROM test_results;
SELECT salary, IF(salary > 50000, 'High', 'Normal') AS salary_level FROM employees;

-- CASE WHEN - 多条件判断
SELECT 
    salary,
    CASE 
        WHEN salary >= 100000 THEN '高级'
        WHEN salary >= 50000 THEN '中级'
        WHEN salary >= 30000 THEN '初级'
        ELSE '入门'
    END AS salary_grade
FROM employees;

-- NULLIF() - NULL值处理
SELECT NULLIF(division_result, 0) AS safe_division FROM calculations;

-- COALESCE() - 返回第一个非NULL值
SELECT COALESCE(bonus, 0) AS actual_bonus FROM employees;
SELECT COALESCE(score1, score2, score3, 0) AS final_score FROM test_results;
```

### 数值比较函数

```sql
-- GREATEST() - 返回最大值
SELECT GREATEST(score1, score2, score3) AS highest_score FROM test_results;
SELECT GREATEST(salary, bonus) AS max_income FROM employees;

-- LEAST() - 返回最小值
SELECT LEAST(price1, price2, price3) AS lowest_price FROM price_comparison;
SELECT LEAST(budget, actual_cost) AS final_amount FROM projects;
```

## 窗口函数中的数值计算

### 排名函数

```sql
-- ROW_NUMBER() - 行号
SELECT name, salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;

-- RANK() - 排名（有并列）
SELECT name, score,
    RANK() OVER (ORDER BY score DESC) AS rank
FROM test_results;

-- DENSE_RANK() - 密集排名
SELECT department, salary,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM employees;

-- NTILE() - 分组排名
SELECT name, salary,
    NTILE(4) OVER (ORDER BY salary) AS salary_quartile
FROM employees;
```

### 累计计算

```sql
-- 累计求和
SELECT date, sales,
    SUM(sales) OVER (ORDER BY date) AS cumulative_sales
FROM daily_sales;

-- 移动平均
SELECT date, price,
    AVG(price) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS moving_avg_7
FROM stock_prices;

-- 累计计数
SELECT customer_id, order_date,
    COUNT(*) OVER (PARTITION BY customer_id ORDER BY order_date) AS order_sequence
FROM orders;
```

### 百分位数函数

```sql
-- PERCENT_RANK() - 百分位排名
SELECT name, salary,
    PERCENT_RANK() OVER (ORDER BY salary) AS percentile_rank
FROM employees;

-- CUME_DIST() - 累积分布
SELECT score,
    CUME_DIST() OVER (ORDER BY score) AS cumulative_distribution
FROM test_results;

-- PERCENTILE_CONT() - 连续百分位数
SELECT department,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary
FROM employees
GROUP BY department;
```

## 实际应用示例

### 财务计算

```sql
-- 计算复利
SELECT 
    principal,
    rate,
    years,
    principal * POWER(1 + rate/100, years) AS compound_amount,
    principal * POWER(1 + rate/100, years) - principal AS compound_interest
FROM investments;

-- 计算月供（等额本息）
SELECT 
    loan_amount,
    annual_rate,
    loan_years,
    loan_amount * (annual_rate/1200) * POWER(1 + annual_rate/1200, loan_years * 12) /
    (POWER(1 + annual_rate/1200, loan_years * 12) - 1) AS monthly_payment
FROM loans;

-- 投资回报率计算
SELECT 
    investment_id,
    initial_amount,
    final_amount,
    ROUND((final_amount - initial_amount) / initial_amount * 100, 2) AS roi_percentage
FROM investment_returns;
```

### 统计分析

```sql
-- 计算标准分数（Z-score）
SELECT 
    student_id,
    score,
    (score - AVG(score) OVER()) / STDDEV(score) OVER() AS z_score
FROM test_results;

-- 计算变异系数
SELECT 
    product_category,
    STDDEV(price) / AVG(price) * 100 AS coefficient_of_variation
FROM products
GROUP BY product_category;

-- 计算相关系数（简化版）
SELECT 
    (COUNT(*) * SUM(x * y) - SUM(x) * SUM(y)) /
    SQRT((COUNT(*) * SUM(x * x) - SUM(x) * SUM(x)) *
         (COUNT(*) * SUM(y * y) - SUM(y) * SUM(y))) AS correlation
FROM data_points;
```

### 业务指标计算

```sql
-- 同比增长率
SELECT 
    year,
    revenue,
    LAG(revenue) OVER (ORDER BY year) AS prev_year_revenue,
    ROUND((revenue - LAG(revenue) OVER (ORDER BY year)) / 
          LAG(revenue) OVER (ORDER BY year) * 100, 2) AS yoy_growth_rate
FROM annual_revenue;

-- 客户生命周期价值
SELECT 
    customer_id,
    AVG(order_amount) AS avg_order_value,
    COUNT(*) AS order_frequency,
    DATEDIFF(MAX(order_date), MIN(order_date)) / 365 AS customer_lifespan_years,
    AVG(order_amount) * COUNT(*) / (DATEDIFF(MAX(order_date), MIN(order_date)) / 365) AS clv
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 1;

-- 库存周转率
SELECT 
    product_id,
    SUM(quantity_sold) AS total_sold,
    AVG(inventory_level) AS avg_inventory,
    SUM(quantity_sold) / AVG(inventory_level) AS inventory_turnover
FROM inventory_data
GROUP BY product_id;
```

### 数据质量检查

```sql
-- 异常值检测（基于四分位数）
WITH quartiles AS (
    SELECT 
        PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY value) AS q1,
        PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY value) AS q3
    FROM measurements
)
SELECT 
    id,
    value,
    CASE 
        WHEN value < (q1 - 1.5 * (q3 - q1)) OR value > (q3 + 1.5 * (q3 - q1))
        THEN '异常值'
        ELSE '正常'
    END AS outlier_status
FROM measurements, quartiles;

-- 数据分布检查
SELECT 
    FLOOR(value / 10) * 10 AS value_range,
    COUNT(*) AS frequency,
    COUNT(*) * 100.0 / SUM(COUNT(*)) OVER() AS percentage
FROM measurements
GROUP BY FLOOR(value / 10)
ORDER BY value_range;
```

## 性能优化建议

### 索引优化

```sql
-- 对经常用于数值计算的列创建索引
CREATE INDEX idx_salary ON employees(salary);
CREATE INDEX idx_order_amount ON orders(order_amount);

-- 函数索引（部分数据库支持）
CREATE INDEX idx_abs_profit ON sales(ABS(profit));
```

### 计算优化

```sql
-- 避免重复计算
-- 不推荐
SELECT 
    product_id,
    price * quantity AS total,
    (price * quantity) * 0.1 AS tax
FROM order_items;

-- 推荐
SELECT 
    product_id,
    price * quantity AS total,
    (price * quantity) * 0.1 AS tax
FROM order_items;

-- 使用子查询优化复杂计算
WITH totals AS (
    SELECT 
        product_id,
        price * quantity AS line_total
    FROM order_items
)
SELECT 
    product_id,
    line_total,
    line_total * 0.1 AS tax
FROM totals;
```

## 数据库差异说明

### MySQL 特有函数

```sql
-- DEGREES() - 弧度转角度
SELECT DEGREES(PI()/2) AS degrees_90;              -- 返回: 90

-- RADIANS() - 角度转弧度
SELECT RADIANS(90) AS radians_90;                  -- 返回: 1.5708

-- FORMAT() - 数字格式化
SELECT FORMAT(1234567.89, 2) AS formatted;         -- 返回: 1,234,567.89
```

### SQL Server 特有函数

```sql
-- SQUARE() - 平方
SELECT SQUARE(4) AS squared;                        -- 返回: 16

-- RAND() with seed
SELECT RAND(100) AS seeded_random;

-- ROUND() with function
SELECT ROUND(123.456, 2, 1) AS truncated_round;    -- 截断而非四舍五入
```

### PostgreSQL 特有函数

```sql
-- RANDOM() - 随机数
SELECT RANDOM() AS random_value;                    -- 返回: 0.123456

-- WIDTH_BUCKET() - 分桶函数
SELECT WIDTH_BUCKET(salary, 30000, 100000, 5) AS salary_bucket
FROM employees;

-- GENERATE_SERIES() - 生成数列
SELECT * FROM GENERATE_SERIES(1, 10, 2);           -- 返回: 1,3,5,7,9
```

## 注意事项

- 浮点数计算可能存在精度问题，财务计算建议使用DECIMAL类型
- 除零操作会产生错误，使用前要检查分母是否为零
- NULL值参与数值运算结果为NULL，注意处理
- 大数值计算可能导致溢出，选择合适的数据类型
- 三角函数通常使用弧度制，角度需要转换
- 聚合函数会忽略NULL值，COUNT(*)除外

# 事务的四大特性

## 什么是事务

事务（Transaction）是数据库管理系统中一个重要的概念，它是指作为单个逻辑工作单元执行的一系列操作。事务可以由一条或多条SQL语句组成，这些操作要么全部成功执行，要么全部回滚到初始状态。

## 事务的四大特性（ACID）

事务具有四个基本特性，通常称为ACID特性：

### 1. 原子性（Atomicity）

**定义：** 事务是一个不可分割的工作单位，事务中的操作要么全部完成，要么全部不做。

**特点：**
- 事务中的所有操作被视为一个整体
- 如果事务中任何一个操作失败，整个事务都会被回滚
- 不存在部分完成的事务

**示例：**
```sql
-- 银行转账事务
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 1000 WHERE account_id = 'A001';
UPDATE accounts SET balance = balance + 1000 WHERE account_id = 'B001';

-- 如果任一操作失败，整个事务回滚
COMMIT;
```

### 2. 一致性（Consistency）

**定义：** 事务执行前后，数据库必须处于一致性状态，数据库的完整性约束没有被破坏。

**特点：**
- 事务开始时和结束时，数据库都必须保持一致状态
- 事务执行过程中可能暂时违反一致性，但最终必须恢复
- 包括实体完整性、参照完整性、用户定义完整性等

**示例：**
```sql
-- 确保转账后总金额不变
BEGIN TRANSACTION;

DECLARE @total_before DECIMAL(10,2);
DECLARE @total_after DECIMAL(10,2);

-- 计算转账前总金额
SELECT @total_before = SUM(balance) FROM accounts WHERE account_id IN ('A001', 'B001');

-- 执行转账
UPDATE accounts SET balance = balance - 1000 WHERE account_id = 'A001';
UPDATE accounts SET balance = balance + 1000 WHERE account_id = 'B001';

-- 计算转账后总金额
SELECT @total_after = SUM(balance) FROM accounts WHERE account_id IN ('A001', 'B001');

-- 检查一致性
IF @total_before = @total_after
    COMMIT;
ELSE
    ROLLBACK;
```

### 3. 隔离性（Isolation）

**定义：** 多个事务并发执行时，一个事务的执行不应影响其他事务的执行，每个事务都感觉不到系统中有其他事务在并发执行。

**隔离级别：**

#### 读未提交（Read Uncommitted）
```sql
-- 设置隔离级别
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
-- 可能出现脏读、不可重复读、幻读
```

#### 读已提交（Read Committed）
```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
-- 避免脏读，但可能出现不可重复读、幻读
```

#### 可重复读（Repeatable Read）
```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
-- 避免脏读、不可重复读，但可能出现幻读
```

#### 串行化（Serializable）
```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- 避免所有并发问题，但性能最低
```

**并发问题示例：**
```sql
-- 脏读示例
-- 事务A
BEGIN TRANSACTION;
UPDATE products SET price = 100 WHERE id = 1;
-- 此时事务B读取到price=100（脏数据）
ROLLBACK; -- 事务A回滚，price实际还是原值

-- 不可重复读示例
-- 事务A
BEGIN TRANSACTION;
SELECT price FROM products WHERE id = 1; -- 第一次读取：价格为50
-- 此时事务B修改了价格并提交
SELECT price FROM products WHERE id = 1; -- 第二次读取：价格为100
COMMIT;
```

### 4. 持久性（Durability）

**定义：** 一旦事务提交，其所做的修改就会永久保存到数据库中，即使系统发生崩溃，事务的结果也不会丢失。

**特点：**
- 已提交的事务对数据库的修改是永久性的
- 系统崩溃后重启，数据仍然存在
- 通过日志文件和备份机制保证持久性

**实现机制：**
```sql
-- 事务提交后数据持久化
BEGIN TRANSACTION;

INSERT INTO orders (customer_id, amount, order_date) 
VALUES (12345, 299.99, '2024-03-15');

-- 提交后，即使系统崩溃，这条记录也会被保留
COMMIT;
```

## 事务控制语句

### 开始事务
```sql
-- 显式开始事务
BEGIN TRANSACTION;
-- 或
START TRANSACTION;
-- 或
BEGIN;
```

### 提交事务
```sql
-- 提交事务，使所有修改永久生效
COMMIT;
-- 或
COMMIT TRANSACTION;
```

### 回滚事务
```sql
-- 回滚事务，撤销所有修改
ROLLBACK;
-- 或
ROLLBACK TRANSACTION;
```

### 保存点
```sql
-- 创建保存点
BEGIN TRANSACTION;

INSERT INTO table1 VALUES (1, 'data1');
SAVEPOINT sp1;

INSERT INTO table2 VALUES (2, 'data2');
SAVEPOINT sp2;

-- 回滚到指定保存点
ROLLBACK TO SAVEPOINT sp1;

-- 释放保存点
RELEASE SAVEPOINT sp1;

COMMIT;
```

## 实际应用示例

### 电商订单处理
```sql
BEGIN TRANSACTION;

-- 1. 创建订单
INSERT INTO orders (customer_id, total_amount, status) 
VALUES (12345, 299.99, 'pending');

SET @order_id = LAST_INSERT_ID();

-- 2. 添加订单明细
INSERT INTO order_items (order_id, product_id, quantity, price)
VALUES (@order_id, 101, 2, 149.99);

-- 3. 更新库存
UPDATE products 
SET stock_quantity = stock_quantity - 2 
WHERE product_id = 101;

-- 4. 检查库存是否足够
IF (SELECT stock_quantity FROM products WHERE product_id = 101) < 0
BEGIN
    ROLLBACK;
    RAISERROR('库存不足', 16, 1);
END
ELSE
BEGIN
    -- 5. 更新订单状态
    UPDATE orders SET status = 'confirmed' WHERE order_id = @order_id;
    COMMIT;
END
```

### 银行转账系统
```sql
BEGIN TRANSACTION;

DECLARE @from_balance DECIMAL(10,2);
DECLARE @to_account_exists INT;

-- 检查转出账户余额
SELECT @from_balance = balance 
FROM accounts 
WHERE account_id = @from_account;

-- 检查转入账户是否存在
SELECT @to_account_exists = COUNT(*) 
FROM accounts 
WHERE account_id = @to_account;

-- 验证转账条件
IF @from_balance < @transfer_amount
BEGIN
    ROLLBACK;
    RAISERROR('余额不足', 16, 1);
    RETURN;
END

IF @to_account_exists = 0
BEGIN
    ROLLBACK;
    RAISERROR('目标账户不存在', 16, 1);
    RETURN;
END

-- 执行转账
UPDATE accounts 
SET balance = balance - @transfer_amount 
WHERE account_id = @from_account;

UPDATE accounts 
SET balance = balance + @transfer_amount 
WHERE account_id = @to_account;

-- 记录转账日志
INSERT INTO transfer_log (from_account, to_account, amount, transfer_time)
VALUES (@from_account, @to_account, @transfer_amount, GETDATE());

COMMIT;
```

## 事务的最佳实践

### 1. 保持事务简短
```sql
-- 推荐：简短的事务
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- 不推荐：长时间运行的事务
BEGIN TRANSACTION;
-- 复杂的数据处理...
-- 大量的数据操作...
-- 可能导致锁等待和死锁
COMMIT;
```

### 2. 合理使用隔离级别
```sql
-- 根据业务需求选择合适的隔离级别
-- 对于读多写少的报表查询
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- 对于金融交易等高一致性要求
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

### 3. 异常处理
```sql
BEGIN TRY
    BEGIN TRANSACTION;
    
    -- 业务逻辑
    UPDATE table1 SET column1 = value1;
    INSERT INTO table2 VALUES (value2);
    
    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
        ROLLBACK TRANSACTION;
    
    -- 错误处理
    THROW;
END CATCH;
```

### 4. 避免死锁
```sql
-- 始终以相同的顺序访问资源
-- 事务A和B都按照相同顺序访问表
BEGIN TRANSACTION;
UPDATE table1 SET column1 = value1 WHERE id = 1;
UPDATE table2 SET column2 = value2 WHERE id = 2;
COMMIT;
```

## 事务监控和诊断

### 查看当前事务状态
```sql
-- MySQL
SELECT * FROM information_schema.innodb_trx;

-- SQL Server
SELECT * FROM sys.dm_tran_active_transactions;

-- PostgreSQL
SELECT * FROM pg_stat_activity WHERE state = 'active';
```

### 查看锁信息
```sql
-- MySQL
SELECT * FROM performance_schema.data_locks;

-- SQL Server
SELECT * FROM sys.dm_tran_locks;

-- 查看阻塞信息
SELECT blocking_session_id, wait_type, wait_time_ms
FROM sys.dm_exec_requests
WHERE blocking_session_id <> 0;
```

### 死锁检测
```sql
-- SQL Server 死锁图
SELECT * FROM sys.dm_xe_session_events 
WHERE object_name = 'xml_deadlock_report';

-- MySQL 死锁信息
SHOW ENGINE INNODB STATUS;
```

## 注意事项

- **自动提交：** 大多数数据库默认开启自动提交模式，每条SQL语句自动构成一个事务
- **嵌套事务：** 某些数据库不支持真正的嵌套事务，需要使用保存点
- **长事务：** 避免长时间运行的事务，会增加锁争用和死锁风险
- **资源消耗：** 事务会消耗数据库资源，包括内存、锁、日志空间等
- **备份恢复：** 事务日志是数据库备份和恢复的重要依据
- **性能影响：** 严格的隔离级别会影响并发性能，需要在一致性和性能间平衡

# SQL 存储引擎详细介绍

## 什么是存储引擎

存储引擎是数据库管理系统（DBMS）的核心组件，负责数据的存储、检索和管理。它决定了数据在磁盘上的物理存储方式、索引结构、锁机制、事务处理等关键特性。不同的存储引擎有不同的特点和适用场景。

## 主要存储引擎类型

### MySQL 存储引擎

#### InnoDB（默认引擎）

**特点：**
- 支持事务（ACID特性）
- 支持外键约束
- 支持行级锁
- 支持崩溃恢复
- 支持多版本并发控制（MVCC）

**适用场景：**
```sql
-- 创建InnoDB表
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

-- 适用于：
-- 1. 需要事务支持的应用
-- 2. 高并发读写场景
-- 3. 数据一致性要求高的场景
-- 4. 需要外键约束的应用
```

**配置示例：**
```sql
-- 查看InnoDB状态
SHOW ENGINE INNODB STATUS;

-- 设置InnoDB缓冲池大小
SET GLOBAL innodb_buffer_pool_size = 1073741824; -- 1GB

-- 设置日志文件大小
SET GLOBAL innodb_log_file_size = 268435456; -- 256MB
```

#### MyISAM

**特点：**
- 不支持事务
- 不支持外键
- 支持表级锁
- 读取速度快
- 占用空间小

**适用场景：**
```sql
-- 创建MyISAM表
CREATE TABLE logs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    log_level VARCHAR(10),
    message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=MyISAM;

-- 适用于：
-- 1. 读多写少的应用
-- 2. 日志记录系统
-- 3. 数据仓库和报表系统
-- 4. 不需要事务的简单应用
```

#### Memory（HEAP）

**特点：**
- 数据存储在内存中
- 访问速度极快
- 服务器重启后数据丢失
- 支持哈希和B-tree索引

**适用场景：**
```sql
-- 创建Memory表
CREATE TABLE temp_cache (
    id INT PRIMARY KEY,
    data VARCHAR(255),
    INDEX hash_idx(id) USING HASH
) ENGINE=MEMORY;

-- 适用于：
-- 1. 临时数据存储
-- 2. 缓存表
-- 3. 会话数据
-- 4. 计算中间结果
```

#### Archive

**特点：**
- 高压缩比
- 只支持INSERT和SELECT
- 适合归档数据

**示例：**
```sql
CREATE TABLE archive_logs (
    id INT AUTO_INCREMENT,
    log_data TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    KEY(id)
) ENGINE=Archive;
```

### PostgreSQL 存储引擎

#### PostgreSQL 默认存储引擎

**特点：**
- 支持完整的ACID事务
- 支持复杂数据类型
- 支持继承和多态
- 支持并行查询

**示例：**
```sql
-- PostgreSQL表空间管理
CREATE TABLESPACE fast_storage LOCATION '/fast/disk/postgresql';

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_id INT,
    order_data JSONB,
    created_at TIMESTAMP DEFAULT NOW()
) TABLESPACE fast_storage;

-- 分区表
CREATE TABLE sales_data (
    id SERIAL,
    sale_date DATE,
    amount DECIMAL(10,2)
) PARTITION BY RANGE (sale_date);
```

### SQL Server 存储引擎

#### SQL Server 存储结构

**特点：**
- 页面存储（8KB页面）
- 支持行存储和列存储
- 支持内存优化表
- 支持文件组管理

**示例：**
```sql
-- 创建文件组
ALTER DATABASE MyDB ADD FILEGROUP fast_data;
ALTER DATABASE MyDB ADD FILE 
(NAME = 'fast_data_file', FILENAME = 'C:\data\fast_data.ndf') 
TO FILEGROUP fast_data;

-- 在特定文件组创建表
CREATE TABLE high_performance_table (
    id INT PRIMARY KEY,
    data NVARCHAR(255)
) ON fast_data;

-- 内存优化表
CREATE TABLE memory_optimized_table (
    id INT PRIMARY KEY NONCLUSTERED,
    data NVARCHAR(255)
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA);
```

### Oracle 存储引擎

#### Oracle 存储结构

**特点：**
- 表空间管理
- 段、区、块层次结构
- 支持聚簇表
- 支持索引组织表

**示例：**
```sql
-- 创建表空间
CREATE TABLESPACE users_data
DATAFILE '/oracle/data/users_data.dbf' SIZE 100M
AUTOEXTEND ON NEXT 10M;

-- 在指定表空间创建表
CREATE TABLE customers (
    customer_id NUMBER PRIMARY KEY,
    customer_name VARCHAR2(100),
    email VARCHAR2(255)
) TABLESPACE users_data;

-- 索引组织表
CREATE TABLE iot_table (
    id NUMBER PRIMARY KEY,
    data VARCHAR2(255)
) ORGANIZATION INDEX;
```

## 存储引擎比较

### 功能特性对比

| 特性 | InnoDB | MyISAM | Memory | Archive |
|------|--------|--------|---------|---------|
| 事务支持 | ✓ | ✗ | ✗ | ✗ |
| 外键约束 | ✓ | ✗ | ✗ | ✗ |
| 锁级别 | 行级锁 | 表级锁 | 表级锁 | 行级锁 |
| 崩溃恢复 | ✓ | ✗ | ✗ | ✓ |
| 压缩 | ✓ | ✓ | ✗ | ✓ |
| 全文索引 | ✓ | ✓ | ✗ | ✗ |

### 性能对比测试

```sql
-- 创建测试表
CREATE TABLE innodb_test (
    id INT PRIMARY KEY AUTO_INCREMENT,
    data VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

CREATE TABLE myisam_test (
    id INT PRIMARY KEY AUTO_INCREMENT,
    data VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=MyISAM;

-- 插入性能测试
INSERT INTO innodb_test (data) 
SELECT CONCAT('test_data_', n) 
FROM (SELECT @row := @row + 1 AS n FROM 
     (SELECT 0 UNION SELECT 1 UNION SELECT 2 UNION SELECT 3) t1,
     (SELECT 0 UNION SELECT 1 UNION SELECT 2 UNION SELECT 3) t2,
     (SELECT @row := 0) r) numbers
LIMIT 10000;

-- 查询性能测试
SELECT COUNT(*) FROM innodb_test WHERE data LIKE 'test_data_1%';
SELECT COUNT(*) FROM myisam_test WHERE data LIKE 'test_data_1%';
```

## 存储引擎选择指南

### 业务场景分析

#### 1. 高并发OLTP系统
```sql
-- 推荐：InnoDB
CREATE TABLE user_sessions (
    session_id VARCHAR(64) PRIMARY KEY,
    user_id INT NOT NULL,
    login_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_activity TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_user_id (user_id),
    INDEX idx_last_activity (last_activity)
) ENGINE=InnoDB;

-- 特点：
-- 1. 支持事务保证数据一致性
-- 2. 行级锁提高并发性能
-- 3. MVCC减少读写冲突
```

#### 2. 数据仓库和报表系统
```sql
-- 推荐：MyISAM 或 Archive
CREATE TABLE sales_history (
    sale_id BIGINT PRIMARY KEY,
    product_id INT,
    customer_id INT,
    sale_amount DECIMAL(10,2),
    sale_date DATE,
    INDEX idx_date (sale_date),
    INDEX idx_product (product_id)
) ENGINE=MyISAM;

-- 特点：
-- 1. 读取性能优秀
-- 2. 占用空间小
-- 3. 适合批量数据导入
```

#### 3. 缓存和临时数据
```sql
-- 推荐：Memory
CREATE TABLE session_cache (
    session_key VARCHAR(64) PRIMARY KEY,
    session_data TEXT,
    expires_at TIMESTAMP,
    INDEX idx_expires (expires_at)
) ENGINE=Memory;

-- 特点：
-- 1. 访问速度极快
-- 2. 适合临时数据
-- 3. 重启后数据自动清理
```

#### 4. 日志和归档系统
```sql
-- 推荐：Archive
CREATE TABLE application_logs (
    log_id BIGINT AUTO_INCREMENT,
    log_level ENUM('ERROR', 'WARN', 'INFO', 'DEBUG'),
    message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    KEY(log_id)
) ENGINE=Archive;

-- 特点：
-- 1. 极高的压缩比
-- 2. 适合只写不更新的场景
-- 3. 长期数据保存
```

## 存储引擎优化

### InnoDB 优化

#### 缓冲池优化
```sql
-- 查看缓冲池状态
SELECT 
    pool_id,
    pool_size,
    free_buffers,
    database_pages,
    old_database_pages
FROM information_schema.innodb_buffer_pool_stats;

-- 优化缓冲池大小（设置为可用内存的70-80%）
SET GLOBAL innodb_buffer_pool_size = 2147483648; -- 2GB

-- 设置缓冲池实例数
SET GLOBAL innodb_buffer_pool_instances = 8;
```

#### 日志优化
```sql
-- 优化重做日志
SET GLOBAL innodb_log_file_size = 536870912; -- 512MB
SET GLOBAL innodb_log_buffer_size = 16777216; -- 16MB

-- 刷新策略
SET GLOBAL innodb_flush_log_at_trx_commit = 1; -- 最安全
-- SET GLOBAL innodb_flush_log_at_trx_commit = 2; -- 性能优先
```

#### 并发优化
```sql
-- 设置并发线程数
SET GLOBAL innodb_thread_concurrency = 0; -- 不限制

-- 设置IO容量
SET GLOBAL innodb_io_capacity = 200;
SET GLOBAL innodb_io_capacity_max = 2000;

-- 自适应哈希索引
SET GLOBAL innodb_adaptive_hash_index = ON;
```

### MyISAM 优化

#### 键缓存优化
```sql
-- 设置键缓存大小
SET GLOBAL key_buffer_size = 268435456; -- 256MB

-- 查看键缓存使用情况
SHOW STATUS LIKE 'Key_%';

-- 预加载索引
CACHE INDEX table_name IN key_cache_name;
LOAD INDEX INTO CACHE table_name;
```

#### 表维护
```sql
-- 检查表
CHECK TABLE myisam_table;

-- 修复表
REPAIR TABLE myisam_table;

-- 优化表
OPTIMIZE TABLE myisam_table;

-- 分析表
ANALYZE TABLE myisam_table;
```

### Memory 引擎优化

```sql
-- 设置最大堆表大小
SET GLOBAL max_heap_table_size = 134217728; -- 128MB
SET SESSION tmp_table_size = 134217728; -- 128MB

-- 选择合适的索引类型
CREATE TABLE memory_table (
    id INT PRIMARY KEY,
    hash_key VARCHAR(32),
    btree_key INT,
    data TEXT,
    INDEX hash_idx (hash_key) USING HASH,
    INDEX btree_idx (btree_key) USING BTREE
) ENGINE=Memory;
```

## 存储引擎监控

### 性能监控

#### InnoDB 监控
```sql
-- 查看InnoDB状态
SHOW ENGINE INNODB STATUS;

-- 查看缓冲池命中率
SELECT 
    (1 - (innodb_buffer_pool_reads / innodb_buffer_pool_read_requests)) * 100 
    AS buffer_pool_hit_rate
FROM (
    SELECT 
        variable_value AS innodb_buffer_pool_reads 
    FROM performance_schema.global_status 
    WHERE variable_name = 'Innodb_buffer_pool_reads'
) reads,
(
    SELECT 
        variable_value AS innodb_buffer_pool_read_requests 
    FROM performance_schema.global_status 
    WHERE variable_name = 'Innodb_buffer_pool_read_requests'
) requests;

-- 查看锁等待
SELECT * FROM performance_schema.data_lock_waits;
```

#### 通用监控
```sql
-- 查看表的存储引擎
SELECT 
    table_schema,
    table_name,
    engine,
    table_rows,
    data_length,
    index_length
FROM information_schema.tables
WHERE table_schema = 'your_database';

-- 查看存储引擎使用情况
SELECT 
    engine,
    COUNT(*) AS table_count,
    SUM(data_length + index_length) AS total_size
FROM information_schema.tables
WHERE table_schema NOT IN ('information_schema', 'mysql', 'performance_schema', 'sys')
GROUP BY engine;
```

## 存储引擎转换

### 更改表的存储引擎

```sql
-- 方法1：ALTER TABLE（可能锁表时间长）
ALTER TABLE table_name ENGINE = InnoDB;

-- 方法2：CREATE和RENAME（推荐）
CREATE TABLE table_name_new LIKE table_name;
ALTER TABLE table_name_new ENGINE = InnoDB;
INSERT INTO table_name_new SELECT * FROM table_name;
RENAME TABLE table_name TO table_name_old, table_name_new TO table_name;
DROP TABLE table_name_old;

-- 方法3：使用pt-online-schema-change（大表推荐）
-- pt-online-schema-change --alter "ENGINE=InnoDB" D=database,t=table_name --execute
```

### 批量转换
```sql
-- 生成转换语句
SELECT CONCAT('ALTER TABLE ', table_name, ' ENGINE = InnoDB;') AS alter_sql
FROM information_schema.tables
WHERE table_schema = 'your_database' 
AND engine = 'MyISAM';
```

## 最佳实践

### 1. 选择合适的存储引擎

```sql
-- 根据业务特点选择
-- 事务处理：InnoDB
-- 读密集型：MyISAM
-- 缓存数据：Memory
-- 归档数据：Archive
```

### 2. 定期维护

```sql
-- 定期优化表
OPTIMIZE TABLE table_name;

-- 更新表统计信息
ANALYZE TABLE table_name;

-- 检查表完整性
CHECK TABLE table_name;
```

### 3. 监控和调优

```sql
-- 监控慢查询
SET GLOBAL slow_query_log = ON;
SET GLOBAL long_query_time = 2;

-- 监控存储引擎状态
SHOW ENGINE INNODB STATUS\G
SHOW ENGINE PERFORMANCE_SCHEMA STATUS\G
```

### 4. 备份策略

```sql
-- InnoDB热备份
-- mysqldump --single-transaction --routines --triggers

-- MyISAM冷备份
-- FLUSH TABLES WITH READ LOCK;
-- 复制数据文件
-- UNLOCK TABLES;
```

## 注意事项

- **数据一致性**：需要事务支持的应用必须使用InnoDB
- **性能权衡**：不同存储引擎在不同场景下性能差异很大
- **维护成本**：不同存储引擎的维护复杂度不同
- **备份恢复**：不同存储引擎的备份恢复策略不同
- **版本兼容**：某些存储引擎可能在新版本中被废弃
- **混合使用**：同一数据库可以使用多种存储引擎，但要注意事务一致性

# B+树详细介绍

## 什么是B+树

B+树（B+ Tree）是一种多路搜索树，是B树的一种变形。它是数据库索引最常用的数据结构之一，特别适合用于磁盘等外存储器上的文件组织。B+树在B树的基础上进行了优化，所有的数据都存储在叶子节点中，内部节点只存储键值用于导航。

## B+树的基本结构

### 节点类型

```sql
-- B+树节点示例（概念性表示）
-- 内部节点（非叶子节点）
Internal Node: [10 | 20 | 30]
              /    |    |    \
         Child1  Child2 Child3 Child4

-- 叶子节点
Leaf Node: [5,data | 7,data | 8,data] -> [10,data | 12,data | 15,data] -> ...
```

### 核心特征

1. **所有数据存储在叶子节点**
2. **内部节点只存储键值**
3. **叶子节点之间有指针连接**
4. **所有叶子节点在同一层次**

```sql
-- B+树在数据库中的应用示例
CREATE TABLE employees (
    id INT PRIMARY KEY,          -- B+树聚簇索引
    name VARCHAR(50),
    department_id INT,
    salary DECIMAL(10,2),
    
    INDEX idx_department (department_id),  -- B+树辅助索引
    INDEX idx_salary (salary)              -- B+树辅助索引
);
```

## B+树的详细特性

### 阶数定义

```sql
-- m阶B+树的特性：
-- 1. 根节点至少有2个子树（除非它是叶子节点）
-- 2. 每个内部节点最多有m个子树，最少有⌈m/2⌉个子树
-- 3. 每个叶子节点最多有m-1个数据项，最少有⌈(m-1)/2⌉个数据项

-- 假设3阶B+树（通常用于教学，实际应用中阶数更大）
-- 内部节点：最多2个键，最多3个子节点
-- 叶子节点：最多2个数据项
```

### 节点结构详解

#### 内部节点结构
```sql
-- 内部节点只存储键值和子节点指针
Internal Node Structure:
[P0 | K1 | P1 | K2 | P2 | ... | Km-1 | Pm-1]

-- 其中：
-- Pi: 指向子节点的指针
-- Ki: 键值
-- P0指向的子树中所有键值 < K1
-- Pi指向的子树中所有键值在 Ki 和 Ki+1 之间
```

#### 叶子节点结构
```sql
-- 叶子节点存储实际数据
Leaf Node Structure:
[K1,Data1 | K2,Data2 | ... | Km-1,Datam-1 | Next]

-- 其中：
-- Ki,Datai: 键值对
-- Next: 指向下一个叶子节点的指针
```

## B+树操作详解

### 查找操作

#### 单值查找
```sql
-- 在B+树中查找值为15的记录
-- 假设B+树结构如下：
--         [10, 20]
--        /    |    \
--   [5,8]   [12,15]  [25,30]

-- 查找过程：
-- 1. 从根节点开始：15 > 10 且 15 < 20，走中间分支
-- 2. 到达叶子节点[12,15]，找到15对应的数据
SELECT * FROM employees WHERE id = 15;
```

#### 范围查找
```sql
-- B+树特别适合范围查询
-- 查找salary在50000到80000之间的员工
SELECT * FROM employees 
WHERE salary BETWEEN 50000 AND 80000;

-- B+树范围查找优势：
-- 1. 找到起始位置后，可以顺序遍历叶子节点
-- 2. 叶子节点间的链表结构支持高效的顺序访问
```

### 插入操作

#### 正常插入
```sql
-- 插入键值12到B+树中
-- 假设当前叶子节点有空间：[10, 11] -> 插入后变为 [10, 11, 12]

INSERT INTO employees (id, name, salary) VALUES (12, 'John', 55000);
```

#### 分裂插入
```sql
-- 当叶子节点满时需要分裂
-- 原叶子节点：[10, 11, 13]（已满，3阶B+树）
-- 插入12后分裂：
-- 新节点1：[10, 11]
-- 新节点2：[12, 13] 
-- 中间键12上升到父节点

-- 父节点分裂示例：
-- 原父节点：[15, 25]（已满）
-- 插入12后：[12, 15, 25] -> 分裂为：
-- 新父节点1：[12]
-- 新父节点2：[25]
-- 中间键15上升到祖父节点
```

### 删除操作

#### 简单删除
```sql
-- 从非最小的叶子节点删除
-- 原节点：[10, 11, 12, 13] -> 删除11后：[10, 12, 13]

DELETE FROM employees WHERE id = 11;
```

#### 借用元素
```sql
-- 当删除导致节点元素不足时，从兄弟节点借用
-- 左兄弟：[5, 6, 7]
-- 当前节点：[10, 11] -> 删除10后：[11]（不足）
-- 从左兄弟借用：
-- 左兄弟：[5, 6]
-- 当前节点：[7, 11]
-- 父节点键值相应调整
```

#### 合并节点
```sql
-- 当借用不可行时，合并节点
-- 左兄弟：[5]（最小）
-- 当前节点：[11] -> 删除11后：[]（空）
-- 合并后：左兄弟变为[5]，当前节点删除
-- 父节点中相应的键值也要删除
```

## B+树在数据库中的应用

### 聚簇索引（主键索引）

```sql
-- InnoDB中的聚簇索引使用B+树
CREATE TABLE products (
    product_id INT PRIMARY KEY,     -- 聚簇索引
    product_name VARCHAR(100),
    price DECIMAL(10,2),
    category_id INT
);

-- 聚簇索引特点：
-- 1. 叶子节点存储完整的行数据
-- 2. 表数据按主键顺序物理存储
-- 3. 一个表只能有一个聚簇索引
```

### 辅助索引（二级索引）

```sql
-- 为price列创建辅助索引
CREATE INDEX idx_price ON products(price);

-- 辅助索引特点：
-- 1. 叶子节点存储索引列值和主键值
-- 2. 查询时可能需要回表操作
-- 3. 一个表可以有多个辅助索引

-- 回表查询示例
SELECT product_name FROM products WHERE price = 99.99;
-- 1. 通过price索引找到主键product_id
-- 2. 通过主键在聚簇索引中找到完整行数据
-- 3. 返回product_name
```

### 联合索引

```sql
-- 创建多列联合索引
CREATE INDEX idx_category_price ON products(category_id, price);

-- 联合索引的B+树结构：
-- 按第一列排序，第一列相同时按第二列排序
-- 支持左前缀原则

-- 可以使用索引的查询：
SELECT * FROM products WHERE category_id = 1;                    -- ✓
SELECT * FROM products WHERE category_id = 1 AND price > 50;     -- ✓
SELECT * FROM products WHERE category_id = 1 AND price = 99.99;  -- ✓

-- 无法有效使用索引的查询：
SELECT * FROM products WHERE price = 99.99;                      -- ✗
```

## B+树 vs 其他数据结构

### B+树 vs B树

| 特性 | B+树 | B树 |
|------|------|-----|
| 数据存储 | 仅在叶子节点 | 所有节点都可存储 |
| 叶子节点连接 | 有指针连接 | 无连接 |
| 查询性能 | 稳定的O(logn) | 不稳定，可能更快 |
| 范围查询 | 非常高效 | 需要树遍历 |
| 磁盘I/O | 更少的I/O | 可能更多I/O |

```sql
-- B树结构示例（数据分布在所有节点）
--       [15,data]
--      /         \
-- [8,data]    [25,data]
--            /          \
--      [20,data]    [30,data]

-- B+树结构示例（数据只在叶子节点）
--        [15]
--       /    \
--    [8]      [25]
--   /   \    /    \
-- [5,d][8,d][20,d][25,d][30,d]
```

### B+树 vs 哈希索引

```sql
-- 哈希索引特点
CREATE TABLE hash_example (
    id INT,
    data VARCHAR(100),
    KEY hash_idx (id) USING HASH
) ENGINE=Memory;

-- 比较：
-- B+树索引：
-- ✓ 支持范围查询
-- ✓ 支持排序
-- ✓ 支持前缀查询
-- ✗ 等值查询稍慢

-- 哈希索引：
-- ✓ 等值查询极快 O(1)
-- ✗ 不支持范围查询
-- ✗ 不支持排序
-- ✗ 不支持前缀查询
```

### B+树 vs 红黑树

```sql
-- 内存数据库可能使用红黑树等平衡二叉树
-- B+树优势（针对磁盘存储）：
-- 1. 更高的分支因子，减少树的高度
-- 2. 更少的磁盘I/O操作
-- 3. 更好的缓存局部性

-- 红黑树优势（针对内存存储）：
-- 1. 结构简单
-- 2. 插入删除操作稍快
-- 3. 内存使用更少
```

## B+树性能优化

### 阶数选择

```sql
-- InnoDB页大小通常为16KB
-- 假设键值8字节，指针8字节，数据记录100字节
-- 内部节点阶数：16KB ÷ 16字节 ≈ 1000
-- 叶子节点容量：16KB ÷ 108字节 ≈ 150条记录

-- 高阶数B+树的优势：
-- 1. 更矮的树结构
-- 2. 更少的磁盘I/O
-- 3. 更好的缓存利用率
```

### 页面填充因子

```sql
-- InnoDB默认页面填充因子
SET GLOBAL innodb_fill_factor = 90; -- 90%填充

-- 填充因子的影响：
-- 高填充因子：空间利用率高，但分裂频繁
-- 低填充因子：分裂少，但空间浪费

-- 查看页面分裂情况
SHOW GLOBAL STATUS LIKE 'Innodb_page_splits';
```

### 索引维护

```sql
-- 重建索引以减少碎片
ALTER TABLE products FORCE;

-- 或者删除重建
DROP INDEX idx_price ON products;
CREATE INDEX idx_price ON products(price);

-- 统计索引使用情况
SELECT 
    table_schema,
    table_name,
    index_name,
    cardinality
FROM information_schema.statistics
WHERE table_name = 'products';
```

## B+树的实际应用场景

### 数据库查询优化

```sql
-- 利用B+树索引优化查询
-- 1. 等值查询
SELECT * FROM orders WHERE order_id = 12345;

-- 2. 范围查询
SELECT * FROM orders WHERE order_date BETWEEN '2024-01-01' AND '2024-01-31';

-- 3. 排序查询
SELECT * FROM orders ORDER BY order_date LIMIT 10;

-- 4. 分组查询
SELECT customer_id, COUNT(*) 
FROM orders 
GROUP BY customer_id;
```

### 文件系统

```sql
-- B+树在文件系统中的应用
-- 目录结构、文件分配表等都可能使用B+树

-- 模拟文件系统目录结构
CREATE TABLE file_system (
    path_id INT PRIMARY KEY,
    parent_id INT,
    file_name VARCHAR(255),
    file_type ENUM('file', 'directory'),
    file_size BIGINT,
    
    INDEX idx_parent (parent_id),
    INDEX idx_name (file_name)
);
```

### 内存数据库

```sql
-- B+树在内存数据库中的应用
-- Redis的sorted set部分使用跳表，但概念类似

-- 时间序列数据
CREATE TABLE time_series (
    timestamp BIGINT PRIMARY KEY,
    metric_name VARCHAR(50),
    metric_value DOUBLE,
    
    INDEX idx_metric_time (metric_name, timestamp)
);
```

## B+树监控和诊断

### 索引使用分析

```sql
-- 查看索引使用情况
SELECT 
    object_name,
    index_name,
    leaf_insert_count,
    leaf_delete_count,
    leaf_page_split_count,
    nonleaf_insert_count,
    nonleaf_delete_count,
    nonleaf_page_split_count
FROM sys.dm_db_index_operational_stats(DB_ID(), NULL, NULL, NULL);

-- MySQL查看索引统计
SHOW INDEX FROM products;

-- 分析查询执行计划
EXPLAIN SELECT * FROM products WHERE price BETWEEN 50 AND 100;
```

### 性能监控

```sql
-- 监控B+树相关的性能指标
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool%';
SHOW GLOBAL STATUS LIKE 'Innodb_pages%';

-- 查看页面分裂和合并
SHOW GLOBAL STATUS LIKE 'Innodb_page_splits';
SHOW GLOBAL STATUS LIKE 'Innodb_page_merges';

-- 缓冲池命中率
SELECT 
    (innodb_buffer_pool_read_requests - innodb_buffer_pool_reads) / 
    innodb_buffer_pool_read_requests * 100 AS hit_rate
FROM 
    (SELECT variable_value AS innodb_buffer_pool_read_requests 
     FROM performance_schema.global_status 
     WHERE variable_name = 'Innodb_buffer_pool_read_requests') a,
    (SELECT variable_value AS innodb_buffer_pool_reads 
     FROM performance_schema.global_status 
     WHERE variable_name = 'Innodb_buffer_pool_reads') b;
```

## B+树最佳实践

### 索引设计原则

```sql
-- 1. 选择性高的列创建索引
-- 选择性 = 不重复值数量 / 总记录数
SELECT 
    COUNT(DISTINCT column_name) / COUNT(*) AS selectivity
FROM table_name;

-- 2. 复合索引的列顺序
-- 将选择性高的列放在前面
CREATE INDEX idx_status_date ON orders(status, order_date); -- 好
CREATE INDEX idx_date_status ON orders(order_date, status); -- 可能不够优

-- 3. 避免过长的索引
-- 使用前缀索引
CREATE INDEX idx_description ON products(description(50));
```

### 插入优化

```sql
-- 1. 有序插入减少页面分裂
-- 使用自增主键
CREATE TABLE log_entries (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    log_message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. 批量插入
INSERT INTO products (name, price) VALUES
    ('Product 1', 99.99),
    ('Product 2', 149.99),
    ('Product 3', 199.99);

-- 3. 禁用自动提交进行批量操作
SET autocommit = 0;
-- 执行大量插入操作
COMMIT;
SET autocommit = 1;
```

### 查询优化

```sql
-- 1. 利用索引覆盖查询
SELECT product_id, price FROM products WHERE category_id = 1;
-- 如果有索引(category_id, price)，则无需回表

-- 2. 避免函数破坏索引
-- 不好的查询
SELECT * FROM orders WHERE YEAR(order_date) = 2024;

-- 好的查询
SELECT * FROM orders 
WHERE order_date >= '2024-01-01' 
AND order_date < '2025-01-01';

-- 3. 使用LIMIT减少无必要的扫描
SELECT * FROM products ORDER BY price LIMIT 10;
```

## 注意事项和限制

### 常见问题

```sql
-- 1. 索引维护开销
-- 每次INSERT/UPDATE/DELETE都可能导致B+树调整
-- 需要在查询性能和写入性能间平衡

-- 2. 存储空间开销
-- 索引占用额外存储空间
-- 查看表和索引大小
SELECT 
    table_name,
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS total_mb,
    ROUND((data_length / 1024 / 1024), 2) AS data_mb,
    ROUND((index_length / 1024 / 1024), 2) AS index_mb
FROM information_schema.tables
WHERE table_schema = 'your_database';

-- 3. 内存使用
-- B+树节点需要缓存在内存中
-- 设置合适的缓冲池大小
SET GLOBAL innodb_buffer_pool_size = 2147483648; -- 2GB
```

### 设计限制

```sql
-- 1. 索引长度限制
-- InnoDB单列索引最大767字节（或3072字节）
-- 联合索引总长度不超过3072字节

-- 2. 索引数量限制
-- 每个表最多64个二级索引（InnoDB）

-- 3. 页面大小限制
-- 影响B+树的阶数和性能
SHOW VARIABLES LIKE 'innodb_page_size';
```

B+树作为数据库索引的核心数据结构，在理解其原理和特性的基础上，合理设计和使用索引是数据库性能优化的关键。通过监控B+树的使用情况并遵循最佳实践，可以显著提升数据库系统的整体性能。


# InnoDB存储引擎中的索引分类

## 什么是InnoDB索引

InnoDB是MySQL默认的存储引擎，采用B+树数据结构来实现索引。索引是提高数据库查询性能的关键技术，InnoDB支持多种类型的索引，每种索引都有其特定的用途和优化场景。

## 按索引结构分类

### 1. 聚簇索引（Clustered Index）

**定义：** 聚簇索引是InnoDB表中数据行的物理存储顺序与索引顺序相同的索引，也称为主索引。

**特点：**
- 每个InnoDB表有且仅有一个聚簇索引
- 叶子节点包含完整的数据行
- 数据页按照主键顺序物理存储

```sql
-- 创建带主键的表（聚簇索引）
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,  -- 聚簇索引
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

-- 查看索引信息
SHOW INDEX FROM users;

-- 聚簇索引的查询（最优性能）
SELECT * FROM users WHERE user_id = 12345;
```

**聚簇索引的选择规则：**
```sql
-- 1. 如果有PRIMARY KEY，使用主键作为聚簇索引
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_amount DECIMAL(10,2)
);

-- 2. 如果没有主键，选择第一个UNIQUE NOT NULL索引
CREATE TABLE customers (
    email VARCHAR(100) UNIQUE NOT NULL,  -- 作为聚簇索引
    name VARCHAR(50),
    phone VARCHAR(20)
);

-- 3. 如果都没有，InnoDB自动创建6字节的隐藏主键
CREATE TABLE logs (
    message TEXT,
    created_at TIMESTAMP
    -- InnoDB会自动创建隐藏的ROW_ID作为聚簇索引
);
```

### 2. 辅助索引（Secondary Index）

**定义：** 辅助索引是非聚簇索引，叶子节点存储索引键值和对应的主键值。

**特点：**
- 一个表可以有多个辅助索引
- 叶子节点不包含完整数据行，只包含索引列值和主键值
- 查询时可能需要回表操作

```sql
-- 创建辅助索引
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,           -- 聚簇索引
    name VARCHAR(50),
    department VARCHAR(30),
    salary DECIMAL(10,2),
    hire_date DATE,
    
    -- 创建辅助索引
    INDEX idx_name (name),            -- 单列辅助索引
    INDEX idx_dept_salary (department, salary),  -- 复合辅助索引
    INDEX idx_hire_date (hire_date)   -- 日期索引
);

-- 辅助索引查询示例
SELECT * FROM employees WHERE name = 'John Smith';
-- 执行过程：
-- 1. 在idx_name索引中找到'John Smith'对应的主键emp_id
-- 2. 用emp_id在聚簇索引中查找完整行数据（回表）

-- 覆盖索引查询（无需回表）
SELECT emp_id, name FROM employees WHERE name = 'John Smith';
-- 因为emp_id（主键）和name都在idx_name索引中，无需回表
```

## 按索引列数分类

### 1. 单列索引

```sql
-- 创建单列索引
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(10,2),
    category_id INT,
    created_at TIMESTAMP,
    
    -- 单列索引
    INDEX idx_price (price),
    INDEX idx_category (category_id),
    INDEX idx_name (product_name)
);

-- 单列索引的使用
SELECT * FROM products WHERE price = 99.99;
SELECT * FROM products WHERE category_id = 5;
SELECT * FROM products WHERE product_name = 'iPhone 15';
```

### 2. 复合索引（联合索引）

```sql
-- 创建复合索引
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    status VARCHAR(20),
    amount DECIMAL(10,2),
    
    -- 复合索引
    INDEX idx_customer_date (customer_id, order_date),
    INDEX idx_date_status (order_date, status),
    INDEX idx_status_amount (status, amount)
);

-- 左前缀原则示例
-- 可以使用idx_customer_date索引的查询：
SELECT * FROM orders WHERE customer_id = 123;                              -- ✓
SELECT * FROM orders WHERE customer_id = 123 AND order_date = '2024-01-15'; -- ✓
SELECT * FROM orders WHERE customer_id = 123 AND order_date > '2024-01-01'; -- ✓

-- 无法有效使用idx_customer_date索引的查询：
SELECT * FROM orders WHERE order_date = '2024-01-15';                      -- ✗
```

## 按索引用途分类

### 1. 普通索引

```sql
-- 普通索引（无唯一性约束）
CREATE TABLE articles (
    id INT PRIMARY KEY,
    title VARCHAR(200),
    content TEXT,
    author_id INT,
    category VARCHAR(50),
    published_at TIMESTAMP,
    
    -- 普通索引
    INDEX idx_author (author_id),
    INDEX idx_category (category),
    INDEX idx_published (published_at)
);

-- 普通索引查询
SELECT * FROM articles WHERE author_id = 100;
SELECT * FROM articles WHERE category = 'Technology';
```

### 2. 唯一索引

```sql
-- 唯一索引确保列值的唯一性
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    phone VARCHAR(20),
    
    -- 唯一索引
    UNIQUE INDEX uk_username (username),
    UNIQUE INDEX uk_email (email),
    UNIQUE INDEX uk_phone (phone)
);

-- 唯一索引的约束
INSERT INTO users VALUES (1, 'john', 'john@email.com', '1234567890');
-- 以下插入会失败，违反唯一约束
-- INSERT INTO users VALUES (2, 'john', 'jane@email.com', '0987654321');

-- 复合唯一索引
CREATE TABLE user_roles (
    user_id INT,
    role_id INT,
    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE INDEX uk_user_role (user_id, role_id)
);
```

### 3. 主键索引

```sql
-- 主键索引（特殊的唯一索引）
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,  -- 主键索引
    customer_code VARCHAR(20) UNIQUE,            -- 唯一索引
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100)
);

-- 复合主键
CREATE TABLE order_items (
    order_id INT,
    item_id INT,
    quantity INT,
    price DECIMAL(10,2),
    
    PRIMARY KEY (order_id, item_id)  -- 复合主键索引
);
```

## 按存储方式分类

### 1. B+树索引（默认）

```sql
-- InnoDB默认使用B+树索引
CREATE TABLE performance_test (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    score INT,
    
    INDEX idx_name (name),        -- B+树索引
    INDEX idx_score (score)       -- B+树索引
);

-- B+树索引特点：
-- 1. 支持范围查询
SELECT * FROM performance_test WHERE score BETWEEN 80 AND 95;

-- 2. 支持排序
SELECT * FROM performance_test ORDER BY score DESC LIMIT 10;

-- 3. 支持前缀匹配
SELECT * FROM performance_test WHERE name LIKE 'John%';
```

### 2. 哈希索引

```sql
-- InnoDB在特定条件下会使用自适应哈希索引
-- 这是自动的，不能手动创建

-- 查看自适应哈希索引状态
SHOW ENGINE INNODB STATUS;

-- 在status信息中查找"ADAPTIVE HASH INDEX"部分
-- 或使用以下查询
SELECT * FROM information_schema.innodb_metrics 
WHERE name LIKE '%adaptive_hash%';

-- 自适应哈希索引特点：
-- 1. 自动创建和管理
-- 2. 只对频繁访问的页面创建
-- 3. 等值查询性能极佳
-- 4. 不支持范围查询
```

## 按数据类型分类

### 1. 全文索引

```sql
-- 全文索引用于文本搜索
CREATE TABLE documents (
    doc_id INT PRIMARY KEY,
    title VARCHAR(200),
    content TEXT,
    
    -- 全文索引（MySQL 5.6+支持InnoDB全文索引）
    FULLTEXT INDEX ft_title (title),
    FULLTEXT INDEX ft_content (content),
    FULLTEXT INDEX ft_title_content (title, content)
);

-- 全文索引查询
SELECT * FROM documents 
WHERE MATCH(title) AGAINST('数据库 MySQL' IN NATURAL LANGUAGE MODE);

SELECT * FROM documents 
WHERE MATCH(content) AGAINST('+InnoDB +索引 -MyISAM' IN BOOLEAN MODE);

-- 查看全文索引配置
SHOW VARIABLES LIKE '%ft_%';
```

### 2. 前缀索引

```sql
-- 对长字符串创建前缀索引
CREATE TABLE articles (
    id INT PRIMARY KEY,
    title VARCHAR(500),
    url VARCHAR(1000),
    description TEXT,
    
    -- 前缀索引
    INDEX idx_title_prefix (title(50)),     -- 只索引前50个字符
    INDEX idx_url_prefix (url(100))         -- 只索引前100个字符
);

-- 选择合适的前缀长度
SELECT 
    COUNT(DISTINCT LEFT(title, 10)) / COUNT(*) AS sel_10,
    COUNT(DISTINCT LEFT(title, 20)) / COUNT(*) AS sel_20,
    COUNT(DISTINCT LEFT(title, 30)) / COUNT(*) AS sel_30,
    COUNT(DISTINCT LEFT(title, 50)) / COUNT(*) AS sel_50
FROM articles;

-- 前缀索引的限制
-- 1. 不能用于ORDER BY
-- 2. 不能用于覆盖索引
-- 3. 只支持前缀匹配
SELECT * FROM articles WHERE title LIKE 'MySQL索引%';  -- 可以使用
-- SELECT * FROM articles WHERE title LIKE '%索引优化';  -- 无法使用
```

## 特殊索引类型

### 1. 覆盖索引

```sql
-- 覆盖索引：查询的所有列都包含在索引中
CREATE TABLE user_profiles (
    user_id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    age INT,
    city VARCHAR(50),
    
    -- 覆盖索引
    INDEX idx_username_email (username, email),
    INDEX idx_age_city (age, city)
);

-- 覆盖索引查询（无需回表）
SELECT username, email FROM user_profiles WHERE username = 'john';
EXPLAIN SELECT username, email FROM user_profiles WHERE username = 'john';
-- Extra列显示"Using index"表示使用了覆盖索引

-- 非覆盖索引查询（需要回表）
SELECT username, email, city FROM user_profiles WHERE username = 'john';
```

### 2. 多值索引（MySQL 8.0+）

```sql
-- 多值索引用于JSON数组
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    tags JSON,
    
    -- 多值索引
    INDEX idx_tags ((CAST(tags->'$[*]' AS CHAR(50) ARRAY)))
);

-- 插入包含JSON数组的数据
INSERT INTO products VALUES 
(1, 'Laptop', JSON_ARRAY('electronics', 'computer', 'portable')),
(2, 'Phone', JSON_ARRAY('electronics', 'mobile', 'communication'));

-- 使用多值索引查询
SELECT * FROM products WHERE JSON_CONTAINS(tags, '"electronics"');
```

### 3. 函数索引（MySQL 8.0+）

```sql
-- 基于表达式或函数的索引
CREATE TABLE employees (
    id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    birth_date DATE,
    
    -- 函数索引
    INDEX idx_full_name ((CONCAT(first_name, ' ', last_name))),
    INDEX idx_birth_year ((YEAR(birth_date))),
    INDEX idx_name_upper ((UPPER(CONCAT(first_name, ' ', last_name))))
);

-- 使用函数索引的查询
SELECT * FROM employees WHERE CONCAT(first_name, ' ', last_name) = 'John Smith';
SELECT * FROM employees WHERE YEAR(birth_date) = 1990;
SELECT * FROM employees WHERE UPPER(CONCAT(first_name, ' ', last_name)) = 'JOHN SMITH';
```

## 索引的物理存储

### 索引页面结构

```sql
-- 查看索引的物理信息
SELECT 
    table_name,
    index_name,
    non_unique,
    seq_in_index,
    column_name,
    cardinality,
    sub_part,
    index_type
FROM information_schema.statistics
WHERE table_schema = 'your_database'
AND table_name = 'your_table';

-- 查看索引大小
SELECT 
    table_name,
    index_name,
    ROUND(stat_value * @@innodb_page_size / 1024 / 1024, 2) AS size_mb
FROM mysql.innodb_index_stats
WHERE stat_name = 'size'
AND database_name = 'your_database';
```

### 索引统计信息

```sql
-- 查看索引统计信息
SELECT * FROM mysql.innodb_index_stats
WHERE database_name = 'your_database'
AND table_name = 'your_table';

-- 手动更新统计信息
ANALYZE TABLE your_table;

-- 查看索引使用情况
SELECT 
    object_schema,
    object_name,
    index_name,
    count_read,
    count_write,
    count_fetch,
    count_insert,
    count_update,
    count_delete
FROM performance_schema.table_io_waits_summary_by_index_usage
WHERE object_schema = 'your_database';
```

## 索引优化策略

### 1. 索引设计原则

```sql
-- 1. 选择性高的列适合创建索引
SELECT 
    column_name,
    COUNT(DISTINCT column_name) AS distinct_count,
    COUNT(*) AS total_count,
    COUNT(DISTINCT column_name) / COUNT(*) AS selectivity
FROM information_schema.columns c
JOIN your_table t
GROUP BY column_name
ORDER BY selectivity DESC;

-- 2. 复合索引的列顺序优化
-- 将选择性高的列放在前面
CREATE INDEX idx_status_date_customer ON orders(status, order_date, customer_id);
-- 而不是
-- CREATE INDEX idx_customer_date_status ON orders(customer_id, order_date, status);

-- 3. 避免冗余索引
-- 如果已有索引(a, b, c)，就不需要索引(a)和(a, b)
SHOW INDEX FROM your_table;
```

### 2. 索引监控和维护

```sql
-- 查找未使用的索引
SELECT 
    t.table_schema,
    t.table_name,
    t.index_name
FROM information_schema.statistics t
LEFT JOIN performance_schema.table_io_waits_summary_by_index_usage p
    ON t.table_schema = p.object_schema
    AND t.table_name = p.object_name
    AND t.index_name = p.index_name
WHERE t.table_schema NOT IN ('mysql', 'information_schema', 'performance_schema', 'sys')
    AND p.index_name IS NULL
    AND t.index_name != 'PRIMARY';

-- 查看索引碎片
SELECT 
    table_schema,
    table_name,
    ROUND(data_length / 1024 / 1024, 2) AS data_mb,
    ROUND(index_length / 1024 / 1024, 2) AS index_mb,
    ROUND(data_free / 1024 / 1024, 2) AS free_mb
FROM information_schema.tables
WHERE table_schema = 'your_database'
ORDER BY free_mb DESC;

-- 重建索引减少碎片
ALTER TABLE your_table FORCE;
-- 或
OPTIMIZE TABLE your_table;
```

## 索引最佳实践

### 1. 查询优化

```sql
-- 利用索引优化常见查询模式
-- 1. 等值查询
SELECT * FROM users WHERE user_id = 123;

-- 2. 范围查询
SELECT * FROM orders WHERE order_date BETWEEN '2024-01-01' AND '2024-01-31';

-- 3. 排序查询
SELECT * FROM products ORDER BY price DESC LIMIT 10;

-- 4. 分组查询
SELECT category, COUNT(*) FROM products GROUP BY category;

-- 5. 连接查询
SELECT u.username, o.order_total
FROM users u
JOIN orders o ON u.user_id = o.customer_id
WHERE u.status = 'active';
```

### 2. 索引设计建议

```sql
-- 1. 主键设计
-- 使用自增整数作为主键
CREATE TABLE optimal_table (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,  -- 推荐
    -- uuid VARCHAR(36) PRIMARY KEY,       -- 不推荐，随机性导致页面分裂
    business_data VARCHAR(100)
);

-- 2. 复合索引设计
-- 考虑查询模式设计索引顺序
CREATE INDEX idx_user_status_date ON user_activities(user_id, status, activity_date);
-- 支持的查询模式：
-- WHERE user_id = ?
-- WHERE user_id = ? AND status = ?
-- WHERE user_id = ? AND status = ? AND activity_date = ?
-- WHERE user_id = ? AND activity_date = ? (部分支持)

-- 3. 避免过度索引
-- 每个写操作都需要维护所有相关索引
-- 在查询性能和写入性能间找平衡
```

### 3. 性能监控

```sql
-- 监控慢查询中的索引使用
SET GLOBAL slow_query_log = ON;
SET GLOBAL long_query_time = 1;
SET GLOBAL log_queries_not_using_indexes = ON;

-- 分析执行计划
EXPLAIN FORMAT=JSON SELECT * FROM users WHERE username = 'john';

-- 使用性能模式监控索引
SELECT * FROM performance_schema.events_statements_summary_by_digest
WHERE digest_text LIKE '%your_table%'
ORDER BY avg_timer_wait DESC;
```

## 注意事项

- **索引不是越多越好**：每个索引都会增加写操作的开销
- **选择合适的索引类型**：根据查询模式选择最适合的索引
- **定期维护索引**：监控索引使用情况，删除不必要的索引
- **考虑存储空间**：索引会占用额外的存储空间
- **注意索引失效**：某些SQL写法会导致索引失效
- **主键选择很重要**：主键的选择直接影响聚簇索引的效率


# SQL性能分析与优化

## 什么是SQL性能优化

SQL性能优化是通过分析SQL语句的执行计划、识别性能瓶颈并采取相应优化措施来提高数据库查询效率的过程。它涉及SQL语句编写、索引设计、数据库配置、硬件资源等多个层面的优化。

## 性能分析工具

### 执行计划分析

#### EXPLAIN语句
```sql
-- 基本执行计划
EXPLAIN SELECT * FROM employees WHERE department_id = 10;

-- 详细执行计划（MySQL 5.6+）
EXPLAIN FORMAT=JSON SELECT * FROM employees WHERE department_id = 10;

-- 分析执行计划
EXPLAIN ANALYZE SELECT * FROM employees WHERE department_id = 10;

-- 执行计划关键字段解释：
-- id: 查询序列号
-- select_type: 查询类型（SIMPLE, PRIMARY, SUBQUERY等）
-- table: 表名
-- type: 访问类型（system > const > eq_ref > ref > range > index > ALL）
-- possible_keys: 可能使用的索引
-- key: 实际使用的索引
-- key_len: 使用的索引长度
-- rows: 扫描的行数
-- filtered: 返回结果的行数占需读取行数的比例，值越大表示索引越有效
-- Extra: 额外信息
```

#### 访问类型性能排序
```sql
-- 性能从好到差的访问类型：
-- 1. system: 表只有一行记录
-- 2. const: 通过主键或唯一索引访问，最多返回一行
EXPLAIN SELECT * FROM users WHERE id = 1;

-- 3. eq_ref: 唯一性索引扫描，对于每个索引键，表中只有一条记录匹配
EXPLAIN SELECT * FROM users u JOIN profiles p ON u.id = p.user_id;

-- 4. ref: 非唯一性索引扫描
EXPLAIN SELECT * FROM employees WHERE department_id = 10;

-- 5. range: 范围扫描
EXPLAIN SELECT * FROM orders WHERE order_date BETWEEN '2024-01-01' AND '2024-01-31';

-- 6. index: 全索引扫描
EXPLAIN SELECT id FROM users;

-- 7. ALL: 全表扫描（最差）
EXPLAIN SELECT * FROM logs WHERE message LIKE '%error%';
```

### SQL执行时间分析

#### SHOW PROFILES语句
```sql
-- 启用SQL语句分析
SET profiling = 1;

-- 执行要分析的SQL语句
SELECT * FROM employees WHERE department_id = 10;
SELECT COUNT(*) FROM orders WHERE order_date > '2024-01-01';
SELECT u.username, COUNT(o.id) FROM users u LEFT JOIN orders o ON u.id = o.user_id GROUP BY u.id;

-- 查看最近执行的查询
SHOW PROFILES;

-- 查看特定查询的详细执行信息
SHOW PROFILE FOR QUERY 1;

-- 查看详细的CPU和IO信息
SHOW PROFILE CPU, BLOCK IO FOR QUERY 1;

-- 查看所有可用的信息类型
SHOW PROFILE ALL FOR QUERY 1;

-- 查看内存使用情况
SHOW PROFILE MEMORY FOR QUERY 1;

-- 关闭profiling
SET profiling = 0;
```

#### SHOW PROFILE详细分析
```sql
-- 查看完整的执行阶段信息
SHOW PROFILE FOR QUERY 2;
-- 输出示例：
-- +----------------------+----------+
-- | Status               | Duration |
-- +----------------------+----------+
-- | starting             | 0.000015 |
-- | checking permissions | 0.000004 |
-- | Opening tables       | 0.000012 |
-- | init                 | 0.000018 |
-- | System lock          | 0.000007 |
-- | optimizing           | 0.000003 |
-- | statistics           | 0.000009 |
-- | preparing            | 0.000006 |
-- | executing            | 0.000002 |
-- | Sending data         | 0.001234 |
-- | end                  | 0.000003 |
-- | query end            | 0.000002 |
-- | closing tables       | 0.000004 |
-- | freeing items        | 0.000008 |
-- | cleaning up          | 0.000002 |
-- +----------------------+----------+

-- 分析CPU使用情况
SHOW PROFILE CPU FOR QUERY 2;
-- 输出包含CPU使用时间信息

-- 分析IO操作
SHOW PROFILE BLOCK IO FOR QUERY 2;
-- 显示块IO读写次数

-- 分析内存使用
SHOW PROFILE MEMORY FOR QUERY 2;
-- 显示内存分配和释放情况
```

#### INFORMATION_SCHEMA.PROFILING表
```sql
-- 直接查询profiling信息
SELECT STATE, FORMAT(DURATION, 6) as DURATION 
FROM INFORMATION_SCHEMA.PROFILING 
WHERE QUERY_ID = 1 
ORDER BY SEQ;

-- 查看特定阶段的资源使用
SELECT 
    STATE,
    ROUND(DURATION * 1000, 2) as DURATION_MS,
    CPU_USER,
    CPU_SYSTEM,
    BLOCK_OPS_IN,
    BLOCK_OPS_OUT
FROM INFORMATION_SCHEMA.PROFILING 
WHERE QUERY_ID = 1 
    AND STATE IN ('Sending data', 'statistics', 'preparing')
ORDER BY SEQ;
```

### 慢查询日志

#### 启用慢查询日志
```sql
-- 启用慢查询日志
SET GLOBAL slow_query_log = ON;
SET GLOBAL long_query_time = 2;  -- 超过2秒的查询记录
SET GLOBAL log_queries_not_using_indexes = ON;  -- 记录未使用索引的查询

-- 查看慢查询配置
SHOW VARIABLES LIKE '%slow_query%';
SHOW VARIABLES LIKE 'long_query_time';

-- 查看慢查询统计
SHOW GLOBAL STATUS LIKE '%slow_queries%';
```

#### 分析慢查询日志
```bash
# 使用mysqldumpslow分析慢查询日志
mysqldumpslow -s c -t 10 /var/log/mysql/slow.log  # 按查询次数排序，显示前10条
mysqldumpslow -s t -t 10 /var/log/mysql/slow.log  # 按查询时间排序
mysqldumpslow -s l -t 10 /var/log/mysql/slow.log  # 按锁定时间排序
```

### 性能模式（Performance Schema）

#### 监控SQL执行统计
```sql
-- 启用性能模式
SET GLOBAL performance_schema = ON;

-- 查看执行时间最长的SQL
SELECT 
    digest_text,
    count_star,
    avg_timer_wait/1000000000 AS avg_time_seconds,
    max_timer_wait/1000000000 AS max_time_seconds,
    sum_timer_wait/1000000000 AS total_time_seconds
FROM performance_schema.events_statements_summary_by_digest
ORDER BY avg_timer_wait DESC
LIMIT 10;

-- 查看表级别的I/O统计
SELECT 
    object_schema,
    object_name,
    count_read,
    count_write,
    count_fetch,
    count_insert,
    count_update,
    count_delete
FROM performance_schema.table_io_waits_summary_by_table
WHERE object_schema = 'your_database'
ORDER BY count_read DESC;

-- 查看索引使用情况
SELECT 
    object_schema,
    object_name,
    index_name,
    count_fetch,
    count_insert,
    count_update,
    count_delete
FROM performance_schema.table_io_waits_summary_by_index_usage
WHERE object_schema = 'your_database'
ORDER BY count_fetch DESC;
```

### 系统状态监控

#### SHOW STATUS语句
```sql
-- 查看服务器状态变量
SHOW STATUS;
SHOW GLOBAL STATUS;

-- 查看特定状态信息
SHOW STATUS LIKE 'Com_%';           -- 查看各种命令执行次数
SHOW STATUS LIKE 'Handler_%';       -- 查看存储引擎操作统计
SHOW STATUS LIKE 'Innodb_%';        -- 查看InnoDB相关状态
SHOW STATUS LIKE 'Created_tmp_%';   -- 查看临时表创建情况
SHOW STATUS LIKE 'Sort_%';          -- 查看排序操作统计

-- 重要的性能指标
SELECT 
    VARIABLE_NAME,
    VARIABLE_VALUE
FROM performance_schema.global_status
WHERE VARIABLE_NAME IN (
    'Questions',                    -- 查询总数
    'Queries',                      -- 包含存储过程调用的查询数
    'Slow_queries',                 -- 慢查询数
    'Threads_connected',            -- 当前连接数
    'Threads_running',              -- 正在运行的线程数
    'Created_tmp_disk_tables',      -- 磁盘临时表数
    'Created_tmp_tables',           -- 临时表总数
    'Handler_read_rnd_next',        -- 全表扫描数
    'Innodb_buffer_pool_reads',     -- 物理读次数
    'Innodb_buffer_pool_read_requests' -- 逻辑读次数
);
```

#### SHOW PROCESSLIST语句
```sql
-- 查看当前正在执行的查询
SHOW PROCESSLIST;
SHOW FULL PROCESSLIST;

-- 使用INFORMATION_SCHEMA查看更详细信息
SELECT 
    ID,
    USER,
    HOST,
    DB,
    COMMAND,
    TIME,
    STATE,
    INFO
FROM INFORMATION_SCHEMA.PROCESSLIST
WHERE COMMAND != 'Sleep'
ORDER BY TIME DESC;

-- 查看长时间运行的查询
SELECT 
    ID,
    USER,
    HOST,
    DB,
    TIME,
    STATE,
    LEFT(INFO, 100) as QUERY_START
FROM INFORMATION_SCHEMA.PROCESSLIST
WHERE TIME > 30  -- 运行超过30秒
    AND COMMAND != 'Sleep'
ORDER BY TIME DESC;

-- 终止长时间运行的查询
KILL QUERY 123;  -- 终止查询但保持连接
KILL 123;        -- 终止连接
```

### 实时性能监控

#### 实时查询分析
```sql
-- 开启实时查询监控
SET profiling = 1;

-- 执行查询并立即分析
SELECT COUNT(*) FROM large_table WHERE status = 'active';
SHOW PROFILE FOR QUERY LAST;

-- 分析当前运行的查询
SELECT 
    p.ID,
    p.TIME,
    p.STATE,
    LEFT(p.INFO, 200) as QUERY,
    t.TRXS_MYSQL_THREAD_ID,
    t.TRX_STATE,
    t.TRX_STARTED,
    t.TRX_ROWS_LOCKED,
    t.TRX_ROWS_MODIFIED
FROM INFORMATION_SCHEMA.PROCESSLIST p
LEFT JOIN INFORMATION_SCHEMA.INNODB_TRX t ON p.ID = t.TRXS_MYSQL_THREAD_ID
WHERE p.COMMAND != 'Sleep'
ORDER BY p.TIME DESC;
```

#### 锁等待分析
```sql
-- 查看当前锁等待情况
SELECT 
    r.trx_id waiting_trx_id,
    r.trx_mysql_thread_id waiting_thread,
    r.trx_query waiting_query,
    b.trx_id blocking_trx_id,
    b.trx_mysql_thread_id blocking_thread,
    b.trx_query blocking_query
FROM information_schema.innodb_lock_waits w
INNER JOIN information_schema.innodb_trx b ON b.trx_id = w.blocking_trx_id
INNER JOIN information_schema.innodb_trx r ON r.trx_id = w.requesting_trx_id;

-- 查看死锁信息
SHOW ENGINE INNODB STATUS;
-- 在输出中查找"LATEST DETECTED DEADLOCK"部分
```

### 资源使用监控

#### 内存使用分析
```sql
-- 查看内存使用情况
SELECT 
    EVENT_NAME,
    CURRENT_COUNT_USED,
    CURRENT_SIZE_USED,
    CURRENT_COUNT_ALLOC,
    CURRENT_SIZE_ALLOC
FROM performance_schema.memory_summary_global_by_event_name
WHERE CURRENT_SIZE_USED > 0
ORDER BY CURRENT_SIZE_USED DESC
LIMIT 20;

-- 查看缓冲池使用情况
SHOW STATUS LIKE 'Innodb_buffer_pool_%';

-- 计算缓冲池命中率
SELECT 
    ROUND(
        (1 - (
            (SELECT VARIABLE_VALUE FROM performance_schema.global_status WHERE VARIABLE_NAME = 'Innodb_buffer_pool_reads') /
            (SELECT VARIABLE_VALUE FROM performance_schema.global_status WHERE VARIABLE_NAME = 'Innodb_buffer_pool_read_requests')
        )) * 100, 2
    ) AS buffer_pool_hit_rate_percent;
```

#### IO性能监控
```sql
-- 查看文件IO统计
SELECT 
    file_name,
    event_name,
    count_read,
    count_write,
    sum_timer_read/1000000000 as read_time_seconds,
    sum_timer_write/1000000000 as write_time_seconds
FROM performance_schema.file_summary_by_instance
WHERE count_read > 0 OR count_write > 0
ORDER BY (sum_timer_read + sum_timer_write) DESC
LIMIT 20;

-- 查看表空间IO统计
SELECT 
    tablespace_name,
    file_name,
    engine,
    total_extents,
    extent_size,
    initial_size,
    maximum_size,
    autoextend_size
FROM information_schema.files
WHERE file_type = 'TABLESPACE';
```

## SQL语句优化

### WHERE子句优化

#### 索引优化
```sql
-- 不好的查询（无法使用索引）
SELECT * FROM employees WHERE YEAR(hire_date) = 2024;
SELECT * FROM products WHERE price * 0.8 > 100;
SELECT * FROM users WHERE UPPER(username) = 'JOHN';

-- 优化后的查询
SELECT * FROM employees WHERE hire_date >= '2024-01-01' AND hire_date < '2025-01-01';
SELECT * FROM products WHERE price > 100 / 0.8;
SELECT * FROM users WHERE username = 'john';  -- 假设数据库不区分大小写

-- 使用复合索引
CREATE INDEX idx_emp_dept_salary ON employees(department_id, salary);
-- 好的查询
SELECT * FROM employees WHERE department_id = 10 AND salary > 50000;
-- 部分使用索引
SELECT * FROM employees WHERE department_id = 10;
-- 无法使用索引
SELECT * FROM employees WHERE salary > 50000;
```

#### 条件优化
```sql
-- 使用EXISTS替代IN（大数据集）
-- 不推荐
SELECT * FROM customers 
WHERE customer_id IN (SELECT customer_id FROM orders WHERE order_date > '2024-01-01');

-- 推荐
SELECT * FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id AND o.order_date > '2024-01-01');

-- 使用UNION ALL替代OR（在某些情况下）
-- 可能较慢
SELECT * FROM products WHERE category_id = 1 OR category_id = 2;

-- 可能更快（如果有合适的索引）
SELECT * FROM products WHERE category_id = 1
UNION ALL
SELECT * FROM products WHERE category_id = 2;
```

### JOIN优化

#### JOIN类型选择
```sql
-- 确保JOIN条件有索引
CREATE INDEX idx_order_customer ON orders(customer_id);
CREATE INDEX idx_customer_id ON customers(id);

-- 好的JOIN查询
SELECT c.name, COUNT(o.order_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name;

-- 避免笛卡尔积
-- 错误示例
SELECT * FROM table1, table2;  -- 没有JOIN条件

-- 正确示例
SELECT * FROM table1 t1 JOIN table2 t2 ON t1.id = t2.table1_id;
```

#### 大表JOIN优化
```sql
-- 使用适当的JOIN算法提示（MySQL 8.0+）
SELECT /*+ NO_HASH_JOIN(t1, t2) */ *
FROM large_table1 t1
JOIN large_table2 t2 ON t1.id = t2.table1_id;

-- 分步JOIN（复杂查询）
-- 将复杂JOIN分解为多个步骤
CREATE TEMPORARY TABLE temp_result AS
SELECT customer_id, SUM(amount) as total_amount
FROM orders 
WHERE order_date >= '2024-01-01'
GROUP BY customer_id;

SELECT c.name, t.total_amount
FROM customers c
JOIN temp_result t ON c.id = t.customer_id;
```

### 子查询优化

#### 相关子查询优化
```sql
-- 低效的相关子查询
SELECT c.customer_id, c.name,
    (SELECT COUNT(*) FROM orders o WHERE o.customer_id = c.customer_id) as order_count
FROM customers c;

-- 优化为JOIN
SELECT c.customer_id, c.name, COALESCE(o.order_count, 0) as order_count
FROM customers c
LEFT JOIN (
    SELECT customer_id, COUNT(*) as order_count
    FROM orders
    GROUP BY customer_id
) o ON c.customer_id = o.customer_id;

-- 使用窗口函数优化
SELECT DISTINCT c.customer_id, c.name,
    COUNT(o.order_id) OVER (PARTITION BY c.customer_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

#### EXISTS vs IN优化
```sql
-- 大数据集时，EXISTS通常比IN快
-- 使用EXISTS
SELECT * FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);

-- 使用IN（当子查询结果集较小时）
SELECT * FROM customers
WHERE customer_id IN (SELECT DISTINCT customer_id FROM vip_orders);

-- 使用JOIN（通常最快）
SELECT DISTINCT c.*
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id;
```

### ORDER BY和GROUP BY优化

#### 排序优化
```sql
-- 创建支持排序的索引
CREATE INDEX idx_order_date_status ON orders(order_date DESC, status);

-- 好的排序查询
SELECT * FROM orders 
WHERE status = 'completed'
ORDER BY order_date DESC
LIMIT 20;

-- 避免文件排序
-- 查看是否使用了文件排序
EXPLAIN SELECT * FROM orders ORDER BY order_date DESC;
-- 如果Extra列显示"Using filesort"，考虑添加索引

-- 复合排序优化
SELECT * FROM products 
ORDER BY category_id, price DESC, product_name;
-- 需要索引：(category_id, price DESC, product_name)
```

#### 分组优化
```sql
-- 创建支持分组的索引
CREATE INDEX idx_customer_order_date ON orders(customer_id, order_date);

-- 高效的分组查询
SELECT customer_id, COUNT(*), SUM(amount)
FROM orders
GROUP BY customer_id;

-- 避免不必要的GROUP BY
-- 不好
SELECT customer_id FROM orders GROUP BY customer_id;
-- 好
SELECT DISTINCT customer_id FROM orders;

-- 使用索引优化GROUP BY
-- 如果GROUP BY的列有索引，可以避免临时表
EXPLAIN SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id;
```

## 索引优化策略

### 索引设计原则

#### 选择性分析
```sql
-- 分析列的选择性
SELECT 
    COUNT(DISTINCT column_name) / COUNT(*) AS selectivity,
    COUNT(DISTINCT column_name) AS distinct_values,
    COUNT(*) AS total_rows
FROM table_name;

-- 选择性高的列适合创建索引
-- 选择性 > 0.1 通常值得创建索引

-- 查看索引基数
SHOW INDEX FROM table_name;
-- 查看Cardinality列，值越高选择性越好
```

#### 复合索引优化
```sql
-- 索引列顺序优化原则：
-- 1. 等值条件的列放在前面
-- 2. 选择性高的列放在前面
-- 3. 考虑查询模式

-- 示例：查询模式分析
-- 查询1: WHERE status = 'active' AND created_date > '2024-01-01'
-- 查询2: WHERE status = 'active' AND user_id = 123
-- 查询3: WHERE status = 'active'

-- 最优索引：(status, created_date, user_id)
CREATE INDEX idx_status_date_user ON table_name(status, created_date, user_id);

-- 前缀索引优化
-- 分析前缀长度的选择性
SELECT 
    COUNT(DISTINCT LEFT(url, 10)) / COUNT(*) AS prefix_10,
    COUNT(DISTINCT LEFT(url, 20)) / COUNT(*) AS prefix_20,
    COUNT(DISTINCT LEFT(url, 30)) / COUNT(*) AS prefix_30
FROM urls;

-- 创建前缀索引
CREATE INDEX idx_url_prefix ON urls(url(20));
```

### 索引监控与维护

#### 查找无效索引
```sql
-- 查找未使用的索引
SELECT 
    s.table_schema,
    s.table_name,
    s.index_name,
    s.column_name
FROM information_schema.statistics s
LEFT JOIN performance_schema.table_io_waits_summary_by_index_usage p
    ON s.table_schema = p.object_schema
    AND s.table_name = p.object_name
    AND s.index_name = p.index_name
WHERE s.table_schema = 'your_database'
    AND p.index_name IS NULL
    AND s.index_name != 'PRIMARY';

-- 查找重复索引
SELECT 
    table_schema,
    table_name,
    GROUP_CONCAT(index_name) as duplicate_indexes,
    GROUP_CONCAT(column_name ORDER BY seq_in_index) as columns
FROM information_schema.statistics
WHERE table_schema = 'your_database'
GROUP BY table_schema, table_name, column_name
HAVING COUNT(*) > 1;
```

#### 索引碎片分析
```sql
-- 查看表碎片情况
SELECT 
    table_schema,
    table_name,
    ROUND(data_length/1024/1024, 2) AS data_mb,
    ROUND(index_length/1024/1024, 2) AS index_mb,
    ROUND(data_free/1024/1024, 2) AS free_mb,
    ROUND(data_free/(data_length + index_length) * 100, 2) AS fragmentation_pct
FROM information_schema.tables
WHERE table_schema = 'your_database'
    AND data_free > 0
ORDER BY fragmentation_pct DESC;

-- 重建索引减少碎片
ALTER TABLE table_name FORCE;
-- 或
OPTIMIZE TABLE table_name;
```

## 数据库配置优化

### 内存配置优化

#### InnoDB缓冲池优化
```sql
-- 查看当前配置
SHOW VARIABLES LIKE 'innodb_buffer_pool_%';

-- 设置缓冲池大小（通常设为可用内存的70-80%）
SET GLOBAL innodb_buffer_pool_size = 8589934592;  -- 8GB

-- 设置缓冲池实例数（大内存系统）
SET GLOBAL innodb_buffer_pool_instances = 8;

-- 监控缓冲池命中率
SELECT 
    (1 - (innodb_buffer_pool_reads / innodb_buffer_pool_read_requests)) * 100 AS hit_rate
FROM (
    SELECT variable_value AS innodb_buffer_pool_reads 
    FROM performance_schema.global_status 
    WHERE variable_name = 'Innodb_buffer_pool_reads'
) reads,
(
    SELECT variable_value AS innodb_buffer_pool_read_requests 
    FROM performance_schema.global_status 
    WHERE variable_name = 'Innodb_buffer_pool_read_requests'
) requests;
-- 命中率应该 > 99%
```

#### 查询缓存优化（MySQL 5.7及更早版本）
```sql
-- 查看查询缓存配置
SHOW VARIABLES LIKE 'query_cache%';

-- 配置查询缓存
SET GLOBAL query_cache_type = ON;
SET GLOBAL query_cache_size = 268435456;  -- 256MB

-- 监控查询缓存效果
SHOW STATUS LIKE 'Qcache%';
-- 关注Qcache_hits和Qcache_inserts的比率
```

### 连接和线程优化

```sql
-- 连接数配置
SHOW VARIABLES LIKE 'max_connections';
SET GLOBAL max_connections = 1000;

-- 线程缓存配置
SHOW VARIABLES LIKE 'thread_cache_size';
SET GLOBAL thread_cache_size = 100;

-- 监控连接使用情况
SHOW STATUS LIKE 'Connections';
SHOW STATUS LIKE 'Threads_%';
SHOW PROCESSLIST;

-- InnoDB并发配置
SET GLOBAL innodb_thread_concurrency = 0;  -- 0表示不限制
SET GLOBAL innodb_commit_concurrency = 0;
```

### 日志优化

#### 重做日志优化
```sql
-- 重做日志配置
SHOW VARIABLES LIKE 'innodb_log%';

-- 设置日志文件大小
SET GLOBAL innodb_log_file_size = 1073741824;  -- 1GB

-- 设置日志缓冲区大小
SET GLOBAL innodb_log_buffer_size = 67108864;  -- 64MB

-- 刷新策略
SET GLOBAL innodb_flush_log_at_trx_commit = 1;  -- 最安全
-- innodb_flush_log_at_trx_commit = 2;  -- 性能优先，可能丢失1秒数据
```

#### 慢查询日志优化
```sql
-- 优化慢查询日志记录
SET GLOBAL min_examined_row_limit = 1000;  -- 只记录检查行数超过1000的查询
SET GLOBAL log_slow_admin_statements = ON;  -- 记录管理语句
SET GLOBAL log_slow_slave_statements = ON;  -- 记录从库慢查询
```

## 应用层优化

### 连接池优化

```java
// 连接池配置示例（HikariCP）
HikariConfig config = new HikariConfig();
config.setMaximumPoolSize(20);           // 最大连接数
config.setMinimumIdle(5);                // 最小空闲连接
config.setConnectionTimeout(30000);      // 连接超时
config.setIdleTimeout(600000);           // 空闲超时
config.setMaxLifetime(1800000);          // 连接最大生命周期
config.setLeakDetectionThreshold(60000); // 连接泄漏检测
```

### 批量操作优化

```sql
-- 批量插入优化
-- 不好的方式
INSERT INTO products (name, price) VALUES ('Product 1', 99.99);
INSERT INTO products (name, price) VALUES ('Product 2', 149.99);
-- ... 重复多次

-- 好的方式
INSERT INTO products (name, price) VALUES 
('Product 1', 99.99),
('Product 2', 149.99),
('Product 3', 199.99),
-- ... 一次插入多行
('Product 100', 299.99);

-- 使用事务优化批量操作
START TRANSACTION;
INSERT INTO products (name, price) VALUES ('Product 1', 99.99);
INSERT INTO products (name, price) VALUES ('Product 2', 149.99);
-- ... 多个操作
COMMIT;

-- 批量更新优化
UPDATE products 
SET price = CASE 
    WHEN id = 1 THEN 109.99
    WHEN id = 2 THEN 159.99
    WHEN id = 3 THEN 209.99
END
WHERE id IN (1, 2, 3);
```

### 分页优化

```sql
-- 传统分页（大偏移量时性能差）
SELECT * FROM products ORDER BY id LIMIT 10000, 20;

-- 游标分页（推荐）
SELECT * FROM products WHERE id > 10000 ORDER BY id LIMIT 20;

-- 覆盖索引分页
SELECT p.* FROM products p
JOIN (
    SELECT id FROM products ORDER BY id LIMIT 10000, 20
) tmp ON p.id = tmp.id;

-- 计算总数优化
-- 不要每次都COUNT(*)
-- 可以缓存总数或使用估算值
SELECT SQL_CALC_FOUND_ROWS * FROM products ORDER BY id LIMIT 20;
SELECT FOUND_ROWS() AS total_count;
```

## 实际案例分析

### 案例1：慢查询优化

```sql
-- 原始慢查询
SELECT u.username, u.email, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at > '2024-01-01'
    AND u.status = 'active'
GROUP BY u.id, u.username, u.email
ORDER BY order_count DESC;

-- 分析执行计划
EXPLAIN FORMAT=JSON [上述查询];

-- 优化步骤：
-- 1. 创建复合索引
CREATE INDEX idx_user_status_created ON users(status, created_at);
CREATE INDEX idx_order_user ON orders(user_id);

-- 2. 优化查询结构
SELECT u.username, u.email, COALESCE(o.order_count, 0) as order_count
FROM users u
LEFT JOIN (
    SELECT user_id, COUNT(*) as order_count
    FROM orders
    GROUP BY user_id
) o ON u.id = o.user_id
WHERE u.created_at > '2024-01-01'
    AND u.status = 'active'
ORDER BY order_count DESC;
```

### 案例2：大表查询优化

```sql
-- 大表范围查询优化
-- 原查询：扫描大量数据
SELECT * FROM log_entries 
WHERE created_at BETWEEN '2024-01-01' AND '2024-01-31'
    AND log_level = 'ERROR';

-- 优化方案1：调整索引顺序
CREATE INDEX idx_date_level ON log_entries(created_at, log_level);

-- 优化方案2：分区表
CREATE TABLE log_entries (
    id BIGINT AUTO_INCREMENT,
    message TEXT,
    log_level VARCHAR(10),
    created_at TIMESTAMP,
    PRIMARY KEY (id, created_at),
    INDEX idx_level (log_level)
) PARTITION BY RANGE (TO_DAYS(created_at)) (
    PARTITION p202401 VALUES LESS THAN (TO_DAYS('2024-02-01')),
    PARTITION p202402 VALUES LESS THAN (TO_DAYS('2024-03-01')),
    -- ... 更多分区
);

-- 优化方案3：数据归档
-- 将旧数据移动到归档表
CREATE TABLE log_entries_archive LIKE log_entries;
INSERT INTO log_entries_archive 
SELECT * FROM log_entries WHERE created_at < '2024-01-01';
DELETE FROM log_entries WHERE created_at < '2024-01-01';
```

### 案例3：复杂JOIN优化

```sql
-- 复杂多表JOIN查询
-- 原查询：性能较差
SELECT 
    c.name AS customer_name,
    p.name AS product_name,
    SUM(oi.quantity) AS total_quantity,
    SUM(oi.quantity * oi.price) AS total_amount
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE o.order_date >= '2024-01-01'
    AND c.status = 'active'
GROUP BY c.id, p.id;

-- 优化策略：
-- 1. 确保所有JOIN列都有索引
CREATE INDEX idx_order_customer ON orders(customer_id);
CREATE INDEX idx_order_items_order ON order_items(order_id);
CREATE INDEX idx_order_items_product ON order_items(product_id);
CREATE INDEX idx_customer_status ON customers(status);
CREATE INDEX idx_order_date ON orders(order_date);

-- 2. 重写查询，先过滤再JOIN
WITH filtered_orders AS (
    SELECT o.id, o.customer_id
    FROM orders o
    JOIN customers c ON o.customer_id = c.id
    WHERE o.order_date >= '2024-01-01'
        AND c.status = 'active'
)
SELECT 
    c.name AS customer_name,
    p.name AS product_name,
    SUM(oi.quantity) AS total_quantity,
    SUM(oi.quantity * oi.price) AS total_amount
FROM filtered_orders fo
JOIN customers c ON fo.customer_id = c.id
JOIN order_items oi ON fo.id = oi.order_id
JOIN products p ON oi.product_id = p.id
GROUP BY c.id, p.id;
```

## 监控和维护

### 性能监控指标

```sql
-- 关键性能指标监控
-- 1. QPS（每秒查询数）
SHOW GLOBAL STATUS LIKE 'Questions';
SHOW GLOBAL STATUS LIKE 'Queries';

-- 2. TPS（每秒事务数）
SHOW GLOBAL STATUS LIKE 'Com_commit';
SHOW GLOBAL STATUS LIKE 'Com_rollback';

-- 3. 慢查询统计
SHOW GLOBAL STATUS LIKE 'Slow_queries';

-- 4. 连接数
SHOW GLOBAL STATUS LIKE 'Threads_connected';
SHOW GLOBAL STATUS LIKE 'Max_used_connections';

-- 5. 缓冲池命中率
SELECT 
    (1 - (reads / read_requests)) * 100 AS hit_rate
FROM (
    SELECT 
        SUM(CASE WHEN variable_name = 'Innodb_buffer_pool_reads' THEN variable_value END) AS reads,
        SUM(CASE WHEN variable_name = 'Innodb_buffer_pool_read_requests' THEN variable_value END) AS read_requests
    FROM performance_schema.global_status
    WHERE variable_name IN ('Innodb_buffer_pool_reads', 'Innodb_buffer_pool_read_requests')
) metrics;
```

### 定期维护任务

```sql
-- 1. 更新表统计信息
ANALYZE TABLE table_name;

-- 2. 优化表（整理碎片）
OPTIMIZE TABLE table_name;

-- 3. 检查表完整性
CHECK TABLE table_name;

-- 4. 修复表
REPAIR TABLE table_name;

-- 5. 清理二进制日志
PURGE BINARY LOGS BEFORE DATE(NOW() - INTERVAL 7 DAY);

-- 6. 定期检查慢查询
-- 定期分析慢查询日志，识别新的性能问题
```

## 最佳实践总结

### SQL编写规范

1. **使用合适的数据类型**：选择最小的数据类型
2. **避免SELECT ***：只查询需要的列
3. **合理使用索引**：创建必要的索引，删除无用索引
4. **优化WHERE条件**：将选择性高的条件放在前面
5. **使用LIMIT**：限制返回的行数
6. **避免在WHERE中使用函数**：函数会破坏索引使用

### 性能优化流程

1. **识别问题**：通过监控发现性能瓶颈
2. **分析根因**：使用EXPLAIN分析执行计划
3. **制定方案**：选择合适的优化策略
4. **实施优化**：在测试环境验证效果
5. **监控效果**：持续监控性能指标
6. **文档记录**：记录优化过程和效果

### 注意事项

- **避免过度优化**：优化要有针对性，解决真正的性能瓶颈
- **测试验证**：所有优化都要在测试环境验证
- **监控跟踪**：持续监控优化效果
- **备份数据**：重要的结构变更前要备份
- **考虑并发**：优化要考虑并发访问的影响
- **平衡权衡**：在查询性能和写入性能间找平衡


# 索引使用规则详解

## 什么是索引使用规则

索引使用规则是指数据库查询优化器在执行SQL语句时，决定是否使用索引以及如何使用索引的一系列规则和原则。理解这些规则对于编写高效的SQL查询和设计合理的索引策略至关重要。

## 基本索引使用规则

### 1. 最左前缀原则

**定义：** 对于复合索引，查询条件必须从索引的最左列开始，且不能跳过中间的列。

```sql
-- 创建复合索引
CREATE INDEX idx_name_age_city ON users(name, age, city);

-- 可以使用索引的查询
SELECT * FROM users WHERE name = 'John';                           -- ✓ 使用索引前缀 (name)
SELECT * FROM users WHERE name = 'John' AND age = 25;              -- ✓ 使用索引前缀 (name, age)
SELECT * FROM users WHERE name = 'John' AND age = 25 AND city = 'NY'; -- ✓ 使用完整索引
SELECT * FROM users WHERE name = 'John' AND city = 'NY';           -- ✓ 使用索引前缀 (name)，city条件在索引中过滤

-- 无法使用索引的查询
SELECT * FROM users WHERE age = 25;                                -- ✗ 跳过了最左列name
SELECT * FROM users WHERE city = 'NY';                             -- ✗ 跳过了name和age
SELECT * FROM users WHERE age = 25 AND city = 'NY';               -- ✗ 跳过了最左列name

-- 部分使用索引的查询
SELECT * FROM users WHERE name = 'John' AND city = 'NY';          -- 部分使用，只能用到name列
```

### 2. 索引列不能参与计算

**定义：** 在WHERE子句中，索引列参与计算或函数操作会导致索引失效。

```sql
-- 创建索引
CREATE INDEX idx_salary ON employees(salary);
CREATE INDEX idx_hire_date ON employees(hire_date);
CREATE INDEX idx_name ON employees(name);

-- 导致索引失效的查询
SELECT * FROM employees WHERE salary * 12 > 120000;               -- ✗ 索引列参与计算
SELECT * FROM employees WHERE YEAR(hire_date) = 2024;             -- ✗ 索引列使用函数
SELECT * FROM employees WHERE UPPER(name) = 'JOHN';               -- ✗ 索引列使用函数
SELECT * FROM employees WHERE salary + bonus > 100000;            -- ✗ 索引列参与计算

-- 正确的查询方式
SELECT * FROM employees WHERE salary > 120000 / 12;               -- ✓ 计算移到右侧
SELECT * FROM employees WHERE hire_date >= '2024-01-01' 
    AND hire_date < '2025-01-01';                                  -- ✓ 使用范围查询
SELECT * FROM employees WHERE name = 'john';                      -- ✓ 直接比较（假设不区分大小写）
-- 如果需要大小写不敏感查询，可以考虑：
-- 1. 创建函数索引（MySQL 8.0+）
CREATE INDEX idx_name_upper ON employees((UPPER(name)));
-- 2. 或者存储时统一大小写
```

### 3. 类型转换规则

**定义：** 数据类型不匹配会导致隐式类型转换，可能使索引失效。

```sql
-- 创建表和索引
CREATE TABLE products (
    id INT PRIMARY KEY,
    product_code VARCHAR(20),
    price DECIMAL(10,2),
    is_active TINYINT(1)
);

CREATE INDEX idx_product_code ON products(product_code);
CREATE INDEX idx_price ON products(price);
CREATE INDEX idx_active ON products(is_active);

-- 导致索引失效的查询
SELECT * FROM products WHERE product_code = 12345;                -- ✗ 字符串列与数字比较
SELECT * FROM products WHERE price = '99.99';                     -- ✗ 数字列与字符串比较
SELECT * FROM products WHERE is_active = 'true';                  -- ✗ 布尔列与字符串比较

-- 正确的查询方式
SELECT * FROM products WHERE product_code = '12345';              -- ✓ 字符串与字符串比较
SELECT * FROM products WHERE price = 99.99;                       -- ✓ 数字与数字比较
SELECT * FROM products WHERE is_active = 1;                       -- ✓ 布尔列与数字比较

-- 查看类型转换影响
EXPLAIN SELECT * FROM products WHERE product_code = 12345;
-- 如果Extra列显示"Using where"且type为"ALL"，说明发生了全表扫描
```

### 4. NULL值处理规则

**定义：** 索引通常不存储NULL值，涉及NULL的查询需要特殊处理。

```sql
-- 创建测试表
CREATE TABLE customers (
    id INT PRIMARY KEY,
    email VARCHAR(100),
    phone VARCHAR(20),
    created_at TIMESTAMP
);

CREATE INDEX idx_email ON customers(email);
CREATE INDEX idx_phone ON customers(phone);

-- NULL值相关查询
SELECT * FROM customers WHERE email IS NULL;                      -- 可能无法使用索引
SELECT * FROM customers WHERE email IS NOT NULL;                  -- 可能无法使用索引
SELECT * FROM customers WHERE email = '';                         -- ✓ 可以使用索引（空字符串不是NULL）

-- 复合索引中的NULL值
CREATE INDEX idx_email_phone ON customers(email, phone);
SELECT * FROM customers WHERE email = 'john@email.com' AND phone IS NULL;  -- 部分使用索引

-- 优化NULL值查询的方法
-- 1. 设计时避免NULL值
ALTER TABLE customers MODIFY email VARCHAR(100) NOT NULL DEFAULT '';
-- 2. 使用特殊值代替NULL
UPDATE customers SET phone = 'UNKNOWN' WHERE phone IS NULL;
-- 3. 创建部分索引（MySQL 8.0+）
CREATE INDEX idx_email_not_null ON customers(email) WHERE email IS NOT NULL;
```

## 范围查询使用规则

### 1. 范围查询索引使用

```sql
-- 创建索引
CREATE INDEX idx_age_salary ON employees(age, salary, department_id);

-- 范围查询规则
SELECT * FROM employees WHERE age = 30 AND salary > 50000;        -- ✓ age等值 + salary范围
SELECT * FROM employees WHERE age > 25 AND salary = 60000;        -- 部分使用，age范围后salary无法使用索引
SELECT * FROM employees WHERE age BETWEEN 25 AND 35;              -- ✓ 使用age索引进行范围查询

-- 多个范围条件
SELECT * FROM employees 
WHERE age BETWEEN 25 AND 35 
    AND salary BETWEEN 50000 AND 80000;                           -- 只能使用age的范围查询

-- 优化多范围查询
-- 方法1：调整索引顺序，将等值条件放前面
CREATE INDEX idx_dept_age_salary ON employees(department_id, age, salary);
SELECT * FROM employees 
WHERE department_id = 10 
    AND age BETWEEN 25 AND 35 
    AND salary BETWEEN 50000 AND 80000;                          -- 更好的索引使用

-- 方法2：分别创建单列索引，让优化器选择
CREATE INDEX idx_age ON employees(age);
CREATE INDEX idx_salary ON employees(salary);
```

### 2. LIKE查询规则

```sql
-- 创建索引
CREATE INDEX idx_name ON customers(name);
CREATE INDEX idx_email ON customers(email);

-- 可以使用索引的LIKE查询
SELECT * FROM customers WHERE name LIKE 'John%';                  -- ✓ 前缀匹配
SELECT * FROM customers WHERE email LIKE 'john@%';               -- ✓ 前缀匹配

-- 无法使用索引的LIKE查询
SELECT * FROM customers WHERE name LIKE '%John';                 -- ✗ 后缀匹配
SELECT * FROM customers WHERE name LIKE '%John%';                -- ✗ 中间匹配
SELECT * FROM customers WHERE name LIKE '_ohn%';                 -- ✗ 通配符开头

-- 优化LIKE查询的方法
-- 1. 使用全文索引
CREATE FULLTEXT INDEX ft_name ON customers(name);
SELECT * FROM customers WHERE MATCH(name) AGAINST('John' IN NATURAL LANGUAGE MODE);

-- 2. 反向索引（用于后缀匹配）
ALTER TABLE customers ADD COLUMN name_reverse VARCHAR(100);
UPDATE customers SET name_reverse = REVERSE(name);
CREATE INDEX idx_name_reverse ON customers(name_reverse);
SELECT * FROM customers WHERE name_reverse LIKE REVERSE('%John');

-- 3. 使用多列组合避免通配符
-- 如果是搜索姓名，可以分成first_name和last_name
CREATE INDEX idx_first_last ON customers(first_name, last_name);
```

## 复合索引使用规则

### 1. 索引列顺序的重要性

```sql
-- 创建测试表
CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    status VARCHAR(20),
    amount DECIMAL(10,2)
);

-- 分析查询模式来确定索引顺序
-- 查询模式1：按客户查询订单
SELECT * FROM orders WHERE customer_id = 123;
-- 查询模式2：按客户和日期查询
SELECT * FROM orders WHERE customer_id = 123 AND order_date = '2024-01-15';
-- 查询模式3：按客户、状态和日期查询
SELECT * FROM orders WHERE customer_id = 123 AND status = 'completed' AND order_date > '2024-01-01';

-- 最优索引设计
CREATE INDEX idx_customer_status_date ON orders(customer_id, status, order_date);
-- 这个索引可以支持所有上述查询模式

-- 验证索引使用情况
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;                                    -- 使用完整索引前缀
EXPLAIN SELECT * FROM orders WHERE customer_id = 123 AND status = 'completed';         -- 使用完整索引前缀
EXPLAIN SELECT * FROM orders WHERE customer_id = 123 AND order_date > '2024-01-01';    -- 部分使用索引
```

### 2. 等值与范围条件混合

```sql
-- 复合索引中等值和范围条件的使用规则
CREATE INDEX idx_status_date_amount ON orders(status, order_date, amount);

-- 最优使用：等值条件在前，范围条件在后
SELECT * FROM orders 
WHERE status = 'completed' 
    AND order_date BETWEEN '2024-01-01' AND '2024-01-31';        -- ✓ 使用status等值 + order_date范围

-- 部分使用：范围条件后的列无法使用索引
SELECT * FROM orders 
WHERE status = 'completed' 
    AND order_date > '2024-01-01' 
    AND amount > 1000;                                            -- amount条件无法使用索引

-- 优化策略：调整索引顺序
CREATE INDEX idx_status_amount_date ON orders(status, amount, order_date);
SELECT * FROM orders 
WHERE status = 'completed' 
    AND amount = 1000 
    AND order_date > '2024-01-01';                               -- ✓ 更好的索引使用
```

## ORDER BY索引使用规则

### 1. 排序索引规则

```sql
-- 创建索引支持排序
CREATE INDEX idx_date_status ON orders(order_date, status);

-- 可以使用索引的排序
SELECT * FROM orders ORDER BY order_date;                        -- ✓ 使用索引排序
SELECT * FROM orders ORDER BY order_date, status;                -- ✓ 使用索引排序
SELECT * FROM orders WHERE order_date > '2024-01-01' ORDER BY order_date; -- ✓ WHERE和ORDER BY都使用索引

-- 无法使用索引的排序
SELECT * FROM orders ORDER BY status;                            -- ✗ 不符合最左前缀
SELECT * FROM orders ORDER BY order_date DESC, status ASC;       -- ✗ 排序方向不一致
SELECT * FROM orders ORDER BY amount;                            -- ✗ amount不在索引中

-- 解决排序方向问题
CREATE INDEX idx_date_desc_status_desc ON orders(order_date DESC, status DESC);
SELECT * FROM orders ORDER BY order_date DESC, status DESC;      -- ✓ 排序方向匹配

-- 混合条件的排序优化
CREATE INDEX idx_customer_date ON orders(customer_id, order_date);
SELECT * FROM orders WHERE customer_id = 123 ORDER BY order_date; -- ✓ WHERE使用customer_id，ORDER BY使用order_date
```

### 2. GROUP BY索引使用规则

```sql
-- 创建支持分组的索引
CREATE INDEX idx_customer_status ON orders(customer_id, status);

-- 可以使用索引的分组
SELECT customer_id, COUNT(*) FROM orders GROUP BY customer_id;                    -- ✓ 使用索引
SELECT customer_id, status, COUNT(*) FROM orders GROUP BY customer_id, status;   -- ✓ 使用索引
SELECT customer_id, COUNT(*) FROM orders 
WHERE customer_id > 100 GROUP BY customer_id;                                     -- ✓ WHERE和GROUP BY都使用索引

-- 优化分组查询
-- 避免不必要的GROUP BY
-- 不好的写法
SELECT customer_id FROM orders GROUP BY customer_id;
-- 更好的写法
SELECT DISTINCT customer_id FROM orders;

-- 使用覆盖索引优化分组
CREATE INDEX idx_customer_status_amount ON orders(customer_id, status, amount);
SELECT customer_id, status, SUM(amount) FROM orders GROUP BY customer_id, status; -- 覆盖索引，无需回表
```

## 索引失效场景

### 1. OR条件索引失效

```sql
-- OR条件可能导致索引失效
CREATE INDEX idx_name ON employees(name);
CREATE INDEX idx_department ON employees(department);

-- 可能无法有效使用索引的查询
SELECT * FROM employees WHERE name = 'John' OR department = 'IT';  -- 两个不同索引的OR

-- 优化方案
-- 1. 使用UNION代替OR
SELECT * FROM employees WHERE name = 'John'
UNION
SELECT * FROM employees WHERE department = 'IT';

-- 2. 创建复合索引（如果经常一起查询）
CREATE INDEX idx_name_dept ON employees(name, department);

-- 3. 使用IN代替OR（相同列的多个值）
SELECT * FROM employees WHERE department IN ('IT', 'HR', 'Finance'); -- ✓ 可以使用索引
```

### 2. 不等于操作符索引失效

```sql
-- 不等于操作符可能导致索引使用效率低下
CREATE INDEX idx_status ON orders(status);

-- 效率较低的查询
SELECT * FROM orders WHERE status != 'cancelled';                 -- 可能扫描大量数据
SELECT * FROM orders WHERE status <> 'cancelled';                 -- 同上

-- 优化方案
-- 1. 使用IN列举具体值
SELECT * FROM orders WHERE status IN ('pending', 'processing', 'completed', 'shipped');

-- 2. 反向思考，先查询不要的，再排除
-- 如果cancelled的记录很少
SELECT * FROM orders WHERE NOT EXISTS (
    SELECT 1 FROM orders o2 WHERE o2.id = orders.id AND o2.status = 'cancelled'
);

-- 3. 使用复合条件
SELECT * FROM orders WHERE status = 'completed' OR status = 'shipped';
```

### 3. 模糊查询索引失效

```sql
-- 前导模糊查询无法使用普通索引
CREATE INDEX idx_product_name ON products(product_name);

-- 无法使用索引
SELECT * FROM products WHERE product_name LIKE '%phone%';         -- ✗ 前导通配符
SELECT * FROM products WHERE product_name LIKE '%phone';          -- ✗ 前导通配符

-- 可以使用索引
SELECT * FROM products WHERE product_name LIKE 'phone%';          -- ✓ 后置通配符

-- 优化方案
-- 1. 使用全文索引
CREATE FULLTEXT INDEX ft_product_name ON products(product_name);
SELECT * FROM products WHERE MATCH(product_name) AGAINST('phone' IN NATURAL LANGUAGE MODE);

-- 2. 使用倒排索引思路
-- 创建关键词表
CREATE TABLE product_keywords (
    product_id INT,
    keyword VARCHAR(50),
    INDEX idx_keyword (keyword),
    INDEX idx_product (product_id)
);

-- 查询时关联关键词表
SELECT DISTINCT p.* FROM products p
JOIN product_keywords pk ON p.id = pk.product_id
WHERE pk.keyword = 'phone';
```

## 覆盖索引使用规则

### 1. 覆盖索引定义和优势

```sql
-- 覆盖索引：查询所需的所有列都包含在索引中，无需回表查询
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    age INT,
    city VARCHAR(50),
    created_at TIMESTAMP
);

-- 创建覆盖索引
CREATE INDEX idx_username_email_age ON users(username, email, age);

-- 覆盖索引查询（无需回表）
SELECT username, email FROM users WHERE username = 'john';        -- ✓ 覆盖索引
SELECT username, email, age FROM users WHERE username = 'john';   -- ✓ 覆盖索引
SELECT COUNT(*) FROM users WHERE username LIKE 'j%';              -- ✓ 覆盖索引

-- 非覆盖索引查询（需要回表）
SELECT username, email, city FROM users WHERE username = 'john';  -- ✗ city不在索引中，需要回表

-- 验证是否使用覆盖索引
EXPLAIN SELECT username, email FROM users WHERE username = 'john';
-- 查看Extra列是否显示"Using index"
```

### 2. 覆盖索引设计策略

```sql
-- 分析查询模式设计覆盖索引
-- 常见查询1：根据用户名查询用户基本信息
SELECT id, username, email FROM users WHERE username = ?;

-- 常见查询2：统计特定条件下的用户数量
SELECT COUNT(*) FROM users WHERE age BETWEEN 18 AND 65;

-- 常见查询3：查询用户列表（分页）
SELECT id, username, email FROM users ORDER BY username LIMIT 20 OFFSET 100;

-- 设计覆盖索引
CREATE INDEX idx_username_id_email ON users(username, id, email);  -- 支持查询1和3
CREATE INDEX idx_age ON users(age);                                -- 支持查询2

-- 包含主键的覆盖索引
-- 注意：InnoDB的辅助索引自动包含主键，可以利用这个特性
CREATE INDEX idx_username ON users(username);
-- 以下查询也是覆盖索引查询，因为id（主键）自动包含在索引中
SELECT id, username FROM users WHERE username = 'john';
```

## 索引优化最佳实践

### 1. 索引监控和维护

```sql
-- 监控索引使用情况
SELECT 
    object_schema,
    object_name,
    index_name,
    count_fetch,
    count_insert,
    count_update,
    count_delete
FROM performance_schema.table_io_waits_summary_by_index_usage
WHERE object_schema = 'your_database'
    AND count_fetch > 0
ORDER BY count_fetch DESC;

-- 查找未使用的索引
SELECT 
    t.table_schema,
    t.table_name,
    t.index_name
FROM information_schema.statistics t
LEFT JOIN performance_schema.table_io_waits_summary_by_index_usage p
    ON t.table_schema = p.object_schema
    AND t.table_name = p.object_name
    AND t.index_name = p.index_name
WHERE t.table_schema = 'your_database'
    AND p.index_name IS NULL
    AND t.index_name != 'PRIMARY';

-- 分析索引选择性
SELECT 
    table_name,
    index_name,
    column_name,
    cardinality,
    (cardinality / (SELECT table_rows FROM information_schema.tables 
                    WHERE table_name = s.table_name AND table_schema = s.table_schema)) AS selectivity
FROM information_schema.statistics s
WHERE table_schema = 'your_database'
    AND cardinality IS NOT NULL
ORDER BY selectivity DESC;
```

### 2. 索引设计原则

```sql
-- 1. 高选择性列优先
-- 分析列的选择性
SELECT 
    'status' AS column_name,
    COUNT(DISTINCT status) AS distinct_values,
    COUNT(*) AS total_rows,
    COUNT(DISTINCT status) / COUNT(*) AS selectivity
FROM orders
UNION ALL
SELECT 
    'customer_id',
    COUNT(DISTINCT customer_id),
    COUNT(*),
    COUNT(DISTINCT customer_id) / COUNT(*)
FROM orders;

-- 2. 频繁查询的列组合
-- 根据实际查询模式创建索引
-- 查询频率: WHERE customer_id = ? (90%), WHERE status = ? (60%), WHERE order_date = ? (40%)
CREATE INDEX idx_customer_status_date ON orders(customer_id, status, order_date);

-- 3. 考虑写入性能
-- 写入频繁的表要平衡索引数量
-- 每个索引都会增加INSERT/UPDATE/DELETE的开销

-- 4. 避免冗余索引
-- 检查索引重复
SELECT 
    table_name,
    GROUP_CONCAT(DISTINCT index_name) as indexes,
    GROUP_CONCAT(column_name ORDER BY seq_in_index) as columns
FROM information_schema.statistics
WHERE table_schema = 'your_database'
GROUP BY table_name, column_name
HAVING COUNT(DISTINCT index_name) > 1;
```

### 3. 特殊场景索引策略

```sql
-- 1. 大表分页查询优化
-- 传统分页（性能差）
SELECT * FROM large_table ORDER BY id LIMIT 100000, 20;

-- 使用覆盖索引优化
SELECT lt.* FROM large_table lt
JOIN (
    SELECT id FROM large_table ORDER BY id LIMIT 100000, 20
) tmp ON lt.id = tmp.id;

-- 2. 时间范围查询优化
-- 为时间字段创建合适的索引
CREATE INDEX idx_created_at ON logs(created_at);
-- 支持范围查询
SELECT * FROM logs WHERE created_at BETWEEN '2024-01-01' AND '2024-01-31';

-- 3. JSON字段索引（MySQL 5.7+）
-- 为JSON字段的特定路径创建索引
ALTER TABLE products ADD INDEX idx_json_category ((CAST(attributes->'$.category' AS CHAR(50))));
SELECT * FROM products WHERE attributes->'$.category' = 'electronics';

-- 4. 函数索引（MySQL 8.0+）
-- 为计算列创建索引
CREATE INDEX idx_full_name ON users((CONCAT(first_name, ' ', last_name)));
SELECT * FROM users WHERE CONCAT(first_name, ' ', last_name) = 'John Smith';
```

## 索引性能测试

### 1. 执行计划分析

```sql
-- 详细分析执行计划
EXPLAIN FORMAT=JSON 
SELECT u.username, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.customer_id
WHERE u.created_at > '2024-01-01'
GROUP BY u.id, u.username;

-- 分析关键指标：
-- 1. rows: 扫描行数，越少越好
-- 2. type: 访问类型，const > eq_ref > ref > range > index > ALL
-- 3. key: 使用的索引
-- 4. Extra: 额外信息，注意"Using filesort"、"Using temporary"

-- 使用SHOW WARNINGS查看优化器重写的查询
EXPLAIN SELECT * FROM users WHERE id = 1;
SHOW WARNINGS;
```

### 2. 性能基准测试

```sql
-- 开启查询分析
SET profiling = 1;

-- 测试不同索引策略的性能
-- 测试1：无索引
SELECT COUNT(*) FROM orders WHERE customer_id = 12345 AND status = 'completed';

-- 测试2：单列索引
CREATE INDEX idx_customer ON orders(customer_id);
SELECT COUNT(*) FROM orders WHERE customer_id = 12345 AND status = 'completed';

-- 测试3：复合索引
DROP INDEX idx_customer ON orders;
CREATE INDEX idx_customer_status ON orders(customer_id, status);
SELECT COUNT(*) FROM orders WHERE customer_id = 12345 AND status = 'completed';

-- 比较执行时间
SHOW PROFILES;

-- 关闭分析
SET profiling = 0;
```

## 常见索引使用误区

### 1. 索引越多越好的误区

```sql
-- 错误做法：为每个列都创建索引
CREATE INDEX idx_col1 ON table_name(col1);
CREATE INDEX idx_col2 ON table_name(col2);
CREATE INDEX idx_col3 ON table_name(col3);
-- ... 更多单列索引

-- 正确做法：根据查询模式创建复合索引
-- 分析实际查询：
-- SELECT * FROM table_name WHERE col1 = ? AND col2 = ?; (80%的查询)
-- SELECT * FROM table_name WHERE col1 = ?; (15%的查询)
-- SELECT * FROM table_name WHERE col3 = ?; (5%的查询)

-- 创建合理的索引
CREATE INDEX idx_col1_col2 ON table_name(col1, col2);  -- 覆盖主要查询
CREATE INDEX idx_col3 ON table_name(col3);             -- 覆盖少数查询
```

### 2. 忽略索引维护成本

```sql
-- 监控索引对写操作的影响
-- 查看索引更新统计
SELECT 
    table_name,
    index_name,
    count_insert,
    count_update,
    count_delete
FROM performance_schema.table_io_waits_summary_by_index_usage
WHERE object_schema = 'your_database'
    AND (count_insert > 0 OR count_update > 0 OR count_delete > 0)
ORDER BY (count_insert + count_update + count_delete) DESC;

-- 定期清理无用索引
-- 1. 备份索引定义
SELECT CONCAT('CREATE INDEX ', index_name, ' ON ', table_name, ' (', 
    GROUP_CONCAT(column_name ORDER BY seq_in_index), ');') AS create_statement
FROM information_schema.statistics
WHERE table_schema = 'your_database' AND index_name = 'unused_index_name'
GROUP BY table_name, index_name;

-- 2. 删除无用索引
DROP INDEX unused_index_name ON table_name;

-- 3. 监控性能变化
```

### 3. 过度依赖索引提示

```sql
-- 避免过度使用索引提示
-- 不推荐：强制使用特定索引
SELECT * FROM orders FORCE INDEX (idx_customer_date)
WHERE customer_id = 123 AND order_date > '2024-01-01';

-- 推荐：让优化器自动选择
SELECT * FROM orders 
WHERE customer_id = 123 AND order_date > '2024-01-01';

-- 只在确实需要时使用提示
-- 例如：优化器选择错误的索引时
SELECT * FROM large_table USE INDEX (idx_specific)
WHERE complex_condition = 'value';
```

## 总结

索引使用规则是数据库性能优化的关键，主要包括：

1. **最左前缀原则**：复合索引必须从最左列开始使用
2. **避免索引失效**：不在索引列上使用函数、计算或类型转换
3. **合理设计复合索引**：考虑查询模式和列的选择性
4. **利用覆盖索引**：减少回表操作提高查询效率
5. **监控和维护**：定期分析索引使用情况，清理无用索引

理解和正确应用这些规则，可以显著提升数据库查询性能，同时避免不必要的存储开销和维护成本。

# 索引设计原则

1. 针对于数据量较大，且查询比较频繁的表建立索引。
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引。
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。
4. 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。

