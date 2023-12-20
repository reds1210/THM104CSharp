# T-SQL概述

T-SQL（Transact-SQL）是SQL（Structured Query Language）的一個擴展，主要用於與Microsoft SQL Server。  
它增加了一些在標準SQL中不存在的功能，比如交易控制、異常處理處理等。

## 特點

1. **交易控制** - T-SQL提供了交易管理能力，允許使用者定義交易的開始和結束，確保資料的一致性和完整性。

2. **異常處理** - 透過TRY...CATCH塊，T-SQL可以有效地處理運行時錯誤，提高資料庫語法的健壯性。

3. **內建函數** - T-SQL包含大量的內建函數，涵蓋了字符串處理、數學計算、日期和時間處理等。

4. **程式化控制** - 包括變數、流程控制語句（如IF...ELSE、WHILE）和預存程序（如函數、觸發器）。

5. **聚合函數** - 對數據集的子集執行計算，如行數、平均值、總和等。

6. **CTE和臨時表** - 提供了處理複雜查詢和改善查詢組織的方式。

7. **索引和查詢優化** - T-SQL提供了強大的索引和查詢優化工具，幫助改進數據檢索的性能。

## T-SQL和標準SQL的區別

## T-SQL的基本語法欄位表格

## 查詢

### SELECT

`SELECT` 語句用於從資料庫中選擇數據。它允許指定要從哪個資料表中檢索哪些欄位，並可以通過添加條件來過濾結果。

>**如果不指定欄位則使用`*`符號**

#### **格式**  

```SQL
SELECT "欄位名" FROM "資料表名稱";
 ```

#### **範例**
  
```SQL
SELECT * FROM Employees; --撈出所有欄位
SELECT FirstName FROM Employees;--撈出FirstName欄位
SELECT FirstName,LastName FROM Employees;--撈出FirstName與LastName欄位

```

### DISTINCT

`SQL DISTINCT` 關鍵字用於從查詢結果中返回唯一的不重複值，排除所有重複項。

#### **格式**

```SQL
SELECT DISTINCT "欄位名" FROM "資料表名稱";
```

#### **範例**
  
```SQL
--列出 `Customers` 表中所有不同的國家。
SELECT DISTINCT Country FROM Customers;
```

### FROM

它用於指定 `SELECT`, `UPDATE`, `DELETE` 等 SQL 語句中指定的資料表名稱。當需要從資料庫中提取數據時，`FROM` 是在告知 SQL 要操作資料表。

#### **格式**
  
```SQL
SELECT "欄位名" FROM "資料表名稱";
```

#### **範例**
  
```SQL
--選擇Customers資料表。
SELECT * FROM Customers;
```

------

>當使用 `SELECT * FROM Orders, Customers` 查詢而不指定聯接條件時，將產生 `Orders` 和 `Customers` 表的笛卡爾積。  

#### **笛卡爾積**

* Orders資料表

  | OrderID | CustomerID | OrderDate  |
  | ------- | ---------- | ---------- |
  | 1       | C001       | 2021-01-01 |
  | 2       | C002       | 2021-01-02 |

* Customers 資料表

  | CustomerID | CustomerName | City        |
  | ---------- | ------------ | ----------- |
  | C001       | John Doe     | New York    |
  | C003       | Jane Smith   | Los Angeles |

* SQL 查詢

  ```SQL
  SELECT * FROM Orders, Customers;
  ```

* 笛卡爾積結果

  >該查詢將產生以下的笛卡爾積結果，其中 Orders 表中的每一行與 Customers 表中的每一行進行配對  

  | OrderID | CustomerID (from Orders) | OrderDate  | CustomerID (from Customers) | CustomerName | City        |
  | ------- | ------------------------ | ---------- | --------------------------- | ------------ | ----------- |
  | 1       | C001                     | 2021-01-01 | C001                        | John Doe     | New York    |
  | 1       | C001                     | 2021-01-01 | C003                        | Jane Smith   | Los Angeles |
  | 2       | C002                     | 2021-01-02 | C001                        | John Doe     | New York    |
  | 2       | C002                     | 2021-01-02 | C003                        | Jane Smith   | Los Angeles |

### WHERE
  
用於設定過濾條件，從而精確控制查詢結果中包含哪些數據。可以基於特定條件對數據進行篩選，如比較、範圍測試和模式匹配。

#### **格式**
  
```SQL
SELECT "欄位名" FROM "表格名" WHERE "條件";
```

#### **範例**

* 比較運算符 （=, <, >, !=）

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

* 邏輯運算（AND, OR, NOT）

  ```SQL
  --選擇 Orders 表中訂單日期在 2021年1月1日或之後且總金額超過 500 的所有訂單
  SELECT * FROM Orders WHERE OrderDate >= '2021-01-01' AND TotalAmount > 500;

  --選擇 Customers 表中城市為柏林或慕尼黑的所有客戶
  SELECT * FROM Customers WHERE City = 'Berlin' OR City = 'Munich';

  --選擇 Products 表中類別不是飲料的所有產品
  SELECT * FROM Products WHERE NOT Category = 'Beverages';
  ```

* LIKE 模糊運算

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

#### **格式**

```SQL
-- '值1' 和 '值2' 指定了範圍的開始和結束
SELECT "欄位名" FROM "表格名" WHERE "欄位名" BETWEEN '值1' AND '值2';
```

#### **範例**

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

`ORDER BY` 語法用於對查詢結果進行排序。可以根據一個或多個列進行排序，並指定升序 (`ASC`) 還是降序 (`DESC`) 排序。  
如果不指定，預設為升序排序。

