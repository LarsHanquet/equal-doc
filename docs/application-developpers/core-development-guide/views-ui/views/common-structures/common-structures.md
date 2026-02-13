# Views Common Structure

Views in eQual share a common set of properties that define their behavior, appearance, and access control. Additional properties are available for specific view types (List, Menu, Dashboard, Form).

---

## Common Properties

All view types support the following core properties:

| **PROPERTY**  | **TYPE**                    | **DESCRIPTION**                                                                            |
| ------------- | --------------------------- | ------------------------------------------------------------------------------------------ |
| `name`        | `string`                    | Name of the view displayed in the application                                              |
| `description` | `string`                    | Description of the view and its purpose                                                    |
| `domain`      | `array`                     | [Domain](TODO) to filter which items from the input collection will be displayed           |
| `filters`     | array of [Filter](#filters) | Associative array of filters with name and clauses                                         |
| `controller`  | `string`                    | Data controller used to fetch objects from the database (defaults to `core_model_collect`) |
| `header`      | [Header](#header)           | Configuration to override the default header behavior                                      |
| `actions`     | array of [Action](#actions) | Custom actions applicable to the view context (typically for form views)                   |
| `routes`      | array of [Route](#routes)   | Contextual links to other parts of the application                                         |
| `access`      | [Access](#access)           | Access control rules to restrict view visibility by user group or login                    |

## View-Type-Specific Properties

### List

| **PROPERTY** | **TYPE**                    | **DESCRIPTION**                                     |
| ------------ | --------------------------- | --------------------------------------------------- |
| `order`      | `string`                    | (optional) Sort direction: `asc` or `desc`          |
| `sort`       | `string`                    | (optional) Field name(s) to sort by default         |
| `limit`      | `integer`                   | (optional) Number of items fetched per page         |
| `exports`    | array of [Export](#exports) | (optional) Export/print configurations for the list |

### Menu

| **PROPERTY** | **TYPE** | **DESCRIPTION**                                                                    |
| ------------ | -------- | ---------------------------------------------------------------------------------- |
| `type`       | `string` | (mandatory) Either `entry` or `parent`. Parent items include a `children` property |

### Dashboard

| **PROPERTY** | **TYPE** | **DESCRIPTION**                         |
| ------------ | -------- | --------------------------------------- |
| `width`      | `string` | Width of the item as a percentage value |

---

## Detailed Configuration

### Domain

The `domain` property conditionally filters which data is displayed in the view. It uses the [domain](TODO) syntax to define filter criteria.

**Example:**

```json
{
  "domain": ["type", "<>", "I"]
}
```

### Filters

The `filters` property customizes the filtering features available in the view header. Each filter allows users to apply predefined conditions to the displayed data.

**Structure:**

| **PROPERTY**  | **TYPE** | **DESCRIPTION**                                                            |
| ------------- | -------- | -------------------------------------------------------------------------- |
| `id`          | `string` | Unique identifier for translation purposes                                 |
| `label`       | `string` | Default display name if no translation is set                              |
| `description` | `string` | (optional) Description explaining the filter's purpose                     |
| `clause`      | `array`  | [Clause](TODO) that will be added to the domain when the filter is applied |

**Example:**

```json
{
  "filters": [
    {
      "id": "lang.french",
      "label": "French",
      "description": "French speaking people",
      "clause": ["language", "=", "fr"]
    }
  ]
}
```

### Controller

The `controller` property specifies which data controller retrieves objects for the view. The controller must be a data-controller extending `core_model_collect` (the default).

**Example:**

```json
{
  "controller": "core_model_collect"
}
```

### Header

The `header` section allows customization of the view's header behavior and layout.

**Structure:**

| **PROPERTY** | **TYPE**  | **DESCRIPTION**                                                                                                                                            |
| ------------ | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `actions`    | object    | Configuration of action buttons shown in the header (see [Header Actions](#header-actions))                                                                |
| `filters`    | `boolean` | Flag to display or hide the default filtering input                                                                                                        |
| `layout`     | `string`  | `full` (default) or `inline`. The [`inline`](TODO) mode displays a compact list with minimal navigation and icon-based actions, ideal for embedded layouts |

**Example - Inline layout:**

```json
{
  "header": {
    "layout": "inline",
    "actions": {
      "ACTION.CREATE_INLINE": true
    }
  }
}
```

### Actions

The `actions` property defines custom actions for the view. Each action corresponds to a button that, when clicked, invokes a specified controller and automatically refreshes the view upon completion.

**Context:** Root-level actions are typically used in form views. For list views, use [header.actions](#header-actions) to control actions on selected objects.

**Structure:**

| **PROPERTY**  | **TYPE**          | **DESCRIPTION**                                                                                                                                           |
| ------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`          | `string`          | Identifier for translation and reference purposes                                                                                                         |
| `label`       | `string`          | Button label displayed to the user                                                                                                                        |
| `description` | `string`          | Description shown when the user hovers over the button                                                                                                    |
| `controller`  | `string`          | [ORM/Entity controller](TODO) invoked when the action is triggered. The current object's `id` is sent as a parameter by default                           |
| `visible`     | `array`           | (optional) [Domain](TODO) conditions to determine if the action button is shown                                                                           |
| `confirm`     | `boolean`         | (optional) If `true`, a confirmation dialog appears before executing the action                                                                           |
| `params`      | object            | (optional) Associative array mapping fields to values. Values can reference user properties (e.g., `user.login`) or object properties (e.g., `object.id`) |
| `access`      | [Access](#access) | (optional) Access control to limit action visibility and invocation                                                                                       |

**Example:**

```json
{
  "actions": [
    {
      "id": "action.option",
      "label": "Set as Option",
      "description": "Block rental units without claiming funding.",
      "controller": "lodging_booking_option",
      "visible": ["status", "=", "quote"],
      "confirm": true,
      "params": {
        "id": "object.id"
      }
    },
    {
      "id": "action.validate",
      "label": "Confirm Booking",
      "description": "Block units and set up invoicing plan.",
      "controller": "lodging_booking_confirm",
      "visible": ["status", "in", ["quote", "option"]]
    }
  ]
}
```

If the target controller requires parameters, a dialog prompts the user for values. If no parameters are required but `confirm` is `true`, a confirmation dialog appears instead. See the [action flow diagram](/_assets/img/eq_confirm_diagram.png) for details.

### Header Actions

Header actions control predefined buttons (like Create, Save, Cancel) and their behavior. Unlike root-level actions, header actions apply to interactions with selected objects in list views.

**Note:** Predefined action IDs must be used; custom IDs are not supported for header actions.

Default actions can be hidden by setting `visible` to `false`. To define a split-button (multiple action variants), use an array of action items.

For a complete list of predefined action IDs and their default behaviors, see the [Header section: Views & Actions](TODO) documentation.

**Example:**

```json
{
  "header": {
    "actions": {
      "ACTION.CREATE": [
        {
          "view": "form.create",
          "description": "Custom form for object creation.",
          "domain": ["parent_status", "=", "object.status"],
          "access": {
            "groups": ["admin"]
          },
          "controller": "custompackage_mode_update"
        }
      ],
      "ACTION.SELECT": false,
      "ACTION.SAVE": [
        {"id": "SAVE_AND_CONTINUE"},
        {"id": "SAVE_AND_CLOSE"}
      ],
      "ACTION.CANCEL": [
        {"id": "CANCEL_AND_VIEW"}
      ]
    }
  }
}
```

### Routes

Routes define contextual links to other parts of the application, displayed within the view to guide user navigation.

**Structure:**

| **PROPERTY** | **TYPE** | **DESCRIPTION**                               |
| ------------ | -------- | --------------------------------------------- |
| `package`    | `string` | Target package of the route (e.g., `lodging`) |

### Access

Access control restricts view visibility to specific users and groups.

**Structure:**

| **PROPERTY** | **DESCRIPTION**                                        |
| ------------ | ------------------------------------------------------ |
| `groups`     | Array of group names whose members can access the view |
| `users`      | Array of user logins that can access the view          |

**Example:**

```json
{
  "access": {
    "groups": ["users"]
  }
}
```

### Operations

Operations apply calculations (like SUM, COUNT, AVG) to columns in list views, displaying aggregate results. Operations can be defined at three levels:

#### 1. In `group_by` (Simple Field)

Group results by a field without additional operations:

```json
{
  "group_by": ["date"]
}
```

#### 2. In `group_by` (As Object)

Apply an operation while grouping, with options like sort order:

```json
{
  "group_by": [
    {
      "field": "time_slot_id",
      "operation": ["SUM", "object.qty"],
      "order": "asc"
    }
  ]
}
```

#### 3. In `operations` (Global)

Define named calculation rows independent from groupings. Each row can contain multiple named operations:

**Structure:**

| **PROPERTY** | **TYPE** | **DESCRIPTION**                                                                          |
| ------------ | -------- | ---------------------------------------------------------------------------------------- |
| `operation`  | `string` | The operation to apply (e.g., `SUM`, `COUNT`, `AVG`)                                     |
| `usage`      | `string` | [Usage](TODO) hint for displaying the result (e.g., `amount/money:2`, `numeric/integer`) |
| `prefix`     | `string` | (optional) String prepended to the result                                                |
| `suffix`     | `string` | (optional) String appended to the result                                                 |

**Example:**

```json
{
  "operations": {
    "total": {
      "total_paid": {
        "id": "operations.total.total_paid",
        "label": "Total received",
        "operation": "SUM",
        "usage": "amount/money:2"
      },
      "total_due": {
        "operation": "SUM",
        "usage": "amount/money:2"
      }
    }
  }
}
```

### Exports

The `exports` property defines document export/print configurations for list views. Each export allows users to generate and print documents (e.g., contracts, reports) based on view data.

**Structure:**

| **PROPERTY**  | **TYPE** | **DESCRIPTION**                                                                                |
| ------------- | -------- | ---------------------------------------------------------------------------------------------- |
| `id`          | `string` | Unique identifier for the export                                                               |
| `label`       | `string` | Button label displayed to the user                                                             |
| `icon`        | `string` | Icon identifier (e.g., `print`)                                                                |
| `description` | `string` | Description of what the export generates                                                       |
| `controller`  | `string` | [ORM/Entity controller](TODO) that handles the export (e.g., `lodging_booking_print-contract`) |
| `view`        | `string` | View ID for export display (typically `print.default`)                                         |
| `visible`     | `array`  | (optional) [Domain](TODO) conditions to determine if the export button is displayed            |

**Example:**

```json
{
  "exports": [
    {
      "id": "export.print.contract",
      "label": "Print contract",
      "icon": "print",
      "description": "Print the contract related to this booking.",
      "controller": "lodging_booking_print-contract",
      "view": "print.default",
      "visible": ["status", "=", "quote"]
    }
  ]
}
```

---