```sql
# 没有去重前107条记录
SELECT department_id FROM atguigudb.employees;
# 使用DISTINCT 去重后12 条记录，包含null值
SELECT DISTINCT department_id FROM atguigudb.employees;
# 分别对两个或多个字段进行去重，两个都重复才会被去重
SELECT DISTINCT department_id, salary FROM atguigudb.employees e ;

# 空置参与运算的场景
# 1、空值：null
# 2、null不等同于 0 ，'','null'.
SELECT * FROM employees;
# 3、空值只要参与运算（未做处理），结果也一定为空
SELECT e.employee_id , e.salary , e.salary  * (1 + e.commission_pct) * 12 "年工资", e.commission_pct  FROM atguigudb.employees e;
# ifnull 如果commission_pct不是null的话 就拿该值计算，如果是null 就拿0来替换
SELECT e.employee_id , e.salary , e.salary  * (1 + ifnull(e.commission_pct, 0)) * 12 "年工资", e.commission_pct  FROM atguigudb.employees e;

# 显示表结构 DESCRIBE 与 DESC等效
DESCRIBE atguigudb.employees ;  # 显示表中字段的详细信息
DESC atguigudb.employees ;



# 多表查询 案例：查询员工的姓名及其部门名称
SELECT last_name, department_name FROM employees, departments; # 输出 107 * 27 = 2889 条记录
# 出现以上的错误我们称之为：笛卡尔积的错误
SELECT count(last_name) FROM employees; # 107条
SELECT count(department_name) FROM  departments; # 27条
SELECT 107 * 27 FROM DUAL;

# 查询员工姓名和所在部门名称
SELECT last_name,department_name FROM employees,departments;
SELECT last_name,department_name FROM employees CROSS JOIN departments;
SELECT last_name,department_name FROM employees INNER JOIN departments;
SELECT last_name,department_name FROM employees JOIN departments;

SELECT * FROM atguigudb.employees e , atguigudb.departments d WHERE e.department_id = d.department_id 
SELECT e.last_name, e.department_id, d.department_name,d.department_id  FROM atguigudb.employees e , atguigudb.departments d WHERE e.department_id = d.department_id 

SELECT
    employees.employee_id,
    employees.last_name,
    employees.department_id,
    departments.department_id,
    departments.location_id
FROM
    employees,
    departments
WHERE
    employees.department_id = departments.department_id;

SELECT * FROM employees worker;
SELECT CONCAT(worker.last_name ,' works for ', manager.last_name) FROM employees worker, employees manager WHERE worker.manager_id = manager.employee_id 

# 练习：查询出last_name为 ‘Chen’ 的员工的 manager 的信息。
SELECT manager.* FROM employees worker, employees manager WHERE worker.manager_id = manager.employee_id AND worker.last_name = 'Chen';


# SQL92左外连接
SELECT last_name,department_name
FROM employees ,departments
WHERE employees.department_id = departments.department_id(+);

# SQL92右外连接
SELECT last_name,department_name
FROM employees ,departments
WHERE employees.department_id(+) = departments.department_id;

# 内连接(INNER JOIN)的实现
SELECT
    e.employee_id,
    e.last_name,
    e.department_id,
    d.department_id,
    d.location_id
FROM
    employees e
INNER JOIN departments d ON (e.department_id = d.department_id);


# 
SELECT
    employee_id,
    city,
    department_name
FROM
    employees e
JOIN departments d
ON
    d.department_id = e.department_id
JOIN locations l
ON
    d.location_id = l.location_id;



SELECT  * FROM atguigudb.countries c ;


SELECT
    *
FROM
    employees e
LEFT JOIN departments d ON
    e.department_id = d.department_id
LEFT JOIN locations l ON
    d.location_id = l.location_id
WHERE
    e.last_name = 'Abel' ;

SELECT  * FROM departments d WHERE 1=2;

# 创建表：复制departments表结构及数据 然后创建一张departments_copy且带数据
CREATE TABLE departments_copy AS SELECT * FROM departments;

DESC departments_copy;
-- 27条数据
SELECT * FROM departments_copy;

-- 清空表: TRUNCATE语句不能回滚，而使用 DELETE 语句删除数据，可以回滚
truncate TABLE departments_copy;
DELETE FROM  departments_copy;

-- 删除表
DROP TABLE departments_copy;
DROP TABLE IF EXISTS departments_copy;

COMMIT;
# 关闭自动提交
SET autocommit = FALSE;

# 回滚操作：回滚到最近一次commit之后
ROLLBACK;


# 如何创建视图 view
CREATE DATABASE dbtest14;
USE dbtest14;

# 快速创建（复制表结构和数据）表
CREATE TABLE emps AS SELECT * FROM atguigudb.employees e ;

SELECT * FROM dbtest14.emps;
DESC dbtest14.emps;

CREATE TABLE depts AS SELECT * FROM atguigudb.departments d;
SELECT * FROM dbtest14.depts;

# 针对单表

# 创建视图
CREATE VIEW vu_emp1 AS SELECT employee_id,  last_name,  salary FROM emps ;

SELECT * FROM vu_emp1;




DECLARE 
    num nummber;
BEGIN 
    SELECT count(1) INTO num FROM all_tables WHERE TABLE_NAME = 'credit_black_list_credit_limit_info_temp' AND owner = 'credit';
    IF num = 1 then
        EXECUTE IMMEDIATE 'drop table credit.credit_black_list_credit_limit_info_temp';
    end IF;
    
END
-- CREATE table 创建临时表（普通表）
CREATE TABLE credit.credit_black_list_credit_limit_info_temp(
    -- 建表语句
)


-- 数据拷贝：
DECLARE 
    num number;
BEGIN
    SELECT count(1) INTO num FROM all_tables  WHERE TABLE_NAME = 'credit_black_list_credit_limit_info' AND  owner = 'credit'; 
    IF  num = 1 THEN
        EXECUTE IMMEDIATE 'insert /*+parallel 8*/ into credit.credit_black_list_credit_limit_info_temp (USER_ID, USER_NAME, CERT_NO, ...省略表字段..., PRODUCT_ID) 
        select /* +parallel 8*/ USER_ID, USER_NAME, CERT_NO, ...省略表字段..., PRODUCT_ID from credit.credit_black_list_credit_limit_info'; 
    END if
END;
/




-- 修改表明
ALTER TABLE credit.credit_black_list_credit_limit_info RENAME TO credit_black_list_credit_limit_info_bak0908;



-- 重建用信管控分区表，用来存放存量数据
DECLARE 
    num number;
BEGIN 
    SELECT count(1) INTO num FROM all_tables WHERE TABLE_NAME = 'credit_black_list_credit_limit_info' AND owner = 'credit';
    IF num = 1 then
        EXECUTE IMMEDIATE 'drop table credit.credit_black_list_credit_limit_info';
    end IF;
END
-- credit table
CREATE TABLE credit.credit_black_list_credit_limit_info(

    -- 建表语句
    credit_time date NOT NULL,
)

PARTITION BY RANGE (create_time) (
    PARTITION PART_DEFAULT VALUES less THEN (TO_DATE('2022-08-10 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))
    tablespace CREATE 
    pctfree 10
    initrans 1
    maxtrans 255
    storage (
        initial 64k
        NEXT 1M
        minextents 1
        maxextents unlimites
    )
)
-- Add comments to the COLUMNS 


-- 重建用信管控分区表，用来存放存量数据
DECLARE 
    num number;
BEGIN
    SELECT count(1) info num FROM all_tab_partitions WHERE TABLE_NAME = 'credit_black_list_credit_limit_info' AND TABLE_OWNER = 'credit' AND PARTITION_NAME = 'PARTITION_CREATE_TIME_20220907';
    IF NUM = 1 THEN
        EXECUTE IMMEDIATE 'ALTER TABLE CREDIT.credit_black_list_credit_limit_info DORP PARTITION PARTITION_CREATE_TIME_20220907';        
    END IF
    
END

-- 创建分区
ALTER TABLE CREDIT.credit_black_list_credit_limit_info ADD PARTITION PARTITION_CREATE_TIME_20220907 VALUES less THEN (TO_DATE('2022-08-10 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) tablespace credit;



-- 交换分区：将TEMP临时表中的数据统一添加到 PARTITION_CREATE_TIME_20220907 分区中保存
ALTER TABLE CREDIT.credit_black_list_credit_limit_info exchange PARTITION PARTITION_CREATE_TIME_20220907 WITH TABLE CREDIT.credit_black_list_credit_limit_info_temp;


SELECT concat("WYD", e.employee_id) a , e.* FROM atguigudb.employees e   WHERE a =  'WYD101';


SELECT * FROM emps e .;
SELECT d.department_id dept_id , e.job_id, sum(e.salary) FROM atguigudb.employees e, atguigudb.departments d WHERE e.employee_id = d.department_id  GROUP BY d.department_id ,e.job_id HAVING count(1) > 1; 


```

