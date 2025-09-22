# Loan-Default-Analysis-Dashboard

### Dashboard Link: https://app.powerbi.com/links/_lCFa3oBfH?ctid=3ad87f0e-1f93-41bd-94f4-82c37d971c88&pbi_source=linkShare

## Problem Statement

This project involves building an analytical dashboard in Power BI to assess the risk of loan default. The goal is to identify the key factors contributing to defaults by analyzing various borrower attributes like income, employment status, age, and credit history. The dashboard is structured into multiple pages, providing a high-level overview and a deep dive into credit score analysis. It integrates a wide range of custom DAX measures, including advanced time intelligence calculations, to track trends and segment data effectively. These insights are crucial for financial institutions to improve risk management, refine lending policies, and minimize financial losses.

### Steps Followed

-   **Step 1: Data Loading and Preparation**
    -   Loaded the `Loan_default.csv` dataset into Power BI Desktop.
    -   In the Power Query Editor, performed data cleaning and created several calculated columns such as `Age Group`, `Credit score Bin`, and `Year` to be used for analysis.

-   **Step 2: DAX Measure Creation**
    -   An extensive set of DAX measures was created to power the dashboard's visuals. These were organized into multiple measure tables for clarity.

    -   **### Core Metrics & Demographics**
        ```dax
        Average Income by Employmenttype = 
        CALCULATE(AVERAGE('Loan_default'[Income]),ALLEXCEPT('Loan_default','Loan_default'[EmploymentType]))

        Average Loan By Age group = 
        AVERAGEX(VALUES('Loan_default'[Age Group]),
        AVERAGE('Loan_default'[LoanAmount]))

        Deafult Rate by Year = 
        VAR totalloans = 
             CALCULATE(COUNTROWS('Loan_default'),
             ALLEXCEPT('Loan_default',Loan_default[Year]))
        VAR default =  CALCULATE(COUNTROWS(FILTER('Loan_default'
        ,'Loan_default'[Default] = 1)),ALLEXCEPT('Loan_default',Loan_default[Year]))
        RETURN
        DIVIDE(default,totalloans)*100

        Default Rate for Employment type = 
        var totalrecords = COUNTROWS(ALL('Loan_default'))
        var DefaultCases = COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=1.0))
         RETURN 
         CALCULATE(DIVIDE(DefaultCases,totalrecords),ALLEXCEPT('Loan_default','Loan_default'[EmploymentType]))*100

        Loan Amount by Purpose = 
        SUMX(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanAmount]))),'Loan_default'[LoanAmount])
        ```

    -   **### Segment-Specific Analysis**
        ```dax
        Average loan Amt (high credit) = 
        AVERAGEX(FILTER('Loan_default','Loan_default'[Creadit score Bin] = "High"),'Loan_default'[LoanAmount])

        Loans by Eduction type = 
        COUNTROWS(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanID]))))

        Median by Credit score bins = 
        MEDIANX('Loan_default','Loan_default'[LoanAmount])

        Total loan (Credit Bin) = 
        CALCULATE(SUM(Loan_default[LoanAmount]),'Loan_default'[Age Group] = "Adult",
        ALLEXCEPT('Loan_default',Loan_default[Age],Loan_default[Age Group],
        Loan_default[CreditScore],Loan_default[Creadit score Bin]))

        Total loan (Middle Age adult) = 
        SUMX(FILTER('Loan_default',Loan_default[Age Group] = "Middle Aged Adults"),'Loan_default'[LoanAmount])
        ```

    -   **### Time Intelligence Calculations**
        ```dax
        YOY default loans change = 
        DIVIDE(
             CALCULATE(COUNTROWS(FILTER('Loan_default',
             'Loan_default'[Default]=1)),'Loan_default'
             [Year] = YEAR(MAX('Loan_default'
             [Loan Date (DD/MM/YYYY)]))) - 
             CALCULATE(COUNTROWS(FILTER('Loan_default',
             'Loan_default'[Default]=1)),'Loan_default'
             [Year] = YEAR(MAX('Loan_default'
             [Loan Date (DD/MM/YYYY)]))-1)
             ,CALCULATE(COUNTROWS(FILTER('Loan_default',
             'Loan_default'[Default]=1)),'Loan_default'
             [Year] = YEAR(MAX('Loan_default'
             [Loan Date (DD/MM/YYYY)]))-1),0) * 100

        YOY loan amount charge = 
        DIVIDE(
            CALCULATE(SUM('Loan_default'[LoanAmount]),
            'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))) - 
            CALCULATE(SUM('Loan_default'[LoanAmount]),
            'Loan_default'[Year] = Year(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))-1)
            ,CALCULATE(SUM('Loan_default'[LoanAmount]),
            'Loan_default'[Year] = Year(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))-1),0)*100

        YTD loan Amount = 
        CALCULATE(SUM('Loan_default'[LoanAmount]),DATEVALUE('Loan_default'[Loan Date (DD/MM/YYYY)]),ALLEXCEPT('Loan_default',Loan_default[Creadit score Bin],Loan_default[MaritalStatus]))
        ```

