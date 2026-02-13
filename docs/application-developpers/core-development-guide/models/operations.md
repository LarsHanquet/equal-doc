# Operations

**Operations** in eQual allow defining **aggregation or computation logic** to be applied on one or more fields of the records being displayed (typically in a [list view](TODO)). 

These operations are useful for **displaying totals, averages, counts, or custom calculations** on a dataset.

---

## Operation Types

There are two types of operators:

### Unary Operators

Unary operators take a single operand (either a single value or a field reference representing an array of values).

```php
<?php
private static $unary_operators = ['ABS', 'AVG', 'COUNT', 'DIFF', 'MAX', 'MIN', 'SUM'];
```

### Binary Operators

Binary operators take two operands. Each operand can be another operation, a single value, or a field reference.

```php
<?php
private static $binary_operators = [
    '+',            // addition
    '-',            // subtraction
    '*',            // multiplication
    '/',            // division
    '%',            // modulo
    '^'             // exponentiation
];
```

---

## Operation Syntax

Operations follow a functional-style array syntax. Field references use the `object.` prefix followed by the field name.

**Unary operator example:**

```json
["SUM", "object.qty"]
```

**Binary operator example (nested):**

```json
["/", ["SUM", "object.total"], ["COUNT", "object.id"]]
```

This example divides the sum of `total` by the count of `id`, effectively calculating an average.

---

## Operation Object Format

Each operation object supports the following properties:

| Property    | Description                                                         |
| ----------- | ------------------------------------------------------------------- |
| `operation` | The operator (e.g., `SUM`, `COUNT`) or a nested operation array     |
| `usage`     | Output format (e.g., `amount/money:2` for currency with 2 decimals) |
| `label`     | Display label shown in the UI (optional)                            |
| `id`        | Translation key for [translation](TODO) support (optional)          |
| `suffix`    | Text appended after the result (optional)                           |

---

## Examples

### Summing monetary values

```json
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
```

### Counting records with a suffix

```json
"operations": {
    "total": {
        "rental_unit_id": {
           "operation": "COUNT",
           "usage": "numeric/integer",
           "suffix": "p."
        }
    }
}
```