---
name: win-backend-craft
description: Implement or review backend changes in Flask-style backend repositories that use layered architecture with controllers, services, repositories, schemas, validators, models, shared responses, and centralized error handling. Use when Codex needs to follow Win's explicit, readability-first backend style that prefers direct code, clear separation of responsibilities, service-owned transactions, SQLAlchemy repositories, and avoids lambda-heavy or abstraction-heavy solutions.
---

# Project Style Guide

This document summarizes the coding style and project structure preferences that should be reused in future backend projects.

## First-Time Repository Setup

When using this skill in a repository for the first time:

1. Check whether `AGENTS.md` exists in the project root.
2. If it does not exist, create a concise `AGENTS.md` before making large or structural backend changes.
3. Keep `AGENTS.md` shorter than this skill. It should act as the repository-level operating guide, not a full reference document.
4. Summarize the mandatory working rules from this skill in `AGENTS.md`, including:
 - explicit readable code
 - clear controller/service/repository separation
 - service-owned transactions
 - repositories focused on data access
 - shared response and error handling
 - centralized messages or constants
 - readable SQLAlchemy usage
 - avoid lambda-heavy, regex-heavy, or abstraction-heavy solutions unless clearly justified
5. If `AGENTS.md` already exists, follow it and only extend it when the repository clearly needs missing rules from this skill.

## 1. Coding Style Preference

### Core Principles

- Prefer code that is easy to read at a glance.
- Prioritize maintainability over cleverness.
- Prefer clear structure with separated responsibilities.
- Prefer explicit code over generic magic.
- If there is a choice, choose the approach that the team can read and maintain more easily.

### Preferred Style

- Use function and variable names that clearly express intent.
- Centralize repeated text or shared values in files such as `messages.py` or `constants.py`.
- Prefer simple conditionals over complicated transformations.
- Keep flow readable from top to bottom.
- Keep business logic in the service layer.
- Keep repositories focused on query and CRUD responsibilities.
- Keep a consistent shared response format.
- Keep validation clear and readable.

### Things To Avoid

- `lambda`
- Regex-heavy or pattern-heavy approaches unless truly necessary
- Translator-style or dynamic transformation layers that make code harder to follow
- Multi-layer abstraction for small problems
- Generic helpers that are harder to understand than direct code
- Clever logic that makes debugging harder
- Magic strings scattered across the system

### Practical Rules

- If direct code is clear enough, do not generalize it.
- If a message is reused in multiple places, centralize it.
- If choosing between shorter and clearer code, choose clearer code.
- Do not add a helper unless it removes real duplication and improves readability.
- Only extract reusable logic when the result stays easy to read.
- Avoid techniques that require teammates to interpret hidden behavior before understanding the code.

## 2. Preferred Coding Pattern

### Response And Message

- Use a shared response format.
- Centralize standard messages in `app/common/messages.py`.
- Write outward-facing messages so they are ready to use at the point they are produced.
- Avoid multi-step retroactive message transformation when a direct message can be written instead.

Preferred pattern:

```python
from app.common.messages import MSG_SUCCESS
from app.common.responses import success_response

return success_response(data=result, message=MSG_SUCCESS)
```

### Error Handling

- Use shared custom exceptions such as `ValidationError`, `UnauthorizedError`, and `NotFoundError`.
- Error messages should be safe and usable by the frontend.
- Do not leak raw exceptions to clients.

Preferred pattern:

```python
if not product:
 raise ValidationError("Product not found")
```

### Business Logic

- Controllers receive the request, validate input, call services, and return responses.
- Services handle the main business flow.
- Repositories handle database access.
- Helpers should only exist for clearly scoped sub-logic that genuinely improves readability.

### Validation

- Use schemas as the first validation layer.
- Validation messages should be readable and usable by the frontend.
- If validation text is reused, centralize it in messages or constants.

## 3. Project Structure Philosophy

This style uses a layered backend structure that is well suited for maintainable API projects.

Layer split:

- `blueprints/` handles requests and endpoint mapping
- `services/` handles business logic and orchestration
- `repositories/` handles data access
- `models/` defines ORM models
- `schemas/` handles validation and serialization
- `common/` contains shared modules used across the system

## 4. Recommended Project Structure

