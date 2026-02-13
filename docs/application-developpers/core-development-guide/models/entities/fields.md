# Fields
## Field Categories

| **Category** | **Description**                                                                                                             |
| ------------ | --------------------------------------------------------------------------------------------------------------------------- |
| simple       | Direct field holding a value of a given type. Stored as-is in the database and automatically converted between SQL and PHP. |
| relational   | Field whose value targets one or more objects. Supported relations: `one2many`, `many2many`, and `many2one`.                |
| computed     | Indirect field resulting from a computation based on other values. See [Computed Fields](computed-fields.md) for details.   |

---

## Field Types

The `type` property sets the field type. The following types are supported by the ORM (Object-Relational Mapping):

### Scalar Types

#### boolean

Numeric value of Boolean type (`true` or `false`).

!!! note "Boolean Notation"
    Use PHP built-in constants `true` and `false`. When using `'0'`, `'1'`, `0`, or `1`, the value is automatically converted to a boolean.

#### integer

Signed numeric value (negative or positive). By default, integers are stored in the DBMS (Database Management System) using `INT(11)`.

!!! note "Integer Precision"
    PHP integer size depends on the platform (`PHP_INT_SIZE`), while SQL `INT` is always 32 bits.

#### float

Floating-point numeric value. By default, floats are stored using `DECIMAL(10,2)`.

#### string

Short string without formatting or carriage returns (e.g., lastname, place). By default, stored using `VARCHAR(255)`.

