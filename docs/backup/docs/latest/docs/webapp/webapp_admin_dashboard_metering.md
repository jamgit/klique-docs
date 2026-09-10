# Metering

> The Metering dashboard tab tracks usage and spend over time based on custom metered events.

The **Metering** tab is available where the service administrators have configured [custom event metering](../deploying_klique/extra_configs/event_metering.mdx),
enabling administrators to monitor usage and spend over a selected time period, based on custom events metered by their server.
Spend estimates are provided for events that have cost associated to them by the service administrators.

This tab helps admins:
* Understand how usage changes over time
* Compare current usage to previous periods
* Identify cost drivers across metered events

The **Metering** tab shows:
* Estimated Cost for the selected period
* [Usage and cost plots](#usage-and-cost-plots) for the metered events

The time period of the displayed metered events can be controlled through the **Report Period** menu on the top of the tab. 
Changing the report period updates all plots and the estimated cost accordingly.

## Usage and Cost Plots
The Metering tab shows plots for all metered events. By default, Klique provides metering of:
* Cluster Compute
* Service Accounts
* Storage
* Users

Each plot displays:
* Selected period - Usage over the selected report period
* Comparative period - Usage over the previous equivalent period (e.g. usage for the previous 7 days)
* Trend indicator - Displays the increase or decrease in usage and cost compared to the previous period. 

Click **View Period Details** for a detailed breakdown of a metrics usage and costs.

In the details view, you can:
* Adjust the Report Period specifically for that metric
* Toggle between: 
  * Usage view - View usage over time and a breakdown by usage categories (e.g. Compute: usage per GPU type, Storage: usage per storage service)
  * Cost view - View cost over time and a breakdown by metric categories (e.g. Compute: cost per GPU type, Storage: cost per storage service)
* Switch how the breakdown is grouped:
  * By Category (default) - Breaks down usage/cost by the metric's categories (e.g. Compute: by GPU type, Storage: by storage service)
  * By User - Breaks down usage/cost by user
  * By Project - Breaks down usage/cost by project

Totals for the period, grouped by whichever breakdown is selected (category, user, or project), are also available in the table below the chart.

Click a category, user, or project to drill down into its breakdown by the other groupings (e.g. clicking a category 
lets you break it down further by user or project; clicking a user or project breaks it down by category).
