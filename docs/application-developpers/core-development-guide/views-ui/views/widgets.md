# Widget Rendering

Widgets define how fields are displayed and interacted with in views. They allow customization of the display type, format, and styling for fields without requiring custom code.

**Purpose:** Configure the visual representation and behavior of fields in forms and lists.

**Key Features:**

* Override the default display type for a field (e.g., show datetime as a simple date)
* Apply custom formatting through usage specifications
* Add visual styling through simple, flat CSS-like properties
* Support multiple input variants for relational fields (inline selection, multi-select, etc.)

---

## Widget Configuration

Widgets are specified within an item's configuration and support three customization layers:

* **Type:** Override the default display widget (e.g., `date`, `text`, `select`)
* **Usage:** Apply formatting rules (e.g., `datetime/short`, `amount/money:2`)
* **Styling:** Add visual customizations (colors, spacing, icons, etc.)

---

## Display Types

The `type` property overrides the default widget type determined by the field's data type.

**Universal Types (work with most field types):**

* `date` - Display datetime fields as simple dates
* `string` - Render as a text input
* `text` - Render as a textarea
* `link` - Display as a clickable link
* `file` - Display as a file input

**Example - Display datetime as date:**

```json
{
  "type": "field",
  "value": "moment",
  "width": 33,
  "widget": {
    "type": "date"
  }
}
```

### Field-Type-Specific Types

Different field types support additional widget variants:

| **FIELD TYPE** | **WIDGET TYPE** | **DESCRIPTION**                                                                                          |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------- |
| `boolean`      | `toggle`        | Toggle switch (default)                                                                                  |
| `boolean`      | `checkbox`      | Checkbox input                                                                                           |
| `many2one`     | `select`        | Dropdown with inline selection, typeahead, and options to create or perform advanced selection (default) |
| `one2many`     | `list`          | Table display of related items (default)                                                                 |
| `one2many`     | `multiselect`   | Multi-select input view for bulk selection                                                               |
| `many2many`    | `list`          | Table display of related items (default)                                                                 |
| `many2many`    | `multiselect`   | Multi-select input view for bulk selection                                                               |

**Example - Display one2many as multi-select:**

```json
{
  "type": "field",
  "value": "services",
  "widget": {
    "type": "multiselect"
  }
}
```

---

## Usage Formatting

The `usage` property applies formatting rules to field values based on predefined or custom [usage specifications](TODO). This is useful for displaying dates in specific formats, currencies with proper symbols, or other specialized representations.

**Common Usages:**

* `datetime/short` - Short datetime format
* `datetime/long` - Long datetime format
* `date/short` - Short date format
* `amount/money:2` - Currency with 2 decimal places
* `numeric/integer` - Integer formatting
* `numeric/decimal` - Decimal number formatting
* `color` - Color picker or color display
* `icon` - Icon selector or display
* `url` - URL with link rendering

For a complete list of available usages, refer to your project's [content-type specifications](TODO).

**Example - Display date with short format:**

```json
{
  "type": "field",
  "value": "issue_date",
  "width": 15,
  "widget": {
    "usage": "datetime/short"
  }
}
```

**Example - Display amount as formatted currency:**

```json
{
  "type": "field",
  "value": "total_price",
  "width": 20,
  "widget": {
    "usage": "amount/money:2"
  }
}
```

---

## Styling

Widgets support simple, built-in styling properties that control visual appearance without requiring custom CSS. These properties follow consistent naming conventions and can be combined to create diverse visual effects.

### Supported Style Properties

| **PROPERTY**       | **TYPE** | **DESCRIPTION**                                                      |
| ------------------ | -------- | -------------------------------------------------------------------- |
| `text_color`       | `string` | Text color (CSS color name or hex code)                              |
| `text_weight`      | `string` | Font weight (e.g., `bold`, `500`, `700`)                             |
| `text_align`       | `string` | Horizontal text alignment (`left`, `center`, `right`)                |
| `text_decoration`  | `string` | Text decoration (e.g., `underline`, `overline`, `line-through`)      |
| `background_color` | `string` | Background color (CSS color name or hex code)                        |
| `border_color`     | `string` | Border color (CSS color name or hex code)                            |
| `border_radius`    | `string` | Border corner radius (CSS syntax, e.g., `4px`, `0.25rem`)            |
| `padding`          | `string` | Inner spacing (CSS syntax, e.g., `8px`, `0.5rem`)                    |
| `margin`           | `string` | Outer spacing (CSS syntax, e.g., `0 auto`, `10px 5px`)               |
| `icon`             | `string` | Icon to display by name or emoji (e.g., `info`, `⚠️`, `check_circle`) |
| `icon_position`    | `string` | Icon placement relative to text (`left`, `right`)                    |
| `icon_color`       | `string` | Icon color (falls back to `text_color` if unspecified)               |

