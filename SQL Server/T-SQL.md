# T-SQL概述

## 什麼是T-SQL

## T-SQL和標準SQL的區別

## T-SQL的基本語法欄位表格

## 查詢

### SELECT

`SELECT` 語句用於從資料庫中選擇數據。它允許指定要從哪個資料表中檢索哪些欄位，並可以通過添加條件來過濾結果。

>**如果不指定欄位則使用`*`符號**

* 格式  

```SQL
SELECT "欄位名" FROM "資料表名稱";
 ```

* 範例
  
```SQL
SELECT * FROM Employees; --撈出所有欄位
SELECT FirstName FROM Employees;--撈出FirstName欄位
SELECT FirstName,LastName FROM Employees;--撈出FirstName與LastName欄位

```

### DISTINCT

`SQL DISTINCT` 關鍵字用於從查詢結果中返回唯一的不重複值，排除所有重複項。

* 格式

```SQL
SELECT DISTINCT "欄位名" FROM "資料表名稱";
```

* 範例
  
```SQL
--列出 `Customers` 表中所有不同的國家。
SELECT DISTINCT Country FROM Customers;
```

### FROM

它用於指定 `SELECT`, `UPDATE`, `DELETE` 等 SQL 語句中指定的資料表名稱。當需要從資料庫中提取數據時，`FROM` 是在告知 SQL 要操作資料表。

* 格式
  
```SQL
SELECT "欄位名" FROM "資料表名稱";
```

* 範例
  
```SQL
--選擇Customers資料表。
SELECT * FROM Customers;

--在您提供的 SQL 查詢中，目前使用的是老式的隱式聯接語法，其中 WHERE 用於指定聯接條件。為了使用現代的 SQL 語法，我們可以用 JOIN 關鍵字來明確地表達這個聯接。這不僅使查詢更易於理解，而且是當前推薦的做法。
SELECT A1.Region_Name REGION, SUM(A2.Sales) SALES
FROM Geography A1, Store_Information A2
WHERE A1.Store_Name = A2.Store_Name
GROUP BY A1.Region_Name;

```

------

>當使用 `SELECT * FROM Orders, Customers` 查詢而不指定聯接條件時，將產生 `Orders` 和 `Customers` 表的笛卡爾積。  

#### Orders資料表

| OrderID | CustomerID | OrderDate  |
| ------- | ---------- | ---------- |
| 1       | C001       | 2021-01-01 |
| 2       | C002       | 2021-01-02 |

#### Customers 資料表

| CustomerID | CustomerName | City        |
| ---------- | ------------ | ----------- |
| C001       | John Doe     | New York    |
| C003       | Jane Smith   | Los Angeles |

#### SQL 查詢

```SQL
SELECT * FROM Orders, Customers;
```

#### 笛卡爾積結果

>該查詢將產生以下的笛卡爾積結果，其中 Orders 表中的每一行與 Customers 表中的每一行進行配對  

| OrderID | CustomerID (from Orders) | OrderDate  | CustomerID (from Customers) | CustomerName | City        |
| ------- | ------------------------ | ---------- | --------------------------- | ------------ | ----------- |
| 1       | C001                     | 2021-01-01 | C001                        | John Doe     | New York    |
| 1       | C001                     | 2021-01-01 | C003                        | Jane Smith   | Los Angeles |
| 2       | C002                     | 2021-01-02 | C001                        | John Doe     | New York    |
| 2       | C002                     | 2021-01-02 | C003                        | Jane Smith   | Los Angeles |

### WHERE
  
用於設定過濾條件，從而精確控制查詢結果中包含哪些數據。可以基於特定條件對數據進行篩選，如比較、範圍測試和模式匹配。

* 格式
  
```SQL
SELECT "欄位名" FROM "表格名" WHERE "條件";
```

#### 比較運算符 （=, <, >, !=）

"條件" 可以加上其他規則