#### **格式**

```SQL
SELECT "欄位名" FROM "表格名" ORDER BY "欄位名" [ASC|DESC];
```

#### **範例**

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
當使用 `GROUP BY` 時，資料會根據一個或多個列被分組，並可以對每個組計算聚合值。

#### **格式**

```SQL
SELECT "欄位名", "聚合函數(欄位名)" FROM "表格名" GROUP BY "欄位名";
```

#### **範例**
  
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

`HAVING` 用於指定 `GROUP BY` 分組後的結果再進行過濾的條件。  
與 `WHERE` 不同， `HAVING` 是用於過濾聚合函數的結果，例如 `COUNT` , `SUM` , `AVG` 等。

#### **格式**

```SQL
SELECT "欄位名", "聚合函數(欄位名)" FROM "表格名" GROUP BY "欄位名" HAVING "聚合條件";
```

#### **範例**
  
```SQL
--計算 Employees 表中每個部門的員工數量，並只選擇員工數量超過 10 的部門
SELECT Department, COUNT(*) FROM Employees GROUP BY Department HAVING COUNT(*) > 10;

--計算每個客戶的訂單總金額，並只選擇訂單總金額超過 5000 的客戶
SELECT CustomerID, SUM(OrderAmount) FROM Orders GROUP BY CustomerID HAVING SUM(OrderAmount) > 5000;


--首先使用 WHERE 過濾出 2021 年的銷售數據，然後按銷售人員分組，最後只選擇那些年銷售額超過 100000 的銷售人員
SELECT Salesperson, SUM(Sales) FROM SalesData WHERE Year = 2021 GROUP BY Salesperson HAVING SUM(Sales) > 100000;
```

#### HAVING注意事項

* `HAVING` 必須跟在 `GROUP BY` 之後。
* 在 `HAVING` 中不能使用未在 `GROUP BY` 中指定的列，除非這些列包含在聚合函數中。
* 有些資料庫可以單獨使用 `HAVING` ，但通常與 `GROUP BY` 一起使用。

### 别名(Alias)/AS

#### **Alias(別名)**  

  1. 是指給表或列取的另一個名稱，讓查詢中更易於引用。特別是當表名或列名很長、複雜或不直觀時。
  2. 別名僅在該查詢中有效，並不會改變數據庫中的實際表名或列名。
  3. 別名可以用於簡化表名，特別是在表與表的聯接操作時，或者用於提供列的更有意義的名稱。

#### **AS 關鍵字**  

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

`JOIN` 語法在 SQL 中用於結合兩個或多個資料表的行。  
它根據兩個資料表中的共同文字（通常是主鍵和外鍵關係）來創建新的結果集。  
`JOIN` 的類型包括 `INNER JOIN`、`LEFT JOIN`、`RIGHT JOIN` 和 `FULL JOIN`，每種類型根據條件不同的方式結合表格。

#### **格式**

```SQL
SELECT columns FROM table1 JOIN table2 ON table1.column_name = table2.column_name;
```

#### JOIN 的類型

#### INNER JOIN  

只返回兩個資料表中匹配條件的行。

```SQL
SELECT Orders.OrderID, Customers.CustomerName
FROM Orders
INNER JOIN Customers 
ON Orders.CustomerID = Customers.CustomerID;
```

>**返回那些在 Orders 和 Customers 表中都有記錄的訂單 ID 和客戶名稱**

------

#### LEFT JOIN (LEFT OUTER JOIN)

返回左資料表的所有行，即使右資料表中沒有匹配。  
如果右資料表中沒有匹配將為 NULL。

```SQL
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
LEFT JOIN Departments 
ON Employees.DepartmentID = Departments.DepartmentID;
```

>*返回所有員工的名字，以及他們所屬部門的名稱。即使某些員工沒有分配到部門（即 Departments 表中沒有對應記錄），他們的名字仍會顯示，部門名稱則為 NULL*

------

#### RIGHT JOIN (RIGHT OUTER JOIN)

返回右資料表的所有行，即使左資料表中沒有匹配。  
如果左資料表中沒有匹配將顯示為 NULL。

```SQL
SELECT Orders.OrderID, Customers.CustomerName
FROM Orders
RIGHT JOIN Customers 
ON Orders.CustomerID = Customers.CustomerID;
```

>*返回所有客戶的名字，以及他們的訂單 ID。如果某些客戶沒有訂單（即 Orders 表中沒有對應記錄），則訂單 ID 會為 NULL*

------

#### FULL JOIN (FULL OUTER JOIN)

返回兩個資料表中有匹配的行，以及左資料表和右資料表中沒有匹配的行。

```SQL
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
FULL JOIN Departments 
ON Employees.DepartmentID = Departments.DepartmentID;
```

>*返回所有員工和所有部門的名字，包括那些沒有對應部門的員工以及沒有員工的部門*

#### JOIN注意事項

* 在實際應用中，選擇的 `JOIN` 類型取決於您的數據結構和預期獲得的查詢結果。

## 進階

### 子查詢

子查詢是指嵌套在另一個查詢（主查詢）內的查詢。  
可以出現在 `SELECT` 、 `FROM` 或 `WHERE` 子句中，並且通常在方括號內執行。  

#### **格式**

```SQL
--SELECT內
SELECT column_name,
  (SELECT column_name 
   FROM table_name 
   WHERE condition) AS alias_name 
FROM table_name;

--FROM內
SELECT * 
FROM (SELECT column_name FROM table_name) AS subquery_name;

--WHERE內
SELECT * 
FROM table_name 
WHERE column_name IN 
(SELECT column_name 
FROM table_name 
WHERE condition);
```

