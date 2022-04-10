# Power-BI-SQl-Analysis
A demo project to get hands on experience with SQL management studio and Power BI

Sales Report for last two years

This project can be made by using these resources:
Power BI Desktop Version
SQL Server 2019
AdventureWorks Sample Database
SQL Server Management Studio (SSMS)
In SSMS, connecting the database and updating it with a SQL script. (https://github.com/techtalkcorner/SampleDemoFiles/blob/master/Database/AdventureWorks/Update_AdventureWorksDW_Data.sql)

The project has 3 parts:
Business Request & Planning
Data Clean & Transformation
Create Dashboard

The finished project will have:
A Power BI dashboard which updates data once a day.
A Power BI dashboard which allows me to filter data for each customer.
A Power BI dashboard which allows me to filter data for each Product.
A Power BI dashboard with graphs and KPIs comparing against budget.

Queries to clean the tables for visualization:
dbo.DimDate:
/****** Cleaned Dim_DateTable  ******/
SELECT
[DateKey]
      ,[FullDateAlternateKey] AS Date
      ,[EnglishDayNameOfWeek] AS Day
      ,[WeekNumberOfYear] AS WeekNr
      ,[EnglishMonthName] AS Month,
LEFT([EnglishMonthName] ,3) AS MonthShort
      ,[MonthNumberOfYear] AS MonthNo
      ,[CalendarQuarter] AS Quarter
      ,[CalendarYear] AS Year
 FROM [AdventureWorksDW2019].[dbo].[DimDate]
WHERE CalendarYear >= 2019

dbo.DimCustomer:
-- Cleansed Customers Table --
SELECT 
  c.customerkey AS CustomerKey, 
  c.firstname AS [First Name], ]
  c.lastname AS [Last Name], 
  c.firstname + ' ' + lastname AS [Full Name], 
  CASE c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender,
  c.datefirstpurchase AS DateFirstPurchase, 
  g.city AS [Customer City] -- Joined in Customer City from Geography Table
FROM 
  [AdventureWorksDW2019].[dbo].[DimCustomer] as c
LEFT JOIN dbo.dimgeography AS g ON g.geographykey = c.geographykey 
ORDER BY 
  CustomerKey ASC -- Ordered List by CustomerKey

dbo.DimProduct:
-- Cleansed Products Table --
SELECT 
  p.[ProductKey], 
  p.[ProductAlternateKey] AS ProductItemCode, 
  p.[EnglishProductName] AS [Product Name], 
  ps.EnglishProductSubcategoryName AS [Sub Category], -- Joined in from Sub Category Table
  pc.EnglishProductCategoryName AS [Product Category], -- Joined in from Category Table 
  p.[Color] AS [Product Color],  
  p.[Size] AS [Product Size], 
  p.[ProductLine] AS [Product Line], 
  p.[ModelName] AS [Product Model Name], 
  p.[EnglishDescription] AS [Product Description],  
  ISNULL (p.Status, 'Outdated') AS [Product Status] 
FROM 
  [AdventureWorksDW2019].[dbo].[DimProduct] as p
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey 
order by 
  p.ProductKey asc

dbo.FactInternetSales:
-- Cleansed FACT_InternetSales Table --
SELECT 
  [ProductKey], 
  [OrderDateKey], 
  [DueDateKey], 
  [ShipDateKey], 
  [CustomerKey], 
  [SalesOrderNumber], 
  [SalesAmount]
FROM 
  [AdventureWorksDW2019].[dbo].[FactInternetSales]
WHERE 
LEFT (OrderDateKey, 4) >= YEAR(GETDATE()) -2 -- Ensures we always only bring two years of date from extraction.
ORDER BY
  OrderDateKey ASC

