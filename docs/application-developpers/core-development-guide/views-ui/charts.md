# Charts

Charts are visual components for displaying data aggregations, such as graphs, metrics, or trends, integrated into dashboards and views. They enable no-code creation of data visualizations by defining datasets, operations, and filters. 

## Structure

Charts are defined as JSON objects with properties for access control, data ranges, datasets, and filters. 

| **PROPERTY** | **TYPE** | **DESCRIPTION** |
| ------------ | --------  | --------------- |
| `access` | array | (optional) List of group IDs allowed to view the chart (e.g., `["group.admin"]`). If omitted, visible to all authenticated users |
| `layout` | object | (optional) Describes how chart items (e.g., axes, legends) are displayed. Structure depends on chart type (e.g., `{ "type": "bar", "x_axis": "date", "y_axis": "value" }`) |
| `range_interval` | string | (optional) Time period for data delimitation. Options: `"day"`, `"week"`, `"month"`, `"quarter"`, `"semester"`, `"year"` |
| `range_from` | string | (optional) Start of the data range, linked to `range_interval`. Format: `date[this | prev | next].[day | week | month | quarter | semester | year].[first | last]` (e.g., `"date.this.month.first"`) |
| `range_to` | string | (optional) End of the data range, linked to `range_interval`. Same format as `range_from` (e.g., `"date.this.month.last"`) |
| `dataset` | array | Array of data series. Each object includes `label` (string: name for the series) and other properties like `field` (string: data field) or `color` (string: display color) |
| `operation` | string | Aggregation operation for data (e.g., `"count"`, `"sum"`, `"avg"`). References operations from related sections (e.g., ORM aggregations) |
| `domain` | array | (optional) Additional filters as an array of conditions (e.g., `[["status", "=", "active"]]`). Applied to the dataset before aggregation |

Example:
```json
{
    "access": ["group.admin", "group.manager"],
    "layout": {
        "type": "bar",
        "x_axis": "month",
        "y_axis": "count",
        "legend": true
    },
    "range_interval": "month",
    "range_from": "date.this.year.first",
    "range_to": "date.this.year.last",
    "dataset": [
        {
            "label": "Active Users",
            "field": "id",
            "color": "#007bff"
        }
    ],
    "operation": "count",
    "domain": [["is_active", "=", true]]
}
```

This structure allows flexible data visualization. Charts are rendered in dashboards and can include multiple datasets or complex domains. If you need examples from specific packages, check core for chart-related models.