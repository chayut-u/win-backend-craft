---
name: win-backend
description: Readability-first backend engineering for general API and admin systems. Use when Codex needs to implement or refactor backend code into clear layers, simplify business logic, shape CRUD flows, add validation, define API responses, or keep controllers thin and maintainable in Flask-style, FastAPI-style, or similar server projects.
---

## 1. Core Principles

- Readability > cleverness
- Maintainability over abstraction
- Explicit code over magic
- Clear separation of responsibilities
- Avoid lambda-heavy, regex-heavy, or abstraction-heavy solutions
- Prefer simple, direct, and debuggable logic

## 2. Recommended Project Structure (Preferred)

```text
app/
|-- __init__.py # Application factory
|-- config.py # Config classes
|-- extensions.py # Flask extensions
|-- routes.py # Base route / health check
|
|-- blueprints/ # Controller / API endpoints
| |-- auth_controller.py
| |-- customer_controller.py
| |-- product_controller.py
| |-- rent_controller.py
| |-- report_controller.py
| `-- ...
|
|-- services/ # Business logic layer
| |-- auth_service.py
| |-- customer_service.py
| |-- product_service.py
| |-- rent_service.py
| |-- report_service.py
| |-- todo_service.py
| `-- helpers/ # Small domain-specific helper logic
|
|-- repositories/ # Query / CRUD layer
| |-- customer_repository.py
| |-- product_repository.py
| |-- rent_repository.py
| `-- ...
|
|-- models/ # SQLAlchemy models
| |-- m_customer.py
| |-- m_product.py
| |-- t_rent.py
| `-- ...
|
|-- schemas/ # Validation / serialization
| |-- auth_schema.py
| |-- customer_schema.py
| |-- product_create_schema.py
| |-- rent_schema.py
| `-- ...
|
|-- validators/ # Business validation too complex for basic schemas
| |-- category_validator.py
| |-- owner_validator.py
| `-- product_validator.py
|
|-- common/ # Shared modules
| |-- constants.py
| |-- decorators.py
| |-- error_handlers.py
| |-- exceptions.py
| |-- logger.py
| |-- messages.py
| |-- responses.py
| `-- utils.py
|
|-- dto/ # DTO / transport objects
|-- utils/ # Utility functions
`-- tests/ # Unit / integration tests
```

## 3. Required Architecture

Layered structure:

- Controller (blueprints): request/response handling
- Service: business logic + orchestration
- Repository: database access only
- Schema: validation and serialization
- Common: shared modules (messages, responses, exceptions)

### Rules

- Controllers must stay thin
- Services own business logic and transactions
- Repositories handle only DB operations
- No database access in controllers
- No business logic in repositories

## 4. Naming and Structure Rules

### File Naming

- `*_controller.py`
- `*_service.py`
- `*_repository.py`
- `*_schema.py`
- `*_validator.py`

### Function Naming

Use clear intent:

- `create_customer`
- `update_product`
- `search_orders`

Avoid:

- `process_data`
- `handle_logic`
- `do_action`

### Python Function Naming Convention

Follow standard Python naming style aligned with PEP 8:

- Use `snake_case` for function names
- Prefer verb-led names for actions:
 - `create_user`
 - `update_profile`
 - `delete_order`
 - `validate_input`
- Names must be descriptive and readable:
 - Good: `send_email`, `calculate_tax`, `fetch_orders`
 - Bad: `do_stuff`, `func1`, `f1`
- Avoid CamelCase or mixed style for functions:
 - Bad: `GetUserData`, `getUserData`
- Avoid names that shadow Python built-ins:
 - Bad: `list`
 - Better: `get_list`, `user_list`
- Boolean-returning functions should prefer:
 - `is_...`
 - `has_...`
 - `can_...`
- Keep names reasonably sized:
 - Avoid names that are too short and cryptic
 - Avoid names that are excessively long
- Private/internal helper functions should use a leading underscore:
 - `_calculate_internal_score`
- Reserved special methods use double underscore only when matching Python's built-in protocol:
 - `__init__`
 - `__str__`

## 5. Model Naming Convention (`m_` / `t_`)

### Concept

