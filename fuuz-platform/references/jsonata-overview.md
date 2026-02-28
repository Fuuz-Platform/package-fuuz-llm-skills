# JSONata Reference — Overview

JSONata is a lightweight query and transformation language for JSON data. It is used throughout the platform in **data flow transform nodes**, **screen element transform props**, **onChange handlers**, and **validation expressions**.

---

## Core Syntax

### Path Expressions

```jsonata
Account.Order.Product        /* Navigate nested objects */
Account.Order[0]             /* Array index (zero-based) */
Account.Order[-1]            /* Negative index = from end */
Account.Order[0].Product     /* Chained navigation */
Phone[type="mobile"]         /* Filter predicate */
Account.Order.Product.Price  /* Implicit map over arrays */
*.Price                      /* Wildcard — all children */
**.Price                     /* Descendant wildcard — any depth */
```

### Result Construction

```jsonata
/* Object construction */
Account.Order.{
  "product": Product.Description,
  "total": Price * Quantity
}

/* Array construction */
[Account.Order.Product.Price]  /* Wrap in array */
```

### Singleton Array Equivalence (CRITICAL)

JSONata treats a single-element array and the element itself as equivalent:
- `[42]` and `42` are interchangeable in path expressions
- `Phone[0]` on a non-array returns the value itself
- `Phone.number[0]` gets the first `number` of EACH phone (map then index)
- `(Phone.number)[0]` gets the first from ALL numbers (flatten then index)

---

## Critical Gotchas

1. **No `!` operator** — Use `$not(expr)` function instead
2. **`$not()` is a function**, not an operator — `$not($exists(x))` not `!$exists(x)`
3. **Dot-before-brace matters** — `Account.Order.{"p": Product}` constructs per-item; `Account.{"o": Order}` constructs once
4. **Ternary vs Elvis** — `expr ? truthy : falsy` (ternary), `expr ?: default` (elvis — default when falsy), `expr ?? default` (null coalescing — default when null/undefined only)
5. **`$round` uses banker's rounding** — Rounds to nearest even on .5 boundary. `$round(2.5)` = 2, `$round(3.5)` = 4
6. **`$now()` and `$millis()` are frozen** — Return same value for entire evaluation (consistent timestamps)
7. **No explicit return** — Last expression evaluated is the result
8. **`undefined` propagates** — Accessing a missing field returns `undefined`, which silently drops from results rather than erroring
9. **String concatenation uses `&`** — Not `+`. `"hello" & " " & "world"`
10. **Comparison `=` not `==`** — Single equals for comparison. No `===`.
11. **`in` operator for membership** — `x in [1,2,3]` not `[1,2,3].includes(x)`

---

## Platform Integration

### Where JSONata is Used

| Context | Usage | Available Bindings |
|---------|-------|--------------------|
| Data Flow Transform nodes | Transform payloads between nodes | Core + Moment + XML + EDI + Encryption + Network + Units + Calendars + Joins + Semver + MFGx App |
| Screen Element transform props | Dynamic property evaluation | Core + Moment + $components + metadata |
| Screen Element onChange | React to user input | Core + Moment + $components + metadata + MFGx App |
| Validation expressions | Form/field validation | Core + Moment + $components + metadata |
| Table column data/style | Dynamic column rendering | Core + Moment + $components + metadata |

### Transform Props Pattern

Screen element props that accept JSONata use the transform object pattern:

```json
{
  "__transform": "$components.myForm.data.status = 'active'",
  "__remote": false,
  "__refreshInterval": null,
  "__dynamicFields": null,
  "__cacheKey": null
}
```

- `__transform` — The JSONata expression string
- `__remote` — Execute on server (for heavy computation)
- `__refreshInterval` — Re-evaluate on interval (ms)
- `__dynamicFields` — Paths in payload/context that the transform depends on. When values at these paths change, the transform re-executes and the component re-renders. Shape: `{ payload: ["data.field"], context: [] }`. Set to `false` to evaluate once only.
- `__cacheKey` — Unique key for storing the transform result, scoped to the element

### Data Flow Context (`$state`)

In data flow nodes, the input is the previous node's output. Additional context:
- `$state.payload` — Current node input (same as `$`)
- `$state.context` — Shared flow context object
- `$state.claims` — Auth claims of the triggering user

### Screen Element Context

In screen transforms, the expression receives:
- `$components.<Name>.data` — Component's current data
- `$components.<Name>.fn.<method>()` — Component's exposed functions
- `$components.<Name>.context` — Component's context (row data in tables, etc.)
- `metadata.user` — Current user info
- `metadata.tenant` — Current tenant
- `metadata.settings` — Application settings
- `metadata.screen` — Current screen info
- `metadata.environment` — Environment variables
- `metadata.userRoles` — User's roles
- `metadata.urlParameters` — URL path parameters
- `metadata.querystring` — URL query parameters

See `context.md` for full details.

---

## File Index

| File | Contents |
|------|----------|
| `operators.md` | Path, numeric, comparison, boolean, and other operators |
| `functions-builtin.md` | All 65 standard JSONata functions |
| `functions-platform.md` | All custom platform bindings ($moment, $query, $http, etc.) |
| `programming.md` | Variables, lambdas, conditionals, regex, chaining |
| `context.md` | Platform evaluation context ($components, metadata, $state) |
