# Task Management

The task management system provides a way to track actionable items derived from entity [workflows](TODO). Tasks can be created manually or automatically based on task models that define when tasks should appear and their deadlines.

---

## Task

A **Task** is an actionable item derived either manually or automatically from a TaskModel.

### Types of Tasks

| Type              | Description                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------- |
| **Informational** | Automatically marked as done (e.g., "contract received signed").                          |
| **Actionable**    | Requires responsibility ("Who must do what?") or communication ("Who must be informed?"). |

### Fields

| Field               | Description                                       |
| ------------------- | ------------------------------------------------- |
| `id`                | Identifier.                                       |
| `name`              | Label describing the action.                      |
| `is_done`           | Boolean flag indicating completion status.        |
| `done_by`           | User who completed the task.                      |
| `done_date`         | Completion date.                                  |
| `visible_date`      | When the task becomes visible.                    |
| `deadline_date`     | Calculated deadline (from TaskEvent).             |
| `has_task_model`    | Whether linked to a TaskModel.                    |
| `task_model_id`     | Originating model (nullable if created manually). |
| `notes`             | Free text notes.                                  |
| `trigger_event_id`  | Reference to the triggering event.                |
| `deadline_event_id` | Reference to the deadline event.                  |
| `object_class`      | Linked entity class.                              |
| `object_id`         | Linked entity identifier.                         |

### Sorting and Display

- **Priority**: Non-completed tasks appear first.
- **Order**: Sorted by `deadline_date`.
- **Completed tasks**: Sorted by `done_date` or deadline order.
- **Grouping**: Can be grouped by deadline event.

---

## Task Events

A **TaskEvent** represents a point in time where a task becomes relevant or due. Events can be based on:

- **Status changes** in an entity workflow (e.g., booking → *confirmed*).
- **Date fields** with optional offsets (e.g., *15 days before arrival*).

### Fields

| Field               | Description                                                        |
| ------------------- | ------------------------------------------------------------------ |
| `name`              | Human-readable event label.                                        |
| `object_class`      | Namespace of the associated entity (e.g., `sale\booking\Booking`). |
| `event_type`        | Either `status_change` or `date_field`.                            |
| `entity_status`     | Status value, if event type is `status_change`.                    |
| `entity_date_field` | Date field name, if event type is `date_field`.                    |
| `offset`            | Number of days relative to the event (default `0`).                |

### Examples

- `confirmation`
- `confirmation +15 days`
- `1 month before reservation`
- `15 days before reservation`

---

## Task Models

A **TaskModel** defines a reusable template for tasks. It specifies when a task should appear and when it must be completed.

### Characteristics

- Bound to a specific entity type (`object_class`).
- Defines **trigger** and **deadline** events.
- Can be grouped into `task_group` for organizational purposes.
- Automatically spawns tasks when the trigger condition is met.

### Fields

| Field               | Description                                 |
| ------------------- | ------------------------------------------- |
| `name`              | Template label.                             |
| `description`       | Text description.                           |
| `task_group`        | Group name for filtering tasks.             |
| `trigger_event_id`  | Event that activates the task.              |
| `deadline_event_id` | Event that sets the latest completion date. |
| `tasks_ids`         | Generated tasks linked to this template.    |

### Example

Task Model: *"Send Allergy List"*
- **Trigger**: Booking → *confirmed*
- **Deadline**: *15 days before arrival*

---

## Task Lifecycle

### Creation

A task is created when:

1. **Manual creation**: Via code or user action.
2. **Automatic creation**: When a TaskModel's trigger condition is met.

!!! note "Constraint"
    There can only be one task per TaskModel per object. If one already exists, it is removed and replaced.

### Execution Logic

On each status change of an entity:

1. Fetch all TaskModels relevant for the management team.
2. Compare the entity's status with the `trigger_event_id`.
3. If matching, create a new Task.
4. Remove any previous task from the same TaskModel.

## Example Scenarios

### Scenario: Event Management

| Phase                                    | Tasks                                                              |
| ---------------------------------------- | ------------------------------------------------------------------ |
| **Confirmed**                            | Receive contract, Send access codes, Prepare stay binder           |
| **Confirmed +15 days**                   | Draft schedule, Send message code, Publish blog                    |
| **Confirmed (max. 1 month before stay)** | Prepare summary sheet, Collect allergy info, Finalize waiting list |
| **Validated (before stay)**              | Assign room numbers                                                |
| **Terminated (+1 day max)**              | Archive pedagogical sheets                                         |

### Scenario: Contact History

Used as a contact history where each line has:
* A date
* A description of what was done
* A "validated" status


---
