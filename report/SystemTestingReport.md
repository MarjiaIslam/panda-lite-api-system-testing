# System Testing Report: Panda-Lite API

**Team:** C3  
**Name:** Marjia Islam  
**Date:** 02.09.2026

## 1\. Test Plan

### 1.1 POST /\_internal/reset-database

| **Scenario**                            | **Input Summary**                                  | **Expected Output**                 | **Expected Status Code**       |
| --------------------------------------- | -------------------------------------------------- | ----------------------------------- | ------------------------------ |
| Reset the team database before testing. | Send the internal reset request with the STQA key. | HTTP 200 and reset status reported. | As documented for the endpoint |

### 1.2 GET /\_internal/health

| **Scenario**                 | **Input Summary**                                | **Expected Output**          | **Expected Status Code**       |
| ---------------------------- | ------------------------------------------------ | ---------------------------- | ------------------------------ |
| Verify service availability. | Send the health-check request with the STQA key. | HTTP 200 and status healthy. | As documented for the endpoint |

### 1.3 GET /\_internal/db-state

| **Scenario**                              | **Input Summary**              | **Expected Output**                                                                | **Expected Status Code**       |
| ----------------------------------------- | ------------------------------ | ---------------------------------------------------------------------------------- | ------------------------------ |
| Verify the database is empty after reset. | Request the database snapshot. | HTTP 200 and empty users, restaurants, menuItems, orders, and ratings collections. | As documented for the endpoint |

### 1.4 POST /auth/register

| **Scenario**                                                                                                                         | **Input Summary**                                         | **Expected Output**                                                     | **Expected Status Code**       |
| ------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------- | ----------------------------------------------------------------------- | ------------------------------ |
| Register valid customer, restaurant, and rider accounts; validate missing fields, invalid role, short password, and duplicate email. | Send registration payloads with valid and invalid values. | 201 for valid registration; 400/409 for documented validation failures. | As documented for the endpoint |

### 1.5 POST /auth/login

| **Scenario**                                                             | **Input Summary**                      | **Expected Output**                                                  | **Expected Status Code**       |
| ------------------------------------------------------------------------ | -------------------------------------- | -------------------------------------------------------------------- | ------------------------------ |
| Validate successful login for each role and invalid/missing credentials. | Send valid and invalid login payloads. | 200 with JWT for valid credentials; 400/401 for invalid credentials. | As documented for the endpoint |

### 1.6 POST /restaurants

| **Scenario**                                      | **Input Summary**                                                          | **Expected Output**                                                           | **Expected Status Code**       |
| ------------------------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------ |
| Validate role-based creation and required fields. | Create restaurants as authorized/unauthorized users and with missing data. | 201 for valid restaurant owner; 403/400 for unauthorized or invalid requests. | As documented for the endpoint |

### 1.7 GET /restaurants

| **Scenario**                                                                                             | **Input Summary**                                              | **Expected Output**                                             | **Expected Status Code**       |
| -------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | --------------------------------------------------------------- | ------------------------------ |
| Validate listing, partial name search, sorting, exact boolean filtering, and invalid query combinations. | Execute list requests with valid and invalid query parameters. | 200 for valid list operations; 400 for invalid list parameters. | As documented for the endpoint |

### 1.8 GET /restaurants/:id

| **Scenario**                                                                              | **Input Summary**                             | **Expected Output**                                                  | **Expected Status Code**       |
| ----------------------------------------------------------------------------------------- | --------------------------------------------- | -------------------------------------------------------------------- | ------------------------------ |
| Validate retrieval of existing and nonexistent restaurants, including closed restaurants. | Request valid and nonexistent restaurant IDs. | 200 for existing restaurant; 404 for nonexistent; 401 without token. | As documented for the endpoint |

### 1.9 PATCH /restaurants/:id

| **Scenario**                                               | **Input Summary**              | **Expected Output**                       | **Expected Status Code**       |
| ---------------------------------------------------------- | ------------------------------ | ----------------------------------------- | ------------------------------ |
| Validate owner-only updates and open/closed state changes. | Update as owner and non-owner. | 200 for owner updates; 403 for non-owner. | As documented for the endpoint |

### 1.10 POST /restaurants/:id/menu

| **Scenario**                                                             | **Input Summary**                                       | **Expected Output**                                      | **Expected Status Code**       |
| ------------------------------------------------------------------------ | ------------------------------------------------------- | -------------------------------------------------------- | ------------------------------ |
| Validate owner-only menu creation, required fields, and negative values. | Create menu items using valid/invalid roles and values. | 201 for valid creation; 403/400 for invalid access/data. | As documented for the endpoint |

### 1.11 GET /restaurants/:id/menu

| **Scenario**                                                                                                     | **Input Summary**                                        | **Expected Output**                                         | **Expected Status Code**       |
| ---------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------ |
| Validate menu listing, unavailable items, filtering, invalid filters, missing token, and nonexistent restaurant. | Run menu list queries with valid and invalid parameters. | 200 for valid queries; 400/401/404 for documented failures. | As documented for the endpoint |

### 1.12 PATCH /menu-items/:id

| **Scenario**                                                                                               | **Input Summary**                                            | **Expected Output**                                                                               | **Expected Status Code**       |
| ---------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------- | ------------------------------ |
| Validate owner-only updates, negative values, nonexistent items, and automatic availability at zero stock. | Update menu items with valid/invalid price and stock values. | 200 for valid updates; 400/403/404 for documented failures; stock 0 should make item unavailable. | As documented for the endpoint |

