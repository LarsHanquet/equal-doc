# Dashboards

Dashboards are control panels that display multiple [views](TODO) (lists, charts, forms) on a single page, enabling aggregated overviews and interactive data monitoring. They allow users to monitor multiple metrics and datasets simultaneously without navigating between different pages.

**Purpose:** Summarize and display data from multiple views or entities in a unified interface.

**Example:** A sales dashboard showing a revenue trend chart, recent orders list, and key metrics, all updated in real-time.

**Key Features:**

* Aggregates multiple subcontexts and views in a single layout
* Flexible grid-based layout with customizable widths
* Responsive design adapting to different screen sizes
* Domain-based filtering applied to embedded views
* Support for multiple view types: lists, charts, forms, and metrics
* Independent refresh and interaction of embedded views

---

## Dashboard Structure

Dashboards are configured as JSON objects containing an array of dashboard items. Each item represents an embedded view with layout and filtering properties.

**Dashboard Properties:**

| **PROPERTY** | **TYPE**                         | **DESCRIPTION**                                    |
| ------------ | -------------------------------- | -------------------------------------------------- |
| `items`      | array of [Item](#item-structure) | Array of embedded views displayed on the dashboard |

**Complete Dashboard Example:**

```json
{
  "name": "Sales Dashboard",
  "description": "Overview of sales metrics and recent activity",
  "items": [
    {
      "id": "revenue_chart",
      "label": "Monthly Revenue",
      "width": 50,
      "entity": "sales\\Sale",
      "view": "chart.revenue"
    },
    {
      "id": "recent_orders",
      "label": "Recent Orders",
      "width": 50,
      "entity": "sales\\Order",
      "view": "list.default",
      "domain": [["status", "=", "completed"]]
    }
  ]
}
```

---

## Item Structure

Dashboard items are objects representing embedded views on the dashboard. Each item specifies which view to display, how to position it, and optional filters to apply.

**Item Properties:**

| **PROPERTY**  | **TYPE**  | **DESCRIPTION**                                                                                                 |
| ------------- | --------- | --------------------------------------------------------------------------------------------------------------- |
| `id`          | `string`  | Unique identifier for the item (used for translations and internal references)                                  |
| `label`       | `string`  | Title displayed above the item on the dashboard                                                                 |
| `description` | `string`  | (optional) Short explanation of the item's content or purpose (e.g., "Shows active users from the past month")  |
| `width`       | `integer` | (optional) Width as a percentage of the dashboard width (1-100). If omitted, defaults to responsive auto-layout |
| `entity`      | `string`  | Full class name (with namespace) of the entity (e.g., `core\User`, `sales\Order`)                               |
| `view`        | `string`  | View ID to embed (e.g., `list.default`, `chart.revenue`, `form.overview`)                                       |
| `domain`      | `array`   | (optional) [Domain](TODO) conditions to filter the embedded view (e.g., `[["status", "=", "active"]]`)          |

---

## Layout and Positioning

Dashboard items are arranged in a responsive grid layout. The `width` property controls how many columns an item spans.

**Width Guidelines:**

* **Full width:** `100` - Item spans the entire dashboard width
* **Half width:** `50` - Two items fit side-by-side
* **Third width:** `33` - Three items fit side-by-side
* **Quarter width:** `25` - Four items fit side-by-side
* **Custom width:** Any value 1-100 is supported, though widths that divide evenly (25, 33, 50, 100) work best for clean layouts

**Single Row, Two Columns:**

```json
{
  "items": [
    {
      "id": "item_1",
      "label": "First Chart",
      "width": 50,
      "entity": "core\\User",
      "view": "chart.default"
    },
    {
      "id": "item_2",
      "label": "Second Chart",
      "width": 50,
      "entity": "core\\User",
      "view": "chart.default"
    }
  ]
}
```

**Two Rows, Three Columns (Row 1) + Two Columns (Row 2):**

```json
{
  "items": [
    {
      "id": "metric_1",
      "label": "Active Users",
      "width": 33,
      "entity": "core\\User",
      "view": "chart.metrics"
    },
    {
      "id": "metric_2",
      "label": "Revenue",
      "width": 33,
      "entity": "sales\\Sale",
      "view": "chart.metrics"
    },
    {
      "id": "metric_3",
      "label": "Conversions",
      "width": 34,
      "entity": "sales\\Conversion",
      "view": "chart.metrics"
    },
    {
      "id": "list_1",
      "label": "Recent Orders",
      "width": 50,
      "entity": "sales\\Order",
      "view": "list.default"
    },
    {
      "id": "list_2",
      "label": "Pending Approvals",
      "width": 50,
      "entity": "approval\\Request",
      "view": "list.default"
    }
  ]
}
```

---

## View Types

Dashboards can embed any view type available in the system. Each view type renders differently within the dashboard context.

**Supported View Types:**

* **[Lists](TODO)** - Tabular display of records with sorting, filtering, and pagination
* **[Charts](TODO)** - Visual data aggregations (bar, pie, line, area charts)
* **[Forms](TODO)** - Single record display or editing (typically in read-only mode for dashboards)

**Example - Mixed View Types:**

```json
{
  "items": [
    {
      "id": "kpi_revenue",
      "label": "Total Revenue",
      "width": 25,
      "entity": "sales\\Sale",
      "view": "metric.total_revenue"
    },
    {
      "id": "kpi_users",
      "label": "Active Users",
      "width": 25,
      "entity": "core\\User",
      "view": "metric.active_count"
    },
    {
      "id": "revenue_chart",
      "label": "Revenue Trend",
      "width": 50,
      "entity": "sales\\Sale",
      "view": "chart.monthly_revenue"
    },
    {
      "id": "orders_list",
      "label": "Recent Orders",
      "width": 100,
      "entity": "sales\\Order",
      "view": "list.default"
    }
  ]
}
```

---

## Filtering

The `domain` property applies [domain](TODO) conditions to filter data displayed in embedded views. Filters are applied independently to each item without affecting other dashboard items.

**Single Domain Condition:**

```json
{
  "id": "active_users",
  "label": "Active Users",
  "entity": "core\\User",
  "view": "list.default",
  "domain": [["is_active", "=", true]]
}
```

**Multiple Conditions (AND):**

```json
{
  "id": "recent_sales",
  "label": "Recent Completed Sales",
  "entity": "sales\\Sale",
  "view": "list.default",
  "domain": [
    ["status", "=", "completed"],
    ["created", ">", "2024-01-01"]
  ]
}
```

**Complex Conditions (OR):**

```json
{
  "id": "priority_orders",
  "label": "Priority Orders",
  "entity": "sales\\Order",
  "view": "list.default",
  "domain": [
    ["priority", "in", ["high", "urgent"]],
    ["is_archived", "=", false]
  ]
}
```

---

## Access Control

Dashboard visibility can be restricted through the [access control](TODO) system. Refer to the [views documentation](TODO) for configuring group-based or user-based access restrictions.

Individual items within a dashboard inherit access restrictions from their referenced views. If a user lacks permissions to view a particular item's view or entity, that item is either hidden or displays an access denied message.

---

## Complete Dashboard Examples

### Sales and Revenue Dashboard

```json
{
  "name": "Sales Overview",
  "description": "Real-time sales metrics and order tracking",
  "items": [
    {
      "id": "total_revenue",
      "label": "Total Revenue",
      "width": 25,
      "entity": "sales\\Sale",
      "view": "metric.revenue_total"
    },
    {
      "id": "pending_orders",
      "label": "Pending Orders",
      "width": 25,
      "entity": "sales\\Order",
      "view": "metric.pending_count"
    },
    {
      "id": "conversion_rate",
      "label": "Conversion Rate",
      "width": 25,
      "entity": "sales\\Conversion",
      "view": "metric.rate"
    },
    {
      "id": "avg_order_value",
      "label": "Avg Order Value",
      "width": 25,
      "entity": "sales\\Order",
      "view": "metric.average_value"
    },
    {
      "id": "revenue_trend",
      "label": "Revenue Trend",
      "width": 60,
      "entity": "sales\\Sale",
      "view": "chart.monthly_revenue"
    },
    {
      "id": "orders_by_status",
      "label": "Orders by Status",
      "width": 40,
      "entity": "sales\\Order",
      "view": "chart.status_distribution"
    },
    {
      "id": "recent_orders",
      "label": "Recent Orders",
      "width": 100,
      "entity": "sales\\Order",
      "view": "list.default",
      "domain": [["status", "!=", "archived"]]
    }
  ]
}
```

### User Management Dashboard

```json
{
  "name": "User Management",
  "description": "User statistics and activity monitoring",
  "items": [
    {
      "id": "total_users",
      "label": "Total Users",
      "width": 25,
      "entity": "core\\User",
      "view": "metric.total_count"
    },
    {
      "id": "active_users",
      "label": "Active Users",
      "width": 25,
      "entity": "core\\User",
      "view": "metric.active_count"
    },
    {
      "id": "new_users",
      "label": "New Users (This Month)",
      "width": 25,
      "entity": "core\\User",
      "view": "metric.new_count"
    },
    {
      "id": "inactive_users",
      "label": "Inactive Users",
      "width": 25,
      "entity": "core\\User",
      "view": "metric.inactive_count"
    },
    {
      "id": "user_growth",
      "label": "User Growth",
      "width": 50,
      "entity": "core\\User",
      "view": "chart.monthly_growth"
    },
    {
      "id": "users_by_role",
      "label": "Users by Role",
      "width": 50,
      "entity": "core\\User",
      "view": "chart.role_distribution"
    },
    {
      "id": "all_users",
      "label": "All Users",
      "width": 100,
      "entity": "core\\User",
      "view": "list.default"
    }
  ]
}
```

### Multi-Entity Dashboard

```json
{
  "name": "Business Summary",
  "description": "Comprehensive overview of all business operations",
  "items": [
    {
      "id": "revenue_metric",
      "label": "Monthly Revenue",
      "width": 33,
      "entity": "sales\\Sale",
      "view": "metric.month_revenue"
    },
    {
      "id": "user_metric",
      "label": "Active Users",
      "width": 33,
      "entity": "core\\User",
      "view": "metric.active"
    },
    {
      "id": "inventory_metric",
      "label": "Low Stock Items",
      "width": 34,
      "entity": "inventory\\Product",
      "view": "metric.low_stock_count"
    },
    {
      "id": "sales_chart",
      "label": "Sales by Region",
      "width": 50,
      "entity": "sales\\Sale",
      "view": "chart.by_region"
    },
    {
      "id": "inventory_chart",
      "label": "Inventory Status",
      "width": 50,
      "entity": "inventory\\Product",
      "view": "chart.status_breakdown"
    },
    {
      "id": "pending_tasks",
      "label": "Pending Tasks",
      "width": 50,
      "entity": "core\\Task",
      "view": "list.default",
      "domain": [["status", "=", "pending"]]
    },
    {
      "id": "recent_activity",
      "label": "Recent Activity",
      "width": 50,
      "entity": "core\\Activity",
      "view": "list.recent"
    }
  ]
}
```

---

## Dashboard Design Best Practices

* **Organize by importance:** Place critical metrics and charts at the top of the dashboard
* **Use consistent sizing:** Group related items with similar widths for visual coherence
* **Apply meaningful filters:** Use domain conditions to show only relevant data (e.g., active records, recent activity)
* **Avoid overcrowding:** Limit to 6-8 items per dashboard; create multiple dashboards for different purposes
* **Mix view types:** Combine metrics for quick insights, charts for trends, and lists for detailed information
* **Label clearly:** Use descriptive labels and descriptions to help users understand each item's purpose
* **Test responsiveness:** Verify the dashboard layout works well on different screen sizes and devices

---