- `m_` = Master table (reference data)
- `t_` = Transaction table (event-based data)

### Master Table (`m_`)

Used for:

- stable data
- shared reference

Examples:

- `m_customer` -> `MCustomer`
- `m_product` -> `MProduct`
- `m_category` -> `MCategory`

### Transaction Table (`t_`)

Used for:

- actions/events
- time-based records

Examples:

- `t_rent` -> `TRent`
- `t_order` -> `TOrder`
- `t_payment` -> `TPayment`

### Naming Rules

- File:
 - `m_xxx.py`
 - `t_xxx.py`
- Class:
 - `MCustomer`
 - `TRent`
- Table:
 - `m_customer`
 - `t_rent`

### Relationship Rule (Important)

- Transaction SHOULD conceptually reference Master
- ForeignKey is NOT required to be explicitly defined in model

Example (concept only):

```python
customer_id = db.Column(db.Integer)
```

We rely on conceptual relationship, not strict ORM constraint.

Reason:

- Easier delete / update
- More flexible data handling
- Avoid tight coupling between tables

### Soft Delete (Master)

```python
active_flag = db.Column(db.Boolean, default=True)
```

### Summary

- Use `m_` / `t_` consistently
- Treat FK as conceptual (not mandatory in model)
- Optimize for flexibility + maintainability

## 6. ORM and Data Access Rules

### Mandatory Rules

- Use SQLAlchemy ORM for all database access
- Do not use raw SQL
- Do not build SQL strings manually
- Prefer ORM query APIs, relationships, filters, joins, and model-based updates only

### Security Rationale

- ORM-first access helps reduce SQL injection risk by avoiding direct SQL string construction
- Keep all query input bound through ORM expressions, not string interpolation
- Readability and consistency are better when the codebase uses one database access style only

### Additional Rules

- Do not query DB in controllers
- Do not mix ORM with raw SQL
- Models represent table structure (not business logic)

## 7. Transaction Rules

- Transactions belong to service layer only

Example:

```python
with db.session.begin():
 ...
```

- Repositories must NOT call `commit()`
- Controllers must NOT manage transactions

## 8. Response and Error Rules

### Standard Response

Success:

```json
{
 "success": true,
 "message": "...",
 "data": {}
}
```

Error:

```json
{
 "success": false,
 "error_code": "...",
 "message": "..."
}
```

### Rules

- Use shared response format
- Centralize messages (`messages.py`)
- Do not leak internal exceptions

### Exceptions

- `ValidationError`
- `NotFoundError`
- `UnauthorizedError`
- `AppError`

## 9. Decision Rules (Very Important)

### Use Schema When

- Validating request shape
- Required fields / type checking

### Use Validator When

- Validation depends on DB
- Complex multi-field logic

### Use Repository When

- Querying database
- CRUD operations
- Joins, filters, pagination

### Use Service When

- Business logic
- Orchestration across repositories
- Transactions
- Building response payload

## 10. Query / Pagination Rules

For list endpoints:

- Use:
 - `page`
 - `page_size`
 - `sort_by`
 - `sort_order`
- Validate sortable fields
- Keep filtering in service/repository
- Return consistent pagination structure

## 11. Backend Security Rules

- Always validate input via schema
- Never expose internal errors
- Never hardcode secrets
- Use ORM expressions only for database access
- Never concatenate user input into SQL-like strings
- Validate file upload (type + size)
- Avoid mass assignment
- Apply auth checks in service/decorator layer

## 12. Database Migration Rules

- Use Alembic for schema changes
- In Flask projects, use Flask-Migrate / `flask db` commands as the standard migration workflow
- Do not modify DB manually in production
- Keep models aligned with migrations
- Explicitly define:
 - `nullable`
 - `default`
 - `index`
 - `unique constraints`

### Standard Migration Workflow

After changing models:

1. Update the SQLAlchemy model first
2. Generate a migration with `flask db migrate -m "short description"`
3. Review the generated migration file before applying it
4. Apply it with `flask db upgrade`
5. Verify the app still works with the migrated schema

### Migration Rules

- Migration files must be reviewed, not trusted blindly
- Keep one migration focused on one logical schema change when possible
- Use clear migration messages
- Do not edit production schema manually as a shortcut
- Do not let model changes exist without a matching migration

