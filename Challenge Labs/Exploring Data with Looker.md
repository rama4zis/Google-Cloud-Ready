# 1. Create Looks

1.  Most heliports by state

    In this section, you will need to use the **Airports** dataset to build a visualization that answers the following question: Which states and cities have the most airports with heliports?

    -  Navigate to Explore > Airports.
    -  Select **City**, **State**, and **Count**.
    -  Limit the results (rows) to the top **10** states ( *It can be different by your challenge lab environment* )
    -  Hover to **Facility** Type > Click the filter icon > **Is equal to** **HELIPORT**.
    -  Click Run, show visualization as table.
    -  Save as a Look called **Top Count Cities With Most Heliports** ( *It can be different by your challenge lab environment* ).

2.  Facility type breakdown

    In this section, you will need to use the Airports dataset to build a visualization that answers the following question: What is the facility type breakdown for the states with the most airports?

    -  Navigate to Explore > Airports.
    -  Select State, Facility Type, and Count.
    -  Hover to Facility Type and make it Descending order.
    -  Click setting gear on Facility Type > Click Pivot 
    -  Then make it Facility Type Ascending order.
    -  Set Row limit to **10**. ( *It can be different by your challenge lab environment* )
    -  Click Run.
    -  See the visualization as a table.
    -  Save as a Look called **Facility Type Breakdown for Top Count States** ( *It can be different by your challenge lab environment* ).

3.  Percentage cancelled

    In this section, you will need to use the Flights dataset to build a visualization that answers the following question: What are the airports and states with the highest percentage of flight cancellations with over 10,000 flights?

    -   Navigate to Explore > Flights.
    -   Select Aircraft Origin > City, State.
    -   Select Flights Details > **Cancelled Count**.
    -   Select Flights > **Count**.
    -   Select "+Add" on Custom Fields > Table Calculation, then paste the following formula: 
    ```
    ${flights.cancelled_count}/${flights.count}
    ```
    -   Select Format > Percentage.
    -   Make the percentage to **3** ( *It can be different by your challenge lab environment*)
    -   Set name to **Percentage of Flights Cancelled**
    -   Click save
    -   Click the gear icon next to the Flights Count column and select Filter > **Is greater than** **10000**. ( *It can be different by your challenge lab environment* )
    -   Make sure the **Percentage of Flights Cancelled** column is sorted in descending order.
    -   Click the gear icon next to the Flights Count column and select "Hide from Visualization". Repeat this for the Cancelled Count Column. In the visualization pane, make sure you're using a table.
    -   Click Run
    -   Save this visualization as a Look. Title it: "**States and Cities with Highest Percentage of Cancellations: Flights over 10,000**".

4.  Smallest average distance

    In this section, you will need to use the Flights dataset to build a visualization that answers the following question: What are the origin and destination airports with the smallest average distance between them?

    -   Navigate to Explore > Flights.
    -   Select Flights > Origin and Destination.
    -   Clcik "+Add" on Custom Fields > Custom Measure, then paste the following formula:
    
            Field to measure: Average Distance
            Name: Average Distance (Miles)
    -   Click save
    -   Then Filter your custom measure to **Is greater than** **0**. ( *It can be different by your challenge lab environment* )
    -   Make sure the Average Distance (Miles) column is sorted in ascending order.
    -   Set row limit to **10**. ( *It can be different by your challenge lab environment* )
    -   Click Run
    -   See visualization as a table
    -   Save this visualization as a Look. Title it: "**Top Count Airports With Smallest Average Distance**".


# 2. Merge results

-   Navigate to Explore > Flights.
-   Select Aircraft Origin > City, State and Code.
-   Select Flights > Count.
-   Set row limit to **10**. ( *It can be different by your challenge lab environment* )
-   Click Run
-   Click setting gear next to Run > Merge Results
-   Open Airports
-   Select Airport > State, City and Code.
-   Hover to Control Tower (Yes/No) > Click filter icon > Is equal to **Yes**.
-   Hover to Is Major (Yes/No) > Click filter icon > Is equal to **Yes**.
-   Hover to Joint Use (Yes/No) > Click filter icon > Is equal to **Yes**.
-   Set limit row to **10**. ( *It can be different by your challenge lab environment* )
-   Click Run
-   Click Save
-   Click Run again
-   See Visualization as a Bar
-   Click gear icon next to Run > Save to Dashboard
-   Save as a Dashboard called **Busiest, Major Joint-Use Airports with Control Towers** ( *It can be different by your challenge lab environment* )
-   Click "New Dashboard" > Save as a Dashboard called **Plane and Helicopter Rental Hub Board** ( *It can be different by your challenge lab environment* )
-   Click save to dashboard

# 3. Save looks to a dashboard

Navigate to Looker Home

-   Navigate to Folders > My Folders
-   Open First Item in new tab
-   Then click "Add to Dashboard"
-   Click on Shared > Select the dashboard you created in the previous step
-   Do the same for each of the Looks you created in the previous step