### 1.13 POST /orders

| **Scenario**                                                                                                                                              | **Input Summary**                                                        | **Expected Output**                                           | **Expected Status Code**       |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------- | ------------------------------ |
| Validate authentication, customer-only ordering, restaurant/menu/item validation, closed restaurant handling, stock rules, and successful order creation. | Place valid and invalid orders using role-specific tokens and item data. | 201 for valid order; 400/401/403/404 for documented failures. | As documented for the endpoint |

### 1.14 GET /orders

| **Scenario**                                                                         | **Input Summary**                                                                | **Expected Output**                               | **Expected Status Code**       |
| ------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | ------------------------------------------------- | ------------------------------ |
| Validate role-based order listing, exact filtering, invalid filters, and visibility. | List orders as customer, restaurant owner, and rider with valid/invalid filters. | 200 for permitted views; 400 for invalid filters. | As documented for the endpoint |

### 1.15 GET /orders/:id

| **Scenario**                                                                                                     | **Input Summary**                              | **Expected Output**                                                         | **Expected Status Code**       |
| ---------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------------------------------- | ------------------------------ |
| Validate order visibility for customer, owning restaurant, assigned rider, unrelated users, and nonexistent IDs. | Retrieve the same order using different roles. | 200 only for permitted users; 403 for unrelated users; 404 for nonexistent. | As documented for the endpoint |

### 1.16 GET /orders/:id/timeline

| **Scenario**                          | **Input Summary**                                                             | **Expected Output**                                            | **Expected Status Code**       |
| ------------------------------------- | ----------------------------------------------------------------------------- | -------------------------------------------------------------- | ------------------------------ |
| Validate timeline access and sorting. | Retrieve timeline as authorized and unauthorized users and with invalid sort. | 200 for authorized requests; 403/400 for invalid access/query. | As documented for the endpoint |

### 1.17 PATCH /orders/:id/status

| **Scenario**                                                   | **Input Summary**                                                                                                     | **Expected Output**                                              | **Expected Status Code**       |
| -------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------------------------------ |
| Validate the documented order lifecycle and role restrictions. | Attempt valid and invalid transitions across placed, accepted, preparing, ready_for_pickup, picked_up, and delivered. | 200 for allowed transitions; 400/403 for invalid state or actor. | As documented for the endpoint |

### 1.18 GET /orders/available

| **Scenario**                                              | **Input Summary**                                                      | **Expected Output**                                        | **Expected Status Code**       |
| --------------------------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------- | ------------------------------ |
| Validate rider-only access and available-order filtering. | Request available orders as customer/rider with valid/invalid filters. | 200 for rider; 403 for non-rider; 400 for invalid filters. | As documented for the endpoint |

### 1.19 PATCH /orders/:id/claim

| **Scenario**                                            | **Input Summary**                                        | **Expected Output**                                           | **Expected Status Code**       |
| ------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------ |
| Validate rider-only claiming and first-rider ownership. | Claim a ready order with eligible and ineligible riders. | 200 for first eligible rider; 403/409/400 for invalid claims. | As documented for the endpoint |

### 1.20 POST /orders/:id/rate

| **Scenario**                                                                                                             | **Input Summary**                                  | **Expected Output**                                            | **Expected Status Code**       |
| ------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------- | -------------------------------------------------------------- | ------------------------------ |
| Validate rating ownership, delivered-state requirement, target, score range, rider assignment, and duplicate prevention. | Submit valid and invalid restaurant/rider ratings. | 201 for valid rating; 400/403/404/409 for documented failures. | As documented for the endpoint |

### 1.21 GET /restaurants/:id/ratings

| **Scenario**                                                                                                     | **Input Summary**                               | **Expected Output**                                          | **Expected Status Code**       |
| ---------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------ |
| Validate rating listing, score/target filters, invalid filter fields, missing token, and nonexistent restaurant. | Execute valid and invalid rating list requests. | 200 for valid requests; 400/401/404 for documented failures. | As documented for the endpoint |

### 1.22 PATCH /orders/:id/cancel

| **Scenario**                                                                                                                      | **Input Summary**                                                  | **Expected Output**                                                | **Expected Status Code**       |
| --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------ | ------------------------------ |
| Validate cancellation authorization, free cancellation from placed, penalty cancellation from accepted, and lifecycle boundaries. | Cancel orders in supported lifecycle states using different roles. | 200 for allowed cancellation; 400/403/404 for documented failures. | As documented for the endpoint |

## 2\. Test Cases