```text
app/
├─ __init__.py # Application factory
├─ config.py # Config classes
├─ extensions.py # Flask extensions
├─ routes.py # Base route / health check
│
├─ blueprints/ # Controller / API endpoints
│ ├─ auth_controller.py
│ ├─ customer_controller.py
│ ├─ product_controller.py
│ ├─ rent_controller.py
│ ├─ report_controller.py
│ └─ ...
│
├─ services/ # Business logic layer
│ ├─ auth_service.py
│ ├─ customer_service.py
│ ├─ product_service.py
│ ├─ rent_service.py
│ ├─ report_service.py
│ ├─ todo_service.py
│ └─ helpers/ # Small domain-specific helper logic
│
├─ repositories/ # Query / CRUD layer
│ ├─ customer_repository.py
│ ├─ product_repository.py
│ ├─ rent_repository.py
│ └─ ...
│
├─ models/ # SQLAlchemy models
│ ├─ m_customer.py
│ ├─ m_product.py
│ ├─ t_rent.py
│ └─ ...
│
├─ schemas/ # Validation / serialization
│ ├─ auth_schema.py
│ ├─ customer_schema.py
│ ├─ product_create_schema.py
│ ├─ rent_schema.py
│ └─ ...
│
├─ validators/ # Business validation too complex for basic schemas
│ ├─ category_validator.py
│ ├─ owner_validator.py
│ └─ product_validator.py
│
├─ common/ # Shared modules
│ ├─ constants.py
│ ├─ decorators.py
│ ├─ error_handlers.py
│ ├─ exceptions.py
│ ├─ logger.py
│ ├─ messages.py
│ ├─ responses.py
│ └─ utils.py
│
├─ dto/ # DTO / transport objects
├─ utils/ # Utility functions
└─ tests/ # Unit / integration tests
```

## 5. Layer Responsibilities

### Blueprint Layer

Responsibilities:

- Receive requests
- Parse input
- Call schema validation or load
- Call services
- Return the shared response format

Should not:

- Query the database directly
- Hold long business logic
- Own transaction flow unless absolutely necessary

### Service Layer

Responsibilities:

- Own the main business flow
- Combine repository calls
- Enforce business rules
- Own transaction boundaries

Should not:

- Format HTTP responses directly
- Replace repository responsibilities with very detailed query logic

### Repository Layer

Responsibilities:

- Query data
- Create, update, and delete data
- Own database access

Should not:

- Decide business flow
- Build outward response messages

## 6. Naming Style

### Files

- Controllers use `*_controller.py`
- Services use `*_service.py`
- Repositories use `*_repository.py`
- Schemas use `*_schema.py`
- Validators use `*_validator.py`

### Classes And Functions

- Use names that reflect business meaning
- Prefer clear verb-based method names such as `create_customer`, `update_product`, and `search_rents`
- Avoid vague names such as `process_data`, `handle_it`, or `do_action`

## 7. Message Style

Guidelines:

- Keep messages short and clear.
- Keep message language consistent within the system.
- Separate user-facing messages from internal debug messages.

Good examples:

## 8. Sonar Maintainability Guardrails

Use these rules to prevent common Sonar maintainability issues, especially Cognitive Complexity problems in backend services and repositories.

### Keep Function Roles Narrow

- One function should do one level of work.
- Keep orchestration in the main function.
- Move repeated branching, transformation, and nested update logic into clearly named helpers.
- Prefer a main flow that reads top to bottom without deep nesting.

Preferred pattern:

- main function: validate input, load dependencies, call helpers, return result
- helper functions: one focused responsibility each

### Refactor Before Complexity Grows

When a function starts combining several concerns, split by concern early.

Common split points:

- input validation
- query or filter preparation
- stock or availability checks
- payload building
- result formatting
- state transition handling
- repeated dict or list aggregation
- note, address, owner, or customer resolution

### Use Guard Clauses

- Prefer early return or early raise over nested `if/else`
- Fail fast for invalid input, missing entities, and unsupported states
- Flatten condition trees whenever possible

Preferred pattern:

```python
if not product:
 raise ValidationError("Product not found")

if not items:
 raise ValidationError("items is required")
```

Avoid wrapping the main logic in multiple nested branches when a guard clause can exit earlier.

### Extract Helpers Only When They Improve Readability

- Do not create generic helpers with unclear meaning.
- Extract helpers when they remove real duplication or reduce nesting.
- Helper names must explain the business purpose, not only the technical action.

