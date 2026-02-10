# Dashboards

Dashboards are control panels that display multiple views (e.g., lists, charts, forms) on a single page, enabling aggregated overviews and interactive data monitoring. 

## Item Structure

Dashboard items are objects representing embedded views. Each item has the following properties:

| **Property** | **Type** | **Description** |
| ------------ | -------- | --------------- |
| `id` | string | Unique identifier of the item (used for translations and references) |
| `label` | string | Title of the item displayed on the dashboard |
| `description` | string | (optional) Short string explaining the purpose of the item (e.g., the view it represents) |
| `width` | string | (optional) Width of the item as a percentage (e.g., `"50%"`). If omitted, defaults to full width or auto-layout |
| `entity` | string | Full name (with namespace) of the entity related to the view (e.g., `'core\User'`) |
| `view` | string | ID of the view to embed (e.g., `'list.default'`, `'chart.default'`) |
| `domain` | array | (optional) Domain filter as an array of conditions (e.g., `[['status', '=', 'active']]`). Applied to the embedded view |

Example:

```json
[
    {
        "id": "user_list_panel",
        "label": "User List",
        "description": "List of active users",
        "width": "50%",
        "entity": "core\\User",
        "view": "list.default",
        "domain": [["is_active", "=", true]]
    },
    {
        "id": "user_chart_panel",
        "label": "User Growth Chart",
        "description": "Monthly user growth",
        "width": "50%",
        "entity": "core\\User",
        "view": "chart.default"
    }
]
```

This structure allows flexible multi-view layouts. Dashboards are rendered responsively and can include various view types. If you need examples from specific packages, check core for dashboard-related models.