| **ID**     | **Short Title**                                         | **Pre-conditions**                                                                                             | **Expected** | **Actual**                                                                                                 | **Status** | **Evidence**                                                                       | **Pass/Fail** |
| ---------- | ------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------ | ---------------------------------------------------------------------------------------------------------- | ---------- | ---------------------------------------------------------------------------------- | ------------- |
| SYS-000    | Reset database                                          | Fresh test run; STQA key configured.                                                                           | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: SYS-000: Reset database                                           | **PASS**      |
| SYS-001    | Health check                                            | Fresh test run; STQA key configured.                                                                           | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: SYS-001: Health check                                             | **PASS**      |
| SYS-002    | Inspect database state                                  | Fresh test run; STQA key configured.                                                                           | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: SYS-002: Inspect database state                                   | **PASS**      |
| AUTH-001   | Register customer 1                                     | Database reset; no test accounts from this run.                                                                | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: AUTH-001: Register customer 1                                     | **PASS**      |
| AUTH-002   | Register customer 2                                     | Database reset; no test accounts from this run.                                                                | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: AUTH-002: Register customer 2                                     | **PASS**      |
| AUTH-003   | Register restaurant owner 1                             | Database reset; no test accounts from this run.                                                                | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: AUTH-003: Register restaurant owner 1                             | **PASS**      |
| AUTH-004   | Register restaurant owner 2                             | Database reset; no test accounts from this run.                                                                | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: AUTH-004: Register restaurant owner 2                             | **PASS**      |
| AUTH-005   | Register rider 1                                        | Database reset; no test accounts from this run.                                                                | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: AUTH-005: Register rider 1                                        | **PASS**      |
| AUTH-006   | Register rider 2                                        | Database reset; no test accounts from this run.                                                                | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: AUTH-006: Register rider 2                                        | **PASS**      |
| AUTH-007   | Register with a missing field                           | Database reset; no test accounts from this run.                                                                | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: AUTH-007: Register with a missing field                           | **PASS**      |
| AUTH-008   | Register with invalid role                              | Database reset; no test accounts from this run.                                                                | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: AUTH-008: Register with invalid role                              | **PASS**      |
| AUTH-009   | Register with short password                            | Database reset; no test accounts from this run.                                                                | HTTP 400     | HTTP 201; failed assertion(s): \[AUTH-009\] Register with short password: expected HTTP 400                | Executed   | Postman request: AUTH-009: Register with short password                            | **FAIL**      |
| AUTH-010   | Register duplicate email                                | Database reset; no test accounts from this run.                                                                | HTTP 409     | HTTP 409                                                                                                   | Executed   | Postman request: AUTH-010: Register duplicate email                                | **PASS**      |
| AUTH-012   | Login customer 1                                        | Valid accounts created by registration tests.                                                                  | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: AUTH-012: Login customer 1                                        | **PASS**      |
| AUTH-013   | Login customer 2                                        | Valid accounts created by registration tests.                                                                  | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: AUTH-013: Login customer 2                                        | **PASS**      |
| AUTH-014   | Login restaurant owner 1                                | Valid accounts created by registration tests.                                                                  | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: AUTH-014: Login restaurant owner 1                                | **PASS**      |
| AUTH-015   | Login restaurant owner 2                                | Valid accounts created by registration tests.                                                                  | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: AUTH-015: Login restaurant owner 2                                | **PASS**      |
| AUTH-016   | Login rider 1                                           | Valid accounts created by registration tests.                                                                  | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: AUTH-016: Login rider 1                                           | **PASS**      |
| AUTH-017   | Login rider 2                                           | Valid accounts created by registration tests.                                                                  | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: AUTH-017: Login rider 2                                           | **PASS**      |
| AUTH-018   | Login with missing password                             | Valid accounts created by registration tests.                                                                  | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: AUTH-018: Login with missing password                             | **PASS**      |
| AUTH-019   | Login with unknown email                                | Valid accounts created by registration tests.                                                                  | HTTP 401     | HTTP 401                                                                                                   | Executed   | Postman request: AUTH-019: Login with unknown email                                | **PASS**      |
| AUTH-020   | Login with wrong password                               | Valid accounts created by registration tests.                                                                  | HTTP 401     | HTTP 401                                                                                                   | Executed   | Postman request: AUTH-020: Login with wrong password                               | **PASS**      |
| REST-001   | Customer cannot create restaurant                       | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: REST-001: Customer cannot create restaurant                       | **PASS**      |
| REST-002   | Restaurant creation requires name and address           | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: REST-002: Restaurant creation requires name and address           | **PASS**      |
| REST-003   | Create restaurant 1                                     | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: REST-003: Create restaurant 1                                     | **PASS**      |
| REST-004   | Create restaurant 2                                     | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: REST-004: Create restaurant 2                                     | **PASS**      |
| REST-005   | List open restaurants                                   | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200; failed assertion(s): Response is an array/list                                                   | Executed   | Postman request: REST-005: List open restaurants                                   | **FAIL**      |
| REST-006   | Filter restaurants by partial name, case-insensitive    | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: REST-006: Filter restaurants by partial name, case-insensitive    | **PASS**      |
| REST-007   | Sort restaurants descending by name                     | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: REST-007: Sort restaurants descending by name                     | **PASS**      |
| REST-008   | Exact boolean filter for open restaurants               | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: REST-008: Exact boolean filter for open restaurants               | **PASS**      |
| REST-009   | Reject invalid sort direction                           | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: REST-009: Reject invalid sort direction                           | **PASS**      |
| REST-010   | Reject filter_field without filter_value                | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: REST-010: Reject filter_field without filter_value                | **PASS**      |
| REST-011   | Reject filter_value without filter_field                | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: REST-011: Reject filter_value without filter_field                | **PASS**      |
| REST-012   | Reject unsupported restaurant filter field              | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: REST-012: Reject unsupported restaurant filter field              | **PASS**      |
| REST-013   | Get restaurant by ID                                    | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: REST-013: Get restaurant by ID                                    | **PASS**      |
| REST-014   | Get nonexistent restaurant ID                           | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 404     | HTTP 404                                                                                                   | Executed   | Postman request: REST-014: Get nonexistent restaurant ID                           | **PASS**      |
| REST-016   | Non-owner cannot update restaurant                      | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 403     | HTTP 200; failed assertion(s): \[REST-016\] Non-owner cannot update restaurant: expected HTTP 403          | Executed   | Postman request: REST-016: Non-owner cannot update restaurant                      | **FAIL**      |
| REST-017   | Owner closes restaurant                                 | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200; failed assertion(s): Restaurant is closed                                                        | Executed   | Postman request: REST-017: Owner closes restaurant                                 | **FAIL**      |
| REST-018   | Closed restaurant excluded from public list             | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: REST-018: Closed restaurant excluded from public list             | **PASS**      |
| REST-019   | Closed restaurant still retrievable by ID               | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: REST-019: Closed restaurant still retrievable by ID               | **PASS**      |
| REST-020   | Owner reopens and updates restaurant                    | Authenticated users/tokens created; restaurant accounts available.                                             | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: REST-020: Owner reopens and updates restaurant                    | **PASS**      |
| MENU-001   | Customer cannot add menu item                           | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: MENU-001: Customer cannot add menu item                           | **PASS**      |
| MENU-002   | Different restaurant owner cannot add menu item         | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: MENU-002: Different restaurant owner cannot add menu item         | **PASS**      |
| MENU-003   | Menu item requires name and price                       | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: MENU-003: Menu item requires name and price                       | **PASS**      |
| MENU-004   | Reject negative menu price                              | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: MENU-004: Reject negative menu price                              | **PASS**      |
| MENU-005   | Reject negative stock quantity                          | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: MENU-005: Reject negative stock quantity                          | **PASS**      |
| MENU-006   | Create available menu item                              | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: MENU-006: Create available menu item                              | **PASS**      |
| MENU-007   | Create zero-stock unavailable menu item                 | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: MENU-007: Create zero-stock unavailable menu item                 | **PASS**      |
| MENU-008   | Create menu item at restaurant 2                        | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 201     | HTTP 201                                                                                                   | Executed   | Postman request: MENU-008: Create menu item at restaurant 2                        | **PASS**      |
| MENU-009   | List all menu items including unavailable               | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: MENU-009: List all menu items including unavailable               | **PASS**      |
| MENU-010   | Filter menu by availability                             | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: MENU-010: Filter menu by availability                             | **PASS**      |
| MENU-011   | Filter menu by exact numeric price                      | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: MENU-011: Filter menu by exact numeric price                      | **PASS**      |
| MENU-012   | Filter menu by exact text case-insensitively            | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: MENU-012: Filter menu by exact text case-insensitively            | **PASS**      |
| MENU-013   | Reject unsupported menu filter field                    | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: MENU-013: Reject unsupported menu filter field                    | **PASS**      |
| MENU-014   | Menu list requires token                                | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 401     | HTTP 401                                                                                                   | Executed   | Postman request: MENU-014: Menu list requires token                                | **PASS**      |
| MENU-015   | Menu list for nonexistent restaurant                    | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 404     | HTTP 404                                                                                                   | Executed   | Postman request: MENU-015: Menu list for nonexistent restaurant                    | **PASS**      |
| MENU-016   | Non-owner cannot update menu item                       | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: MENU-016: Non-owner cannot update menu item                       | **PASS**      |
| MENU-017   | Reject negative price on update                         | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 400     | HTTP 500; failed assertion(s): \[MENU-017\] Reject negative price on update: expected HTTP 400             | Executed   | Postman request: MENU-017: Reject negative price on update                         | **FAIL**      |
| MENU-018   | Set stock back to zero and verify automatic unavailable | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 200     | HTTP 200; failed assertion(s): Zero stock automatically disables item                                      | Executed   | Postman request: MENU-018: Set stock back to zero and verify automatic unavailable | **FAIL**      |
| MENU-019   | Update nonexistent menu item                            | Restaurants created; corresponding owner and customer tokens available.                                        | HTTP 404     | HTTP 404                                                                                                   | Executed   | Postman request: MENU-019: Update nonexistent menu item                            | **PASS**      |
| ORD-001    | Place order without token                               | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 401     | HTTP 401                                                                                                   | Executed   | Postman request: ORD-001: Place order without token                                | **PASS**      |
| ORD-002    | Restaurant role cannot place order                      | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 403     | HTTP 201; failed assertion(s): \[ORD-002\] Restaurant role cannot place order: expected HTTP 403           | Executed   | Postman request: ORD-002: Restaurant role cannot place order                       | **FAIL**      |
| ORD-003    | Order requires restaurantId and items                   | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: ORD-003: Order requires restaurantId and items                    | **PASS**      |
| ORD-004    | Order for nonexistent restaurant                        | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 404     | HTTP 404                                                                                                   | Executed   | Postman request: ORD-004: Order for nonexistent restaurant                         | **PASS**      |
| ORD-005A   | Close restaurant for closed-order test                  | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: ORD-005A: Close restaurant for closed-order test                  | **PASS**      |
| ORD-005    | Cannot order from closed restaurant                     | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: ORD-005: Cannot order from closed restaurant                      | **PASS**      |
| ORD-005B   | Reopen restaurant after closed-order test               | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: ORD-005B: Reopen restaurant after closed-order test               | **PASS**      |
| ORD-006    | Reject item belonging to another restaurant             | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: ORD-006: Reject item belonging to another restaurant              | **PASS**      |
| ORD-007    | Reject nonexistent menu item                            | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: ORD-007: Reject nonexistent menu item                             | **PASS**      |
| ORD-008    | Reject unavailable menu item                            | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: ORD-008: Reject unavailable menu item                             | **PASS**      |
| ORD-009    | Reject quantity greater than stock                      | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: ORD-009: Reject quantity greater than stock                       | **PASS**      |
| ORD-010    | Create primary lifecycle order                          | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 201     | HTTP 201; failed assertion(s): Order totals and initial state are correct                                  | Executed   | Postman request: ORD-010: Create primary lifecycle order                           | **FAIL**      |
| ORD-011    | Verify stock decremented after order                    | Users, restaurants, and menu items created; required tokens available.                                         | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: ORD-011: Verify stock decremented after order                     | **PASS**      |
| VIEW-001   | Customer lists own orders                               | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-001: Customer lists own orders                               | **PASS**      |
| VIEW-002   | Restaurant lists orders at owned restaurants            | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-002: Restaurant lists orders at owned restaurants            | **PASS**      |
| VIEW-003   | Unassigned rider list is empty                          | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-003: Unassigned rider list is empty                          | **PASS**      |
| VIEW-004   | Filter orders by exact status                           | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-004: Filter orders by exact status                           | **PASS**      |
| VIEW-005   | Filter orders by exact total                            | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-005: Filter orders by exact total                            | **PASS**      |
| VIEW-006   | Reject orders filter missing value                      | Primary order(s) created and required role tokens available.                                                   | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: VIEW-006: Reject orders filter missing value                      | **PASS**      |
| VIEW-007   | Customer views full order                               | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-007: Customer views full order                               | **PASS**      |
| VIEW-008   | Restaurant owner views full order                       | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-008: Restaurant owner views full order                       | **PASS**      |
| VIEW-009   | Unrelated customer cannot view order                    | Primary order(s) created and required role tokens available.                                                   | HTTP 403     | HTTP 200; failed assertion(s): \[VIEW-009\] Unrelated customer cannot view order: expected HTTP 403        | Executed   | Postman request: VIEW-009: Unrelated customer cannot view order                    | **FAIL**      |
| VIEW-010   | Unrelated restaurant cannot view order                  | Primary order(s) created and required role tokens available.                                                   | HTTP 403     | HTTP 200; failed assertion(s): \[VIEW-010\] Unrelated restaurant cannot view order: expected HTTP 403      | Executed   | Postman request: VIEW-010: Unrelated restaurant cannot view order                  | **FAIL**      |
| VIEW-011   | Unassigned rider cannot view order                      | Primary order(s) created and required role tokens available.                                                   | HTTP 403     | HTTP 200; failed assertion(s): \[VIEW-011\] Unassigned rider cannot view order: expected HTTP 403          | Executed   | Postman request: VIEW-011: Unassigned rider cannot view order                      | **FAIL**      |
| VIEW-012   | Get nonexistent order                                   | Primary order(s) created and required role tokens available.                                                   | HTTP 404     | HTTP 404                                                                                                   | Executed   | Postman request: VIEW-012: Get nonexistent order                                   | **PASS**      |
| VIEW-014   | Customer views initial timeline                         | Primary order(s) created and required role tokens available.                                                   | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: VIEW-014: Customer views initial timeline                         | **PASS**      |
| VIEW-015   | Unrelated user cannot view timeline                     | Primary order(s) created and required role tokens available.                                                   | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: VIEW-015: Unrelated user cannot view timeline                     | **PASS**      |
| VIEW-016   | Reject invalid timeline sort                            | Primary order(s) created and required role tokens available.                                                   | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: VIEW-016: Reject invalid timeline sort                            | **PASS**      |
| LIFE-001   | Wrong restaurant owner cannot accept                    | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: LIFE-001: Wrong restaurant owner cannot accept                    | **PASS**      |
| LIFE-002   | Cannot skip placed directly to preparing                | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 200; failed assertion(s): \[LIFE-002\] Cannot skip placed directly to preparing: expected HTTP 400    | Executed   | Postman request: LIFE-002: Cannot skip placed directly to preparing                | **FAIL**      |
| LIFE-003   | Missing status is rejected                              | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: LIFE-003: Missing status is rejected                              | **PASS**      |
| LIFE-004   | Restaurant accepts placed order                         | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-004: Restaurant accepts placed order                         | **PASS**      |
| LIFE-005   | Cannot repeat accepted transition                       | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 200; failed assertion(s): \[LIFE-005\] Cannot repeat accepted transition: expected HTTP 400           | Executed   | Postman request: LIFE-005: Cannot repeat accepted transition                       | **FAIL**      |
| LIFE-006   | Non-owner customer cannot cancel accepted order         | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: LIFE-006: Non-owner customer cannot cancel accepted order         | **PASS**      |
| LIFE-007   | Wrong restaurant owner cannot start preparation         | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: LIFE-007: Wrong restaurant owner cannot start preparation         | **PASS**      |
| LIFE-008   | Restaurant starts preparation                           | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-008: Restaurant starts preparation                           | **PASS**      |
| LIFE-009   | Customer cannot cancel once preparing                   | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 200; failed assertion(s): \[LIFE-009\] Customer cannot cancel once preparing: expected HTTP 400       | Executed   | Postman request: LIFE-009: Customer cannot cancel once preparing                   | **FAIL**      |
| LIFE-010   | Cannot skip preparing directly to picked_up             | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 200; failed assertion(s): \[LIFE-010\] Cannot skip preparing directly to picked_up: expected HTTP 400 | Executed   | Postman request: LIFE-010: Cannot skip preparing directly to picked_up             | **FAIL**      |
| LIFE-011   | Restaurant marks order ready for pickup                 | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-011: Restaurant marks order ready for pickup                 | **PASS**      |
| LIFE-012   | Customer cannot list available delivery orders          | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: LIFE-012: Customer cannot list available delivery orders          | **PASS**      |
| LIFE-013   | Rider lists available orders                            | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-013: Rider lists available orders                            | **PASS**      |
| LIFE-014   | Available orders support exact status filter            | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-014: Available orders support exact status filter            | **PASS**      |
| LIFE-015   | Available orders reject invalid filter field            | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: LIFE-015: Available orders reject invalid filter field            | **PASS**      |
| LIFE-016   | Rate order before delivery is rejected                  | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: LIFE-016: Rate order before delivery is rejected                  | **PASS**      |
| LIFE-017   | Customer cannot claim order                             | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: LIFE-017: Customer cannot claim order                             | **PASS**      |
| LIFE-018   | Rider 1 claims ready order                              | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-018: Rider 1 claims ready order                              | **PASS**      |
| LIFE-019   | Second rider cannot claim assigned order                | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 409     | HTTP 200; failed assertion(s): \[LIFE-019\] Second rider cannot claim assigned order: expected HTTP 409    | Executed   | Postman request: LIFE-019: Second rider cannot claim assigned order                | **FAIL**      |
| LIFE-020   | Assigned rider can view full order                      | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-020: Assigned rider can view full order                      | **PASS**      |
| LIFE-021   | Unassigned rider cannot view claimed order              | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 200; failed assertion(s): \[LIFE-021\] Unassigned rider cannot view claimed order: expected HTTP 403  | Executed   | Postman request: LIFE-021: Unassigned rider cannot view claimed order              | **FAIL**      |
| LIFE-022   | Claimed order disappears from available list            | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-022: Claimed order disappears from available list            | **PASS**      |
| LIFE-023   | Wrong rider cannot mark picked up                       | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 200; failed assertion(s): \[LIFE-023\] Wrong rider cannot mark picked up: expected HTTP 403           | Executed   | Postman request: LIFE-023: Wrong rider cannot mark picked up                       | **FAIL**      |
| LIFE-024   | Restaurant owner cannot mark picked up                  | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 200; failed assertion(s): \[LIFE-024\] Restaurant owner cannot mark picked up: expected HTTP 403      | Executed   | Postman request: LIFE-024: Restaurant owner cannot mark picked up                  | **FAIL**      |
| LIFE-025   | Assigned rider marks picked up                          | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-025: Assigned rider marks picked up                          | **PASS**      |
| LIFE-026   | Wrong rider cannot mark delivered                       | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 403     | HTTP 200; failed assertion(s): \[LIFE-026\] Wrong rider cannot mark delivered: expected HTTP 403           | Executed   | Postman request: LIFE-026: Wrong rider cannot mark delivered                       | **FAIL**      |
| LIFE-027   | Assigned rider marks delivered                          | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-027: Assigned rider marks delivered                          | **PASS**      |
| LIFE-028   | Delivered order cannot transition again                 | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 400     | HTTP 200; failed assertion(s): \[LIFE-028\] Delivered order cannot transition again: expected HTTP 400     | Executed   | Postman request: LIFE-028: Delivered order cannot transition again                 | **FAIL**      |
| LIFE-029   | Full timeline contains lifecycle and claim events       | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-029: Full timeline contains lifecycle and claim events       | **PASS**      |
| LIFE-030   | Timeline filter returns claimed event                   | Primary order created; lifecycle requests executed in collection order.                                        | HTTP 200     | HTTP 200                                                                                                   | Executed   | Postman request: LIFE-030: Timeline filter returns claimed event                   | **PASS**      |
| RATE-001   | Restaurant owner cannot rate customer order             | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: RATE-001: Restaurant owner cannot rate customer order             | **PASS**      |
| RATE-002   | Unrelated customer cannot rate order                    | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 403     | HTTP 403                                                                                                   | Executed   | Postman request: RATE-002: Unrelated customer cannot rate order                    | **PASS**      |
| RATE-003   | Reject invalid rating target                            | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: RATE-003: Reject invalid rating target                            | **PASS**      |
| RATE-004   | Reject rating score below range                         | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 400     | HTTP 500; failed assertion(s): \[RATE-004\] Reject rating score below range: expected HTTP 400             | Executed   | Postman request: RATE-004: Reject rating score below range                         | **FAIL**      |
| RATE-005   | Reject rating score above range                         | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 400     | HTTP 500; failed assertion(s): \[RATE-005\] Reject rating score above range: expected HTTP 400             | Executed   | Postman request: RATE-005: Reject rating score above range                         | **FAIL**      |
| RATE-006   | Reject non-integer rating score                         | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: RATE-006: Reject non-integer rating score                         | **PASS**      |
| RATE-007   | Reject missing score                                    | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 400     | HTTP 400                                                                                                   | Executed   | Postman request: RATE-007: Reject missing score                                    | **PASS**      |
| RATE-008   | Customer rates restaurant                               | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 201     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-008: Customer rates restaurant                               | **FAIL**\*    |
| RATE-009   | Duplicate restaurant rating is rejected                 | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 409     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-009: Duplicate restaurant rating is rejected                 | **FAIL**\*    |
| RATE-010   | Customer rates assigned rider                           | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 201     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-010: Customer rates assigned rider                           | **FAIL**\*    |
| RATE-011   | Duplicate rider rating is rejected                      | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 409     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-011: Duplicate rider rating is rejected                      | **FAIL**\*    |
| RATE-012   | List restaurant ratings                                 | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-012: List restaurant ratings                                 | **FAIL**\*    |
| RATE-013   | Filter restaurant ratings by score                      | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-013: Filter restaurant ratings by score                      | **FAIL**\*    |
| RATE-014   | Reject unsupported ratings filter                       | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 400     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-014: Reject unsupported ratings filter                       | **FAIL**\*    |
| RATE-015   | Ratings list for nonexistent restaurant                 | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 404     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-015: Ratings list for nonexistent restaurant                 | **FAIL**\*    |
| RATE-016   | Ratings list requires token                             | Primary order progressed through the required lifecycle; rating actors/tokens available.                       | HTTP 401     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: RATE-016: Ratings list requires token                             | **FAIL**\*    |
| CANCEL-001 | Create order B for free cancellation                    | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 201     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-001: Create order B for free cancellation                  | **FAIL**\*    |
| CANCEL-002 | Restaurant cannot cancel customer order                 | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 403     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-002: Restaurant cannot cancel customer order               | **FAIL**\*    |
| CANCEL-003 | Unrelated customer cannot cancel order                  | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 403     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-003: Unrelated customer cannot cancel order                | **FAIL**\*    |
| CANCEL-004 | Customer cancels placed order for free                  | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-004: Customer cancels placed order for free                | **FAIL**\*    |
| CANCEL-005 | Cannot cancel already-cancelled order                   | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 400     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-005: Cannot cancel already-cancelled order                 | **FAIL**\*    |
| CANCEL-006 | Cancelled order timeline ends in cancelled              | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-006: Cancelled order timeline ends in cancelled            | **FAIL**\*    |
| CANCEL-007 | Cancelled order cannot re-enter lifecycle               | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 400     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-007: Cancelled order cannot re-enter lifecycle             | **FAIL**\*    |
| CANCEL-008 | Create order C for accepted cancellation                | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 201     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-008: Create order C for accepted cancellation              | **FAIL**\*    |
| CANCEL-009 | Restaurant accepts order C                              | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-009: Restaurant accepts order C                            | **FAIL**\*    |
| CANCEL-010 | Customer cancels accepted order with penalty            | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-010: Customer cancels accepted order with penalty          | **FAIL**\*    |
| CANCEL-011 | Accepted-cancelled order timeline ends in cancelled     | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-011: Accepted-cancelled order timeline ends in cancelled   | **FAIL**\*    |
| CANCEL-012 | Create order D for cancellation without reason          | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 201     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-012: Create order D for cancellation without reason        | **FAIL**\*    |
| CANCEL-013 | Cancel placed order without reason field                | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 200     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-013: Cancel placed order without reason field              | **FAIL**\*    |
| CANCEL-014 | Create order E for premature claim test                 | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 201     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-014: Create order E for premature claim test               | **FAIL**\*    |
| CANCEL-015 | Rider cannot claim a placed order                       | Cancellation test data should be created and required role tokens available; later execution was rate-limited. | HTTP 400     | HTTP 429 Too Many Requests; intended functional outcome could not be evaluated                             | Executed   | Postman request: CANCEL-015: Rider cannot claim a placed order                     | **FAIL**\*    |