Strings can be longer and include formatting when combined with specific [usages](#usages):

```php
<?php
// Long text stored as MEDIUMTEXT, displayed as TEXTAREA
'description' => [
    'type'  => 'string',
    'usage' => 'text/plain'
]
```

```php
<?php
// Phone number stored as VARCHAR(20)
'phone' => [
    'type'  => 'string',
    'usage' => 'phone'
]
```

#### binary

Any binary value (e.g., pictures, documents). Binary values can be stored either in the database or within the filesystem under the `/bin` folder.

This behavior is controlled by the `FILE_STORAGE_MODE` and `FILE_STORAGE_DIR` configuration parameters.

#### file

A file value stored in the database as `LONGBLOB`.

#### date, time & datetime

When stored in the DBMS, these types follow standard SQL formats:

- **date**: `YYYY-mm-dd`
- **time**: `HH:mm:ss`
- **datetime**: `YYYY-mm-dd HH:mm:ss`

!!! tip "Datetime Manipulations"
    Within PHP scripts, eQual handles dates, times, and datetimes as UTC timestamps. These are adapted to SQL or JSON format when needed.

---

### Relational Types

#### many2one

Relational field for N-1 relationships. The stored value is an integer holding the identifier of the pointed object.

```php
<?php
// Each task has only one user assigned at a time
'user_id' => [
    'type'           => 'many2one',
    'foreign_object' => 'todolist\User'
]
```

#### one2many

Relational field for 1-N relationships, linked to a `many2one` field on the related object.

```php
<?php
// Each user can have many tasks
'tasks_ids' => [
    'type'           => 'one2many',
    'foreign_object' => 'todolist\Task',
    'foreign_field'  => 'user_id'
]
```

#### many2many

Relational field for M-N relationships, requiring a pivot table.

```php
<?php
// Teachers can teach multiple courses, courses can have multiple teachers
'courses_ids' => [
    'type'            => 'many2many', 
    'foreign_object'  => 'school\Course', 
    'foreign_field'   => 'teachers_ids', 
    'rel_table'       => 'school_rel_course_teacher', 
    'rel_foreign_key' => 'course_id', 
    'rel_local_key'   => 'teacher_id'
]
```

---

### Special Types

#### alias

Targets another field whose value is returned when fetching the field. By default, the `name` field is an alias for `id`.

```php
<?php
'display_name' => [
    'type'  => 'alias',
    'alias' => 'name'
]
```

#### computed

See [Computed Fields](computed-fields.md) for detailed documentation.

---

## Field Properties

### Common Properties

| **Property** | **Type**             | **Description**                                                                 |
| ------------ | -------------------- | ------------------------------------------------------------------------------- |
| type         | `string`             | The field type (see [Field Types](#field-types)).                               |
| usage        | `string`             | Additional format information (see [Usages](#usages)).                          |
| description  | `string`             | Brief description of the field (max 65 characters).                             |
| visible      | `boolean` \| `array` | [Domain](domains.md) holding conditions for field visibility in UI.             |
| default      | mixed                | Default value or callable returning the default.                                |
| readonly     | `boolean`            | Marks the field as non-editable (default: `false`).                             |
| required     | `boolean`            | Marks the field as mandatory (default: `false`).                                |
| multilang    | `boolean`            | Marks the field as [translatable](TODO) (default: `false`).                     |
| onupdate     | `string`             | Method to invoke when field is updated. Format: `package\Class::method`         |
| domain       | `array`              | Additional conditions for relational field targets (see [Domains](domains.md)). |
| dependencies | `array`              | List of computed fields to reset when this field is updated.                    |

### Type-Specific Properties

#### string

| **Property** | **Description**                                       |
| ------------ | ----------------------------------------------------- |
| multilang    | Whether the field is translatable (default: `false`). |
| selection    | Pre-defined list of possible values.                  |

**Selection examples:**

```php
<?php
// Simple list
'status' => [
    'type'      => 'string',
    'selection' => ['pending', 'approved', 'rejected']
]
```

```php
<?php
// Associative array with labels
'priority' => [
    'type'      => 'string',
    'selection' => [
        'low'    => 'Low Priority', 
        'medium' => 'Medium Priority',
        'high'   => 'High Priority'
    ]
]
```

!!! note "Key-Value Mapping"
    In associative arrays, avoid numeric keys as they may be interpreted as array indices when converted to JSON, potentially mixing up values and labels.

#### many2one

| **Property**   | **Description**                                                                            |
| -------------- | ------------------------------------------------------------------------------------------ |
| foreign_object | Full name of the target class.                                                             |
| ondelete       | Behavior when parent is deleted: `'null'` (void pointer) or `'cascade'` (delete children). |

#### one2many

| **Property**   | **Description**                                                                |
| -------------- | ------------------------------------------------------------------------------ |
| foreign_object | Target class name.                                                             |
| foreign_field  | Name of the field pointing back to current class.                              |
| foreign_key    | Field serving as identifier (default: `'id'`).                                 |
| order          | Field to sort pointed objects on.                                              |
| sort           | Sort direction: `'asc'` or `'desc'`.                                           |
| ondetach       | Behavior when relation is removed: `'null'` or `'delete'` (default: `'null'`). |

#### many2many

| **Property**    | **Description**                                                     |
| --------------- | ------------------------------------------------------------------- |
| foreign_object  | Full name of the target class.                                      |
| foreign_field   | Name of the field pointing back to current class.                   |
| rel_table       | Name of the pivot table (recommended: `package_rel_class1_class2`). |
| rel_local_key   | Column in pivot table for current object's identifier.              |
| rel_foreign_key | Column in pivot table for target object's identifier.               |

---

## Usages

The `usage` property is complementary to `type` and refines how the field is handled by the ORM, DBMS, and UI.

### Purpose

- **Storage allocation**: Determines appropriate database column types
- **Format validation**: Ensures data conforms to expected patterns
- **UI rendering**: Guides how fields are displayed and edited

### Syntax

Usages follow this general pattern:
```
content-type/subtype:precision.scale
```

### Common Usages

| **Usage**             | **Description**          | **Example**             |
| --------------------- | ------------------------ | ----------------------- |
| `text/plain`          | Long text content        | `text/plain:65000`      |
| `text/html`           | HTML formatted content   | -                       |
| `amount/money`        | Monetary values          | `amount/money:4`        |
| `amount/percent`      | Percentage values        | -                       |
| `email`               | Email addresses          | -                       |
| `phone`               | Phone numbers            | -                       |
| `password`            | Password fields          | -                       |
| `password/nist`       | NIST-compliant passwords | -                       |
| `date/month`          | Month selection          | -                       |
| `date/year`           | Year selection           | `date/year:4`           |
| `country/iso-3166`    | Country codes            | `country/iso-3166:2`    |
| `language/iso-639`    | Language codes           | `language/iso-639:3`    |
| `uri/url`             | URL values               | -                       |
| `uri/urn.iban`        | IBAN numbers             | -                       |
| `uri/urn.ean`         | EAN barcodes             | -                       |
| `image/jpeg`          | JPEG images              | -                       |
| `numeric/integer`     | Integer with precision   | `numeric/integer:3`     |
| `numeric/hexadecimal` | Hexadecimal values       | `numeric/hexadecimal:1` |
| `number/real`         | Decimal numbers          | `number/real:5.2`       |

### Type to Content-Type Mapping

| **Type**  | **Content-Type** |
| --------- | ---------------- |
| boolean   | `number/boolean` |
| integer   | `number/integer` |
| float     | `number/real`    |
| string    | `text/plain`     |
| date      | `date/plain`     |
| datetime  | `date/datetime`  |
| time      | `time/plain`     |
| binary    | `binary/plain`   |
| many2one  | `number/natural` |
| one2many  | `array`          |
| many2many | `array`          |

### Constraints and Validation

The `Field` class provides a `getConstraints()` method to retrieve constraints specific to the field's type and usage. When values are associated with a usage, their validity is checked via the usage's constraint definitions.

---

## Data Adaptation

Objects manipulated in controllers and classes contain values using PHP types. Collections can be exported by recursively converting values during export using:

```php
$adapter->adaptOut($value, $field->getUsage())
```

This ensures proper format conversion between PHP, SQL, and JSON representations.
````

---

