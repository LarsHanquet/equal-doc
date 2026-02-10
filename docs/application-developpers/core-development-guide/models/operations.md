# Operations

The **operations** in eQual allow to define **aggregation or computation logic** to be applied on one or more fields of the records being displayed (typically in a list view). 

These operations are useful for **showing totals, averages, counts, or custom calculations** on the dataset.

### Operation types

There are two types of operators:

**1) unary operators**

```php
<?php
/**
 * Unary Operators a single operand (either a single value, or a field reference [array of values])
 * @var array
 */
private static $unary_operators = ['ABS', 'AVG', 'COUNT', 'DIFF', 'MAX', 'MIN', 'SUM'];
```

**2) binary operators**

```php
<?php
/**
 * Operators that take two operands (each operand can be an Operation, a single value, or a field reference [array of values])
 * @var array
 */
private static $binary_operators = [
    '+',            // addition
    '-',            // subtraction
    '*',            // multiplication
    '/',            // division
    '%',            // modulo
    '^'             // exponentiation
];
```

### Operation Syntax

Operations follow a functional-style array syntax that supports both unary and binary operators.

**Unary operator:**

```json
["SUM", "object.qty"]
```

**Binary operator:**

```json
["DIV", ["SUM", "object.total"], ["COUNT", "object.id"]]

Examples:

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

## Operation Object Format

Each operation supports the following properties:

- **`operation`**: Operator or nested operation array
- **`usage`**: Output format (e.g., `amount/money:2` for 2-decimal currency)
- **`label`**: Display label (optional, used in UI)
- **`id`**: Translation key (optional, used for i18n)