## 3\. Defect Reports

### Defect DEF-001: AUTH-009

- **Title:** Short passwords are accepted during registration
- **Severity:** Medium
- **Steps to Reproduce:**
  1. Run AUTH-009: Register with short password.
  2. Submit a five-character password (12345).
  3. Observe the response.
- **Expected:** HTTP 400 because the documentation states that a password must be at least six characters long.
- **Actual:** HTTP 201 Created; the account was accepted.
- **Test Case No.:** AUTH-009
- **Request ID:** 1d277ba6-8600-4a00-b866-e2234d5939c8
- **Related/Duplicate Defects:** None identified.

### Defect DEF-002: REST-016

- **Title:** Non-owner can update another restaurant
- **Severity:** High
- **Steps to Reproduce:**
  1. Authenticate as restaurant owner 2.
  2. Send PATCH /restaurants/:id for restaurant 1.
  3. Change the restaurant name.
- **Expected:** HTTP 403; only the restaurant owner may update the restaurant.
- **Actual:** HTTP 200, and the saved response shows the restaurant name changed to Hijacked Name.
- **Test Case No.:** REST-016
- **Request ID:** bb4f1e9d-8677-4a21-bd2d-17ce33c69901
- **Related/Duplicate Defects:** None identified.

### Defect DEF-003: MENU-017