Prefer names like:

- `_build_receipt_items`
- `_validate_editable_rent`
- `_apply_rent_status_transition`

Avoid names like:

- `_process_data`
- `_handle_logic`
- `_do_update`

### Separate Orchestration From Data Assembly

For service and repository code:

- main functions should orchestrate the business flow
- helpers should assemble payloads, maps, grouped structures, and derived values
- large dict or list construction should move to dedicated builders when it obscures the main flow

### Keep Conditionals Local To Their Concern

- Do not mix validation, persistence, formatting, and side effects in the same loop
- If a loop both validates, mutates models, builds response payloads, and updates related records, split that work into focused helpers

### Centralize Repeated Range Or Filter Logic

If the same style of filter or mapping appears repeatedly:

- extract a focused helper
- prefer explicit helper calls over long inline branching
- keep helper input and output simple and predictable

### Preserve Behavior During Refactor

When refactoring for maintainability:

- do not change business logic unless explicitly requested
- preserve output shape and side effects
- prefer structural refactor over behavioral change
- if a suspected bug is noticed, separate refactor from bug fix

### Sonar-Oriented Review Checklist

Before finishing a backend change, check:

- Does the main function mix more than one concern?
- Are nested loops and nested conditionals stacked in the same block?
- Can repeated dict-building move to a builder helper?
- Can repeated state handling move to a dedicated transition helper?
- Can repeated validation move to a validator helper or guard clause?
- Did helper extraction keep the code clearer, not more abstract?

- `Create customer success`
- `Product not found`
- `JSON body required`
- `date must be YYYY-MM-DD`

Avoid:

- Internal debug text leaking to the frontend
- Generic messages such as `Error` or `Something went wrong`

## 8. What To Avoid In Future Projects

- Do not use `lambda` when a normal function is clearer.
- Do not start with abstraction that exceeds the real need.
- Do not use regex-heavy or dynamic translation logic when simple mapping is enough.
- Do not blur service and repository responsibilities.
- Do not scatter important shared messages across many files.
- Do not leak raw exceptions from the API.

## 9. Recommended Default Rule Set

Use these defaults in future projects:

1. Optimize for readability first.
2. Use constants or messages for repeated values.
3. Keep controller, service, and repository responsibilities clear.
4. Avoid techniques the team is unlikely to maintain comfortably.
5. Prefer explicit code over clever code.
6. Simplify anything that makes debugging harder.
7. Write frontend-facing messages so they are ready to use from the start.

## 10. Short Summary

This style prefers:

- Direct readable code
- No hard-to-read syntax such as `lambda`
- Clear separation of responsibilities
- Shared centralized messages and constants
- Long-term maintainability over implementation cleverness

## 11. ORM And SQLAlchemy Conventions

This style uses SQLAlchemy in a fairly direct way. It does not rely on heavy model behavior. Instead, it keeps queries and data composition mostly in repositories and services.

### ORM Guidelines

- Models should mainly represent table structure.
- Do not place heavy business logic inside models.
- Complex queries should live in repositories.
- Services should compose multiple repositories when needed.
- Aggregated multi-table data can be assembled in repositories or domain-specific helpers.

### Model Naming

Observed pattern:

- Master tables use `M` prefixes such as `MProduct`, `MCustomer`, and `MOwner`
- Transaction tables use `T` prefixes such as `TRent`, `TRentDetail`, and `TNote`
- File names map directly to table naming such as `m_product.py` and `t_rent.py`

Reusable guideline:

- If the organization uses `m_` and `t_` naming, keep it consistent.
- Class names should map clearly to table names.
- Field names should reflect the real database fields.

### Soft Delete Convention

This style strongly prefers `active_flag` for soft delete behavior.

Guidelines:

- If business history should be preserved, use soft delete with `active_flag = False`.
- Normal queries should filter on `active_flag == True`.
- Repository and service delete behavior should clearly indicate soft delete versus hard delete.
- If soft delete is the default, avoid accidental hard deletes.

### Audit Fields

Common fields:

- `create_date`
- `create_by`
- `update_date`
- `update_by`
- `active_flag`

Recommended rule:

- Business tables should consistently include audit fields where appropriate.
- Services should own create and update field assignment.
- Controllers should not manage audit fields directly.

### Query Style

Observed patterns:

