# Panda-Lite API System Testing

**Software Testing and Quality Assurance Project**

A Postman collection and system-testing report for the Panda-Lite REST API. The collection covers authentication, restaurant and menu operations, orders, lifecycle transitions, rider authorization, ratings, and validation scenarios.

## Repository contents

- `postman/Panda-Lite-System-Testing.postman_collection.json` ? the Postman collection.
- `report/SystemTestingReport.md` ? the test plan, test cases, reported defects, and reflection. The student ID has been removed from the public copy.

## Safety and authorization

This collection is intended **only for an authorized, disposable local Panda-Lite test instance**. It includes `POST /_internal/reset-database`, which can erase data, and requests that exercise authorization boundaries. Never point it at production, shared environments, or systems you do not own or have explicit permission to test.

A collection-level pre-request guard skips requests unless the effective `BASE_URL` is an HTTP(S) loopback address (`localhost`, `127.0.0.1`, or `::1`). Keep the API on a disposable local database even when using loopback. Do not remove the guard for public use.

## Run the collection

1. Start your authorized Panda-Lite API locally with a disposable test database.
2. Import `postman/Panda-Lite-System-Testing.postman_collection.json` into Postman.
3. Set `BASE_URL` in a local Postman environment to your local API URL, for example `http://localhost:3000` (use the port your local API actually listens on).
4. Set `STQA-Key` in that local environment or Postman Vault if your local test instance requires it. Do not save real keys in the collection or commit environment files.
5. Run the collection sequentially in Postman. The first system setup request resets the test database, so use only a disposable instance.

The collection has blank target and credential values by default. The report records findings from a particular test run; re-test them against the current local application before treating them as current behavior.