- **Title:** Negative menu price update returns a server error instead of a validation error
- **Severity:** Medium
- **Steps to Reproduce:**
  1. Authenticate as the restaurant owner.
  2. Send PATCH /menu-items/:id with a negative price.
  3. Observe the response.
- **Expected:** HTTP 400 with the documented non-negative validation error.
- **Actual:** HTTP 500 Internal Server Error.
- **Test Case No.:** MENU-017
- **Request ID:** 3077ad62-5e17-4482-b67e-18660d99cd1f
- **Related/Duplicate Defects:** None identified.

### Defect DEF-004: MENU-018

- **Title:** Zero stock does not automatically disable a menu item
- **Severity:** Medium
- **Steps to Reproduce:**
  1. Update a menu item with stockQuantity: 0.
  2. Inspect the returned menu item.
  3. Check isAvailable.
- **Expected:** HTTP 200 with isAvailable: false when stock reaches zero.
- **Actual:** HTTP 200, but isAvailable remained true.
- **Test Case No.:** MENU-018
- **Request ID:** 2b9559b8-a8cf-4f90-a0ab-434974ad17c4
- **Related/Duplicate Defects:** None identified.

### Defect DEF-005: ORD-002

- **Title:** Restaurant-role order-placement authorization assertion failed
- **Severity:** High
- **Steps to Reproduce:**
  1. Authenticate as a restaurant account.
  2. Send POST /orders with the test order payload.
  3. Observe the status.