#### **範例**  
  
在 `SELECT` 中的子查詢

```SQL
SELECT EmployeeID, (SELECT COUNT(*) FROM Orders WHERE Orders.EmployeeID = Employees.EmployeeID) AS NumberOfOrders
FROM Employees;
```

>*查詢為每個員工計算他們處理的訂單數量*

在 `FROM` 中的子查詢：

```SQL
SELECT AVG(subquery.Price) FROM (SELECT Price FROM Products WHERE CategoryID = 1) AS subquery;
```

>*這個查詢計算類別 1 中所有產品的平均價格*

在 `WHERE` 中的子查詢：

```SQL
SELECT * FROM Customers WHERE CustomerID IN (SELECT CustomerID FROM Orders WHERE OrderDate > '2021-01-01');
```

>*這個查詢選擇自 2021 年 1 月 1 日以後至少有一個訂單的所有客戶*

#### 子查詢注意事項

* 子查詢應該被括號包圍。  
* 在某些情況下，子查詢可以被公用表達式（`CTE`）或 `JOIN` 替代。
* 子查詢的返回結果通常應該是單列，特別是在用於 `WHERE` 時。

### CTE

### CONCATENATE

於將兩個或多個字符串字段或文字連接（合併）成一個字符串

#### **格式**

```SQL
  SELECT CONCAT(column1, column2, ...) FROM table_name;
```

#### **範例**

1. **連接兩個文串**：

   ```SQL
   SELECT CONCAT(FirstName, ' ', LastName) AS FullName 
   FROM Employees;
   ```

   將 `Employees` 表中的 `FirstName` 和 `LastName` 連接成一個完整的名稱，中間用空格分隔。

2. **連接多個字串和文字**：

   ```SQL
   SELECT CONCAT(Address, ', ', City, ', ', Country) AS FullAddress 
   FROM Customers;
   ```

   將 `Customers` 表中的 `Address`、`City` 和 `Country` 連接成一個完整的地址，每個元素之間用逗號和空格分隔。

#### CONCATENATE注意事項

* `CONCAT` 函數在不同的數據庫系統中可能存在一些變異，但基本功能是一致的。
* 在某些數據庫系統中，您也可以直接使用 `||` 運算符來進行字符串的連接操作，例如：`column1 || column2`。
* 如果任一連接的字串為 NULL，則該字串會被當作空字串處理。

### SUBSTRING

用於從字符串中擷取指定字串。

#### **格式**

```SQL
SELECT SUBSTRING(column_name, start, length) 
FROM table_name;
```

`column_name` 是要從中提取的字串，`start` 是起始位置（1 表示字串的第一個字），`length` 是要提取的數量。

#### **範例**

1. **從指定位置提取固定長度的字串**：

   ```SQL
   SELECT SUBSTRING(FirstName, 1, 3) AS ShortName 
   FROM Employees;
   ```

   從 `Employees` 表中每個員工的 `FirstName` 字段提取前三個字。

2. **從中間位置提取字串**：

   ```SQL
   SELECT SUBSTRING(PhoneNumber, 5, 3) AS AreaCode 
   FROM Customers;
   ```

   `Customers` 表中的 `PhoneNumber` 字串提取第 5 到第 7 個字作為條件。

#### SUBSTRING注意事項

* 在不同的數據庫系統中，`SUBSTRING` 函數的語法可能略有差異。例如，某些系統中使用 `SUBSTR` 作為函數名。
* 確保 `start` 和 `length` 參數在合法範圍內，以避免產生錯誤。

### TRIM

用於從字符串的兩端移除指定的字串，最常見的用途是刪除字串開頭和結尾的空格。

#### **格式**

1. **刪除兩端空格**：

   ```SQL
   SELECT TRIM(column_name) FROM table_name;
   ```

   這將從 `column_name` 字串的開頭和結尾移除空格。

2. **指定字的刪除**：

   ```SQL
   SELECT TRIM(character FROM column_name) FROM table_name;
   ```

   這將從 `column_name` 字串的開頭和結尾移除指定的 `character`。

#### **範例**

1. **移除字串兩端的空格**：

   ```SQL
   SELECT TRIM(FirstName) AS TrimmedName 
   FROM Employees;
   ```

   移除 `Employees` 表中每個員工 `FirstName` 字串兩端的空格。

2. **移除特定字**：

   ```SQL
   SELECT TRIM('.' FROM EmailAddress) AS CleanEmail FROM Customers;
   ```

   從 `Customers` 表中每個客戶的 `EmailAddress` 字串兩端移除點號（`.`）。

3. **結合其他函數使用 `TRIM`**：

   ```SQL
   SELECT CONCAT(TRIM(FirstName), ' ', TRIM(LastName)) AS FullName FROM Employees;
   ```

   去除兩端空格的名字和姓氏連接而成的全名。

#### TRIM注意事項

* 在不同的數據庫系統中，`TRIM` 函數的行為和語法可能略有不同。
* 某些系統還提供了 `LTRIM` 和 `RTRIM` 函數，專門用於刪除字串左側或右側的空格。

### LENGTH

用於獲取字串的長度。`LENGTH` 函數在某些數據庫系統中稱為 `LEN`。

#### **格式**

```SQL
SELECT LENGTH(column_name) FROM table_name;
```

顯示 `column_name` 中字串的長度。

#### **範例**