```SQL
--選擇 Employees 表中部門為 'Sales' 的所有員工
SELECT * FROM Employees WHERE Department = 'Sales';

--選擇 Products 表中價格小於 20 的所有產品
SELECT * FROM Products WHERE Price < 20;

--選擇 Orders 表中數量大於 100 的所有訂單
SELECT * FROM Orders WHERE Quantity > 100;

--選擇 Customers 表中不在美國的所有客戶
SELECT * FROM Customers WHERE Country != 'USA';
```

#### 邏輯運算（AND, OR, NOT）

```SQL
--選擇 Orders 表中訂單日期在 2021年1月1日或之後且總金額超過 500 的所有訂單
SELECT * FROM Orders WHERE OrderDate >= '2021-01-01' AND TotalAmount > 500;

--選擇 Customers 表中城市為柏林或慕尼黑的所有客戶
SELECT * FROM Customers WHERE City = 'Berlin' OR City = 'Munich';

--選擇 Products 表中類別不是飲料的所有產品
SELECT * FROM Products WHERE NOT Category = 'Beverages';
```

#### LIKE 模糊運算

`%`: 代表任意數量（包括零個）的任意字符。  
`_`: 代表任何單個字符。  
`[ ]`: 來指定一個特定的字元範圍或集合。  
`[^]`: 匹配任何不在方括號內的字元。

```SQL
--選擇 Customers 表中客戶名稱包含 'Smith' 的所有客戶
SELECT * FROM Customers WHERE CustomerName LIKE '%Smith%';

--選擇 Products 表中產品名稱的第三個和第四個字符分別是 'a' 和 'e' 的所有產品（例如 'Cater'、'Caber'）
SELECT * FROM Products WHERE ProductName LIKE 'Ca_er';

--選擇 Employees 表中姓氏以 'S' 開頭並以 'th' 結尾的所有員工
SELECT * FROM Employees WHERE LastName LIKE 'S%th';

--選擇 Employees 表中名字以 'A' 或 'B' 開頭的員工
SELECT * FROM Employees WHERE FirstName LIKE '[AB]%';

--選擇 Customers 表中城市名以 'A'、'B' 或 'C' 開頭的城市的客戶
SELECT * FROM Customers WHERE City LIKE '[A-C]%';

--選擇 Products 表中產品名的第一個字元在 'A' 到 'C' 之間，且第二個字元在 'n' 到 'p' 之間的產品
SELECT * FROM Products WHERE ProductName LIKE '[A-C][n-p]%';

--選擇 Employees 表中名字不以 'A' 或 'B' 開頭的員工
SELECT * FROM Employees WHERE FirstName LIKE '[^AB]%';

--選擇 Customers 表中城市名不以 'A'、'B' 或 'C' 開頭的城市的客戶
SELECT * FROM Customers WHERE City LIKE '[^A-C]%';

--選擇 Products 表中產品名的第一個字元不在 'A' 到 'C' 之間，且第二個字元不在 'n' 到 'p' 之間的產品
SELECT * FROM Products WHERE ProductName LIKE '[^A-C][^n-p]%';
```

### BETWEEN

BETWEEN 語法用於指定一個範圍。這個語法既可以用於數字，也可以用於日期和文字等資料類型。

* 格式

```SQL
-- '值1' 和 '值2' 指定了範圍的開始和結束
SELECT "欄位名" FROM "表格名" WHERE "欄位名" BETWEEN '值1' AND '值2';
```

```SQL
--數字範圍
--Products 表中價格在 10 到 20 之間的產品
SELECT * FROM Products WHERE Price BETWEEN 10 AND 20;

--日期範圍
--訂單日期在 2021 年 1 月 1 日到 2021 年 12 月 31 日之間的訂單
SELECT * FROM Orders WHERE OrderDate BETWEEN '2021-01-01' AND '2021-12-31';

--文字範圍
--姓氏開頭字母在 'A' 到 'M' 之間的員工
SELECT * FROM Employees WHERE LastName BETWEEN 'A' AND 'M';
```

#### BETWEEN注意事項  

