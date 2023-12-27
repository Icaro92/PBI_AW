# Sales Overview Dashboard for Adventure Works Company
---
<p align = "center">
  <img src="https://github.com/Icaro92/PBI_AW/assets/58118599/906d8918-bd3f-4cae-9666-69acdecb8c6c" width="500" alt="SalesDash Image">

## Objectives

This dashboard's objective is to provide a clear Sales Overview of Adventure Works. Specifically, the dashboard will accurately represent the Sales Goals as well as a detailed view of top customers and most selling products by category and location.

I've created a mock e-mail from Lucas, our Sales Manager with the requirements for his team. Based on those requirements, I've defined user stories to ensure that acceptance criteria were maintained throughout the project, wrote SQL queries, and organized the data in a Power BI dashboard for precise visualization. 

You can access the dashboard in the link below
* [Link to Adventure Works Sales Dashboard](https://app.powerbi.com/view?r=eyJrIjoiMmNjMjExNTMtNDE0Zi00MTFiLWJjYjktYTkyZmY2YmRhYTU5IiwidCI6IjAyODQyZDljLWVhNTAtNGVkNy1iMWY1LWI2MDIwOGIwM2YzNyJ9)

## Data Source

For this project I used Microsoft's [Adventure Works sample databases](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms) which provide realistic data sets for practicing SQL Server features, tools and techniques. It covers various business scenarios, including sales (used in this example), CRM, Manufacturing and more.

## Business Request & User Stories

  **Business Request**
  
  Lucas - Sales Manager:
  
    Hi Icaro!
    I hope you’re doing well.
    
    We need to improve our internet sales reports and want to move from static reports to visual dashboards.
    Essentially, we want it to focus on how much we have sold of which products, to which clients, and how it has developed over time.
    Seeing as each salesperson works on different products and customers, it would be beneficial to be able to filter them also. 
    We measure our numbers against budget so I added that in a spreadsheet so we can compare our values against performance. 
    The budget is for 2022 and we usually look 2 years back in time when we analyze sales.
    Let me know if you need anything else!
    
    Best Regards,
    Lucas

 **User Stories**
  
    Reporter: Lucas, the Sales Manager
    Value of Change: Visual dashboards and improved Sales Reporting
    Necessary Systems: Power BI, CRM System
    Other Relevant info: Budgets have been delivered on Excel for 2022
  
![UserStories_image](https://github.com/Icaro92/PBI_AW/assets/58118599/b9c6470d-07f3-4be7-9c4a-5cb571d0d256)

## SQL Data Cleaning and Transformation

To create the necessary data model for correcly responding to the Business Request from Sales, the following tables were extracted using SQL.
Also, one table "Sales Budget" was provided in Excel format and was properly connected in the data model in a later step of the process.

**DIM_Calendar**

    -- Cleansed DIM_Datetable
    SELECT [DateKey]
          ,[FullDateAlternateKey] AS Date
          --,[DayNumberOfWeek]
          ,[EnglishDayNameOfWeek] AS Day
          --,[SpanishDayNameOfWeek]
          --,[FrenchDayNameOfWeek]
          --,[DayNumberOfMonth]
         -- ,[DayNumberOfYear]
          ,[WeekNumberOfYear] AS WeekNr
          ,[EnglishMonthName] AS Month,
    	  LEFT([EnglishMonthName],3) AS MonthShort,
          --,[SpanishMonthName]
          --,[FrenchMonthName]
          [MonthNumberOfYear] AS MonthNo
          ,[CalendarQuarter] AS Quarter
          ,[CalendarYear] as Year
          --,[CalendarSemester]
        --  ,[FiscalQuarter]
        --  ,[FiscalYear]
         -- ,[FiscalSemester]
      FROM [AdventureWorksDW2022].[dbo].[DimDate]
      WHERE 
    	CalendarYear >= 2020

**DIM_Customer**

     SELECT 
    	c.customerkey AS CustomerKey
          --,[GeographyKey]
          --,[CustomerAlternateKey]
          --,[Title]
          ,c.firstname AS [First Name]
          --,[MiddleName]
          ,c.lastname as [Last Name],
    	  c.firstname + ' ' + lastname as [Full Name],
          --,[NameStyle]
          --,[BirthDate]
          --,[MaritalStatus]
          --,[Suffix]
          CASE c.gender when 'M' then 'Male' when 'F' then 'Female' END AS Gender,
          --,[EmailAddress]
          --,[YearlyIncome]
          --,[TotalChildren]
          --,[NumberChildrenAtHome]
          --,[EnglishEducation]
          --,[SpanishEducation]
          --,[FrenchEducation]
          --,[EnglishOccupation]
          --,[SpanishOccupation]
          --,[FrenchOccupation]
          --,[HouseOwnerFlag]
          --,[NumberCarsOwned]
          --,[AddressLine1]
          --,[AddressLine2]
          --,[Phone]
          c.datefirstpurchase AS DateFirstPurchase,
    	  g.city as [Customer City] -- Joined in Customer City from Geography Table
         -- ,[CommuteDistance]
     FROM 
    	dbo.DimCustomer AS c
    	LEFT JOIN dbo.DimGeography AS g ON g.geographykey = c.geographykey
    ORDER BY
     CustomerKey ASC -- Ordered List by CustomerKey 

**DIM_Product**

    -- Cleansed DIM_Products Table
    SELECT 
    	p.[ProductKey],
          p.[ProductAlternateKey] AS ProductItemCode
          --,[ProductSubcategoryKey]
          --,[WeightUnitMeasureCode]
          --,[SizeUnitMeasureCode]
          ,p.[EnglishProductName] AS [Product Name],
    	  ps.EnglishProductSubcategoryName AS [Sub Category], -- Joined in from Sub Category Table
    	  pc.EnglishProductCategoryName as [Product Category], -- Joined in from Category Table
          --,[SpanishProductName]
          --,[FrenchProductName]
          --,[StandardCost]
          --,[FinishedGoodsFlag]
          p.[Color] as [Product Color]
          --,[SafetyStockLevel]
          --,[ReorderPoint]
          --,[ListPrice]
          ,p.[Size] AS [Product Size] 
          --,[SizeRange]
          --,[Weight]
          --,[DaysToManufacture]
          ,p.[ProductLine] as [Product Line]
          --,[DealerPrice]
          --,[Class]
          --,[Style]
          ,p.[ModelName] as [Product Model Name]
          --,[LargePhoto]
          ,p.[EnglishDescription] as [Product Description],
          --,[FrenchDescription]
          --,[ChineseDescription]
          --,[ArabicDescription]
          --,[HebrewDescription]
          --,[ThaiDescription]
          --,[GermanDescription]
          --,[JapaneseDescription]
          --,[TurkishDescription]
          --,[StartDate]
          --,[EndDate]
          
    	ISNULL(p.Status, 'Outdated') AS [Product Status]
      FROM 
      [dbo].[DimProduct] as p
      LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey
      LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey
     ORDER BY
    	p.ProductKey ASC

**FACT_InternetSales**

    -- Cleaned FACT_InternetSales Table --
    SELECT 
    	[ProductKey]
          ,[OrderDateKey]
          ,[DueDateKey]
          ,[ShipDateKey]
          ,[CustomerKey]
         -- ,[PromotionKey]
          --,[CurrencyKey]
          --,[SalesTerritoryKey]
          ,[SalesOrderNumber]
          --,[SalesOrderLineNumber]
          --,[RevisionNumber]
          --,[OrderQuantity]
          --,[UnitPrice]
          --,[ExtendedAmount]
          --,[UnitPriceDiscountPct]
          --,[DiscountAmount]
          --,[ProductStandardCost]
          --,[TotalProductCost]
          ,[SalesAmount]
          --,[TaxAmt]
          --,[Freight]
          --,[CarrierTrackingNumber]
          --,[CustomerPONumber]
          --,[OrderDate]
          --,[DueDate]
          --,[ShipDate]
      FROM 
      [dbo].[FactInternetSales]
      WHERE
      Left (OrderDateKey, 4) >= YEAR(GETDATE()) -2 -- Ensures we always bring only Two year of date from extraction
      ORDER BY
      OrderDateKey ASC

## Data Model

Below is a screenshot of the data model after cleanning and preapares tables were uploaded into Power BI.
The models also shows that the  FACT_Budget has been connected to FACT_InternetSales and other necessary DIM tables.

![datamodel_image](https://github.com/Icaro92/PBI_AW/assets/58118599/a92a5571-d2de-4687-b506-5f6a21132c63)

## Sales Management Dashboard

The finished dashboard provides a powerfull and broad overview of Sales, top clients and top products as well as an detailed overview on Customers and Products.

You can check it out in the link below:
* [Link to Adventure Works Sales Dashboard](https://app.powerbi.com/view?r=eyJrIjoiMmNjMjExNTMtNDE0Zi00MTFiLWJjYjktYTkyZmY2YmRhYTU5IiwidCI6IjAyODQyZDljLWVhNTAtNGVkNy1iMWY1LWI2MDIwOGIwM2YzNyJ9)

![SalesDash_image](https://github.com/Icaro92/PBI_AW/assets/58118599/906d8918-bd3f-4cae-9666-69acdecb8c6c)