1. **取得字串長度**：

   ```SQL
   SELECT LENGTH(FirstName) AS NameLength 
   FROM Employees;
   ```

   查詢 `Employees` 表中每個員工 `FirstName` 文字長度。

2. **篩選特定長度的字串**：

   ```SQL
   SELECT * FROM Customers 
   WHERE LENGTH(CustomerName) > 10;
   ```

   查詢 `Customers` 表中 `CustomerName` 文字長度超過 10 個字的所有記錄。

3. **結合使用 `LENGTH` 和其他函數**：

   ```SQL
   SELECT CustomerName, LENGTH(CustomerName) AS NameLength FROM Customers ORDER BY NameLength DESC;
   ```

   查詢 `Customers` 表中所有客戶的名字以及它們的長度，並按照名字長度降序排列。

#### LENGTH注意事項

* `LENGTH` 函數計算的是字串中的字數，而不是字節數。  
* 對於使用多字節的字串（如 UTF-8）的格式，字數和字節數可能不相同。
* 不同數據庫系統對於空字符串和 NULL 值的處理可能不同。  
* 在某些系統中，`LENGTH` 函數可能返回 NULL或是空字串。

### REPLACE

`REPLACE` 函數用於在字串中替換指定的文字。

#### **格式**

```SQL
SELECT REPLACE(column_name, 'old_string', 'new_string') FROM table_name;
```

在`column_name` 字串中將 `old_string` 替換為 `new_string`。

#### **範例**

1. **簡單的字串替換**：

   ```SQL
   SELECT REPLACE(Description, 'old', 'new') AS UpdatedDescription FROM Products;
   ```

   `Products` 表中 `Description` 欄位裡的所有 'old' 字替換為 'new'。

2. **移除特定字串**：

   ```SQL
   SELECT REPLACE(PhoneNumber, '-', '') AS CleanPhoneNumber FROM Customers;
   ```

   從 `Customers` 表中的 `PhoneNumber` 欄位移除所有的破折號 ('-')。

3. **結合其他函數使用 `REPLACE`**：

   ```SQL
   SELECT CustomerName, REPLACE(LOWER(Email), '@example.com', '@newdomain.com') AS UpdatedEmail FROM Customers;
   ```

   首先將 `Email` 欄位轉為小寫，然後將所有包含 '@example.com' 的電子郵件地址替換為 '@chloe.com'。

#### REPLACE注意事項

* 如果 `old_string` 在 `column_name` 中不存在，則 `REPLACE` 函數不會進行任何修改。
* 在處理大量數據時，請注意 `REPLACE` 函數可能會影響查詢性能。
* `REPLACE` 函數在不同的資料庫系統中是標準的，但具體實現可能略有不同。

### CREATE TABLE

用於創建一個新的資料表。
可以在創建過程中定義表的結構，包括欄位的名稱、資料類型和其他可能的約束（如主鍵、唯一性等）。

#### **格式**

```SQL
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
    ....
);
```

* `table_name` 是您想創建的資料表名稱。
* `column1`, `column2`, `column3`, ... 是表中的欄位名。
* `datatype` 指定每列的資料類型，如 `INT`, `VARCHAR`, `DATE`, 等。

#### **範例**

1. **創建一個簡單的資料表**：

   ```SQL
   CREATE TABLE Employees (
       EmployeeID INT,
       FirstName VARCHAR(50),
       LastName VARCHAR(50),
       BirthDate DATE
   );
   ```

   創建了 `Employees` 的表，包含員工 ID、名字、姓氏和出生日期。

2. **包含主鍵約束的資料表**：

   ```SQL
   CREATE TABLE Products (
       ProductID INT PRIMARY KEY,
       ProductName VARCHAR(100),
       Price DECIMAL(10, 2)
   );
   ```

   創建一個包含產品 ID（主鍵）、產品名稱和價格的 `Products` 表。

3. **包含外鍵約束的資料表**：

   ```SQL
   CREATE TABLE Orders (
       OrderID INT PRIMARY KEY,
       OrderDate DATE,
       CustomerID INT,
       FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
   );
   ```

   創建一個包含訂單 ID（主鍵）、訂單日期和客戶 ID 的 `Orders` 表，其中客戶 ID 是 `Customers` 表的外鍵。

#### CREATE TABLE注意事項

* 在創建資料表時，選擇適當的數據類型和約束對於確保數據完整性和優化性能非常重要。
* 在某些資料庫系統中，創建資料表前需要先創建資料庫（例如 MySQL、PostgreSQL），而在其他系統（如 SQLite）中則可能不需要。
* 當表已存在時，嘗試創建同名的表將導致錯誤，除非您在語句中包含了 `IF NOT EXISTS` 語法。

### Constraint

條件約束（Constraint）在資料庫中用於規定表中數據的規則，以保證數據的準確性和可靠性。  
條件約束可以在創建資料表時或之後添加。

#### **約束類型**

1. **PRIMARY KEY**：確保列中每個值都是唯一的，並且不是 NULL。
2. **FOREIGN KEY**：在一個表中的列或列集指向另一個表的主鍵。
3. **UNIQUE**：確保列中所有值都是唯一的。
4. **CHECK**：確保列中的值滿足特定條件。
5. **DEFAULT**：為列設定默認值。

#### **創建約束的語法**

在創建表時添加約束：

```SQL
CREATE TABLE table_name (
    column1 datatype CONSTRAINT constraint_name PRIMARY KEY,
    column2 datatype CONSTRAINT constraint_name UNIQUE,
    column3 datatype CONSTRAINT constraint_name DEFAULT value,
    ...
);
```

