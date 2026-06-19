# Development Methodology

Software development methodologies that define when and how tests are written relative to production code.

## TDD (Test-Driven Development)

Write tests before writing the production code. Follow a short, repeating cycle.

**Red → Green → Refactor Cycle**

1. **Red** — Write a failing test for the desired behavior
2. **Green** — Write the minimum code to make the test pass
3. **Refactor** — Clean up code while keeping tests green

**Characteristics**
- Focused on unit tests at the function/class level
- Forces small, testable design decisions
- Tests act as a living specification of behavior

**Example**
```python
# 1. Red: write failing test
def test_add():
    assert add(2, 3) == 5  # add() doesn't exist yet

# 2. Green: write minimum implementation
def add(a, b):
    return a + b

# 3. Refactor: (nothing to clean up here, but would happen in complex cases)
```

---

## BDD (Behavior-Driven Development)

An extension of TDD that focuses on the *behavior* of the system from the perspective of stakeholders. Tests are written in natural language that both developers and non-developers can read.

**Given → When → Then Pattern**

- **Given** — the initial context / preconditions
- **When** — the action or event that occurs
- **Then** — the expected outcome

**Characteristics**
- Bridges communication between developers, QA, and product/business
- Scenarios written in Gherkin syntax (or similar) serve as both documentation and tests
- Focuses on user-facing behavior, not implementation details

**Example (Gherkin)**
```gherkin
Feature: User login

  Scenario: Successful login with valid credentials
    Given the user is on the login page
    When the user enters valid username and password
    Then the user is redirected to the dashboard
    And a welcome message is displayed
```

---

## SDD (Specification-Driven Development)

Write a formal specification document *before* any code or tests. The spec defines the expected behavior, interfaces, and contracts that both tests and implementation must satisfy.

**Characteristics**
- Spec is the single source of truth — tests and code are derived from it
- Common in domains requiring rigor: protocols, APIs, embedded systems
- Specifications may be formal (e.g., OpenAPI, JSON Schema) or informal (design docs)

**Workflow**
1. Write the specification (API contract, state machine, data schema, etc.)
2. Generate or write tests that validate the specification
3. Implement code to satisfy both

**Example**
```yaml
# OpenAPI spec written first
paths:
  /users/{id}:
    get:
      summary: Get user by ID
      responses:
        '200':
          description: User found
        '404':
          description: User not found
```
Tests and implementation are then written to conform to this contract.

---

## Comparison

| | TDD | BDD | SDD |
|---|---|---|---|
| Written first | Tests | Behavior scenarios | Specification |
| Primary audience | Developers | Developers + Business | Developers + Architects |
| Test level | Unit | Integration / Acceptance | Any (derived from spec) |
| Language | Code | Natural language (Gherkin) | Formal or semi-formal |
| Focus | Implementation correctness | User behavior | Contract / interface |

---

## Reference

<a id="TDD_Beck"></a>
[1] [Test Driven Development: By Example — Kent Beck](https://www.oreilly.com/library/view/test-driven-development/0321146530/)  
<a id="BDD_Cucumber"></a>
[2] [BDD in Action — Cucumber Docs](https://cucumber.io/docs/bdd/)  
<a id="SDD_OpenAPI"></a>
[3] [OpenAPI Specification](https://swagger.io/specification/)