- **Expected:** HTTP 403 because only customers can place orders.
- **Actual:** The supplied test-run records HTTP 201, causing the authorization assertion to fail. However, the saved response inside the collection is from a different response instance and shows HTTP 400 with One or more item ids are invalid. This should therefore be re-tested to confirm reproducibility.
- **Test Case No.:** ORD-002
- **Request ID:** ae13ce0d-1e1c-4e18-8806-63bb6a51cc16
- **Related/Duplicate Defects:** None identified.

### Defect DEF-006: Order visibility authorization

- **Title:** Unrelated users can view an order
- **Severity:** High
- **Steps to Reproduce:**
  1. Create the primary lifecycle order.
  2. Request that order as an unrelated customer, restaurant, and rider.
  3. Observe the responses.
- **Expected:** HTTP 403 for each unrelated actor.
- **Actual:** VIEW-009, VIEW-010, and VIEW-011 each returned HTTP 200.
- **Test Case No.:** VIEW-009, VIEW-010, VIEW-011
- **Request IDs:**   
**VIEW-009:** 527111d9-5990-454f-b255-f39647810e17  
**VIEW-010:** d94608bf-2e36-4ad2-a8d9-c616df446dab  
**VIEW-011:** 743e802e-96bb-4e5b-9d48-0aaac0fd9378
- **Related/Duplicate Defects:** Grouped as one authorization defect.