對現有表添加約束：

```SQL
ALTER TABLE table_name
ADD CONSTRAINT constraint_name UNIQUE (column);
```

#### **範例**

1. **在創建表時添加約束**：

   ```SQL
   CREATE TABLE Employees (
       EmployeeID INT PRIMARY KEY,
       Email VARCHAR(255) UNIQUE,
       Age INT CHECK (Age >= 18)
   );
   ```

   `Employees` 表中的 `EmployeeID` 是主鍵，`Email` 是唯一的，且 `Age` 必須大於或等於 18。

2. **對現有表添加約束**：

   ```SQL
   ALTER TABLE Orders
   ADD CONSTRAINT FK_Customer
   FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID);
   ```

   在 `Orders` 表中添加外鍵約束，參照 `Customers` 表的 `CustomerID`。

#### **約束注意事項**

* 確保約束的設置不會與數據的現實情況相衝突。
* 過多的約束可能會影響資料庫的性能，特別是在大量數據插入或更新時。

### 主键

主鍵（Primary Key）是資料表中用於唯一標識每條記錄的一個或一組欄位。主鍵的值必須是唯一的，並且不能為 NULL。  
在資料庫設計中，正確地使用主鍵可以確保數據的完整性和性能。

#### **主鍵特性**

1. **唯一性**：主鍵的每個值必須在表中是唯一的。
2. **不可為空**：主鍵不能有 NULL 值。
3. **不變性**：一旦創建，主鍵的值不應更改。

#### **創建主鍵的語法**

在創建表時指定主鍵：

```SQL
CREATE TABLE table_name (
    column1 datatype PRIMARY KEY,
    column2 datatype,
    ...
);
```

對現有表添加主鍵約束：

```SQL
ALTER TABLE table_name
ADD PRIMARY KEY (column1);
```

#### **範例**

1. **創建帶有主鍵的新表**：

   ```SQL
   CREATE TABLE Employees (
       EmployeeID INT PRIMARY KEY,
       FirstName VARCHAR(50),
       LastName VARCHAR(50)
   );
   ```

   `EmployeeID` 是 `Employees` 表的主鍵。

2. **向現有表添加主鍵約束**：

   ```SQL
   ALTER TABLE Orders
   ADD PRIMARY KEY (OrderID);
   ```

   將 `OrderID` 欄位設置為 `Orders` 表的主鍵。

#### **主鍵注意事項**

* 在選擇主鍵時，考慮使用具有明確業務意義的欄位，如員工編號、訂單編號等。
* 主鍵可以是單一欄位，也可以是多個欄位的組合（複合主鍵）。
* 主鍵通常是表的外鍵關聯和索引策略的基礎。

### 外來键

外來鍵（Foreign Key）是資料庫中用於建立兩個表之間關聯的一個或一組欄位。  
外來鍵指向另一個表的主鍵，用於維護跨表的數據完整性。  
外來鍵確保了引用完整性，讓新增或異動只能使用那些在關聯表中已存在的值。

#### **外來鍵特性**

1. **參照完整性**：外來鍵列的值必須匹配它指向的主鍵的值，或者是 NULL。
2. **關聯表的連接**：外來鍵建立了表之間的關聯，使得可以在查詢中聯接這些表。

#### **創建外來鍵的語法**

對現有表添加外來鍵約束：

```SQL
ALTER TABLE child_table
ADD FOREIGN KEY (child_column)
REFERENCES parent_table (parent_column);
```

#### **範例**

1. **向現有表添加外鍵約束**：

   ```SQL
   ALTER TABLE Orders
   ADD FOREIGN KEY (CustomerID)
   REFERENCES Customers (CustomerID);
   ```

   `Orders` 表中，`CustomerID` 成為外來鍵，它參照 `Customers` 表的 `CustomerID` 主鍵。

2. **創建帶有外來鍵的新表**：

   ```SQL
   CREATE TABLE OrderDetails (
       OrderDetailID INT PRIMARY KEY,
       OrderID INT,
       ProductID INT,
       Quantity INT,
       FOREIGN KEY (OrderID) REFERENCES Orders (OrderID),
       FOREIGN KEY (ProductID) REFERENCES Products (ProductID)
   );
   ```

   `OrderDetails` 表中，`OrderID` 和 `ProductID` 成為外來鍵，分別參照 `Orders` 和 `Products` 表的主鍵。

#### **外鍵注意事項**

* 外來鍵和它參照的主鍵必須具有相同的資料類型。
* 外來鍵可以防止在子表中插入無效的數據，即那些在父表中沒有對應值的數據。
* 刪除或修改父表中的數據時，需要特別注意，因為這可能會影響依賴於這些數據的子表。

### CREATE VIEW

用於創建一個虛擬的表，這個虛擬表稱為視圖（View）。  
視圖是基於 SQL 查詢的結果集的可查詢對象，它與實際表相似，但不存儲數據本身。  
視圖可以簡化複雜的 SQL 查詢、增強數據安全性和隱私性。

#### **格式**

