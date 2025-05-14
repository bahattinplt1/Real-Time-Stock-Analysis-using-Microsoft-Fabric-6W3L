# Real-Time Stock Analysis using Microsoft Fabric

This project demonstrates how to build a real-time analytics solution using Microsoft Fabric Real-Time Intelligence. It connects to a streaming data source, stores the data in a table, allows querying using KQL, visualizes the results in a live dashboard, and sends email alerts based on real-time conditions.

## Create a Workspace

1. Go to https://app.fabric.microsoft.com/home?experience=fabric and sign in with your Microsoft Fabric account.
2. From the left menu, select **Workspaces**.
3. Create a new workspace. Make sure to select a license mode that includes Fabric capacity (Trial, Premium, or Fabric).
4. Once the workspace is created, open it. It should be empty.

## Create an Eventstream

1. From the left menu, open the **Real-Time hub**.
2. Under the **Connect to** section, choose **Data sources**.
3. Locate the **Stock market sample** data source and click **Connect**.
4. In the connection wizard:
   - Name the source `stock`.
   - Change the eventstream name to `stock-data`.
   - The default stream will be named `stock-data-stream`.
5. Click **Next**, wait for the eventstream to be created, then click **Open eventstream**.
6. You will see `stock` as the source and `stock-data-stream` as the stream on the design canvas.

## Create an Eventhouse

1. From the left menu, select **Create**, then under **Real-Time Intelligence**, choose **Eventhouse**.
2. Give the eventhouse a unique name.
3. Close any onboarding tips that appear.
4. On the left pane, you’ll see a KQL database created with the same name as your eventhouse.
5. Select the database. You’ll see a `queryset` file with sample queries.
6. On the main page of your database, click **Get data**.
7. For the data source, select **Eventstream > Existing eventstream**.
8. In the destination table pane:
   - Create a new table named `stock`.
9. In the source configuration pane:
   - Select your workspace.
   - Choose the `stock-data` eventstream.
   - Name the connection `stock-table`.
10. Click **Next**, inspect the data, then finish the setup.
11. The `stock` table will now appear in your eventhouse.

## Query the Captured Data

1. Open the `queryset` file inside your KQL database.
2. Run the following query to view recent data:

```kql
stock
| take 100
```

3. Then run this query to calculate average prices for each symbol over the last 5 minutes:

```kql
stock
| where ["time"] > ago(5m)
| summarize avgPrice = avg(todecimal(bidPrice)) by symbol
| project symbol, avgPrice
```

4. Run the query again after a few seconds to see how the values change with new incoming data.

## Create a Real-Time Dashboard

1. Select the 5-minute average price query in the editor.
2. Click **Pin to dashboard**.
3. Use the following settings:
   - **Dashboard name**: `Stock Dashboard`
   - **Tile name**: `Average Prices`
4. Create and open the dashboard.
5. At the top of the dashboard, switch from **Viewing mode** to **Editing mode**.
6. Click the pencil icon on the `Average Prices` tile.
7. In the **Visual formatting** pane, change the visual type from **Table** to **Column chart**.
8. Click **Apply changes**.

You now have a live, real-time visualization of streaming stock data.

## Create an Alert

1. In the dashboard, click **Set alert** on the toolbar.
2. In the **Set alert** pane, configure it with the following settings:
   - **Run query every**: 5 minutes
   - **Check**: On each event grouped by
   - **Grouping field**: `symbol`
   - **When**: `avgPrice`
   - **Condition**: Increases by
   - **Value**: 100
   - **Action**: Send me an email
   - **Save location**:
     - **Workspace**: your current workspace
     - **Item**: Create a new item
     - **New item name**: a unique name like `stock-alert-100plus`
3. Click **Create** and wait for the alert to be saved. Then close the confirmation panel.
4. From the left menu, go back to your workspace (save the dashboard if prompted).
5. In your workspace, locate the newly created **Activator** item.
6. Open the activator. Under the `avgPrice` node, select the alert by its name.
7. Open the **History** tab.

If the alert hasn't been triggered yet, the history will be empty. Once the average price increases by more than 100, the alert will be triggered and an email will be sent to you.
