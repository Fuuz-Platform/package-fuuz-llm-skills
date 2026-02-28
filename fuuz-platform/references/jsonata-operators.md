# JSONata Operators

## Path Operators

| Operator | Name | Description |
|----------|------|-------------|
| `.` | Map | Navigate object fields. On arrays, maps over each element. `Account.Order.Product` |
| `[ ]` | Filter/Index | Array index (`[0]`, `[-1]`) or predicate filter (`[price > 10]`). Predicates return matching elements. |
| `^( )` | Order-by | Sort array. `Account.Order^(>Price)` sorts descending by Price. Multiple keys: `^(>field1, <field2)`. `<` = ascending (default), `>` = descending. |
| `{ }` | Reduce | Group and aggregate. `Account.Order.Product.{Description: $sum(Price)}` groups by Description, sums prices. |
| `*` | Wildcard | Matches all fields at current level. `Address.*` returns all values of Address. |
| `**` | Descendants | Matches all fields at any depth. `Account.**[id=123]` finds any nested object with id=123. |
| `%` | Parent | Access parent context in nested filter. `Account.Order.Product[%.OrderID = "order1"]` |
| `#$var` | Index bind | Bind array index to variable. `library.books#$i[genre="fiction"]{title: $i}` |
| `@$var` | Focus bind | Bind current focus to variable. `library.loans@$l.books[isbn=$l.isbn]` |

## Numeric Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `+` | Addition | `Price + Tax` |
| `-` | Subtraction / Negation | `Price - Discount`, `-42` |
| `*` | Multiplication | `Price * Quantity` |
| `/` | Division | `Total / Count` |
| `%` | Modulo | `index % 2 = 0` (context-dependent: path vs numeric) |
| `..` | Range | `[1..5]` produces `[1,2,3,4,5]`. `[1..n]` for dynamic ranges. |

## Comparison Operators

| Operator | Description | Notes |
|----------|-------------|-------|
| `=` | Equal | Type-coerced comparison. Single `=`, not `==`. |
| `!=` | Not equal | |
| `<` | Less than | |
| `<=` | Less than or equal | |
| `>` | Greater than | |
| `>=` | Greater than or equal | |
| `in` | Membership | `value in [1,2,3]`. Also works with strings: `"world" in "hello world"`. |

## Boolean Operators

| Operator | Description | Notes |
|----------|-------------|-------|
| `and` | Logical AND | Short-circuits. Both operands cast to boolean. |
| `or` | Logical OR | Short-circuits. Both operands cast to boolean. |

**Important:** There is no `not` operator. Use `$not(expr)` function.

## String Operator

| Operator | Description | Example |
|----------|-------------|---------|
| `&` | Concatenation | `FirstName & " " & LastName`. Coerces non-strings. |

## Conditional Operators

| Operator | Name | Description |
|----------|------|-------------|
| `? :` | Ternary | `expr ? truthy_result : falsy_result`. Only falsy values: `false`, `null`, `undefined`, `0`, `""`. |
| `?:` | Elvis | `expr ?: default`. Returns `expr` if truthy, else `default`. Note: no space before `:`. |
| `??` | Null coalescing | `expr ?? default`. Returns `expr` if not `null`/`undefined`, else `default`. Unlike `?:`, keeps `0`, `false`, `""`. |

## Assignment Operator

| Operator | Description | Example |
|----------|-------------|---------|
| `:=` | Variable binding | `$x := 42`. Binds value to variable within scope. Variables prefixed with `$`. |

## Chaining Operator

| Operator | Description | Example |
|----------|-------------|---------|
| `~>` | Chain / Pipe | `data ~> $map(fn) ~> $filter(fn2)`. Passes left result as first arg of right function. |

## Transform Operator

```jsonata
| source | update_expr | delete_keys |
```

Creates a modified copy. `update_expr` is an object of field:value pairs to set. `delete_keys` is an array of field names to remove.

```jsonata
/* Update a field */
| Account | {"Status": "Active"} |

/* Delete fields */
| Account | {} | ["TempField", "InternalId"] |

/* Conditional update using path variable */
| Account.Order.Product | {"Price": Price * 1.1} |
```

The transform operator:
- Returns a deep copy (does not mutate original)
- Can target nested paths
- `update_expr` can reference fields of the matched object
- `delete_keys` is optional