### Defect DEF-007: Order lifecycle transition enforcement

- **Title:** Invalid order lifecycle transitions are accepted
- **Severity:** High
- **Steps to Reproduce:**
  1. Attempt placed → preparing.
  2. Repeat an already completed transition.
  3. Attempt cancellation after preparation.
  4. Attempt invalid skips toward pickup/delivery and another transition after delivery.
- **Expected:** HTTP 400 for invalid lifecycle transitions.
- **Actual:** LIFE-002, LIFE-005, LIFE-009, LIFE-010, and LIFE-028 returned HTTP 200.
- **Test Case No.:** LIFE-002, LIFE-005, LIFE-009, LIFE-010, LIFE-028
- **Request IDs:**   
**LIFE-002:** be1a7625-06ba-443c-83fb-7ad5a15e7d5d  
**LIFE-005:** 7398332c-3bc7-4c6b-b2fa-26a5017c082d   
**LIFE-009:** 7253793e-5031-4065-ac18-05b40b6742df  
**LIFE-010:** da74f0e3-7cb2-4f6d-91f2-09656efbbd91  
**LIFE-028:** fc557f9a-53d0-4200-ae1e-7676b168f512
- **Related/Duplicate Defects:** Grouped because all concern lifecycle boundary enforcement.

### Defect DEF-008: Rider ownership and action authorization

