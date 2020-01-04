# DAX-functions
Useful DAX expressions for future use


While generally ideal to aggregate/group data in 'M', sometimes this is not possible as you have created a calculated column that used DAX.
This will not be shown when inside of the query editor. The solution to this is to create a DAX table through the 'SUMMARIZE' function. 


Aggregated Table = SUMMARIZE(table,field1,field2,field3,fieldX,"Aggregate1title",COUNT(field_n),"Aggregate2title",
MIN(field_n1),"Aggregate3title",MAX(field_n2)) 

Cumulative Sum = CALCULATE(SUM(field),FILTER(ALL(table),id_field=EARLIER(id_field) 
&& date_field = EARLIER(date_field)))

This function will take the whole table context into account using the 'ALL' function.
The 'EARLIER' function compares the current row vs. previous rows matching the conditions.

Dynamic Title = "fill text here" & CONCATENATEX(values(field),field,", ")

This function will show all currently selected values for the given field based on the filters applied. 
Replace 'fill text here' with static text.
If multiple values are selected, the values will be seperated by a comma and a space.

For reports and dashboards to be updated hourly or daily, a dynamic value is often required based on the changing dates.
The 'DATESBETWEEN' function will act as a filter and only return values that pass the criteria. 
Using 'MAX' date rather than 'TODAY' is important in the case of there being an error or lack of data for today. In that case,
Power BI would simply pull the previous day in that case.

Ex.
Measure (7 Days Back - Max Date) = CALCULATE(field,DATESBETWEEN(date_field, MAX(date_field)-6,MAX(date_field)))
Measure (13 Days Back - 8 Days Back) = CALCULATE(field,DATESBETWEEN(date_field, MAX(date_field)-13,MAX(date_field)-7))

Using these multiple measures, we can now evaluate the deviation week to week (or whatever date range is set)
Measure (7 Days Back - Max Date)/Measure (13 Days Back - 8 Days Back) = Change From Last Week

Non-Equi Aggregation= CALCULATE(            
        SUMX(Base_Table,Field_to_Sum),
        FILTER(
            Base_Table,
            EndTimeField_Second_Table>EndTimeField_Base_Table
            && Person_Second_Table = Person_Base_Table
            && StartDate_Second_Table = StartDate_Base_Table
            && StartTimeField_Second_Table < StartTimeField_Base_Table
        )
        )

This function will sum up all the values that follow the conditions:
 -Person/Unique ID in table 1 = Person/Unique ID in table 2
 -Start Time is greater in table 2 than table 1
 -End Time is less in table 2 than table 1
 -The date in table 1 = the date in table 2

Additional conditions can be set using '&&'