### Migration Safety Checks

- Confirm column type changes are intentional
- Confirm nullability/default/index changes are intentional
- Confirm rename/drop operations will not destroy required data unexpectedly
- Confirm migration order is safe for existing environments

## 13. SonarQube & Maintainability Rules

### Must Check Before Finishing

- High cognitive complexity
- Deep nested conditionals
- Repeated logic blocks
- Oversized functions
- Duplicate string literals
- Unused variables/imports

### Rules

- Use guard clauses (fail fast)
- Split logic by responsibility
- Extract helpers only if improves readability

### Critical Rule

Do NOT refactor just to reduce Sonar score if readability becomes worse.

## 14. Refactor & Change Safety

- Do not change behavior unless requested
- Preserve API response shape
- Separate refactor from bug fix
- Keep output predictable

## 15. Backend Change Workflow

Before coding:

1. Identify correct layer
2. Reuse existing patterns (messages, responses, exceptions)
3. Preserve response format
4. Avoid introducing new patterns unnecessarily
5. Keep logic consistent with existing structure

## 16. Testing Priority

Focus testing on:

1. Service business logic
2. Validation failure cases
3. Transaction flows
4. Repository queries
5. Critical endpoints

## 17. Anti-Patterns (Avoid These)

- Controller calling database directly
- Repository committing transactions
- Service returning Flask response
- Model containing heavy business logic
- Giant functions mixing everything
- Any raw SQL usage
- Hidden logic or magic behavior

## 18. Persistent Work Memory Rules

Update:

- `AGENTS.md`
- `TASK_STATE.md`

### Trigger When

- New feature
- Refactor
- Business logic change
- Debugging with root cause
- Multi-step tasks

### Do NOT Update For

- Simple Q&A
- Minor fixes

### Purpose

- `AGENTS.md` captures durable working rules, task board state, and handoff context for the next chat
- `TASK_STATE.md` captures the latest request, completed work, decisions, remaining work, blockers, and next step

### AGENTS.md Recommended Structure

Use a practical structure like:

```md
# AGENTS.md

## Working Rules

- Project-specific engineering rules
- Architecture constraints
- Readability / maintainability expectations

## Current Task Board

### In Progress

- ...

### Pending

- ...

### Blocked

- ...

### Done

- ...

## Current Context For Next Chat

- Current user goal
- Important implementation state
- Current risks / assumptions
- Most practical next step
```

### AGENTS.md Writing Rules

- Keep it durable across chats, not tied only to one transient message
- Prefer stable project rules over temporary commentary
- Keep task board status current: `In Progress`, `Pending`, `Blocked`, `Done`
- In `Current Context For Next Chat`, summarize only the state needed to resume work quickly
- Use concise bullets with concrete technical facts

### TASK_STATE.md Recommended Structure

Use a progress-oriented structure like:

```md
## Task Update - <short task name>

### Latest Request

- ...

### Completed

- ...

### Files Updated

- ...

### Important Decisions

- ...

### Remaining Work

- ...

### Blockers (if any)

- None

### Next Step

- ...
```

### TASK_STATE.md Writing Rules

- Focus on the current workstream, not general project rules
- Record concrete completed actions, not vague progress notes
- Track important decisions so future chats understand why changes were made
- Keep `Remaining Work` actionable and specific
- Always include a clear `Next Step`
- Use `Blockers (if any)` even when there are none

### Content Guidance

- `AGENTS.md` should answer:
 - How this project should be worked on
 - What is currently in flight
 - What the next chat needs to know
- `TASK_STATE.md` should answer:
 - What was requested
 - What was completed
 - What decisions were made
 - What remains next

### Update Quality Rules

- Reflect actual repository state, not idealized target state
- Prefer factual summaries over long narratives
- Do not duplicate large sections unnecessarily between the two files
- Keep entries readable for quick continuation in the next session

## 19. Golden Rules (Summary)

1. Readability first
2. Explicit over clever
3. Service owns logic + transaction
4. Repository owns DB access
5. Use ORM only
6. Avoid over-engineering
7. Keep everything predictable
