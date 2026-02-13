# Forms

Forms enable creation and modification of individual entities. They provide input widgets for each field, with dynamic validation and behavior based on context.

**Purpose:** Enable creation or modification of a single entity.

**Example:** A form to update user details.

**Key Features:**

* Input widgets for each field
* Dynamic validation and behavior based on context
* Layout customization through groups, sections, and rows
* Conditional display of fields and sections
* Relational field management for one2many and many2many relationships

---

## Layout Structure

Form views use a **layout** element containing all information needed to display a single object. Layouts are organized hierarchically to provide flexible page organization.

**Layout Hierarchy:**

Layout → Groups → Sections → Rows → Columns → Items → Widgets

### Groups

**Groups** are horizontal containers that organize a form into logical sections. When rendered, groups are displayed one below another.

| **PROPERTY** | **TYPE**                      | **DESCRIPTION**                                        |
| ------------ | ----------------------------- | ------------------------------------------------------ |
| `id`         | `string`                      | Unique identifier, used for translations               |
| `label`      | `string`                      | (optional) Display name if no translation is available |
| `sections`   | array of [Section](#sections) | List of sections (displayed as tabs if more than one)  |

### Sections

A **section** contains a portion of the layout presented in rows and columns. If a group contains multiple sections, they are presented as tabs.

| **PROPERTY** | **TYPE**              | **DESCRIPTION**                                                           |
| ------------ | --------------------- | ------------------------------------------------------------------------- |
| `id`         | `string`              | Unique identifier, used for translations                                  |
| `label`      | `string`              | (optional) Display name if no translation is available                    |
| `rows`       | array of [Row](#rows) | List of rows organizing the section's content                             |
| `visible`    | `array`               | (optional) [Domain](TODO) conditions to conditionally display the section |

### Rows

**Rows** organize content horizontally within a section, containing columns that control layout and spacing.

| **PROPERTY** | **TYPE**                    | **DESCRIPTION**                                                       |
| ------------ | --------------------------- | --------------------------------------------------------------------- |
| `id`         | `string`                    | Unique identifier, used for translations                              |
| `label`      | `string`                    | (optional) Display name if no translation is available                |
| `columns`    | array of [Column](#columns) | List of columns within the row                                        |
| `visible`    | `array`                     | (optional) [Domain](TODO) conditions to conditionally display the row |

### Columns

**Columns** define vertical divisions within rows, containing items that represent actual fields or labels.

| **PROPERTY** | **TYPE**                | **DESCRIPTION**                                                          |
| ------------ | ----------------------- | ------------------------------------------------------------------------ |
| `id`         | `string`                | Unique identifier, used for translations                                 |
| `label`      | `string`                | (optional) Display name if no translation is available                   |
| `items`      | array of [Item](#items) | List of items (fields or labels) in the column                           |
| `visible`    | `array`                 | (optional) [Domain](TODO) conditions to conditionally display the column |
| `width`      | `percentage`            | Width of the column as a percentage of its parent (0-100%)               |

### Items

**Items** describe how a field or label from a Model is displayed in the form view. Each item can be a field (interactive) or a label (static text).

| **PROPERTY** | **TYPE**             | **DESCRIPTION**                                                                  |
| ------------ | -------------------- | -------------------------------------------------------------------------------- |
| `id`         | `string`             | (optional) Unique identifier, used for translations (overrides label and value)  |
| `label`      | `string`             | (optional) Custom title to override the field's default label                    |
| `type`       | `string`             | Type of item: `field` (interactive) or `label` (static text)                     |
| `value`      | `string`             | (for labels) Static text content, or field name for fields                       |
| `width`      | `percentage`         | Width of the item as a percentage of its parent (0-100%)                         |
| `readonly`   | `boolean`            | If `true`, the field cannot be edited in creation/edit contexts                  |
| `visible`    | `boolean` or `array` | If `false` or a [domain](TODO) condition, controls whether the item is displayed |
| `help`       | `string`             | Help text displayed for the field, guiding the user on what to enter             |
| `widget`     | [Widget](#widget)    | Configuration properties for the item's display and behavior                     |

### Widget

Widgets configure how items are displayed and behave. Full documentation is available in the [widgets](TODO) guide.

**Common Properties:**

| **PROPERTY** | **TYPE**  | **DESCRIPTION**                                                                             |
| ------------ | --------- | ------------------------------------------------------------------------------------------- |
| `link`       | `boolean` | If `true`, the item content is displayed as a clickable link                                |
| `heading`    | `boolean` | If `true`, emphasizes the item with larger text                                             |
| `type`       | `string`  | Overrides the default display type based on the field type (e.g., `text`, `select`, `date`) |
| `usage`      | `string`  | Overrides the field's [usage](TODO) to change display formatting                            |

#### Relational Field Widgets

For one2many and many2many fields, additional properties customize how related objects are displayed:

| **PROPERTY** | **TYPE** | **DESCRIPTION**                                                    |
| ------------ | -------- | ------------------------------------------------------------------ |
| `header`     | object   | Overrides the [header](TODO) configuration for the relational view |
| `domain`     | `array`  | Overrides the [domain](TODO) to filter displayed relations         |
| `view`       | `string` | Specifies the view ID to use for displaying the relation           |

**one2many-specific:**

| **PROPERTY** | **TYPE**  | **DESCRIPTION**                                                                             |
| ------------ | --------- | ------------------------------------------------------------------------------------------- |
| `autoselect` | `boolean` | If `true` (default), automatically selects the item when the list contains only one element |

**many2one-specific:**

| **PROPERTY** | **TYPE** | **DESCRIPTION**                                                                                                                                                                                      |
| ------------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fields`     | `array`  | List of sub-field names to load (e.g., `customer_id`, `customer_identity_id.name`). Necessary when sub-fields are referenced in domains or visibility conditions. Default fields are `id` and `name` |

For many2one fields, the display requires loading the full object (not just the ID). Additional sub-fields can be specified using dot notation for nested properties (e.g., `object.target_id.target_field`).

**Example - many2one with sub-fields:**

```json
{
  "type": "field",
  "label": "Booking",
  "value": "booking_id",
  "width": 100,
  "widget": {
    "fields": ["customer_id", "customer_identity_id.name"],
    "readonly": true
  }
}
```

#### Controlling Relational Field Actions

For relational fields, the `header` configuration controls which action buttons are displayed. For example, create and open buttons can be hidden:

```json
{
  "widget": {
    "header": {
      "actions": {
        "ACTION.CREATE": false,
        "ACTION.OPEN": false
      }
    }
  }
}
```

Refer to the [header actions](TODO) documentation for a complete list of available actions.

---

## Routes

Forms can include a **routes** element that displays navigation buttons on the right panel, linking to related views or external resources.

**Structure:**

| **PROPERTY**  | **TYPE**  | **DESCRIPTION**                                                                              |
| ------------- | --------- | -------------------------------------------------------------------------------------------- |
| `id`          | `string`  | Unique identifier for the route                                                              |
| `label`       | `string`  | Button label displayed to the user                                                           |
| `description` | `string`  | (optional) Tooltip text explaining the route's purpose                                       |
| `icon`        | `string`  | Icon identifier (e.g., `library_books`, `print`, `room_service`)                             |
| `route`       | `string`  | Navigation path or external URL. Supports dynamic values like `object.id`                    |
| `context`     | object    | (optional) Context object specifying entity, view, domain, and reset behavior                |
| `target`      | `string`  | (optional) Target for opening the route: `_blank` for new window, default is current context |
| `absolute`    | `boolean` | (optional) If `true`, treats the route as an absolute URL                                    |
| `visible`     | `array`   | (optional) [Domain](TODO) conditions to control button visibility                            |

**Example - Contextual navigation with form updates:**

```json
{
  "routes": [
    {
      "id": "item.booking.file",
      "label": "Booking form",
      "icon": "library_books",
      "route": "/booking/object.id",
      "context": {
        "entity": "lodging\\sale\\booking\\Booking",
        "view": "form.default",
        "domain": ["id", "=", "object.id"],
        "reset": true
      }
    },
    {
      "id": "item.booking.edit",
      "label": "Booked services",
      "icon": "room_service",
      "route": "/booking/object.id/services"
    },
    {
      "id": "item.booking.contract",
      "label": "Send contract",
      "icon": "drive_file_rename_outline",
      "route": "/booking/object.id/contract",
      "visible": [["has_contract", "=", true], ["status", "=", "confirmed"]]
    }
  ]
}
```

**Example - External document export:**

```json
{
  "routes": [
    {
      "id": "print.pdf",
      "label": "Print tender",
      "icon": "print",
      "route": "/?get=renover_tender-pdf&id=object.id",
      "target": "_blank",
      "absolute": true
    }
  ]
}
```

---

## Keyboard Navigation and Shortcuts

Form views support keyboard interaction for improved accessibility and speed:

**Navigation:**

* Use the `<tab>` key to move between input fields
* In dropdown selection boxes, use `<up>` and `<down>` arrow keys to navigate options

**Actions:**

* While in edit mode, press `<ctrl+s>` to save the current form
* When a split-button has multiple save actions (e.g., save and continue, save and close), the first save action is executed

**Implementation Details:**

Keyboard events are captured by the visible Frame and relayed to the active context, which passes them to the view via a `keyboardAction()` call.

---

## Example Form Configuration

A complete form configuration combining layout, routes, and actions:

```json
{
  "name": "Booking Details",
  "description": "View and edit booking information",
    "routes": [
    {
      "id": "services",
      "label": "Manage Services",
      "icon": "room_service",
      "route": "/booking/object.id/services"
    }
  ],
  "layout": {
    "groups": [
      {
        "id": "main",
        "label": "Booking Information",
        "sections": [
          {
            "id": "overview",
            "label": "Overview",
            "rows": [
              {
                "id": "row_1",
                "columns": [
                  {
                    "id": "col_1",
                    "width": 50,
                    "items": [
                      {
                        "type": "field",
                        "value": "reference",
                        "readonly": true
                      },
                      {
                        "type": "field",
                        "value": "status"
                      }
                    ]
                  },
                  {
                    "id": "col_2",
                    "width": 50,
                    "items": [
                      {
                        "type": "field",
                        "value": "customer_id",
                        "widget": {
                          "fields": ["name", "email"]
                        }
                      }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---