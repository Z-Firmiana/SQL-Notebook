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