-   **Step 3: Visualization**
    -   Developed an interactive, multi-page dashboard with slicers for `Year`, `Education`, `MaritalStatus`, and `Credit Score Bin`.
    -   **Main Page:** Created KPI cards for primary metrics, a bar chart for "Total Loans by Education," a donut chart for "Total Loans by Employment Type," an area chart for "Default Rate by Year," line charts for "YOY Changes," and a detailed data table.
    -   **Credit Score Analysis Page:** Built a gauge chart for "Average Loan Amount (High Credit)," a bar chart for "Total Loan by Credit Score Bin," a funnel chart for "Total Loan by Age Group," and KPI cards for YTD and segment-specific loan amounts.

-   **Step 4: Publishing**
    -   The final report was published to the Power BI Service.

---

# Dashboard Snapshot
<img width="1314" height="740" alt="Screenshot 2025-09-22 231036" src="https://github.com/user-attachments/assets/21da4ecb-8fc1-4852-811a-79a93cd7e489" />
<img width="1311" height="742" alt="Screenshot 2025-09-22 231021" src="https://github.com/user-attachments/assets/dfaf8c78-6816-4d9d-86b6-69c2fb283172" />
<img width="1307" height="733" alt="Screenshot 2025-09-22 231051" src="https://github.com/user-attachments/assets/746b1d4a-de81-42f3-96ad-5042f18c1522" />



---

# Insights

### [1] Overall Loan Portfolio Health

-   **Total Loans:** The portfolio consists of **80,000** loans.
-   **Defaulted Loans:** **18,000** loans have defaulted, resulting in an overall **Default Rate of 21.93%**.
-   **Average Loan Amount:** The average loan value across the portfolio is **$18.3K**.

### [2] Key Default Drivers (Demographics)

-   **Employment & Income:** The donut chart identifies the distribution of loans across different employment types (e.g., Self-employed, Salaried), while the bar chart shows how the average income differs between these groups, providing context for default risk.
-   **Education Level:** The "Total Loans by Education" chart highlights which educational segments have taken the most loans.

### [3] Time-Based Trends

-   **Default Rate Over Time:** The "Default Rate by Year" area chart visualizes the evolution of the default rate, showing whether risk is increasing or decreasing over the years.
-   **Year-over-Year Changes:** The YOY line charts provide precise metrics on the percentage change in both the number of defaulted loans and the total loan amount, indicating annual performance trends.

### [4] Credit Score Analysis

-   **Loan Distribution by Credit Score:** The "Total Loan by Credit Score Bin" chart identifies which credit segments (e.g., High, Medium, Low) hold the largest share of the total loan value.
-   **Segment-Specific KPIs:** The dashboard tracks key metrics for specific segments, such as a **YTD Loan Amount of $9.3M** and a total loan value for **Middle Aged Adults of $510.5M**.