- **Title:** Rider/actor ownership restrictions are not enforced
- **Severity:** High
- **Steps to Reproduce:**
  1. Use a second rider or non-rider actor against a claimed/assigned order.
  2. Attempt to claim, view, pick up, or deliver the order.
  3. Observe the responses.
- **Expected:** HTTP 403 for unauthorized actors and HTTP 409 when a second rider attempts to claim an already assigned order.
- **Actual:** LIFE-019, LIFE-021, LIFE-023, LIFE-024, and LIFE-026 returned HTTP 200.
- **Test Case No.:** LIFE-019, LIFE-021, LIFE-023, LIFE-024, LIFE-026
- **Request IDs:**   
**LIFE-019:** a0ad8478-8d7c-4861-aecd-e9a592f06e4a  
**LIFE-021:** 0a4e7f50-46ba-48ab-8005-52572a33ee30  
**LIFE-023:** d3493774-e65b-42f1-94d5-4abc917d365d  
**LIFE-024:** d5590f0e-a4ce-4e7d-9e4e-30d86a46d38d  
**LIFE-026:** 3888c6dc-0468-448f-9621-4a0f0d4c71d0
- **Related/Duplicate Defects:** Grouped because all concern actor/ownership authorization.

### Defect DEF-009: RATE-004 / RATE-005

- **Title:** Out-of-range rating scores produce server errors
- **Severity:** Medium
- **Steps to Reproduce:**
  1. Use a delivered order.
  2. Submit a rating with a score below 1.
  3. Submit another rating with a score above 5.
  4. Observe the responses.
- **Expected:** HTTP 400 with the documented score-range validation error.
- **Actual:** RATE-004 and RATE-005 returned HTTP 500.
- **Test Case No.:** RATE-004, RATE-005
- **Request IDs:**   
**RATE-004:** e497670c-f747-452d-a4d4-6ce3c2d81f7b  
**RATE-005:** 1da5315f-8d32-4a3d-a129-637f0d36eeeb
- **Related/Duplicate Defects:** Grouped as one rating-validation defect.


## 4\. Individual Reflections

### **Marjia Islam:**

I performed the system-level testing of the Panda-Lite REST API using Postman and organized the execution around the documented API behavior. I tested setup and database state, user registration and login, restaurant and menu management, order creation and visibility, lifecycle transitions, rider permissions, ratings, and cancellation scenarios. The testing identified important defects in authorization, lifecycle enforcement, input validation, and automatic stock availability. 