### Icon Naming

TODO: Provide reference to icon library or naming conventions used for the `icon` property.

### Color Values

Colors can be specified as:

* CSS color names: `red`, `blue`, `orange`, `lightgray`
* Hexadecimal codes: `#333`, `#b30000`, `#fff3cd`
* RGB notation: `rgb(255, 0, 0)`

### Spacing Values

Spacing properties accept CSS syntax:

* Single value: `8px` (all sides)
* Horizontal/Vertical: `10px 5px` (top/bottom, left/right)
* Four sides: `10px 5px 10px 5px` (top, right, bottom, left)
* Relative units: `0.5rem`, `1em`

### Styling Example

TODO: Test Example - Styled Label Widget
```json
{
  "type": "label",
  "value": "Important Notice",
  "widget": {
    "type": "text",
    "text_color": "#b30000",
    "text_weight": "bold",
    "text_align": "center",
    "background_color": "#fff3cd",
    "border_color": "#f5c6cb",
    "border_radius": "4px",
    "padding": "12px",
    "margin": "0 0 10px 0",
    "icon": "⚠️",
    "icon_position": "left",
    "icon_color": "orange"
  }
}
```

**Rendered Result:**

A centered, bold red text saying "Important Notice" with a warning emoji on the left, displayed on a light yellow background with an orange emoji, rounded corners, and custom padding/margins.

---

## Widget Configuration Patterns

### Combining Type, Usage, and Styling

Widgets can combine multiple configuration layers:

**Formatted Currency with Styling:**

```json
{
  "type": "field",
  "value": "total_amount",
  "width": 20,
  "widget": {
    "usage": "amount/money:2",
    "text_color": "#155724",
    "text_weight": "bold"
  }
}
```

**Date Field with Custom Formatting:**

```json
{
  "type": "field",
  "value": "deadline",
  "width": 15,
  "widget": {
    "type": "date",
    "usage": "date/short",
    "text_align": "center"
  }
}
```

**Linked Text with Styling:**

```json
{
  "type": "field",
  "value": "documentation_url",
  "widget": {
    "type": "link",
    "text_color": "#0066cc",
    "text_decoration": "underline"
  }
}
```

**Relational Field with Custom Display:**

```json
{
  "type": "field",
  "value": "assigned_to",
  "widget": {
    "type": "select",
    "text_weight": "500",
    "background_color": "#f0f0f0"
  }
}
```

---

## Widget in Different View Contexts

While the core widget configuration is consistent, the context in which widgets appear affects their behavior:

**In Form Views:**

Widgets render as interactive inputs (text boxes, date pickers, dropdowns, etc.) that users can modify during creation or editing.

**In List Views:**

Widgets render as display-only columns, though some styling and formatting still applies. Refer to the [lists documentation](TODO) for list-specific widget behavior.

**In Relational Fields:**

For one2many and many2many fields, widget configuration affects how the related object list is displayed. Refer to the [forms documentation](TODO) for relational field widget examples.

---

## Complete Widget Configuration Example

```json
{
  "name": "Order Details Form",
  "layout": {
    "groups": [
      {
        "id": "main",
        "sections": [
          {
            "id": "order_info",
            "rows": [
              {
                "columns": [
                  {
                    "items": [
                      {
                        "type": "field",
                        "label": "Order Reference",
                        "value": "reference",
                        "readonly": true,
                        "widget": {
                          "text_color": "#666",
                          "text_weight": "bold"
                        }
                      },
                      {
                        "type": "field",
                        "label": "Order Date",
                        "value": "created",
                        "widget": {
                          "usage": "datetime/short"
                        }
                      }
                    ]
                  },
                  {
                    "items": [
                      {
                        "type": "field",
                        "label": "Deadline",
                        "value": "deadline",
                        "widget": {
                          "type": "date",
                          "usage": "date/short",
                          "icon": "calendar_today",
                          "icon_position": "left"
                        }
                      }
                    ]
                  }
                ]
              },
              {
                "columns": [
                  {
                    "items": [
                      {
                        "type": "field",
                        "label": "Total Amount",
                        "value": "total_amount",
                        "widget": {
                          "usage": "amount/money:2",
                          "text_color": "#155724",
                          "text_weight": "bold",
                          "text_align": "right"
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