1. BETWEEN 語法包括其範圍的兩個邊界值。  
2. 在使用 BETWEEN 與日期類型數據時，確保日期格式與資料庫中的格式相匹配。  
3. 對於文字類型，BETWEEN 是按照字母順序比較。

### SQL 函数

函数是 SQL 查詢中用於執行特定操作的內建方法。  
這些函數可以在選擇、計算和處理數據時提供功能

1. 聚合函數（Aggregate Functions）  
    用於數值計算，並返回計算後的單一值。
    1. AVG()：計算平均值。
    2. COUNT()：總數量。
    3. MAX()：找到最大值。
    4. MIN()：找到最小值。
    5. SUM()：計算總和。

    ```SQL
    -- 計算平均價格
    SELECT AVG(Price) FROM Products;
    -- 計算總數
    SELECT COUNT(*) FROM Orders;
    -- 查找最高價格
    SELECT MAX(Price) FROM Products;
    -- 查找最低價格
    SELECT MIN(Price) FROM Products;
    -- 計算價格總和
    SELECT SUM(Price) FROM Products;
    ```

2. 字串函數（String Functions）
   用於處理文字。
   1. LENGTH()：返回文字的長度。
   2. LOWER() 或 LCASE()：將文字轉為小寫。
   3. UPPER() 或 UCASE()：將文字轉為大寫。
   4. CONCAT()：連接兩個或多個文字。
   5. SUBSTRING()：從文字中擷取指定文字。

    ```SQL
    -- 返回字串的長度
    SELECT LENGTH(CustomerName) FROM Customers;
    -- 將字串轉為小寫
    SELECT LOWER(FirstName) FROM Employees;
    -- 將字串轉為大寫
    SELECT UPPER(LastName) FROM Employees;
    -- 連接兩個字串
    SELECT CONCAT(FirstName, ' ', LastName) FROM Employees;
    -- 從字串中擷取指定文字
    SELECT SUBSTRING(Description, 1, 10) FROM Products;
    ```

3. 日期函數（Date Functions）
   用於處理日期和時間數據。
   1. NOW()：返回當前日期和時間。
   2. CURDATE()：返回當前日期。
   3. DAY()：返回日期的日部分。
   4. MONTH()：返回日期的月部分。
   5. YEAR()：返回日期的年部分。

    ```SQL
    -- 返回當前日期和時間
    SELECT NOW();
    -- 返回當前日期
    SELECT CURDATE();
    -- 返回日期的日部分
    SELECT DAY(OrderDate) FROM Orders;
    -- 返回日期的月部分
    SELECT MONTH(OrderDate) FROM Orders;
    -- 返回日期的年部分
    SELECT YEAR(OrderDate) FROM Orders;
    ```

4. 數學函數（Mathematical Functions）
   用於進行數學計算。
   1. ABS()：返回數字的絕對值。
   2. CEILING()：無條件進位。
   3. FLOOR()：無條件捨去。
   4. RAND()：生成隨機數。
   5. ROUND()：四捨五入。

    ```SQL
    -- 返回數字的絕對值
    SELECT ABS(-123.45);
    -- 無條件進位
    SELECT CEILING(123.45);
    -- 無條件捨去
    SELECT FLOOR(123.45);
    -- 生成隨機數
    SELECT RAND();
    -- 四捨五入
    SELECT ROUND(123.4567, 2);
    ```

5. 邏輯函數（Logical Functions）
   用於執行邏輯運算。
   1. COALESCE()：返回第一個非 NULL 值。
   2. IFNULL() 或 ISNULL()：如果第一個表達式為 NULL，則返回第二個表達式的值。
   3. NULLIF()：如果兩個表達式相等，則返回 NULL。

    ```SQL
    -- 從參數列表中返回第一個非 NULL 值
    SELECT COALESCE(Price, 0) FROM Products;
    -- 如果第一個表達式為 NULL，則返回第二個表達式的值
    SELECT IFNULL(Description, 'No Description') FROM Products;
    -- 如果兩個表達式相等，則返回 NULL
    SELECT NULLIF(Volume, 0) FROM Sales;
    ```

### ORDER BY

