## Configuration Parameters

The **eQual** framework allows defining a series of parameters used by controllers that can be modified by administrators through the **Settings** application. These parameters are modeled using the `Setting` entity, which provides flexibility and a robust mechanism for contextual configuration.

### Example of a Setting object
```json
{
  "id": 5,
  "code": "numbers.decimal_precision",
  "title": "Number of decimal digits",
  "package": "core",
  "form_control": "select",
  "section_id": 1,
  "description": "Number of decimal digits",
  "help": "Number of decimal digits to store for fields of type 'float'.",
  "type": "integer"
}
```

### Core Concepts

A **Setting** represents a configurable parameter in the system, uniquely identified by a `package`, a `section`, and a `code`. Each setting must be linked to **at least one value or sequence** to be considered valid.

### SettingValues
**SettingValues** hold the actual value of a setting. These values can be **scoped** to specific contexts using a **selector** — a set of fields like `user_id`, `organization_id`, etc.

### SettingSequences
**SettingSequences** are special settings that manage numeric counters (e.g., invoice numbers). They also support contextual scoping through selectors, allowing, for instance, separate counters per organization.

### Alerts Lifecycle and Models

#### Message Model (`core\alert\MessageModel`)

The `MessageModel` entity defines the semantics of an alert type:

- `name`: Internal identifier.
- `label` and `description`: Multilang text describing the alert.
- `type`: Optional tag for logical grouping.
- `messages_ids`: References all alert instances for this model.

This registry of alert types ensures consistency across the system.

#### Message (`core\alert\Message`)

The `Message` entity represents an emitted alert linked to a specific object. It contains:

- `object_class` and `object_id`: Target of the alert.
- `message_model_id`: Reference to the alert model.
- `severity`: One of `notice`, `warning`, `important`, `error`.
- `controller`: Optional fallback controller to re-evaluate the condition.
- `params` / `links`: JSON payload with controller input or related resources.
- `user_id` / `group_id`: Optional recipient scoping.

Alerts are persisted through the ORM, and a uniqueness constraint ensures one alert per `(object_class, object_id, message_model_id)` tuple.