- Use `Model.query.filter_by(...)` for simple queries
- Use `db.session.query(...)` for more complex join and aggregate logic
- Use `exists`, `or_`, `and_`, `func`, and `case` for more advanced repository queries

Guidelines:

- Keep simple queries simple.
- Move complex queries into repositories.
- Break large queries into intent-based parts such as base query, count, pagination, and final fetch.
- Do not put long query blocks in controllers.

### Serialization Strategy

This style uses both schema dump and manual dict assembly.

Guidelines:

- Use schemas when the response structure maps fairly cleanly to model structure.
- Use manual dict assembly when the response is a multi-table aggregated payload.
- Do not force everything through schemas when transformation becomes harder to understand.

## 12. Transaction And DB Commit Rules

Observed patterns:

- Many services use `with db.session.begin():`
- Some repositories still call `commit()` directly

Preferred rule for future projects:

- Transactions should belong to the service layer.
- Repositories should `add`, `update`, and `delete` without committing.
- Services should decide which operations belong in a single transaction.

Why:

- Services see the full business flow.
- Rollback behavior is easier to reason about.
- Side effects from repository-level commits are reduced.
- Tests become easier to control.

Preferred pattern:

```python
with db.session.begin():
 customer = CustomerRepository.create(obj)
 AddressRepository.create(address)
 NoteRepository.create(note)
```

Avoid:

- Some repositories committing while others do not
- Services assuming a single transaction while repositories commit in the middle
- Controllers managing commits

## 13. Error Handling Conventions

Error flow should center around:

- shared custom exceptions in `app/common/exceptions.py`
- global handlers in `app/common/error_handlers.py`
- shared response formatting in `app/common/responses.py`

### Rules

- Raise custom exceptions for expected business and validation errors.
- Messages should be safe and useful to the frontend.
- Generic exceptions must be caught and should not leak internals.
- Do not use exceptions to hide implementation bugs.

### Exception Split

- `ValidationError` for invalid input or business rule failures
- `UnauthorizedError` for auth or permission problems
- `NotFoundError` for missing resources
- `AppError` for custom application errors with specific codes or statuses

### Handler Guidance

- `AppError` should always be returned in the shared response format.
- `HTTPException` should be normalized to the standard error response.
- `Exception` should return a safe generic internal error message.

### Important Warnings

- Do not expose `str(e)` from internal system exceptions to clients.
- Do not reuse debug or log text as user-facing error text.
- Keep logging responsibilities separate from response formatting responsibilities.

## 14. Response Format Rules

The standard response structure is:

- success:
 - `success`
 - `message`
 - `data`
- error:
 - `success`
 - `error_code`
 - `message`

Guidelines:

- Every endpoint should use the same shape.
- Success messages should be short and clear.
- Error messages should be frontend-safe and usable.
- `data` should be the main payload, not a dumping ground for mixed metadata.

## 15. Blueprint / Controller Rules

Controllers should stay thin.

### Controllers Should

- Read `request.get_json()` or `request.args`
- Validate through schemas
- Map path params, query params, and body fields into service-friendly input
- Return `success_response(...)`

### Controllers Should Not

- Write database queries
- Hold long loop-heavy logic
- Calculate business rules
- Manage multi-step transactions

### Preferred Pattern

- One endpoint per function
- Function names should reflect endpoint intent such as `create_rent` and `search_product`
- If schema load fails, raise custom validation errors immediately

## 16. Service Layer Rules

Services are the center of business behavior.

### Services Should

- Validate business rules beyond schema shape validation
- Orchestrate multiple repositories
- Own transactions
- Build business output payloads
- Call helpers only for narrow domain-specific work

### Services Should Not

- Depend on Flask response objects
- Perform infrastructure-style logging
- Collect large complex query blocks that belong in repositories

### Helper Split Guidance

- Extract helpers when sub-logic is clearly scoped and repeated
- Keep helpers domain-specific, such as `product_create_helper` or `rent_helper`
- Do not turn helpers into a second service layer

## 17. Repository Layer Rules

Repositories should support both simple CRUD and more complex query composition.

### Repositories Should

- Encapsulate query behavior
- Return model objects or grouped data that services can use
- Own join, filter, and pagination query logic

### Repositories Should Not

- Decide business state
- Create outward-facing response messages
- Know about HTTP concerns

### Additional Guidance