```SQL
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

* `view_name` 是您想創建的視圖的名稱。
* 從`AS`開始寫下你的Query
* `SELECT` 你想呈現視圖的內容。

#### **範例**

1. **創建一個簡單的視圖**：

   ```SQL
   CREATE VIEW ViewEmployeeNames AS
   SELECT FirstName, LastName FROM Employees;
   ```

   創建一個名為 `ViewEmployeeNames` 的視圖，顯示 `Employees` 表中所有員工的名字和姓氏。

2. **包含條件的視圖**：

   ```SQL
   CREATE VIEW ViewRecentOrders AS
   SELECT OrderID, CustomerID, OrderDate FROM Orders WHERE OrderDate > '2023-07-22';
   ```

   創建一個顯示 2023 年 07 月 22 日之後所有訂單的 `ViewRecentOrders` 視圖。

3. **從多個表中創建視圖**：

   ```SQL
   CREATE VIEW ViewCustomerOrders AS
   SELECT Customers.CustomerName, Orders.OrderID, Orders.OrderDate
   FROM Customers
   INNER JOIN Orders ON Customers.CustomerID = Orders.CustomerID;
   ```

   創建一個名為 `ViewCustomerOrders` 的視圖，顯示客戶及其相關訂單的詳細信息。

#### VIEW注意事項

* 視圖只是一個查詢結果集的虛擬展示，它本身不存儲數據。
* 視圖可以提供數據的邏輯層，幫助使用者避免直接與複雜的數據表交互。
* 在某些情況下，視圖可以提高數據查詢的效率。
* 視圖的安全性和權限管理可以更加靈活地控制使用者訪問數據的方式。

### CREATE INDEX

用於在資料表的一個或多個欄位上創建索引。  
索引是數據庫管理系統中用來提高數據檢索速度的數據結構。  
使用索引，可以快速找到資料表中的特定資料，而不需要逐行掃描整個表。

#### **格式**

```SQL
CREATE INDEX index_name ON table_name (column1, column2, ...);
```

* `index_name` 是您想要創建的索引的名稱。
* `table_name` 是所需資料表的名稱。
* `column1, column2, ...` 是你希望在其中建立索引的欄位名。

#### **範例**

1. **在單一欄位上創建索引**：

   ```SQL
   CREATE INDEX idx_lastname ON Employees (LastName);
   ```

   `Employees` 表的 `LastName` 欄位上創建一個名為 `idx_lastname` 的索引。

2. **在多欄位上創建復合索引**：

   ```SQL
   CREATE INDEX idx_name ON Customers (FirstName, LastName);
   ```

   在 `Customers` 表的 `FirstName` 和 `LastName` 欄位上創建一個復合索引，名為 `idx_name`。

#### CREATE INDEX注意事項

* 索引可以提高查詢的效率，特別是對於大型資料表。
* 過多的索引或不當的索引可能會降低數據庫的寫入性能(索引破碎)，因為索引需要在數據更新時同步更新。
* 索引最適合用於經常用於查詢條件的欄位，尤其是在 WHERE 子句、JOIN 條件和 ORDER BY 子句中經常使用的欄位。

### ALTER TABLE

用於修改現有資料表的結構。包括添加、刪除或修改欄位，以及更改欄位的資料類型等。

#### **格式**

1. **添加新欄位**：

   ```SQL
   ALTER TABLE table_name ADD column_name datatype;
   ```

2. **刪除欄位**：

   ```SQL
   ALTER TABLE table_name DROP COLUMN column_name;
   ```

3. **修改欄位的資料類型**：

   ```SQL
   ALTER TABLE table_name ALTER COLUMN column_name datatype;
   ```

#### **範例**

1. **向資料表中添加新欄位**：

   ```SQL
   ALTER TABLE Employees ADD Email VARCHAR(255);
   ```

   在 `Employees` 表中添加名為 `Email` 的新欄位。

2. **從資料表中刪除欄位**：

   ```SQL
   ALTER TABLE Customers DROP COLUMN PhoneNumber;
   ```

   從 `Customers` 表中刪除名為 `PhoneNumber` 的欄位。

3. **更改現有欄位的資料類型**：

   ```SQL
   ALTER TABLE Products ALTER COLUMN Price DECIMAL(10, 2);
   ```

   更改 `Products` 表中 `Price` 列的資料類型。

4. **重命名欄位**：

   ```SQL
   ALTER TABLE Orders RENAME COLUMN CustomerID TO ClientID;
   ```

   將 `Orders` 表中的 `CustomerID` 欄位重命名為 `ClientID`。

#### **ALTER TABLE 注意事項**

* 在對表進行結構修改時，需要注意是否在生產環境(Product)中。
* 不同的資料庫系統對 `ALTER TABLE` 的支持可能有所不同，特別是在更細緻的語法層面上。
* 在進行較大規模的修改前，建議先備份相關數據。

### DROP TABLE

用於從資料庫中永久刪除一個表及其所有數據。  
這個命令會徹底移除表和表中的所有數據，而且是不可逆的。  
在使用 `DROP TABLE` 前，確保已經妥善備份了任何重要數據。

#### **格式**

```SQL
DROP TABLE table_name;
```

* `table_name` 是您想要刪除的表的名稱。

#### **範例**

1. **刪除一個表**：

   ```SQL
   DROP TABLE Customers;
   ```

   這個命令將從數據庫中刪除 `Customers` 表。

#### **DROP TABLE 注意事項**

* `DROP TABLE` 命令會永久刪除表和表中的所有數據，請謹慎使用。
* 一旦表被刪除，無法恢復這些數據，除非您有備份。
* 在執行 `DROP TABLE` 前，確保沒有其他數據庫對象（如視圖或預存程序）依賴於該表。

### TRUNCATE TABLE

用於快速清除資料表中的所有資料，但不刪除資料表本身。  
這個命令比使用 `DELETE` 刪除所有資料更高效，因為它不會對每一行進行個別的日誌記錄(Log)。

#### **格式**

```SQL
TRUNCATE TABLE table_name;
```

* `table_name` 是您想要清空的表的名稱。

#### **範例**

1. **清空一個表**：

   ```SQL
   TRUNCATE TABLE Employees;
   ```

   這個命令將刪除 `Employees` 表中的所有行，但表結構仍然保留。

#### **TRUNCATE TABLE 注意事項**

* 這個操作不可逆，一旦執行，刪除的數據將無法恢復。
* `TRUNCATE TABLE` 通常比 `DELETE` 命令更快，因為它不生成個別的日誌(Log)。
  
### INSERT INTO

用於將新資料增加到資料表中。  
可以指定要插入的值。

#### **格式**

1. **插入到指定欄位**：

   ```SQL
   INSERT INTO table_name (column1, column2, column3, ...)
   VALUES (value1, value2, value3, ...);
   ```

2. **插入到所有欄位**：

   ```SQL
   INSERT INTO table_name
   VALUES (value1, value2, value3, ...);
   ```

#### **範例**

1. **向特定欄位插入數據**：

   ```SQL
   INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
   VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');
   ```

   向 `Customers` 表中插入一行新數據，僅指定部分欄位。

2. **向表中的所有欄位插入數據**：

   ```SQL
   INSERT INTO Orders
   VALUES (10248, 'VINET', 5, '1996-07-04', '1996-08-01', '1996-07-16', 3, 32.38);
   ```

   向 `Orders` 表中插入一行新數據，包含所有欄位的值。

#### **INSERT INTO 注意事項**

* 在使用 `INSERT INTO` 時，確保插入的資料類型與資料表的資料類型相匹配。
* 如果表中有主鍵或唯一索引，請確保不要插入重複的值。
* 在插入大量數據時，考慮使用交易處理(TRANSACTION)以保證數據完整性。

### UPDATE

用於修改資料表中的現有數據。  
可以更新一行或多行中的一個或多個欄位的值。

#### **格式**

```SQL
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

