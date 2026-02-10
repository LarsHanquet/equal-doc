# Menus

Menus define custom tree structures of action-buttons for accessing specific routes or contexts. They enable navigation within applications by organizing views (e.g., forms, lists, charts, dashboards) into hierarchical structures.

## Menu Item Structure

Menu items are objects with the following properties:

| **PROPERTY** | **TYPE**  | **DESCRIPTION** |
| ------------ | -------- | --------------- |
| `id` | string  | Unique identifier of the item (used for translations and references) |
| `label` | string  | Title of the item displayed in the menu |
| `description` | string  | (optional) Short string explaining the purpose of the item (e.g., the view it leads to) |
| `icon` | string | (optional) Icon to display alongside the item (e.g., Material Icons name) |
| `type` | string | Either `'entry'` (leaf node) or `'parent'` (has children) |

### Parent Items

Parent items include a `children` property, which is an array of nested menu items (each can be an entry or another parent).

### Entry Items

Entry items include a `context` property, defining the target view or route. The `context` object has the following structure:

| **PROPERTY** | **TYPE**  | **DESCRIPTION** |
| ------------ | -------- | --------------- |
| `entity` | string | Full name (with namespace) of the entity related to the view (e.g., `'core\User'`) |
| `view` | string | ID of the view to display (e.g., `'list.default'` or `'form.default'`) |
| `order` | string | (optional) Column name for sorting results (e.g., `'name'`) |
| `sort` | string | (optional) Sort direction: `'asc'` or `'desc'` (default: `'asc'`) |
| `domain` | array | (optional) Domain filter as an array of conditions (e.g., `[['status', '=', 'active']]`) |

Example:

```json
[
    {
        "id": "users_menu",
        "label": "Users",
        "description": "Manage user accounts",
        "icon": "people",
        "type": "parent",
        "children": [
            {
                "id": "user_list",
                "label": "List Users",
                "description": "View all users",
                "icon": "list",
                "type": "entry",
                "context": {
                    "entity": "core\\User",
                    "view": "list.default",
                    "order": "name",
                    "sort": "asc",
                    "domain": [["is_active", "=", true]]
                }
            },
            {
                "id": "user_form",
                "label": "Add User",
                "description": "Create a new user",
                "icon": "add",
                "type": "entry",
                "context": {
                    "entity": "core\\User",
                    "view": "form.default"
                }
            }
        ]
    }
]
```