- Basic CRUD methods should be predictable, such as `get_by_id`, `create`, `update`, and `delete`
- Paginated repository methods should return `items`, `total`, `page`, and `page_size`
- If a query is large, split it into private helper methods inside the repository

## 18. Schema And Validator Rules

This style uses both `schemas/` and `validators/`.

### Use `schemas/` For

- Request shape validation
- Type validation
- Required-field validation
- Serialization and deserialization

### Use `validators/` For

- Business validation too complex for simple schema fields
- Validation that depends on database checks
- Validation that combines multiple conditions across the domain

### Responsibility Split

- Schemas handle structure
- Validators handle business validation
- Services orchestrate multiple validation steps

## 19. File Splitting Rules

Split files based on business responsibility, not only line count.

### Split Files When

- The code belongs to different domains such as customer, owner, product, or rent
- The code belongs to different responsibilities such as controller, service, or repository
- Helpers grow large enough to hide the main logic
- Schemas or validators belong to separate use cases

### Avoid Splitting When

- The logic is still small and within one domain
- Splitting would force too much jumping between files
- An abstraction exists only for reuse in one place

### Naming Rules

- Endpoint files use `*_controller.py`
- Domain logic files use `*_service.py`
- Data access files use `*_repository.py`
- DTOs are appropriate when the payload shape is clear and a transport object adds clarity
- Shared messages and constants belong in `common/`

## 20. App Initialization And Registration

This style prefers an application factory with explicit blueprint registration.

Guidelines:

- Use `create_app()` as the main entry point
- Load config from a central config module
- Initialize extensions in the app factory
- Register blueprints explicitly for clarity
- Always include a health check route

Benefits:

- Startup flow is easy to follow
- Active modules are easy to discover
- It avoids unnecessary complexity from auto-discovery

## 21. Extensions And Infrastructure Rules

Infrastructure objects such as `db`, `migrate`, `jwt`, `cors`, and `redis_client` should live in a shared extensions module.

Guidelines:

- Keep extension objects in one central file
- Import them from `app.extensions`
- Do not instantiate them repeatedly across modules
- Infrastructure integrations such as Redis, S3, or PDF APIs should have dedicated services or wrappers

## 22. Logging Rules

This style treats logging as a shared cross-cutting concern.

Guidelines:

- Keep request and response logging centralized
- Log request body only when safe
- For multipart requests, log metadata rather than file content
- Log only the response data that is necessary
- Support clear log levels such as info, warning, and error

Warnings:

- Do not log secrets such as tokens or passwords
- Do not log oversized payloads unless required
- Logging must not change response behavior

## 23. Config And Environment Rules

Guidelines:

- Keep config classes in `config.py`
- Keep secrets in `.env`
- Never hardcode secrets in source
- Put environment-dependent infrastructure flags such as logging, Redis rate limiting, or external API URLs into config

## 24. Domain Pattern Observed In This Project

Observed domains:

- auth
- customer
- owner
- address / location
- category / color / size / product type
- product
- rent
- note
- todo
- print
- report
- config rent

Guidelines for future projects:

- Split modules by domain first
- Then split by layer inside the domain
- If a domain becomes large, move it into its own subpackage

## 25. Current Inconsistencies To Learn From

Useful lessons from the current codebase:

- Some repositories commit directly while others do not
- Both manual dict payloads and schema dumps are used
- Some legacy names such as `items.py` and `rentals.py` coexist with the dominant `*_controller.py` convention

Takeaway:

- Future projects should define conventions early
- Especially around transactions, message ownership, naming, and response structure

## 26. Testing Guidance

Recommended testing priorities:

- Service logic with important business rules
- Important endpoint success paths
- Validation failure paths
- Soft delete and `active_flag` behavior
- Transaction-sensitive flows such as create rent, edit rent, and update status

## 27. Recommended Future Standard

Use this as a default standard for future projects:

1. Use an application factory
2. Separate `blueprints`, `services`, `repositories`, `models`, `schemas`, and `common`
3. Use custom exceptions and global error handlers
4. Let services own transactions
5. Let repositories focus only on data access
6. Use `messages.py` or centralized constants for repeated messages
7. Use soft delete consistently when business history matters
8. Write explicit readable code
9. Avoid `lambda`, regex-heavy, and abstraction-heavy solutions
10. Keep one shared response format across the API