`ORDER BY` 語法在 SQL 中用於對查詢結果進行排序。您可以根據一個或多個列進行排序，並指定是按升序 (`ASC`) 還是降序 (`DESC`) 排序。如果不指定，預設為升序排序。

* 格式

```SQL
SELECT "欄位名" FROM "表格名" ORDER BY "欄位名" [ASC|DESC];
```

* 範例

```SQL
--查詢按照 LastName 列升序排序 Customers 表中的數據
SELECT * FROM Customers ORDER BY LastName ASC;

--查詢按照 OrderDate 列降序排序 Orders 表中的數據
SELECT * FROM Orders ORDER BY OrderDate DESC;

--先按 Department 列升序排序，然後在每個部門內按 Salary 列降序排序 Employees 表中的數據
SELECT * FROM Employees ORDER BY Department ASC, Salary DESC;
```

#### ORDER BY注意事項  

* ORDER BY 通常用於查詢的最後部分。
* 根據需要對多個列進行排序，排序會按照列在 ORDER BY 中出現的順序進行。
* 在龐大的資料中，過多的排序操作可能會影響查詢性能。

### GROUP BY

`GROUP BY` 將查詢結果集中的行分組，通常與聚合函數（COUNT, MAX, MIN, SUM, AVG 等）一起使用。  
當您使用 `GROUP BY` 時，資料將根據一個或多個列被分組，並為每個組計算聚合值。

* 格式

```SQL
SELECT "欄位名", "聚合函數(欄位名)" FROM "表格名" GROUP BY "欄位名";
```

* 範例  
  
```SQL
--計算 Employees 表中每個部門的員工數量
SELECT Department, COUNT(*) FROM Employees GROUP BY Department;

--計算 Employees 表中每個部門員工薪資的平均值
SELECT Department, AVG(Salary) FROM Employees GROUP BY Department;

--查詢按產品類別和品牌分組，並計算每個組合的銷售總額
SELECT Category, Brand, SUM(Sales) FROM Products GROUP BY Category, Brand;
```

#### GROUP BY注意事項

* GROUP BY 中列出的每個欄位也必須在 SELECT 語句中出現，除非它們被用於聚合函數。  
* GROUP BY 通常與 ORDER BY 一起使用以改善結果的可讀性。

### HAVING

`HAVING` 用於指定 GROUP BY 分組後的結果進行過濾的條件。  
與 WHERE 不同，HAVING 是用於過濾聚合函數的結果，例如 COUNT, SUM, AVG 等。

* 格式

```SQL
SELECT "欄位名", "聚合函數(欄位名)" FROM "表格名" GROUP BY "欄位名" HAVING "聚合條件";
```

* 範例  
  
```SQL
--計算 Employees 表中每個部門的員工數量，並只選擇員工數量超過 10 的部門
SELECT Department, COUNT(*) FROM Employees GROUP BY Department HAVING COUNT(*) > 10;

--計算每個客戶的訂單總金額，並只選擇訂單總金額超過 5000 的客戶
SELECT CustomerID, SUM(OrderAmount) FROM Orders GROUP BY CustomerID HAVING SUM(OrderAmount) > 5000;


--首先使用 WHERE 過濾出 2021 年的銷售數據，然後按銷售人員分組，最後只選擇那些年銷售額超過 100000 的銷售人員
SELECT Salesperson, SUM(Sales) FROM SalesData WHERE Year = 2021 GROUP BY Salesperson HAVING SUM(Sales) > 100000;
```

#### HAVING注意事項

* HAVING 必須跟在 GROUP BY 之後。
* 在 HAVING 中不能使用未在 GROUP BY 中指定的列，除非這些列包含在聚合函數中。
* HAVING 可以單獨使用，但通常與 GROUP BY 一起使用。

### 别名(Alias)/AS

* Alias（別名）  
  1. 是指給表或列取的另一個名稱，讓查詢中更易於引用。特別是當表名或列名很長、複雜或不直觀時。
  2. 別名僅在該查詢中有效，並不會改變數據庫中的實際表名或列名。
  3. 別名可以用於簡化表名，特別是在表與表的聯接操作時，或者用於提供列的更有意義的名稱。

