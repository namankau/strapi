# Audit Logs Plugin - Design Summary

## Objective
The Audit Logs plugin automatically tracks all create, update, and delete operations performed via Strapi's Content API. It captures key metadata for auditing, debugging, and compliance purposes.

---

## Architecture Overview
Client/API ---> Strapi Content API ---> Audit Logger Middleware ---> Audit Logs Collection


- **Content API**: The standard Strapi REST API where CRUD operations occur.
- **Middleware (`audit-logger.js`)**: Intercepts POST, PUT, DELETE requests to capture operation metadata.
- **Service (`audit-log.js`)**: Handles persistence and query logic for audit logs.
- **Controller (`audit-log.js`)**: Exposes `/api/audit-logs` endpoint for retrieval.
- **Policy (`read-audit-logs.js`)**: Ensures only authorized users can access audit logs.
- **Content-Type (`audit-log`)**: Database schema storing audit details such as user, action, content type, record ID, payload, and timestamp.
- **Bootstrap (`bootstrap.js`)**: Initializes the plugin and optionally registers global middleware if enabled.

---

## Key Features
1. **Automatic Logging**: Tracks every create, update, delete on API content types.
2. **Role-based Access Control**: Only users with `read_audit_logs` permission can access logs.
3. **Filtering & Pagination**: REST endpoint supports filtering by content type, user, action, date range, and sorting.
4. **Configurable**:  
   - `enabled` → toggle logging globally.  
   - `excludeContentTypes` → exclude specific content types from logging.

---

## Implementation Details

- **Middleware**: Intercepts API requests and delegates logging to the service.
- **Service Layer**: Responsible for creating audit entries and querying logs with filters, sorting, and pagination.
- **Controller Layer**: Minimal logic — simply calls service and returns response.
- **Policy Layer**: Checks user roles and permissions before allowing access to logs.
- **Schema Design**: JSON payload stored in a `payload` field, timestamped with `createdAt`. Indexed for efficient queries.
- **Bootstrap**: Registers middleware only if plugin is enabled in config.

---

## Advantages of this Approach

1. **Separation of Concerns**: Middleware handles interception, service handles business logic, controller exposes endpoint, policy enforces security.
2. **Scalability**: Uses indexing and paginated queries to efficiently handle large log datasets.
3. **Flexibility**: Can easily add new logging rules, exclude content types, or extend fields without affecting core Strapi logic.
4. **Non-Intrusive**: Plugin integrates without modifying Strapi core.

---

## Potential Extensions
- Admin UI page for viewing logs directly in Strapi dashboard.
- Webhooks for real-time alerting on specific actions.
- More granular diff logging for updates.

---

## Author
Naman Kaushik