* `table_name` 是要更新數據的表的名稱。

* `SET` 語法指定欄位及其新值。
* `WHERE` 語法確定哪些行應該被更新。

#### **範例**

1. **更新特定行的數據**：

   ```SQL
   UPDATE Customers
   SET ContactName = 'Miona', City= 'Taipei'
   WHERE CustomerID = 1;
   ```

   更新 `Customers` 表中 `CustomerID` 為 1 的客戶的聯絡人姓名和城市。

2. **更新多個列的數據**：

   ```SQL
   UPDATE Employees
   SET LastName = 'Eric', BirthDate = '1980-01-01'
   WHERE EmployeeID = 123;
   ```

   更新 `Employees` 表中 `EmployeeID` 為 123 的員工的姓氏和出生日期。

#### **UPDATE 注意事項**

* 在執行 `UPDATE` 前，請確保您的 `WHERE` 條件正確無誤，以避免意外更新錯誤的行。
* 如果省略 `WHERE` 條件，將會更新資料表中的所有行(三重代表上香)。
* 在更新大型資料表時，考慮更新的性能影響和可能的鎖定。

### DELETE FROM

用於從資料表中刪除一行或多行數據。  
這個語法允許您根據特定條件刪除數據，如果沒有指定條件，則會刪除資料表中的所有行，請小心使用。

#### **格式**

```SQL
DELETE FROM table_name
WHERE condition;
```

* `table_name` 是要刪除數據的表的名稱。

* `WHERE` 指定哪些行應該被刪除。

#### **範例**

1. **根據條件刪除行**：

   ```SQL
   DELETE FROM Customers
   WHERE CustomerName = 'Esther';
   ```

   刪除 `Customers` 表中名稱為 'Esther' 的客戶。

2. **刪除符合多個條件的行**：

   ```SQL
   DELETE FROM Orders
   WHERE OrderDate < '2023-07-22' AND Status = 'Cancelled';
   ```

   刪除 `Orders` 表中訂單日期早於 2023 年 07 月 22 日且狀態為 'Cancelled' 的所有訂單。

#### **DELETE FROM 注意事項**

* 在執行 `DELETE FROM` 前，確保您的 `WHERE` 準確無誤，以避免刪除錯誤的數據。
* 如果沒有指定 `WHERE` 子句，將會刪除表中的所有行，請謹慎操作。
* 在刪除大量數據時，請考慮到對性能的影響。

### UNION

用於結合兩個或多個 `SELECT` 語句的結果集為一個結果集。  
它僅選擇不同的值，也就是說，它會自動刪除重複的行。  
使用時，每個 `SELECT` 語句必須有相同數量的欄位且資料類型也必須相似。

#### **格式**

```SQL
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

* 每個 `SELECT` 語句內的欄位數量和類型必須相同。

#### **範例**

1. **結合兩個查詢的結果**：

   ```SQL
   SELECT CustomerName FROM Customers
   UNION
   SELECT SupplierName FROM Suppliers;
   ```

   這個查詢返回 `Customers` 表中的 `CustomerName` 和 `Suppliers` 表中的 `SupplierName`，且刪除重複的名稱。

2. **使用多個列進行 UNION 操作**：

   ```SQL
   SELECT City, Country FROM Customers
   UNION
   SELECT City, Country FROM Suppliers;
   ```

   結合了來自 `Customers` 和 `Suppliers` 表的 `City` 和 `Country` 欄位，且刪除重複的行。

#### **UNION 注意事項**

* `UNION` 會自動刪除重複的欄位，如果您想要保留資料，可以使用 `UNION ALL`。
* 確保每個 `SELECT` 中的欄位數量和資料類型相匹配。

### UNION ALL

用於結合兩個或多個 `SELECT` 語句的結果集為一個結果集。  
與 `UNION` 不同，`UNION ALL` 會選擇所有的值，包括重複的行。  
這使得在某些情況下比 `UNION` 更高效，因為它不需要額外的操作來刪除重複的行。

#### **格式**

```SQL
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