* AS 關鍵字  
  1. `AS` 是 SQL 中用於指定別名的關鍵字。
  2. 雖然許多情況下使用 `AS` 是可選的，可以使查詢的意圖更清晰。
  3. 在某些 SQL 語法中，使用`AS`是建立別名的必要語法。

```SQL
--e 是 Employees 表的別名，沒有使用 AS
SELECT FirstName, LastName FROM Employees e;

--FName 和 LName 是列 FirstName 和 LastName 的別名，使用了 AS 關鍵字
SELECT FirstName AS FName, LastName AS LName FROM Employees;
```

#### 别名(Alias)/AS注意事項

* 別名是一種給表或列重命名的方法，而 `AS` 是在 SQL 中創建這些別名的標準方式之一。  
* 不同數據庫系統可能對 `AS` 的使用有不同的要求和慣例

### JOIN

`JOIN` 語法在 SQL 中用於結合兩個或多個表的行。  
它根據兩個表中的共同字段（通常是主鍵和外鍵關係）來創建新的結果集。  
`JOIN` 的類型包括 `INNER JOIN`、`LEFT JOIN`、`RIGHT JOIN` 和 `FULL JOIN`，每種類型根據條件不同的方式結合表格。

* 格式

```SQL
SELECT columns FROM table1 JOIN table2 ON table1.column_name = table2.column_name;
```

#### JOIN 的類型

#### INNER JOIN  

只返回兩個表中匹配條件的行。

```SQL
SELECT Orders.OrderID, Customers.CustomerName
FROM Orders
INNER JOIN Customers 
ON Orders.CustomerID = Customers.CustomerID;
```

>**返回那些在 Orders 和 Customers 表中都有記錄的訂單 ID 和客戶名稱**

------

#### LEFT JOIN (LEFT OUTER JOIN)

返回左表（table1）的所有行，即使右表（table2）中沒有匹配。如果右表中沒有匹配，結果中右表的部分將為 NULL。

```SQL
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
LEFT JOIN Departments 
ON Employees.DepartmentID = Departments.DepartmentID;
```

>*返回所有員工的名字，以及他們所屬部門的名稱。即使某些員工沒有分配到部門（即 Departments 表中沒有對應記錄），他們的名字仍會顯示，部門名稱則為 NULL*

------

#### RIGHT JOIN (RIGHT OUTER JOIN)

返回右表的所有行，即使左表中沒有匹配。如果左表中沒有匹配，結果中左表的部分將為 NULL。

```SQL
SELECT Orders.OrderID, Customers.CustomerName
FROM Orders
RIGHT JOIN Customers 
ON Orders.CustomerID = Customers.CustomerID;
```

>*返回所有客戶的名字，以及他們的訂單 ID。如果某些客戶沒有訂單（即 Orders 表中沒有對應記錄），則訂單 ID 會為 NULL*

------

#### FULL JOIN (FULL OUTER JOIN)

返回兩個表中有匹配的行，以及左表和右表中沒有匹配的行。

```SQL
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
FULL JOIN Departments 
ON Employees.DepartmentID = Departments.DepartmentID;
```

>*返回所有員工和所有部門的名字，包括那些沒有對應部門的員工以及沒有員工的部門*

#### JOIN注意事項

* 在實際應用中，選擇的 `JOIN` 類型取決於您的數據結構和預期獲得的查詢結果。

## 進階查詢

### 子查詢

### CTE

### CONCATENATE

### SQL SUBSTRING

### TRIM

### LENGTH

### REPLACE

### CREATE TABLE

### Constraint

### 主键

### 外键

### CREATE VIEW

### CREATE INDEX

### ALTER TABLE

### DROP TABLE

### TRUNCATE TABLE

### INSERT INTO

### UPDATE

### DELETE FROM

### UNION

### UNION ALL

### INTERSECT

### EXISTS

### CASE
