# Introduction

Apply your skills to import, analyze and visualize Human Resources (HR) data using Power BI.

# The Problem

In this case study, a fictitious client, Atlas Labs, have the primary goal of monitoring Key HR metrics on employees and understanding what factors impact attrition (Employee attrition is the naturally occurring, voluntary departure of employees from a company)

# Implemntaion Steps

### Step 1: Data Modeling and EDA

Bringing all your data into Power BI and connecting your data model. Once your data is ready, I will doing some initial exploratory data analysis on general HR trends.

* create a dedicated date table using DAX

```
DimDate =
VAR _minYear =
    YEAR ( MIN ( DimEmployee[HireDate] ) )
VAR _maxYear =
    YEAR ( MAX ( DimEmployee[HireDate] ) )
VAR _fiscalStart = 4
RETURN
    ADDCOLUMNS (
        CALENDAR ( DATE ( _minYear, 1, 1 ), DATE ( _maxYear, 12, 31 ) ),
        "Year", YEAR ( [Date] ),
        "Year Start", DATE ( YEAR ( [Date] ), 1, 1 ),
        "YearEnd", DATE ( YEAR ( [Date] ), 12, 31 ),
        "MonthNumber", MONTH ( [Date] ),
        "MonthStart", DATE ( YEAR ( [Date] ), MONTH ( [Date] ), 1 ),
        "MonthEnd", EOMONTH ( [Date], 0 ),
        "DaysInMonth",
            DATEDIFF (
                DATE ( YEAR ( [Date] ), MONTH ( [Date] ), 1 ),
                EOMONTH ( [Date], 0 ),
                DAY
            ) + 1,
        "YearMonthNumber", INT ( FORMAT ( [Date], "YYYYMM" ) ),
        "YearMonthName", FORMAT ( [Date], "YYYY-MMM" ),
        "DayNumber", DAY ( [Date] ),
        "DayName", FORMAT ( [Date], "DDDD" ),
        "DayNameShort", FORMAT ( [Date], "DDD" ),
        "DayOfWeek", WEEKDAY ( [Date] ),
        "MonthName", FORMAT ( [Date], "MMMM" ),
        "MonthNameShort", FORMAT ( [Date], "MMM" ),
        "Quarter", QUARTER ( [Date] ),
        "QuarterName", "Q" & FORMAT ( [Date], "Q" ),
        "YearQuarterNumber", INT ( FORMAT ( [Date], "YYYYQ" ) ),
        "YearQuarterName",
            FORMAT ( [Date], "YYYY" ) & " Q"
                & FORMAT ( [Date], "Q" ),
        "QuarterStart",
            DATE ( YEAR ( [Date] ), ( QUARTER ( [Date] ) * 3 ) - 2, 1 ),
        "QuarterEnd", EOMONTH ( DATE ( YEAR ( [Date] ), QUARTER ( [Date] ) * 3, 1 ), 0 ),
        "WeekNumber", WEEKNUM ( [Date] ),
        "WeekStart",
            [Date] - WEEKDAY ( [Date] ) + 1,
        "WeekEnd",
            [Date] + 7
                - WEEKDAY ( [Date] ),
        "FiscalYear",
            IF (
                _fiscalStart = 1,
                YEAR ( [Date] ),
                YEAR ( [Date] )
                    + QUOTIENT ( MONTH ( [Date] ) + ( 13 - _fiscalStart ), 13 )
            ),
        "FiscalQuarter",
            QUARTER (
                DATE ( YEAR ( [Date] ), MOD ( MONTH ( [Date] ) + ( 13 - _fiscalStart ) - 1, 12 ) + 1, 1 )
            ),
        "FiscalMonth",
            MOD ( MONTH ( [Date] ) + ( 13 - _fiscalStart ) - 1, 12 ) + 1
    )
```

* Connect the DimDate table to FactPerfomanceRating and the DimEmployee table
* Connect the DimEducationLevel table to the DimEmployee table
* Connect FactPerfomanceRating table columns (EnvironmentSatisfaction, JobSatisfaction, RelationshipSatisfaction and WorkLifeBalance) to DimSatisfactionLevel table and use EnvironmentSatisfaction as the active connection.
* Connect FactPerfomanceRating table columns (SelfRating, ManagerRating) to DimRatingLevel and use the SelfRating column as the active connection.
* **Creating Measures**
    * Count employees : `TotalEmployees = DISTINCTCOUNT(DimEmployee[EmployeeID]) `
    * Count Active Employees : `ActiveEmployee = CALCULATE([TotalEmployees], DimEmployee[Attrition] = "No")`
    * Count Inactive Employees : `InactiveEmployee = CALCULATE([TotalEmployees], DimEmployee[Attrition] = "Yes")`
    * Calculate Attrition Rate : `% Attrition Rate = [InactiveEmployee]/[TotalEmployees]`
* Calculate Hiring trends over time to see where they have had the biggest growth in employees

```
TotalEmployeesDate =
CALCULATE (
    [TotalEmployees],
    USERELATIONSHIP ( DimEmployee[HireDate], DimDate[Date] )
)
```

![Hiring trends over time](/Images/1.png)

### Step 2: Analyzing Demographics and Performance

Using your Power BI skills to extract insights using DAX and learn how to build custom visuals on report.
Atlas Labs would like some answers on its employee demographics and performance.

### Step 3: Bringing it all together

It’s almost time to deliver report to the key stakeholders at Atlas Labs. In this final chapter, We will focus on delivering insights on attrition and what factors affect employee retention.
Finally, we will clean up the overall layout of the report to create a user-friendly, clean, and branded experience.