<div align="center">
  <h1>Postman Tutorial</h1>
  <small>
    <strong>Author:</strong> Nguyễn Tấn Phát
  </small> <br />
  <sub>August 16, 2026</sub>
</div>

## Table of Contents

1. [API Testing — Foundational Theory](#1-api-testing--foundational-theory)
   - [1.1 What is an API? What is API Testing?](#11-what-is-an-api-what-is-api-testing)
   - [1.2 Why is API Testing Important?](#12-why-is-api-testing-important)
   - [1.3 Types of API Testing](#13-types-of-api-testing)
   - [1.4 What to Test in an API?](#14-what-to-test-in-an-api)
   - [1.5 API Contract & OpenAPI Specification](#15-api-contract--openapi-specification)
   - [1.6 Shift-Left Testing in API](#16-shift-left-testing-in-api)
   - [1.7 Real-World API Testing Workflow](#17-real-world-api-testing-workflow)
2. [Postman — Tool Overview](#2-postman--tool-overview)
   - [2.1 What is Postman?](#21-what-is-postman)
   - [2.2 Pros and Cons](#22-pros-and-cons)
   - [2.3 Postman vs Other Tools](#23-postman-vs-other-tools)
   - [2.4 Key Features of Postman](#24-key-features-of-postman)
3. [Newman CLI](#3-newman-cli)
   - [3.1 What is Newman?](#31-what-is-newman)
   - [3.2 Installation and Basic Syntax](#32-installation-and-basic-syntax)
   - [3.3 Reporters in Newman](#33-reporters-in-newman)
4. [Collection Structure & JSON Syntax](#4-collection-structure--json-syntax)
   - [4.1 Collection Format v2.1.0](#41-collection-format-v210)
   - [4.2 Full JSON Structure of a Collection](#42-full-json-structure-of-a-collection)
   - [4.3 Environment File JSON](#43-environment-file-json)
   - [4.4 Variable Scopes](#44-variable-scopes)
5. [Scripts in Postman](#5-scripts-in-postman)
   - [5.1 Pre-request Scripts](#51-pre-request-scripts)
   - [5.2 Test Scripts (Post-response)](#52-test-scripts-post-response)
   - [5.3 Script Execution Order](#53-script-execution-order)
   - [5.4 Common pm API Reference](#54-common-pm-api-reference)
   - [5.5 Setup & Teardown Pattern](#55-setup--teardown-pattern)
6. [Data-Driven Testing](#6-data-driven-testing)
   - [6.1 Concept and How It Works](#61-concept-and-how-it-works)
   - [6.2 Using CSV Files](#62-using-csv-files)
   - [6.3 Using JSON Files](#63-using-json-files)
   - [6.4 Accessing Iteration Data in Scripts](#64-accessing-iteration-data-in-scripts)
   - [6.5 Data-Driven Testing with Newman CLI](#65-data-driven-testing-with-newman-cli)
7. [Mock Server in Postman](#7-mock-server-in-postman)
   - [7.1 When to Use a Mock Server?](#71-when-to-use-a-mock-server)
   - [7.2 Creating a Mock Server from a Collection](#72-creating-a-mock-server-from-a-collection)
   - [7.3 Saved Examples & Response Matching](#73-saved-examples--response-matching)
8. [Production-Ready Collection Structure](#8-production-ready-collection-structure)
   - [8.1 Organization Principles](#81-organization-principles)
   - [8.2 Standard Workspace & Collection Structure](#82-standard-workspace--collection-structure)
   - [8.3 Naming Convention](#83-naming-convention)
9. [CI/CD Integration with GitHub Actions](#9-cicd-integration-with-github-actions)
   - [9.1 CI/CD Flow Overview](#91-cicd-flow-overview)
   - [9.2 Setting Up GitHub Actions with Newman](#92-setting-up-github-actions-with-newman)
   - [9.3 Using the Postman CLI (Alternative to Newman)](#93-using-the-postman-cli-alternative-to-newman)
   - [9.4 Managing Secrets and Environments in CI](#94-managing-secrets-and-environments-in-ci)
10. [Consolidated Best Practices](#10-consolidated-best-practices)
11. [References](#11-references)

## 1. API Testing — Foundational Theory

### 1.1 What is an API? What is API Testing?

**API (Application Programming Interface)** is a set of rules and protocols that allow software applications to communicate with each other. An API acts as an intermediary — it receives requests from clients, processes business logic, and returns responses.

**API Testing** is the process of verifying that an API behaves correctly according to its defined specification — covering data correctness, performance, security, and reliability. Unlike UI Testing (which tests the graphical interface), API Testing directly targets the **business logic layer** without going through a graphical interface.

> Per IBM: _"API tests check for data and formatting accuracy, error handling, authentication and authorization compliance, compatibility issues, load issues and more."_

Common API architectures that require testing:

- **REST (Representational State Transfer):** Uses HTTP methods (GET, POST, PUT, PATCH, DELETE), JSON/XML format.
- **SOAP (Simple Object Access Protocol):** Uses XML; common in enterprise and banking systems.
- **GraphQL:** Clients define the exact data structure they want to receive.
- **gRPC:** Uses Protocol Buffers; commonly used in internal microservices.

### 1.2 Why is API Testing Important?

- **Earlier defect detection:** API Testing finds defects at the business logic layer before UI is built, significantly reducing fix costs (IBM Systems Sciences Institute and NIST research shows defects caught at the commit stage cost 5–15× less to fix than those caught in QA).
- **Faster than UI Testing:** API tests run independently of browser/UI rendering, making execution much faster.
- **Wide coverage:** According to BrowserStack, the API testing market reached nearly $1.75 billion in 2025 and is projected to grow to $4.72 billion by 2030 at a 21.9% CAGR, reflecting its growing critical importance.
- **Suited for microservices:** In microservice architectures, every service communicates via APIs — testing at this layer is essential.
- **CI/CD friendly:** API tests are easy to automate and integrate into delivery pipelines.

### 1.3 Types of API Testing

#### Functional Testing

Verifies the API behaves correctly according to its specification. This is the most fundamental type and includes:

- **Unit Testing:** Tests individual endpoints in isolation with both valid and invalid inputs.
- **Component Testing:** Tests a complete HTTP transaction — including status code, response time, response body — and compares results against expected outcomes.
- **Scenario Testing (End-to-End):** Tests a chain of linked API calls forming a real workflow, e.g.: Login → Create Order → Confirm Payment → Check Order Status.

#### Integration Testing

Verifies that multiple modules, services, or external systems work correctly when combined. It ensures each component honors its communication contract with the others.

#### Contract Testing

Verifies that an API adheres to the contract defined between producer and consumer (typically an OpenAPI/Swagger spec). Contract testing does not validate business logic — it only checks whether the request/response structure matches the spec. This is especially important in microservices for detecting **schema drift** (when the implementation diverges from the spec over time).

#### Performance Testing

Evaluates API behavior under load:

- **Load Testing:** Simulates normal expected user volumes.
- **Stress Testing:** Pushes the system beyond its limits to observe behavior.
- **Soak Testing:** Runs moderate load for an extended period to detect memory leaks.

_Note: While Postman has a Collection Runner and Monitors, it is not a dedicated performance testing tool — use k6, JMeter, or Locust for that purpose._

#### Security Testing

Checks for security vulnerabilities, including:

- Authentication and Authorization (OAuth2, API Key, JWT).
- Input Validation — detecting SQL Injection, XSS.
- Data Exposure — ensuring sensitive data is not leaked.
- OWASP API Security Top 10 (Broken Object Level Authorization, Broken Authentication, etc.).

#### Regression Testing

Ensures that new changes to the codebase do not break existing functionality. Runs automatically in CI/CD pipelines after every commit or merge.

#### Negative Testing

Tests how the API handles unexpected or invalid scenarios: missing required fields, invalid input types, out-of-bound values, requests without authentication, malformed request bodies, etc.

#### Fuzz Testing

Sends random or malformed data to test how the API handles unexpected inputs — useful for discovering security vulnerabilities and unhandled edge cases.

### 1.4 What to Test in an API?

When testing an API endpoint, the following dimensions should be covered comprehensively:

#### Status Code Validation

Verify the HTTP status code returned is correct:

- `2xx`: Success (200 OK, 201 Created, 204 No Content)
- `4xx`: Client errors (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 422 Unprocessable Entity)
- `5xx`: Server errors (500 Internal Server Error, 503 Service Unavailable)

#### Response Body Validation

- JSON/XML structure matches the expected format.
- All required fields are present.
- Data types are correct (string, number, boolean, array).
- Returned values are accurate according to business logic.
- Schema validation against the OpenAPI spec.

#### Response Header Validation

- `Content-Type` is correct (e.g., `application/json`).
- Security headers present (`X-Content-Type-Options`, `Strict-Transport-Security`).
- CORS headers if applicable.
- Rate limit headers (`X-RateLimit-Remaining`).

#### Response Time / Performance

- API responds within an acceptable time threshold (SLA).
- Typical thresholds: under 200ms for simple queries, under 2000ms for complex operations.

#### Error Handling

- Error messages are meaningful and do not expose internal details (stack traces, database info).
- Error format is consistent.
- Edge cases are handled properly (empty list, null values, boundary values).

#### Authentication & Authorization

- Requests without a token are rejected (401).
- Expired tokens are rejected (401).
- Users without proper permissions are rejected (403).
- Valid tokens are accepted.

#### Data Integrity

- Data is correctly persisted after POST/PUT.
- Data is correctly removed after DELETE.
- Data is not corrupted when transformed across multiple steps.

#### Idempotency

- GET, PUT, DELETE must be idempotent (multiple identical calls return the same result).
- POST is typically non-idempotent (creates a new resource each time).

### 1.5 API Contract & OpenAPI Specification

An **API Contract** is a document that defines how an API behaves — including endpoints, HTTP methods, request parameters, request body schema, response schema, authentication, and error codes. The contract serves as an agreement between the API producer (backend team) and API consumers (frontend, mobile, third-party clients).

**OpenAPI Specification (OAS)** — formerly known as Swagger — is the industry standard for describing REST APIs. Postman natively supports importing OpenAPI specs to auto-generate collections.

```yaml
# Example OpenAPI 3.0 specification
openapi: 3.0.0
info:
  title: User API
  version: 1.0.0
paths:
  /users/{id}:
    get:
      summary: Get user by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        "200":
          description: Successful response
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/User"
        "404":
          description: User not found
components:
  schemas:
    User:
      type: object
      required: [id, name, email]
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
          format: email
```

In professional API testing, **the API contract is the starting point**. Every test case is derived from the contract:

- The spec defines `email` as required → there must be a test case that sends a request missing `email` and expects a `400`.
- The spec defines the response has an `id` field of type `integer` → the type must be validated in the test.

### 1.6 Shift-Left Testing in API

**Shift-Left Testing** is the philosophy of moving testing activities as early as possible in the software development lifecycle — instead of only testing at the end, test from the moment code is written (or even before, via contracts).

**In API Testing, Shift-Left means:**

1. **Contract-first:** Define the OpenAPI spec before implementation → both developers and QA have a shared document from day one.
2. **Test as you code:** Developers write API tests as they implement each endpoint.
3. **API tests as PR gates:** Every Pull Request must pass API tests before it can be merged.
4. **Early mock servers:** Frontend and mobile teams can develop in parallel with the backend using Postman Mock Server.
5. **Validate schema drift:** The CI pipeline immediately detects when the implementation diverges from the contract.

**Integrating Postman into Shift-Left:**

- Import the OpenAPI spec into Postman to auto-generate a collection.
- Validate response schemas using JSON schema validation in test scripts.
- Run the collection via Newman in GitHub Actions CI.
- Newman returns a non-zero exit code when tests fail → CI pipeline fails → merge is blocked.

### 1.7 Real-World API Testing Workflow

This is the practical workflow from analysis through to automation:

```
┌─────────────────────────────────────────────────────┐
│            REAL-WORLD API TESTING WORKFLOW          │
├─────────────────────────────────────────────────────┤
│                                                     │
│  1. ANALYZE THE API CONTRACT                        │
│     ├── Read the OpenAPI/Swagger spec               │
│     ├── Identify endpoints, methods, params         │
│     ├── List required vs optional fields            │
│     └── Document the authentication mechanism       │
│                                                     │
│  2. DESIGN TEST CASES                               │
│     ├── Happy path tests (valid input)              │
│     ├── Negative tests (invalid/missing input)      │
│     ├── Boundary value tests                        │
│     ├── Authentication/Authorization tests          │
│     └── Performance thresholds                      │
│                                                     │
│  3. SET UP POSTMAN WORKSPACE                        │
│     ├── Create a Workspace for the project          │
│     ├── Set up Environments (dev/staging/prod)      │
│     ├── Define Collection variables                 │
│     └── Configure authentication                    │
│                                                     │
│  4. CREATE COLLECTIONS & REQUESTS                   │
│     ├── Organize by feature/module                  │
│     ├── Add pre-request scripts (auth, setup)       │
│     ├── Write test assertions                       │
│     └── Create test data files (CSV/JSON)           │
│                                                     │
│  5. RUN & VALIDATE                                  │
│     ├── Manual run in the Collection Runner         │
│     ├── Data-driven run with iteration data         │
│     └── Review results and fix failures             │
│                                                     │
│  6. AUTOMATE VIA CI/CD                              │
│     ├── Export collection + environment JSON        │
│     ├── Commit to the Git repository                │
│     ├── Configure GitHub Actions workflow           │
│     └── Newman run → generate HTML report           │
│                                                     │
│  7. MAINTENANCE                                     │
│     ├── Update tests when the API changes           │
│     ├── Monitor via Postman Monitors                │
│     └── Periodically review & refactor collection   │
└─────────────────────────────────────────────────────┘
```

## 2. Postman — Tool Overview

### 2.1 What is Postman?

Postman is a comprehensive API platform that allows developers and testers to build, test, document, and share APIs. At its core, Postman provides a user-friendly GUI for constructing HTTP requests without writing client code — users simply enter the URL, select the HTTP method, and add the necessary headers and body data.

> Postman was originally created by Abhinav Asthana in 2012 as a Chrome extension. It later evolved into a standalone app and is now one of the most popular API tools in the world, used by tens of millions of developers.

**Postman's primary focus areas:**

- **API Exploration:** Send requests and inspect responses.
- **Test Automation:** Write test scripts in JavaScript, run automatically.
- **Collaboration:** Share collections within team workspaces.
- **Documentation:** Auto-generate API docs from collections.
- **Mocking:** Create mock servers to support parallel development.

### 2.2 Pros and Cons

#### Pros

| Advantage                  | Description                                                      |
| -------------------------- | ---------------------------------------------------------------- |
| **Friendly GUI**           | No need to write HTTP client code; suitable for all skill levels |
| **Collaboration**          | Shared workspaces for teams, version-controlled collections      |
| **Environment Management** | Easy switching between dev/staging/production                    |
| **Powerful scripting**     | Full JavaScript sandbox for pre-request and test scripts         |
| **Collection Runner**      | Run entire collections with one click; supports data files       |
| **Newman CLI**             | Headless runner for CI/CD automation                             |
| **Mock Server**            | Create mocks directly from collections without a backend         |
| **Documentation**          | Auto-generate polished docs from collections                     |
| **Import/Export**          | Supports OpenAPI, Swagger, RAML, cURL, HAR                       |
| **Useful free tier**       | Core features are sufficient for individuals and small teams     |

#### Cons

| Disadvantage                    | Description                                                                    |
| ------------------------------- | ------------------------------------------------------------------------------ |
| **Resource-heavy**              | The desktop app consumes more RAM than lighter alternatives                    |
| **Spec drift**                  | Collections can diverge from the OpenAPI spec over time without a sync process |
| **JavaScript only**             | Test scripts must be written in JavaScript; no other language support          |
| **Limited performance testing** | Not a dedicated tool for load/stress testing                                   |
| **Paid features**               | Some advanced features (more mocks, team plans) require a paid subscription    |
| **CI bolt-on**                  | Newman is a supplementary CLI, not natively integrated into Postman's core     |
| **Collection scale**            | Large collections with many requests become hard to maintain and organize      |

### 2.3 Postman vs Other Tools

| Criterion          | Postman        | Insomnia       | Bruno        | REST Assured | Karate DSL   |
| ------------------ | -------------- | -------------- | ------------ | ------------ | ------------ |
| **GUI**            | ✅ Excellent   | ✅ Good        | ✅ Good      | ❌ Code-only | ❌ Code-only |
| **Scripting**      | JavaScript     | JavaScript     | JavaScript   | Java         | Gherkin/DSL  |
| **CI/CD**          | Newman/CLI     | Inso CLI       | Limited      | Native Java  | Native JVM   |
| **Collaboration**  | ✅ Cloud       | ✅ Cloud       | ✅ Git-based | Via Git      | Via Git      |
| **Mock Server**    | ✅ Built-in    | ✅ Built-in    | ❌ No        | ❌ No        | ✅ Built-in  |
| **Open Source**    | ❌ Proprietary | ❌ Proprietary | ✅ Fully     | ✅           | ✅           |
| **OpenAPI Import** | ✅             | ✅             | ✅           | Manual       | Manual       |
| **Offline**        | ✅ Yes         | ✅ Yes         | ✅ Fully     | ✅           | ✅           |
| **Learning Curve** | Low            | Low            | Low          | High         | Medium       |

**When to choose Postman:**

- Team needs easy collaboration and sharing.
- Need a built-in mock server.
- Want a GUI to explore unfamiliar APIs.
- CI/CD integration via Newman.

**When to consider alternatives:**

- Fully offline with no cloud sync required → **Bruno**.
- Java team that wants tests close to the code → **REST Assured**.
- BDD-style API testing preferred → **Karate DSL**.

### 2.4 Key Features of Postman

#### Collections

A collection is a structured group of related requests. It is the core organizational unit in Postman — functioning as "executable documentation" that both describes the API and can run automated tests. A collection can contain multiple sub-folders and requests, each with their own pre-request scripts and test scripts.

> Per the Postman Blog: _"Collections serve as executable documentation that captures what an API does, as well as how it should be used in practice."_

#### Environments & Variables

Postman allows defining multiple environments (dev, staging, production), each containing its own set of variables (base URL, API key, credentials). Switching between environments requires just a single click, enabling the same collection to run across multiple environments without modification.

#### Pre-request Scripts & Test Scripts

Scripts written in JavaScript that execute before (pre-request) and after (test) each request. This is Postman's most powerful automation feature — enabling dynamic data generation, request chaining, response validation, and state management.

#### Collection Runner

Allows running an entire collection or a subset sequentially, supporting:

- Multiple iterations.
- Data files (CSV/JSON) for data-driven testing.
- Configurable delay between requests.
- Saving and comparing test run results.

#### Mock Server

Creates a simulated server from saved examples in a collection. The mock server returns predefined responses, allowing frontend/mobile teams to develop in parallel with the backend without waiting for the real API to be complete.

#### API Monitors

Schedules collection runs at regular intervals (hourly, daily) to monitor API health in staging or production environments. Automatically detects downtime and regressions.

#### Postman Flows (Visual Workflow Builder)

A drag-and-drop interface for building complex API workflows with conditional logic — no code required.

#### API Documentation

Automatically generates documentation from a collection. Can be published publicly or shared privately with a team.

## 3. Newman CLI

### 3.1 What is Newman?

Newman is the official **command-line collection runner** for Postman. It executes Postman collections exactly as the Postman app's Collection Runner would — sending every request, running every pre-request and test script, and reporting pass/fail — but does so **headlessly** (without a GUI), making it suitable for CI/CD pipelines.

> Per Postman Docs: _"Newman is a command-line tool for running Postman Collections. Newman is built with extensibility in mind, so you can incorporate it in your continuous integration (CI) pipelines and build systems."_

**How it works:**

1. Export the collection and environment as JSON from Postman.
2. Commit those JSON files to the Git repository.
3. In the CI pipeline, install Newman and run: `newman run collection.json`.
4. Newman returns **exit code 0** if all tests pass, **a non-zero exit code** if any test fails.
5. The CI pipeline fails based on the exit code → merges are blocked.

> Per DEV.to: _"Newman exits non-zero when any test fails, and a non-zero exit code fails the Actions step, which fails the workflow, which blocks the merge if you've made the check required."_

### 3.2 Installation and Basic Syntax

**Requirements:** Node.js (v18+)

```bash
# Install Newman globally
npm install -g newman

# Install the htmlextra reporter (beautiful HTML reports)
npm install -g newman-reporter-htmlextra

# Verify installation
newman --version
```

**Basic run commands:**

```bash
# Run a collection from a JSON file
newman run collection.json

# Run with an environment file
newman run collection.json -e environment.json

# Run with a data file (CSV)
newman run collection.json -d testdata.csv

# Run with a specific number of iterations
newman run collection.json -n 5

# Run with a delay between requests (ms)
newman run collection.json --delay-request 500

# Run and export a JSON report
newman run collection.json -r json --reporter-json-export results.json

# Run with an HTML report (requires newman-reporter-htmlextra)
newman run collection.json \
  -e environment.json \
  -d testdata.csv \
  -r htmlextra \
  --reporter-htmlextra-export testResults/report.html \
  --reporter-htmlextra-darkTheme

# Run a collection directly from the Postman API (no export needed)
newman run "https://api.postman.com/collections/{collection_id}?apikey={api_key}"
```

**Key Newman options:**

| Option                  | Description                                             |
| ----------------------- | ------------------------------------------------------- |
| `-e, --environment`     | Environment JSON file                                   |
| `-g, --globals`         | Global variables JSON file                              |
| `-d, --iteration-data`  | CSV/JSON file for data-driven testing                   |
| `-n, --iteration-count` | Number of iterations to run                             |
| `--delay-request`       | Delay between requests (milliseconds)                   |
| `--timeout-request`     | Request timeout (ms)                                    |
| `--bail`                | Stop the run on the first failure                       |
| `-r, --reporters`       | Specify reporters: cli, json, junit, htmlextra          |
| `--folder`              | Run only a specific folder within the collection        |
| `--env-var`             | Add or override an environment variable from the CLI    |
| `--global-var`          | Add or override a global variable from the CLI          |
| `--working-dir`         | Working directory for file references                   |
| `--suppress-exit-code`  | Return exit code 0 even if tests fail (not recommended) |

### 3.3 Reporters in Newman

Newman supports multiple reporter types:

```bash
# CLI reporter (default) - output to terminal
newman run collection.json -r cli

# JSON reporter - machine-readable output
newman run collection.json -r json --reporter-json-export output.json

# JUnit XML reporter - integrates with Jenkins, GitHub Actions
npm install -g newman-reporter-junit
newman run collection.json -r junit --reporter-junit-export results.xml

# HTMLExtra reporter - beautiful, detailed HTML report (recommended)
newman run collection.json -r htmlextra \
  --reporter-htmlextra-export report.html \
  --reporter-htmlextra-title "API Test Report" \
  --reporter-htmlextra-darkTheme

# Run multiple reporters simultaneously
newman run collection.json -r cli,json,htmlextra \
  --reporter-json-export results.json \
  --reporter-htmlextra-export report.html
```

**newman-reporter-htmlextra** is the most recommended reporter because:

- Produces a visually clear, easy-to-read HTML report.
- Supports dark/light theme.
- Displays full request/response details.
- Integrates well with GitHub Actions artifacts.

## 4. Collection Structure & JSON Syntax

### 4.1 Collection Format v2.1.0

Postman uses **Collection Format v2.1.0** — a standard JSON schema for defining collections. When you export a collection from Postman, the resulting JSON file conforms to this schema. The official schema is available at: `https://schema.getpostman.com/json/collection/v2.1.0/collection.json`.

Understanding this format allows you to:

- Version-control collections in Git.
- Programmatically generate or modify collections.
- Debug issues in CI/CD pipelines.

### 4.2 Full JSON Structure of a Collection

```json
{
  "info": {
    "_postman_id": "unique-uuid-here",
    "name": "User Management API Tests",
    "description": "Test suite for the User Management API — covers CRUD operations and authentication",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json",
    "_exporter_id": "12345678"
  },
  "variable": [
    {
      "key": "baseUrl",
      "value": "https://api.example.com/v1",
      "type": "string"
    },
    {
      "key": "apiVersion",
      "value": "v1",
      "type": "string"
    }
  ],
  "auth": {
    "type": "bearer",
    "bearer": [
      {
        "key": "token",
        "value": "{{access_token}}",
        "type": "string"
      }
    ]
  },
  "event": [
    {
      "listen": "prerequest",
      "script": {
        "type": "text/javascript",
        "exec": [
          "// Collection-level pre-request: runs before EVERY request in the collection",
          "console.log('Collection pre-request: Preparing request...');"
        ]
      }
    },
    {
      "listen": "test",
      "script": {
        "type": "text/javascript",
        "exec": [
          "// Collection-level test: runs after EVERY request in the collection",
          "pm.test('Response time is acceptable', function () {",
          "    pm.expect(pm.response.responseTime).to.be.below(2000);",
          "});"
        ]
      }
    }
  ],
  "item": [
    {
      "name": "Authentication",
      "description": "Folder containing requests related to authentication",
      "item": [
        {
          "name": "POST Login - Valid Credentials",
          "event": [
            {
              "listen": "prerequest",
              "script": {
                "type": "text/javascript",
                "exec": [
                  "// Set a timestamp for the request",
                  "pm.collectionVariables.set('timestamp', new Date().toISOString());"
                ]
              }
            },
            {
              "listen": "test",
              "script": {
                "type": "text/javascript",
                "exec": [
                  "pm.test('Status code is 200', function () {",
                  "    pm.response.to.have.status(200);",
                  "});",
                  "",
                  "pm.test('Response has access_token', function () {",
                  "    var jsonData = pm.response.json();",
                  "    pm.expect(jsonData).to.have.property('access_token');",
                  "    pm.collectionVariables.set('access_token', jsonData.access_token);",
                  "});",
                  "",
                  "pm.test('Token is a non-empty string', function () {",
                  "    var jsonData = pm.response.json();",
                  "    pm.expect(jsonData.access_token).to.be.a('string').and.not.empty;",
                  "});"
                ]
              }
            }
          ],
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n  \"email\": \"{{test_email}}\",\n  \"password\": \"{{test_password}}\"\n}",
              "options": {
                "raw": {
                  "language": "json"
                }
              }
            },
            "url": {
              "raw": "{{baseUrl}}/auth/login",
              "host": ["{{baseUrl}}"],
              "path": ["auth", "login"]
            },
            "description": "Login with a valid email and password. Saves the access_token to a collection variable."
          },
          "response": []
        }
      ]
    },
    {
      "name": "Users",
      "description": "CRUD operations for the User resource",
      "item": [
        {
          "name": "GET All Users",
          "event": [
            {
              "listen": "test",
              "script": {
                "type": "text/javascript",
                "exec": [
                  "pm.test('Status is 200', () => pm.response.to.have.status(200));",
                  "",
                  "pm.test('Response is an array', function () {",
                  "    var jsonData = pm.response.json();",
                  "    pm.expect(jsonData).to.be.an('array');",
                  "});",
                  "",
                  "pm.test('Schema validation', function () {",
                  "    var schema = {",
                  "        type: 'array',",
                  "        items: {",
                  "            type: 'object',",
                  "            required: ['id', 'name', 'email'],",
                  "            properties: {",
                  "                id: { type: 'number' },",
                  "                name: { type: 'string' },",
                  "                email: { type: 'string' }",
                  "            }",
                  "        }",
                  "    };",
                  "    pm.expect(tv4.validate(pm.response.json(), schema)).to.be.true;",
                  "});"
                ]
              }
            }
          ],
          "request": {
            "method": "GET",
            "header": [],
            "url": {
              "raw": "{{baseUrl}}/users?page=1&limit=10",
              "host": ["{{baseUrl}}"],
              "path": ["users"],
              "query": [
                { "key": "page", "value": "1" },
                { "key": "limit", "value": "10" }
              ]
            }
          },
          "response": []
        }
      ]
    }
  ]
}
```

**Explanation of key fields:**

| Key                        | Description                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------- |
| `info.schema`              | URL identifying the Collection Format version                                         |
| `variable`                 | Collection-level variables, accessible throughout the entire collection               |
| `auth`                     | Default authentication for the collection (can be overridden at folder/request level) |
| `event`                    | Collection-level scripts (pre-request and test)                                       |
| `item`                     | Array containing folders and/or requests                                              |
| `item[].item`              | Sub-items (if a folder, contains requests)                                            |
| `item[].event`             | Scripts for a specific folder or request                                              |
| `item[].request`           | The HTTP request definition                                                           |
| `item[].request.body.mode` | Body type: `raw`, `formdata`, `urlencoded`, `file`                                    |

### 4.3 Environment File JSON

```json
{
  "id": "unique-env-uuid",
  "name": "Development",
  "values": [
    {
      "key": "baseUrl",
      "value": "https://dev-api.example.com/v1",
      "type": "default",
      "enabled": true
    },
    {
      "key": "test_email",
      "value": "testuser@example.com",
      "type": "default",
      "enabled": true
    },
    {
      "key": "test_password",
      "value": "TestPassword123!",
      "type": "secret",
      "enabled": true
    },
    {
      "key": "access_token",
      "value": "",
      "type": "default",
      "enabled": true
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_at": "2025-01-01T00:00:00.000Z",
  "_postman_exported_using": "Postman/10.x"
}
```

> **Security note:** Never commit environment files containing real credentials (passwords, API keys, secrets) to Git. Use `.gitignore` for production environment files. In CI/CD, use GitHub Secrets to inject sensitive values.

### 4.4 Variable Scopes

Postman has 5 variable scopes, listed in order of priority from lowest to highest (a narrower scope overrides a broader one):

```
Global Variables     ← Lowest priority
    ↑
Collection Variables
    ↑
Environment Variables
    ↑
Data Variables (Iteration Data)
    ↑
Local Variables      ← Highest priority (exist only within a single request execution)
```

**When to use each variable type:**

| Scope                | pm API                           | Use Case                                                                              |
| -------------------- | -------------------------------- | ------------------------------------------------------------------------------------- |
| **Global**           | `pm.globals.set/get`             | Data shared across the entire workspace (rarely used in production)                   |
| **Collection**       | `pm.collectionVariables.set/get` | State shared within the collection: token after login, ID of a newly created resource |
| **Environment**      | `pm.environment.set/get`         | Config that changes per environment: baseUrl, credentials                             |
| **Data (Iteration)** | `pm.iterationData.get`           | Data from a CSV/JSON file in data-driven testing                                      |
| **Local**            | `pm.variables.set/get`           | Temporary within a single request; does not persist                                   |

## 5. Scripts in Postman

### 5.1 Pre-request Scripts

Pre-request scripts run **before the request is sent**. They are the right place to:

```javascript
// ─── 1. GENERATE DYNAMIC DATA ──────────────────────────────────
// Using Postman built-in dynamic variables
pm.collectionVariables.set(
  "randomEmail",
  pm.variables.replaceIn("{{$randomEmail}}"),
);
pm.collectionVariables.set(
  "randomName",
  pm.variables.replaceIn("{{$randomFirstName}}"),
);
pm.collectionVariables.set("timestamp", new Date().toISOString());
pm.collectionVariables.set("uuid", pm.variables.replaceIn("{{$guid}}"));

// ─── 2. MANAGE ACCESS TOKEN (TOKEN REFRESH PATTERN) ────────────
const token = pm.collectionVariables.get("access_token");
const tokenExpiry = pm.collectionVariables.get("token_expiry");

if (!token || Date.now() > parseInt(tokenExpiry)) {
  // Token is missing or expired → call the login API to get a new token
  pm.sendRequest(
    {
      url: pm.environment.get("baseUrl") + "/auth/login",
      method: "POST",
      header: { "Content-Type": "application/json" },
      body: {
        mode: "raw",
        raw: JSON.stringify({
          email: pm.environment.get("test_email"),
          password: pm.environment.get("test_password"),
        }),
      },
    },
    function (err, response) {
      if (err) {
        console.error("Login failed:", err);
        return;
      }
      const data = response.json();
      pm.collectionVariables.set("access_token", data.access_token);
      // Store expiry time (e.g., 1 hour = 3,600,000 ms)
      pm.collectionVariables.set("token_expiry", Date.now() + 3600000);
    },
  );
}

// ─── 3. SET DYNAMIC HEADERS ────────────────────────────────────
const correlationId = pm.variables.replaceIn("{{$guid}}");
pm.request.headers.upsert({ key: "X-Correlation-ID", value: correlationId });
pm.request.headers.upsert({
  key: "X-Request-Timestamp",
  value: new Date().toISOString(),
});

// ─── 4. COMPUTE SIGNATURES / HMACs ─────────────────────────────
// (Example with CryptoJS — available in the Postman sandbox)
const CryptoJS = require("crypto-js");
const secretKey = pm.environment.get("api_secret");
const payload = pm.request.body.raw;
const signature = CryptoJS.HmacSHA256(payload, secretKey).toString();
pm.request.headers.upsert({ key: "X-Signature", value: signature });
```

**Useful Postman built-in dynamic variables:**

| Variable                 | Description                |
| ------------------------ | -------------------------- |
| `{{$guid}}`              | Random UUID v4             |
| `{{$timestamp}}`         | Current Unix timestamp     |
| `{{$isoTimestamp}}`      | Current ISO 8601 timestamp |
| `{{$randomEmail}}`       | Random email address       |
| `{{$randomFirstName}}`   | Random first name          |
| `{{$randomInt}}`         | Random integer             |
| `{{$randomPhoneNumber}}` | Random phone number        |
| `{{$randomUrl}}`         | Random URL                 |
| `{{$randomBoolean}}`     | Random boolean             |

### 5.2 Test Scripts (Post-response)

Test scripts run **after the response is received**. They use the `pm.test` and `pm.expect` APIs (backed by the Chai.js assertion library):

```javascript
// ─── STATUS CODE ASSERTIONS ────────────────────────────────────
pm.test("Status code is 200", () => pm.response.to.have.status(200));
pm.test("Status code is 201 Created", () => pm.response.to.have.status(201));
pm.test("Status should be in 2xx range", () => {
  pm.expect(pm.response.code).to.be.within(200, 299);
});

// ─── RESPONSE TIME ─────────────────────────────────────────────
pm.test("Response time is under 2000ms", () => {
  pm.expect(pm.response.responseTime).to.be.below(2000);
});

// ─── RESPONSE HEADERS ──────────────────────────────────────────
pm.test("Content-Type is application/json", () => {
  pm.expect(pm.response.headers.get("Content-Type")).to.include(
    "application/json",
  );
});

// ─── RESPONSE BODY — BASIC ASSERTIONS ──────────────────────────
const jsonData = pm.response.json();

pm.test("Response has required fields", () => {
  pm.expect(jsonData).to.have.property("id");
  pm.expect(jsonData).to.have.property("name");
  pm.expect(jsonData).to.have.property("email");
});

pm.test("Data types are correct", () => {
  pm.expect(jsonData.id).to.be.a("number");
  pm.expect(jsonData.name).to.be.a("string");
  pm.expect(jsonData.email).to.be.a("string");
  pm.expect(jsonData.active).to.be.a("boolean");
});

pm.test("Email format is valid", () => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  pm.expect(jsonData.email).to.match(emailRegex);
});

// ─── JSON SCHEMA VALIDATION ─────────────────────────────────────
// Using tv4 (built-in) to validate schema
pm.test("Response matches schema", () => {
  const schema = {
    type: "object",
    required: ["id", "name", "email", "createdAt"],
    properties: {
      id: { type: "number" },
      name: { type: "string", minLength: 1 },
      email: { type: "string", format: "email" },
      role: { type: "string", enum: ["admin", "user", "guest"] },
      createdAt: { type: "string" },
    },
    additionalProperties: false,
  };
  pm.expect(tv4.validate(jsonData, schema)).to.be.true;
});

// ─── CHAIN REQUESTS: SAVE DATA FROM RESPONSE ───────────────────
// Used in flows like: Create User → Get User ID → Use in next request
pm.test("Save user ID for next request", () => {
  pm.expect(jsonData.id).to.exist;
  pm.collectionVariables.set("created_user_id", jsonData.id);
});

// ─── NEGATIVE TEST ASSERTIONS ──────────────────────────────────
pm.test("Error response has correct message", () => {
  pm.expect(pm.response.code).to.equal(400);
  const error = pm.response.json();
  pm.expect(error).to.have.property("message");
  pm.expect(error.message).to.include("required");
});

// ─── ARRAY ASSERTIONS ─────────────────────────────────────────
pm.test("Users list is valid", () => {
  const users = pm.response.json();
  pm.expect(users).to.be.an("array").and.not.be.empty;
  pm.expect(users.length).to.be.at.most(10); // Max page size
  users.forEach((user) => {
    pm.expect(user).to.have.all.keys("id", "name", "email");
  });
});
```

### 5.3 Script Execution Order

When scripts exist at multiple levels (Collection, Folder, Request), the execution order is:

```
PRE-REQUEST (outer to inner):
1. Collection Pre-request Script
2. Folder Pre-request Script
3. Request Pre-request Script
4. → HTTP Request is sent ←
TEST (outer to inner):
5. Collection Test Script
6. Folder Test Script
7. Request Test Script
```

> **Best practice:** Place shared logic (e.g., response time checks) at the Collection level. Place scenario-specific logic at the Request level. Avoid duplicating logic across levels.

### 5.4 Common pm API Reference

```javascript
// ─── VARIABLES ────────────────────────────────────────────────
pm.globals.set("key", "value");
pm.globals.get("key");
pm.globals.unset("key");

pm.environment.set("key", "value");
pm.environment.get("key");

pm.collectionVariables.set("key", "value");
pm.collectionVariables.get("key");

pm.variables.get("key"); // Read from any scope (respects hierarchy)
pm.iterationData.get("key"); // Read from CSV/JSON data file

// ─── REQUEST ──────────────────────────────────────────────────
pm.request.url; // URL object
pm.request.method; // 'GET', 'POST', ...
pm.request.headers.get("key"); // Read a header
pm.request.headers.upsert({ key, value }); // Add or update a header

// ─── RESPONSE ─────────────────────────────────────────────────
pm.response.status; // 'OK', 'Not Found', ...
pm.response.code; // 200, 404, ...
pm.response.responseTime; // milliseconds
pm.response.json(); // Parse JSON body
pm.response.text(); // Raw text body
pm.response.headers.get("key"); // Read a response header

// ─── TEST ASSERTIONS ──────────────────────────────────────────
pm.test("Test name", function () {
  pm.expect(value).to.equal(expected);
  pm.expect(value).to.be.a("string");
  pm.expect(value).to.include("substring");
  pm.expect(value).to.be.above(number);
  pm.expect(value).to.be.below(number);
  pm.expect(value).to.be.within(min, max);
  pm.expect(array).to.have.lengthOf(n);
  pm.expect(object).to.have.property("key");
  pm.expect(object).to.deep.equal({ key: "value" });
});

// Shorthand assertions
pm.response.to.have.status(200);
pm.response.to.be.json;
pm.response.to.have.header("Content-Type");

// ─── SEND REQUEST (async in pre-request) ──────────────────────
pm.sendRequest(
  {
    url: "https://api.example.com/token",
    method: "POST",
    header: { "Content-Type": "application/json" },
    body: { mode: "raw", raw: '{"grant_type":"client_credentials"}' },
  },
  function (err, response) {
    if (!err) {
      const token = response.json().access_token;
      pm.collectionVariables.set("access_token", token);
    }
  },
);

// ─── FLOW CONTROL ─────────────────────────────────────────────
postman.setNextRequest("Request Name"); // Jump to a specific request
postman.setNextRequest(null); // Stop the collection run
```

### 5.5 Setup & Teardown Pattern

In production testing, you need a setup phase (prepare data) and a teardown phase (clean up after the test):

**Pattern 1: Using Folder-level scripts**

```
Collection
├── [Folder] Setup
│   ├── POST Login → save token
│   └── POST Create Test User → save user_id
│
├── [Folder] Test Cases
│   ├── GET User by ID (uses user_id from Setup)
│   ├── PUT Update User
│   └── DELETE User
│
└── [Folder] Teardown
    ├── DELETE Test User (uses user_id from Setup)
    └── POST Logout
```

**Pattern 2: Use `postman.setNextRequest()` to control flow**

```javascript
// In the "Teardown - Delete Test User" request
// Test script:
pm.test("User deleted", () => pm.response.to.have.status(204));

// After teardown, stop the collection (avoid looping)
postman.setNextRequest(null);
```

**Pattern 3: Collection-level pre-request for global setup**

```javascript
// Collection Pre-request Script — runs before EVERY request
// Ensures the token is always valid (Token Refresh Pattern)
const tokenExpiry = pm.collectionVariables.get("token_expiry");
const accessToken = pm.collectionVariables.get("access_token");

if (!accessToken || !tokenExpiry || Date.now() > parseInt(tokenExpiry)) {
  // ... token refresh logic (see Section 5.1)
}
```

## 6. Data-Driven Testing

### 6.1 Concept and How It Works

**Data-Driven Testing (DDT)** is a methodology that separates test logic from test data. Instead of writing individual test cases for each dataset, you write a single test case and supply multiple sets of input data — Postman will automatically run that test multiple times, once per dataset. Each run is called an **iteration**.

**How data-driven testing works in Postman:**

1. Create a data file (CSV or JSON) with multiple rows/objects — each row is one set of test data.
2. In the Collection Runner, select the data file → Postman automatically sets the number of iterations equal to the number of rows.
3. For each iteration, Postman reads the current row and exposes it through the `pm.iterationData` object.
4. In the request body/URL/headers, use `{{variableName}}` to inject data from the file.
5. In test scripts, use `pm.iterationData.get('variableName')` to read data and assert against it.

> Per the Postman Blog: _"Inside pre-request and test scripts, the special data dictionary contains values loaded from the data file for a specific iteration."_

### 6.2 Using CSV Files

**CSV format rules:**

- The first row is the header (variable names).
- Every subsequent row is one iteration (one dataset).
- Line endings must be Unix format (`\n`).
- Every row must have the same number of columns.
- Integers longer than 15 digits must be formatted as text.

```csv
username,email,password,expected_status,expected_message
alice,alice@example.com,Password123!,201,User created successfully
bob,bob@example.com,SecurePass456!,201,User created successfully
invalid_user,,NoPassword,400,email is required
duplicate,alice@example.com,Password123!,409,Email already exists
```

**Using it in the request body:**

```json
{
  "username": "{{username}}",
  "email": "{{email}}",
  "password": "{{password}}"
}
```

**Using it in the test script:**

```javascript
const expectedStatus = parseInt(pm.iterationData.get("expected_status"));
const expectedMessage = pm.iterationData.get("expected_message");

pm.test(`Status code should be ${expectedStatus}`, () => {
  pm.response.to.have.status(expectedStatus);
});

pm.test("Response message matches expected", () => {
  const jsonData = pm.response.json();
  pm.expect(jsonData.message).to.include(expectedMessage);
});
```

### 6.3 Using JSON Files

JSON files are more flexible than CSV when nested data structures are needed:

```json
[
  {
    "username": "alice",
    "email": "alice@example.com",
    "password": "Password123!",
    "profile": {
      "age": 28,
      "city": "Hanoi"
    },
    "expected_status": 201,
    "expected_message": "User created successfully"
  },
  {
    "username": "bob",
    "email": "bob@example.com",
    "password": "SecurePass456!",
    "profile": {
      "age": 34,
      "city": "Ho Chi Minh City"
    },
    "expected_status": 201,
    "expected_message": "User created successfully"
  },
  {
    "username": "",
    "email": "",
    "password": "",
    "profile": null,
    "expected_status": 400,
    "expected_message": "username is required"
  }
]
```

**Request body using JSON file data:**

```json
{
  "username": "{{username}}",
  "email": "{{email}}",
  "password": "{{password}}"
}
```

> **Note:** When using `{{variableName}}` in Postman's text fields (URL, headers, body), Postman substitutes the value automatically. In scripts (JavaScript context), you must use `pm.iterationData.get('key')` explicitly.

### 6.4 Accessing Iteration Data in Scripts

```javascript
// ─── READ DATA FROM FILE ──────────────────────────────────────
const username = pm.iterationData.get("username");
const email = pm.iterationData.get("email");
const expectedStatus = pm.iterationData.get("expected_status");

// Get the entire iteration data as an object
const iterData = pm.iterationData.toObject();
console.log("Current iteration data:", JSON.stringify(iterData));

// Access nested properties (JSON file)
// Note: iterationData.get only fetches top-level keys.
// For nested properties, use toObject() and access the property directly.
const allData = pm.iterationData.toObject();
const city = allData.profile ? allData.profile.city : null;

// ─── PRE-REQUEST: BUILD A DYNAMIC BODY FROM ITERATION DATA ─────
// Option 1: Use {{variable}} directly in the body (simplest approach)
// Option 2: Build programmatically in pre-request if complex logic is needed
const requestBody = {
  username: pm.iterationData.get("username"),
  email: pm.iterationData.get("email"),
  password: pm.iterationData.get("password"),
};

// Only include profile if data is present
const profile = pm.iterationData.get("profile");
if (profile) {
  requestBody.profile = profile;
}

pm.collectionVariables.set("dynamic_body", JSON.stringify(requestBody));
// In the request body, set: {{dynamic_body}}

// ─── ASSERT BASED ON ITERATION DATA ───────────────────────────
pm.test(
  `[Iteration ${pm.info.iteration + 1}] Status is ${expectedStatus}`,
  () => {
    pm.response.to.have.status(parseInt(expectedStatus));
  },
);

pm.test("Validate response for " + email, () => {
  if (expectedStatus === 201) {
    const body = pm.response.json();
    pm.expect(body.email).to.equal(email);
  }
});
```

### 6.5 Data-Driven Testing with Newman CLI

```bash
# Run data-driven tests with a CSV file
newman run collection.json \
  -e environment.json \
  -d testdata/users.csv \
  -r htmlextra \
  --reporter-htmlextra-export reports/report.html

# Run data-driven tests with a JSON file
newman run collection.json \
  -e environment.json \
  -d testdata/users.json \
  -r cli,json \
  --reporter-json-export reports/results.json

# Override the iteration count (default = number of rows in data file)
newman run collection.json \
  -d testdata/users.csv \
  -n 3  # Run only the first 3 iterations
```

## 7. Mock Server in Postman

### 7.1 When to Use a Mock Server?

A Mock Server is a feature that creates a simulated server returning predefined responses. Real-world use cases:

- **Parallel Development:** Frontend/mobile teams develop in parallel when the backend API is not yet complete.
- **Testing Unstable APIs:** Avoid dependency on an unreliable or rate-limited third-party API.
- **Simulating Edge Cases:** Reproduce error conditions (5xx, timeouts) that are hard to trigger on a real server.
- **Demo/Prototyping:** Showcase API behavior to stakeholders without a real server.
- **Cost Reduction:** Avoid calling paid external APIs in test environments.

### 7.2 Creating a Mock Server from a Collection

**Step 1:** In the collection, create a request and send it to get a real response.

**Step 2:** Save the response as an **Example** (click "Save as Example" under the response panel). An Example is a saved request + response pair.

**Step 3:** Create the Mock Server:

- Click "..." next to the collection → "Mock Collection".
- Name the mock server (e.g., "User API Mock").
- Optionally select an environment if the examples use variables.
- Choose Private (requires an API key) or Public.
- Click "Create Mock Server".

**Step 4:** Postman provides a URL in the format:

```
https://{mock-id}.mock.pstmn.io
```

Send requests to this URL instead of the real API endpoint.

### 7.3 Saved Examples & Response Matching

When the Mock Server receives a request, it finds the best matching Example based on:

1. **URL path** — must match.
2. **HTTP method** — must match.
3. **Request headers** (optional) — higher match score.
4. **Request body** (optional) — higher match score.
5. **Query parameters** (optional) — higher match score.

If multiple examples match, Postman selects the one with the most matching criteria.

**Example: Simulating multiple response scenarios:**

```
Collection
└── GET /users/{{id}}
    ├── Example: "200 - Success"          → response: { id: 1, name: "Alice" }
    ├── Example: "404 - Not Found"        → response: { error: "User not found" }
    └── Example: "500 - Server Error"     → response: { error: "Internal error" }
```

To trigger a specific response, add the header `x-mock-response-name: "404 - Not Found"` to the request.

**Mock Server with Dynamic Variables:**

```json
{
  "id": "{{$randomInt}}",
  "name": "{{$randomFirstName}} {{$randomLastName}}",
  "email": "{{$randomEmail}}",
  "createdAt": "{{$isoTimestamp}}"
}
```

## 8. Production-Ready Collection Structure

### 8.1 Organization Principles

According to Postman's official recommendations and community best practices, a production-ready collection must follow these principles:

1. **Separate the documentation collection from the test collection:** Documentation collections (describing the API) should be separate from test collections (assertions, edge cases, negative cases).
2. **Use business-context names:** `User Onboarding Flow` is clearer than `POST /users`.
3. **Group by feature/module:** Do not group by HTTP method.
4. **Every request has a full description:** Even one or two sentences describing purpose and expected behavior is better than nothing.
5. **Use variables instead of hardcoding:** Never hardcode URLs, credentials, or IDs in requests.
6. **Collection-level auth:** Define authentication once at the collection level and override only when necessary.

> Per the Postman Blog: _"Keep your API tests in a separate collection from your API documentation. The audience is different — documentation is for consumers of an API, tests are for the authors of an API."_

### 8.2 Standard Workspace & Collection Structure

```
📁 Postman Workspace: [Project Name] API Testing
│
├── 📦 Collection: [Module A] - Auth API Tests
│   ├── 📁 Setup
│   │   └── POST Login (save token)
│   ├── 📁 Happy Path
│   │   ├── POST Login - Valid Credentials
│   │   ├── POST Refresh Token
│   │   └── POST Logout
│   ├── 📁 Negative Cases
│   │   ├── POST Login - Wrong Password
│   │   ├── POST Login - Missing Email
│   │   ├── POST Login - Expired Token
│   │   └── POST Login - Invalid Token Format
│   └── 📁 Teardown
│       └── POST Logout
│
├── 📦 Collection: [Module B] - User Management Tests
│   ├── 📁 Setup
│   │   └── POST Login (get admin token)
│   ├── 📁 CRUD - Happy Path
│   │   ├── POST Create User
│   │   ├── GET User by ID
│   │   ├── GET All Users
│   │   ├── PUT Update User
│   │   └── DELETE User
│   ├── 📁 Validation Tests
│   │   ├── POST Create User - Missing Required Fields
│   │   ├── POST Create User - Invalid Email Format
│   │   └── POST Create User - Duplicate Email
│   ├── 📁 Authorization Tests
│   │   ├── GET User - No Token (expect 401)
│   │   ├── GET User - Wrong Role (expect 403)
│   │   └── GET User - Other User's Data (expect 403)
│   └── 📁 Teardown
│       └── DELETE Test Users (cleanup)
│
├── 📦 Collection: [Regression Suite] - Full API Tests
│   └── (Import/clone requests from module collections)
│
└── 📁 Environments
    ├── 🌍 Development
    ├── 🌍 Staging
    └── 🌍 Production (read-only, health checks only)
```

### 8.3 Naming Convention

**Collections:**

```
[Module/Feature] - [Type] Tests
Examples:
  ✅ "Auth API - Functional Tests"
  ✅ "User Management - Regression Suite"
  ❌ "tests"
  ❌ "API collection v3 final"
```

**Folders:**

```
[Action Group] or [Test Type]
Examples:
  ✅ "Happy Path", "Negative Cases", "Authorization Tests"
  ✅ "Setup", "Teardown"
  ❌ "Folder 1", "misc"
```

**Requests:**

```
[HTTP Method] [Resource] - [Scenario]
Examples:
  ✅ "POST Create User - Valid Data"
  ✅ "GET User by ID - Non-existent ID (expect 404)"
  ✅ "DELETE User - Unauthorized (expect 401)"
  ❌ "test1"
  ❌ "create"
```

## 9. CI/CD Integration with GitHub Actions

### 9.1 CI/CD Flow Overview

```
Developer pushes code / opens PR
        │
        ▼
GitHub Actions triggered
        │
        ▼
Checkout repository
        │
        ▼
Setup Node.js
        │
        ▼
npm install -g newman
npm install -g newman-reporter-htmlextra
        │
        ▼
newman run collection.json \
  -e environments/staging.json \
  -d testdata/users.csv \
  -r htmlextra,junit
        │
        ├── Tests Pass (exit 0) → ✅ CI Green → PR can be merged
        └── Tests Fail (exit ≠ 0) → ❌ CI Red → Merge is blocked
                │
                ▼
        Upload HTML report as artifact
        (Downloadable from the GitHub Actions UI)
```

### 9.2 Setting Up GitHub Actions with Newman

**Repository structure:**

```
repo-root/
├── .github/
│   └── workflows/
│       ├── api-tests.yml             # CI workflow
│       └── api-tests-scheduled.yml   # Scheduled monitoring
├── postman/
│   ├── collections/
│   │   ├── auth-tests.json
│   │   ├── user-management-tests.json
│   │   └── regression-suite.json
│   ├── environments/
│   │   ├── dev.json               # Do not commit real credentials
│   │   └── staging.json           # Do not commit real credentials
│   └── testdata/
│       ├── users.csv
│       └── products.json
├── reports/                       # Add to .gitignore
└── README.md
```

**`.github/workflows/api-tests.yml`:**

```yaml
name: API Tests

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  api-test:
    name: Run Postman API Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Install Newman and reporters
        run: |
          npm install -g newman
          npm install -g newman-reporter-htmlextra
          npm install -g newman-reporter-junit

      - name: Create reports directory
        run: mkdir -p reports

      - name: Run Auth API Tests
        run: |
          newman run postman/collections/auth-tests.json \
            --env-var "baseUrl=${{ secrets.API_BASE_URL }}" \
            --env-var "test_email=${{ secrets.TEST_EMAIL }}" \
            --env-var "test_password=${{ secrets.TEST_PASSWORD }}" \
            -r cli,htmlextra,junit \
            --reporter-htmlextra-export reports/auth-report.html \
            --reporter-junit-export reports/auth-junit.xml \
            --reporter-htmlextra-title "Auth API Test Report"

      - name: Run User Management Tests
        run: |
          newman run postman/collections/user-management-tests.json \
            --env-var "baseUrl=${{ secrets.API_BASE_URL }}" \
            --env-var "test_email=${{ secrets.TEST_EMAIL }}" \
            --env-var "test_password=${{ secrets.TEST_PASSWORD }}" \
            -d postman/testdata/users.csv \
            -r cli,htmlextra,junit \
            --reporter-htmlextra-export reports/users-report.html \
            --reporter-junit-export reports/users-junit.xml

      - name: Upload test reports
        uses: actions/upload-artifact@v4
        if: always() # Upload whether tests pass or fail
        with:
          name: api-test-reports-${{ github.run_number }}
          path: reports/
          retention-days: 30

      - name: Publish JUnit results
        uses: dorny/test-reporter@v1
        if: always()
        with:
          name: API Test Results
          path: reports/*.xml
          reporter: java-junit
```

**`.github/workflows/api-tests-scheduled.yml` (Monitoring / Health Check):**

```yaml
name: API Health Check (Scheduled)

on:
  schedule:
    - cron: "0 */6 * * *" # Run every 6 hours
  workflow_dispatch: # Allow manual trigger

jobs:
  health-check:
    name: API Health Check
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Install Newman
        run: npm install -g newman

      - name: Run health check collection
        run: |
          newman run postman/collections/health-check.json \
            --env-var "baseUrl=${{ secrets.PROD_API_BASE_URL }}" \
            --env-var "api_key=${{ secrets.PROD_API_KEY }}" \
            --bail   # Stop immediately on first failure

      - name: Notify on failure
        if: failure()
        uses: slackapi/slack-github-action@v1.26.0
        with:
          payload: |
            {
              "text": "API Health Check FAILED! Check: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### 9.3 Using the Postman CLI (Alternative to Newman)

In addition to Newman, Postman also provides an official **Postman CLI**. Advantage: runs collections directly from the Postman workspace (no file export needed), and results are posted back to the Postman workspace.

```yaml
# In a GitHub Actions step, using the Postman CLI:
- name: Install Postman CLI
  run: |
    curl -o- "https://dl-cli.pstmn.io/install/linux64.sh" | sh

- name: Run collection via Postman CLI
  env:
    POSTMAN_API_KEY: ${{ secrets.POSTMAN_API_KEY }}
  run: |
    postman login --with-api-key "$POSTMAN_API_KEY"
    postman collection run YOUR-COLLECTION-ID \
      -e YOUR-ENVIRONMENT-ID
```

> **Newman vs Postman CLI:** Newman is more suitable when you want full control with file-based collections version-controlled in Git. The Postman CLI is more convenient when collections are primarily managed on the Postman cloud workspace.

### 9.4 Managing Secrets and Environments in CI

**Critical rules:**

```
❌ NEVER:
  - Commit API keys, passwords to Git
  - Commit environment files with real credentials
  - Hardcode credentials in workflow YAML files

✅ ALWAYS:
  - Store credentials in GitHub Secrets
  - Inject via --env-var when running Newman
  - Use .gitignore for production environment files
  - Only commit environment files with placeholder values
```

**`.gitignore` for Postman:**

```gitignore
# Postman environments with real credentials
postman/environments/production.json
postman/environments/staging.json

# Only commit template/dev environments
!postman/environments/dev-template.json

# Reports (generated files)
reports/
```

**Injecting multiple secrets via Newman:**

```bash
newman run collection.json \
  --env-var "baseUrl=${{ secrets.BASE_URL }}" \
  --env-var "apiKey=${{ secrets.API_KEY }}" \
  --env-var "dbPassword=${{ secrets.DB_PASSWORD }}" \
  --env-var "clientId=${{ secrets.OAUTH_CLIENT_ID }}" \
  --env-var "clientSecret=${{ secrets.OAUTH_CLIENT_SECRET }}"
```

## 10. Consolidated Best Practices

Below is a summary of best practices recommended by Postman's official documentation and the community:

### Organization & Structure

- **Group collections by business function,** not by HTTP method. `User Onboarding Flow` is clearer than `POST /users`.
- **Separate documentation collections from test collections.** Documentation collections are for consumers (describing the API); test collections are for the team (assertions, edge cases, negative cases).
- **Give every request a descriptive name.** The name should convey the purpose and scenario, not just the HTTP method.
- **Add descriptions to collections, folders, and requests.** Even a single sentence is more useful than nothing.
- **Follow the pattern: Setup → Tests → Teardown,** especially for integration tests.

### Variables & Environments

- **Never hardcode URLs, credentials, or IDs in requests.** Always use variables.
- **Use Collection Variables to store state** between requests (tokens, IDs of newly created resources).
- **Use Environment Variables for config that changes per environment** (baseUrl, credentials).
- **Never commit real credentials to Git.** Use GitHub Secrets for CI/CD.
- **Leave the `access_token` initial value blank** in committed environment files (to avoid accidental exposure).

### Scripting

- **Place shared logic at the Collection-level scripts** (response time checks, common headers).
- **Place scenario-specific logic at the Request-level scripts** (business logic validation, data extraction).
- **Implement the Token Refresh Pattern** in the Collection Pre-request Script to ensure the token is always valid.
- **Always log to the Console** when debugging: `console.log('Debug info:', value)`.
- **Use `pm.expect` instead of `if/else` for assertions** — `pm.test` records results more clearly.
- **Give tests descriptive names:** `'Status code is 201 - User Created'` is clearer than `'test1'`.

### Data-Driven

- **Separate test data from the collection** — use CSV/JSON files for parameterized testing.
- **Cover both happy path and negative cases** in data files (valid, invalid, boundary values).
- **Include expected values in the data file** (`expected_status`, `expected_message`) so test scripts can assert dynamically.

### CI/CD

- **Commit collections and test data to Git** for version control.
- **Use the `--bail` flag carefully** — bail stops on the first failure, which is appropriate for smoke tests but not for full regression runs.
- **Always upload test reports** regardless of pass or fail (`if: always()` in GitHub Actions).
- **Run scheduled health checks** in addition to CI triggers — to detect environmental issues.
- **Keep Newman and reporters up to date** to avoid compatibility issues.

### General

- **Write tests for both happy path and negative cases.** Do not only test the "sunny day scenario".
- **Validate the schema, not just the status code.** Schema validation catches contract violations that status codes miss.
- **Use Mock Server early** so frontend/mobile does not have to wait for the backend.
- **Periodically review and refactor the collection** — remove duplicate requests, update when the API changes.
- **Sync the collection with the OpenAPI spec** after every API change to prevent drift.

## 11. References

All content in this document has been compiled and verified from the following sources:

### Official Postman Documentation

- [Postman Learning Center — Newman CLI](https://learning.postman.com/docs/reference/newman-cli/command-line-integration-with-newman)
- [Postman Docs — Run collections with imported data (Data-Driven)](https://learning.postman.com/docs/collections/running-collections/working-with-data-files)
- [Postman Docs — Mock Servers Overview](https://learning.postman.com/docs/design-apis/mock-apis/overview)
- [Postman Docs — Deploy a Mock Server](https://learning.postman.com/docs/design-apis/mock-apis/set-up-mock-servers)
- [Postman Docs — Integrate GitHub Actions](https://learning.postman.com/docs/integrations/available-integrations/ci-integrations/github-actions)
- [Postman Docs — Mock APIs with response examples](https://learning.postman.com/docs/designing-and-developing-your-api/mocking-data/mocking-with-examples/)
- [Postman Docs — Postman CLI Mock Server commands](https://learning.postman.com/docs/postman-cli/postman-cli-mock)
- [Postman Collection Format v2.1.0 Schema](https://schema.postman.com/collection/json/v2.1.0/draft-07/docs/index.html)

### Postman Blog

- [API Test Automation Best Practices with Postman](https://www.postman.com/postman-best-practices/api-test-automation/)
- [Why is API Testing Important? Tips, Tricks & More](https://blog.postman.com/api-testing-tips-from-a-postman-professional/)
- [Continuous API Testing with Postman](https://blog.postman.com/continuous-api-testing-with-postman/)
- [Using CSV and JSON Data Files in the Postman Collection Runner](https://blog.postman.com/using-csv-and-json-files-in-the-postman-collection-runner/)
- [Looping through a Data File in the Postman Collection Runner](https://blog.postman.com/looping-through-a-data-file-in-the-postman-collection-runner/)
- [Document Your API Like a Pro: Postman Collection Best Practices](https://blog.postman.com/document-your-api-like-a-pro-postman-collection-best-practices/)
- [Travelogue of Postman Collection Format v2](https://blog.postman.com/travelogue-of-postman-collection-format-v2/)
- [REST API Best Practices: A Developer's Guide](https://blog.postman.com/rest-api-best-practices/)

### CI/CD Integration

- [From Postman Collection to CI Gate: The Complete GitHub Actions Pipeline (DEV.to)](https://dev.to/imranalmunyeem/from-postman-collection-to-ci-gate-the-complete-github-actions-pipeline-3p0d)
- [Newman Postman CI Automation Guide 2026 (QA Skills)](https://qaskills.sh/blog/newman-postman-ci-automation-guide-2026)
- [Streamlining API Test Automation with Postman, Newman, and GitHub Actions (Medium)](https://medium.com/@raviteja.pendyalas/streamlining-api-test-automation-with-postman-newman-and-github-actions-412bc9a2e4bd)
- [Integrating Postman Collections with GitHub Actions CI/CD Pipeline (Medium)](https://medium.com/heroic-engineering/integrating-postman-collections-with-github-actions-ci-cd-pipeline-cb7f14ae4c03)
- [Newman Action — GitHub Marketplace](https://github.com/marketplace/actions/newman-action)

### API Testing Concepts & Best Practices

- [What is API Testing? — IBM](https://www.ibm.com/think/topics/api-testing)
- [What is API Testing? Types, Tools (2026) — BrowserStack](https://www.browserstack.com/guide/what-is-api-testing)
- [10 Types of API Testing — Nordic APIs](https://nordicapis.com/10-types-of-api-testing/)
- [API Testing Strategies 2026 — Total Shift Left](https://totalshiftleft.com/blog/api-testing-complete-guide)
- [What is API Testing? — Keploy Blog](https://keploy.io/blog/community/what-is-api-testing)
- [API Testing — GeeksforGeeks](https://www.geeksforgeeks.org/software-engineering/api-testing-software-testing/)
- [What is API Testing? — Parasoft Guide](https://www.parasoft.com/learning-center/api-testing-guide/)
- [What, Why & How to Create an API Testing Strategy — ACCELQ](https://www.accelq.com/blog/api-testing-strategy/)

### Scripts & Data-Driven Testing

- [Postman Pre-Request Scripts: A Practical Guide 2026 — QAJobFit](https://qajobfit.com/resources/postman-pre-request-scripts)
- [Mastering Postman: Data-Driven Testing with CSV and JSON — EchoAPI](https://www.echoapi.com/blog/mastering-postman-data-driven-testing-with-csv-and-json-files/)
- [Data Driven Testing in Postman — Software Testing Material](https://www.softwaretestingmaterial.com/data-driven-testing-in-postman/)
- [How to Perform Data Driven Testing from CSV — GeeksforGeeks](https://www.geeksforgeeks.org/software-testing/how-to-perform-data-driven-testing-from-csv-to-postman-requests-scripts/)
- [Adding Tests for Each API Request in Postman (Medium)](https://mohamedsaidibrahim.medium.com/adding-tests-for-each-api-request-in-postman-a-comprehensive-guide-before-and-after-execution-671cc5566d56)
- [How to Use Postman for API Testing in a Clean & Structured Way (Medium)](https://medium.com/@batool15/how-to-use-postman-for-api-testing-in-a-clean-structured-way-42ef3403beab)

### Mock Server

- [Creating Mock Servers in Postman — Moldstud](https://moldstud.com/articles/p-creating-mock-servers-in-postman-a-comprehensive-guide-for-developers)
- [How to Create a Mock Server in Postman — GeeksforGeeks](https://www.geeksforgeeks.org/devops/how-to-create-mock-servers-using-postman/)
- [Postman Mock Server — APIdog Blog](https://apidog.com/blog/postman-mock-server/)

### Tool Comparisons

- [Postman vs OpenAPI Test Automation: Which Paradigm Scales in 2026? — Total Shift Left](https://totalshiftleft.ai/blog/postman-vs-openapi-test-automation)
- [Best API Test Automation Tools Compared (2025) — Total Shift Left](https://totalshiftleft.ai/blog/best-api-test-automation-tools-compared-2025-why-shift-left-wins)
- [Postman vs Apidog vs Total Shift Left (2026) — Total Shift Left](https://totalshiftleft.ai/blog/postman-vs-apidog-vs-totalshiftleft)

### OpenAPI & Contract Testing

- [OpenAPI Testing: The Complete Guide — Apideck](https://www.apideck.com/blog/openapi-testing)
- [API Contract Testing Guide 2026 — Total Shift Left](https://totalshiftleft.ai/api-contract-testing)
- [Validating API Requests and Responses (Medium)](https://medium.com/geekculture/validating-api-requests-and-responses-25ed5cc9e846)
