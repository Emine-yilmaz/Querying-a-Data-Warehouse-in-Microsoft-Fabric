# Querying a Data Warehouse in Microsoft Fabric

This guide demonstrates how to query a data warehouse in Microsoft Fabric using the built-in SQL query editor. You'll learn to execute SQL statements, analyze data, check for data consistency, and create views for simplified data access.

**Note:** This lab requires a Microsoft Fabric trial or a Fabric-enabled license (Premium or Fabric).

## Prerequisites

* A Microsoft Fabric account with a trial or paid license.
* A web browser to access the Microsoft Fabric portal.

## Steps

### 1. Create a Workspace

1.  Navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com/home?experience=fabric) and sign in with your Fabric credentials.
2.  In the left-hand menu, click on **Workspaces** (🗇 icon).
3.  Click **New workspace**.
4.  Provide a name for your workspace.
5.  Ensure that the licensing mode includes Fabric capacity (Trial, Premium, or Fabric).
6.  Click **Create**.

### 2. Create a Sample Data Warehouse

1.  Within your new workspace, click on **Create** in the left-hand menu.
2.  Under the **Data Warehouse** section, select **Sample warehouse**.
3.  Name the new data warehouse `sample-dw`.
4.  Click **Create**.
5.  Wait for a short period while the sample data warehouse is created and populated with data for a taxi ride analysis scenario.

### 3. Query the Data Warehouse

1.  On the `sample-dw` data warehouse page, in the **New SQL query** drop-down list, select **New SQL query**.

2.  In the new blank query pane, enter and run the following query to see the total trips and revenue by month:

    ```sql
    SELECT
        D.MonthName,
        COUNT(*) AS TotalTrips,
        SUM(T.TotalAmount) AS TotalRevenue
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.MonthName;
    ```

    Click the **▷ Run** button to execute the script and view the results.

3.  Enter and run the following query to see the average trip duration and distance by day of the week:

    ```sql
    SELECT
        D.DayName,
        AVG(T.TripDurationSeconds) AS AvgDuration,
        AVG(T.TripDistanceMiles) AS AvgDistance
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.DayName;
    ```

    Run the modified query and view the results.

4.  Enter and run the following queries to see the top 10 most popular pickup and dropoff locations:

    ```sql
    SELECT TOP 10
        G.City,
        COUNT(*) AS TotalTrips
    FROM dbo.Trip AS T
    JOIN dbo.Geography AS G
        ON T.PickupGeographyID=G.GeographyID
    GROUP BY G.City
    ORDER BY TotalTrips DESC;

    SELECT TOP 10
        G.City,
        COUNT(*) AS TotalTrips
    FROM dbo.Trip AS T
    JOIN dbo.Geography AS G
        ON T.DropoffGeographyID=G.GeographyID
    GROUP BY G.City
    ORDER BY TotalTrips DESC;
    ```

    Run the modified query and view the results.

5.  Close all query tabs.

### 4. Verify Data Consistency

1.  In the **New SQL query** drop-down list, select **New SQL query**.

2.  In the new blank query pane, enter and run the following query to check for trips with unusually long duration:

    ```sql
    -- Check for trips with unusually long duration
    SELECT COUNT(*) FROM dbo.Trip WHERE TripDurationSeconds > 86400; -- 24 hours
    ```

    Run the query and view the results.

3.  In the **New SQL query** drop-down list, select **New SQL query** to open a second query tab. In the new tab, run the following query to check for trips with negative trip duration:

    ```sql
    -- Check for trips with negative trip duration
    SELECT COUNT(*) FROM dbo.Trip WHERE TripDurationSeconds < 0;
    ```

4.  In a new blank query pane, enter and run the following query to remove trips with negative trip duration:

    ```sql
    -- Remove trips with negative trip duration
    DELETE FROM dbo.Trip WHERE TripDurationSeconds < 0;
    ```

    **Note:** While this lab demonstrates deletion, consider alternative approaches like replacing inconsistent data with mean or median values in real-world scenarios.

5.  Close all query tabs.

### 5. Save as View

1.  In the **New SQL query** drop-down list, select **New SQL query**.

2.  Enter and run the following Transact-SQL code:

    ```sql
    SELECT
        D.DayName,
        AVG(T.TripDurationSeconds) AS AvgDuration,
        AVG(T.TripDistanceMiles) AS AvgDistance
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.DayName;
    ```

3.  Modify the query to filter data for January by adding `WHERE D.Month = 1`:

    ```sql
    SELECT
        D.DayName,
        AVG(T.TripDurationSeconds) AS AvgDuration,
        AVG(T.TripDistanceMiles) AS AvgDistance
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    WHERE D.Month = 1
    GROUP BY D.DayName;
    ```

4.  Select the entire `SELECT` statement text in your query.

5.  Next to the **▷ Run** button, click **Save as view**.

6.  Create a new view named `vw_JanTrip`.

7.  In the **Explorer** pane, navigate to **Schemas** » **dbo** » **Views**. Verify that the `vw_JanTrip` view has been created.

8.  Close all query tabs.

**Further Information:** For more details on querying a data warehouse, refer to the [Query using the SQL query editor](https://learn.microsoft.com/en-us/fabric/data-warehouse/sql-query-editor) documentation in Microsoft Fabric.

### 6. Clean Up Resources

1.  In the left-hand menu, click on your workspace name.
2.  Select **Workspace settings**.
3.  In the **General** section, scroll down and select **Remove this workspace**.
4.  Click **Delete** to confirm the deletion of the workspace.

You have now successfully queried a data warehouse in Microsoft Fabric, explored its data, checked for consistency, and created a view!