* 每個 `SELECT` 語句內的欄位數量和類型必須相同。

#### **範例**

1. **結合兩個查詢的結果，包括重複的行**：

   ```SQL
   SELECT CustomerName FROM Customers
   UNION ALL
   SELECT SupplierName FROM Suppliers;
   ```

    `Customers` 表中的 `CustomerName` 和 `Suppliers` 表中的 `SupplierName`，包括所有重複的名稱。

2. **使用多個欄位進行 `UNION ALL` 操作**：

   ```SQL
   SELECT City, Country FROM Customers
   UNION ALL
   SELECT City, Country FROM Suppliers;
   ```

   結合了來自 `Customers` 和 `Suppliers` 表的 `City` 和 `Country` 欄位，包括所有重複的行。

#### **UNION ALL 注意事項**

* `UNION ALL` 不會刪除重複的行。
* 確保每個 `SELECT` 中的欄位數量和資料類型相匹配。

### INTERSECT

用於找出兩個或多個結果的交集。

#### **格式**

```SQL
SELECT column_name(s) FROM table1
INTERSECT
SELECT column_name(s) FROM table2;
```

* 每個 `SELECT` 語句內的欄位數量和類型必須相同。

#### **範例**

1. **找出兩個表共有的數據**：

   ```SQL
   SELECT CustomerID FROM Orders
   INTERSECT
   SELECT CustomerID FROM Customers;
   ```

   這個查詢返回同時存在於 `Orders` 表和 `Customers` 表中的 `CustomerID`。

2. **多欄位的 `INTERSECT` 操作**：

   ```SQL
   SELECT City, Country FROM Customers
   INTERSECT
   SELECT City, Country FROM Suppliers;
   ```

   找出 `Customers` 和 `Suppliers` 表中都有的城市和國家組合。

#### **INTERSECT 注意事項**

* `INTERSECT` 返回的是兩個查詢結果的交集，即兩個結果共同擁有的值。
* 確保每個 `SELECT` 語句中的欄位數量和數據類型相匹配。
* 並非所有資料庫系統都支持 `INTERSECT` 操作。

### EXISTS

用於測試子查詢是否返回至少一行數據，是一種邏輯運算子。  
當子查詢的結果不為空時，`EXISTS` 返回true，否則返回false。  
這個運算符通常用在 `WHERE` 中，並且常與子查詢一起使用。

#### **格式**

```SQL
SELECT column_name(s) FROM table1
WHERE EXISTS (SELECT column_name FROM table2 WHERE condition);
```

#### **範例**

1. **檢查是否存在符合條件的數據**：

   ```SQL
   SELECT * FROM Customers
   WHERE EXISTS (SELECT 1 FROM Orders WHERE Customers.CustomerID = Orders.CustomerID);
   ```

   查詢至少有一筆訂單的所有客戶。

2. **結合多個條件使用 `EXISTS`**：

   ```SQL
   SELECT * FROM Employees
   WHERE EXISTS (SELECT 1 FROM Sales WHERE Sales.EmployeeID = Employees.EmployeeID AND Sales.Year = 2021);
   ```

   查詢在 2021 年有銷售記錄的所有員工。

#### **EXISTS 注意事項**

* `EXISTS` 子查詢不實際返回任何數據，它只返回一個布林值。

* `EXISTS` 運算符通常比 `IN` 運算符更高效，特別是在子查詢返回大量行時。
* 子查詢中常用 `SELECT 1`，因為 `EXISTS` 只關心是否有行存在，而不關心行的內容。

### CASE

一種條件語句，用於實現在查詢中的條件邏輯。  
它類似於其他程式語言中的 if-else 或 switch 。  
`CASE` 可以在 `SELECT`、`UPDATE` 和 `WHERE` 等語句中使用，用於根據特定條件對數據進行轉換或比較。

#### **格式**

1. **簡單 CASE 表達式**：

   ```SQL
   CASE column_name
       WHEN value1 THEN result1
       WHEN value2 THEN result2
       ...
       ELSE resultN
   END
   ```

2. **搜索 CASE 表達式**：

   ```SQL
   CASE
       WHEN condition1 THEN result1
       WHEN condition2 THEN result2
       ...
       ELSE resultN
   END
   ```

#### **範例**

1. **使用簡單 CASE 表達式**：

   ```SQL
   SELECT CustomerName, 
          CASE Country
              WHEN 'USA' THEN 'America'
              WHEN 'Canada' THEN 'North America'
              ELSE 'International'
          END AS Region
   FROM Customers;
   ```

   根據客戶所在國家分類客戶為 'America'、'North America' 或 'International'。

2. **使用搜索 CASE 表達式**：

   ```SQL
   SELECT ProductID, 
          CASE 
              WHEN Price < 10 THEN 'Cheap'
              WHEN Price BETWEEN 10 AND 20 THEN 'Normal'
              ELSE 'Expensive'
          END AS PriceCategory
   FROM Products;
   ```

   這個查詢根據產品的價格將產品分類為 'Cheap'、'Normal' 或 'Expensive'。

#### **CASE 注意事項**

* `CASE` 表達式必須以 `END` 結尾。
* `ELSE` 部分是可選的，但要確保所有可能的情況都被處理。
