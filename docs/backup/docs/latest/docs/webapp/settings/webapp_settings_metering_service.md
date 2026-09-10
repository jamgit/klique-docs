# Metering Service

> Metering Service settings define usage categories and pricing for tracking resource consumption and costs.

The **Metering Service** section allows administrators to define usage categories used for tracking resource 
consumption and calculating usage costs. 

Admins can view the usage and spend information through the [**Metering**](../webapp_admin_dashboard_metering.md) tab of 
the Admin Dashboard. 

The Metering tab lists all usage categories available for your tenant. Categories defined by the platform administrator 
are read-only. You can also create, edit, and delete categories that belong to your tenant. 

## Categories

A category defines a type of tracked usage event:

To create a category:
1. Click **Add Category**.
1. Configure the category:
   * Category Key - Identifier used when reporting events.
   * Display Name - Name displayed in the UI.
   * Optional UI indicator information. The Metering tab uses this value to present the data with type-specific styling 
     or icons. Available options:  Compute, Storage, Model Tokens, Users, Service Accounts.
   * Count Strategy -  How daily values are aggregated. The options are:
     * Sum - Sum all reported values for a daily total
     * Max - Store the maximum reported value for the day
   * Unit Name - Optional unit displayed alongside usage values.
   * Labels - Each category can contain one or more labels representing specific usage items within the category. For each label, configure:
     * Label Key - Unique identifier used when reporting events.
     * Display Name - Name displayed in the UI
     * Price - Cost per unit.
     * Units - Number of usage units associated with the configured price.
     * Don't show - Select to ignore the label in usage presentation
1. Click **Save**.
