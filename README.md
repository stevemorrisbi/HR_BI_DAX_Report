# HR Staff Count Report
Power BI report created to analyse staff count from 2017 to 2021.
![hr_staff_count](https://user-images.githubusercontent.com/99413257/169857026-5fb844b1-b43e-4727-a8e2-46534a0d0e8e.gif)

### Production of Dax Measures for HR Staff Report –

#### Measure for Headcount

Headcount = 
CALCULATE( 
    [Count of Employees]
    , FILTER( 
        Employees
        , (
        Employees[start_date] <= MAX('Calendar'[Date]) 
        && Employees[end_date] > MAX('Calendar'[Date]) 
        ) || ISBLANK(Employees[end_date])
    ) 
)

For a dynamic total headcount of staff – the above measure was produced. The CALCULATE function operates similar to a SUMIF function in SQL. The above measure translates to: we want to sum the count of employees (which is a simple measure create using COUNT) and then Filter it by three clauses.
It will total the sum of the employees if they meet this criteria –
•	The employees start date is less than or equal to the last day in the calender used for the analysis.
•	And (&&) the employees end date is greater than the last day in the calender used for the analysis.
•	Or (||) if the employee end date is blank. E.g the employee doesn’t have an end date filled in because they haven’t left.
 

#### Measure for Leavers 

Leavers = 
CALCULATE( 
[Count of Employees], 
USERELATIONSHIP(
'Calendar'[Date]
, Employees[end_date]
) 
) + 0

The above measure translates to: we want the total number of employees whose end date appears in the date selected. The USERELATIONSHIP function takes the inactive relationship set up between the Date table and the Employees table and makes it active within the use of this measure. The + 0 at the end makes sure that if there are no leavers then 0 will be return on the visualisation. 

#### The Starters Measure

Starters = 
CALCULATE( 
[Count of Employees]
, USERELATIONSHIP(
    'Calendar'[Date]
    , Employees[start_date] 
    )
)

The starters measure is very similar to the leavers measure. It requires the USERELATIONSHIP function as there is multiple relationships between the employees table and the dates table (it requires this to ensure the relationship becomes active). 


#### The Turnover percentage measure

Turnover % = DIVIDE([Leavers], [Headcount], 0)

The DIVIDE function has been used over / symbol to ensure best practice. If a calculation involves division by 0 using / then this can cause problems within the visualisation. The 0 at the end means if the result of the calculation is 0, then 0 will be returned instead of Blank. Once the measure is produced it is then formatted as a percentage. 

