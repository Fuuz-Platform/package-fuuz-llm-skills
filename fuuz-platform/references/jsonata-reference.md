# JSONata Complete Reference for Fuuz

Authoritative reference for all JSONata syntax, operators, functions, platform bindings, programming constructs, and evaluation contexts used in the Fuuz Industrial Operations platform.

---

## Overview

JSONata is a lightweight query and transformation language for JSON data. It is used throughout Fuuz in **data flow transform nodes**, **screen element transform props**, **onChange handlers**, and **validation expressions**.

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
4. **Ternary vs Elvis** — `expr ? truthy : falsy` (ternary), `expr ?: default` (elvis -- default when falsy), `expr ?? default` (null coalescing -- default when null/undefined only)
5. **`$round` uses banker's rounding** — Rounds to nearest even on .5 boundary. `$round(2.5)` = 2, `$round(3.5)` = 4
6. **`$now()` and `$millis()` are frozen** — Return same value for entire evaluation (consistent timestamps)
7. **No explicit return** — Last expression evaluated is the result
8. **`undefined` propagates** — Accessing a missing field returns `undefined`, which silently drops from results rather than erroring
9. **String concatenation uses `&`** — Not `+`. `"hello" & " " & "world"`
10. **Comparison `=` not `==`** — Single equals for comparison. No `===`.
11. **`in` operator for membership** — `x in [1,2,3]` not `[1,2,3].includes(x)`

---

## Operators

### Path Operators

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

### Numeric Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `+` | Addition | `Price + Tax` |
| `-` | Subtraction / Negation | `Price - Discount`, `-42` |
| `*` | Multiplication | `Price * Quantity` |
| `/` | Division | `Total / Count` |
| `%` | Modulo | `index % 2 = 0` (context-dependent: path vs numeric) |
| `..` | Range | `[1..5]` produces `[1,2,3,4,5]`. `[1..n]` for dynamic ranges. |

### Comparison Operators

| Operator | Description | Notes |
|----------|-------------|-------|
| `=` | Equal | Type-coerced comparison. Single `=`, not `==`. |
| `!=` | Not equal | |
| `<` | Less than | |
| `<=` | Less than or equal | |
| `>` | Greater than | |
| `>=` | Greater than or equal | |
| `in` | Membership | `value in [1,2,3]`. Also works with strings: `"world" in "hello world"`. |

### Boolean Operators

| Operator | Description | Notes |
|----------|-------------|-------|
| `and` | Logical AND | Short-circuits. Both operands cast to boolean. |
| `or` | Logical OR | Short-circuits. Both operands cast to boolean. |

**Important:** There is no `not` operator. Use `$not(expr)` function.

### String Operator

| Operator | Description | Example |
|----------|-------------|---------|
| `&` | Concatenation | `FirstName & " " & LastName`. Coerces non-strings. |

### Conditional Operators

| Operator | Name | Description |
|----------|------|-------------|
| `? :` | Ternary | `expr ? truthy_result : falsy_result`. Only falsy values: `false`, `null`, `undefined`, `0`, `""`. |
| `?:` | Elvis | `expr ?: default`. Returns `expr` if truthy, else `default`. Note: no space before `:`. |
| `??` | Null coalescing | `expr ?? default`. Returns `expr` if not `null`/`undefined`, else `default`. Unlike `?:`, keeps `0`, `false`, `""`. |

### Assignment Operator

| Operator | Description | Example |
|----------|-------------|---------|
| `:=` | Variable binding | `$x := 42`. Binds value to variable within scope. Variables prefixed with `$`. |

### Chaining Operator

| Operator | Description | Example |
|----------|-------------|---------|
| `~>` | Chain / Pipe | `data ~> $map(fn) ~> $filter(fn2)`. Passes left result as first arg of right function. |

### Transform Operator

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

---

## Built-in Functions (65)

All standard functions from the JSONata specification.

### String Functions (21)

| Function | Description |
|----------|-------------|
| `$string(value, prettify?)` | Cast to string. Optional `prettify` boolean for formatted JSON output. |
| `$length(str)` | String length. |
| `$substring(str, start, length?)` | Extract substring. Negative `start` counts from end. |
| `$substringBefore(str, chars)` | Substring before first occurrence of `chars`. |
| `$substringAfter(str, chars)` | Substring after first occurrence of `chars`. |
| `$uppercase(str)` | Convert to uppercase. |
| `$lowercase(str)` | Convert to lowercase. |
| `$trim(str)` | Remove leading/trailing whitespace. |
| `$pad(str, width, char?)` | Pad string to `width`. Negative = pad left. Default pad char is space. |
| `$contains(str, pattern)` | Test if `str` contains `pattern` (string or regex). Returns boolean. |
| `$split(str, separator, limit?)` | Split string. `separator` can be string or regex. |
| `$join(array, separator?)` | Join array of strings. Default separator is empty string. |
| `$match(str, pattern, limit?)` | Find all regex matches. Returns array of match objects with `match`, `index`, `groups`. |
| `$replace(str, pattern, replacement, limit?)` | Replace occurrences. `pattern` can be string or regex. `replacement` can be string or function. |
| `$base64encode(str)` | Encode string to base64. |
| `$base64decode(str)` | Decode base64 to string. |
| `$encodeUrlComponent(str)` | URI component encode (like `encodeURIComponent`). |
| `$decodeUrlComponent(str)` | URI component decode (like `decodeURIComponent`). |
| `$encodeUrl(str)` | URI encode (like `encodeURI`). |
| `$decodeUrl(str)` | URI decode (like `decodeURI`). |

Note: `$eval(expr, context?)` is also a built-in string function that evaluates a JSONata expression string with an optional context object.

### Numeric Functions (12)

| Function | Description |
|----------|-------------|
| `$number(value)` | Cast to number. Strings, booleans, `null` converted. |
| `$abs(n)` | Absolute value. |
| `$floor(n)` | Round down to integer. |
| `$ceil(n)` | Round up to integer. |
| `$round(n, precision?)` | Round to `precision` decimal places. **Uses banker's rounding** (round half to even). `$round(2.5)` = 2, `$round(3.5)` = 4. |
| `$power(base, exp)` | Exponentiation. `$power(2, 3)` = 8. |
| `$sqrt(n)` | Square root. |
| `$random()` | Random float in [0, 1). |
| `$formatNumber(n, picture, options?)` | Format number using picture string (XPath 3.1 spec). `$formatNumber(1234.5, "#,##0.00")` = "1,234.50". |
| `$formatBase(n, radix?)` | Convert number to string in given base (2-36). Default radix 10. |
| `$formatInteger(n, picture)` | Format integer using picture string. `$formatInteger(42, "w")` = "forty-two". Patterns: `0`=digit, `#`=optional digit, `w`=words, `W`=WORDS, `Ww`=Title Words, `A`=letters, `a`=lowercase letters, `I`/`i`=roman. |
| `$parseInteger(str, picture)` | Parse integer from formatted string. Inverse of `$formatInteger`. |

### Aggregation Functions (4)

| Function | Description |
|----------|-------------|
| `$sum(array)` | Sum of numeric values. Ignores non-numeric. |
| `$max(array)` | Maximum value. Works with numbers and strings. |
| `$min(array)` | Minimum value. Works with numbers and strings. |
| `$average(array)` | Arithmetic mean. |

These operate on arrays. With path expressions, they aggregate the mapped results: `$sum(Account.Order.Product.Price)`.

### Boolean Functions (3)

| Function | Description |
|----------|-------------|
| `$boolean(value)` | Cast to boolean. Falsy: `false`, `0`, `null`, `undefined`, `""`, `[]`, `{}`. Everything else truthy. |
| `$not(value)` | Boolean NOT. **This is the only way to negate** -- there is no `!` operator. |
| `$exists(value)` | Returns `true` if value is not `undefined`. Use to test field existence. |

### Array Functions (7)

| Function | Description |
|----------|-------------|
| `$count(array)` | Number of elements. Returns 1 for non-array values, 0 for `undefined`. |
| `$append(arr1, arr2)` | Concatenate arrays. Non-array args wrapped in array first. |
| `$sort(array, comparator?)` | Sort array. Optional `comparator(a, b)` function returns boolean. Default: ascending. |
| `$reverse(array)` | Reverse array order. |
| `$shuffle(array)` | Randomly reorder array elements. |
| `$distinct(array)` | Remove duplicates. Uses deep equality for objects. |
| `$zip(arr1, arr2, ...)` | Zip arrays together. `$zip([1,2],[3,4])` = `[[1,3],[2,4]]`. |

### Object Functions (9)

| Function | Description |
|----------|-------------|
| `$keys(obj)` | Array of field names. |
| `$lookup(obj, key)` | Get field value by name. `$lookup(obj, "field")` equivalent to `obj.field`. |
| `$spread(obj)` | Convert object to array of single-key objects. `$spread({"a":1,"b":2})` = `[{"a":1},{"b":2}]`. |
| `$merge(array)` | Merge array of objects into one. Later values override earlier for same key. |
| `$sift(obj, fn)` | Filter object fields. `fn(value, key)` returns boolean. Keep fields where true. |
| `$each(obj, fn)` | Map over object fields. `fn(value, key)` returns transformed value. Returns array of results. |
| `$error(message?)` | Throw error with message. Halts evaluation. |
| `$assert(condition, message?)` | Throw error if condition is falsy. |
| `$type(value)` | Returns type string: `"null"`, `"number"`, `"string"`, `"boolean"`, `"array"`, `"object"`, `"function"`, or `"undefined"`. |

### Date/Time Functions (4)

| Function | Description |
|----------|-------------|
| `$now()` | Current timestamp as ISO 8601 string. **Frozen per evaluation** -- returns same value throughout. |
| `$millis()` | Current time as Unix milliseconds. **Frozen per evaluation**. |
| `$fromMillis(ms, picture?, timezone?)` | Convert milliseconds to formatted string. Default ISO 8601. Picture uses XPath date format (`[Y0001]-[M01]-[D01]`). |
| `$toMillis(str, picture?)` | Parse date string to milliseconds. Default parses ISO 8601. |

#### XPath Date/Time Picture Symbols

| Symbol | Meaning | Example |
|--------|---------|---------|
| `[Y0001]` | Year (4 digit) | 2024 |
| `[M01]` | Month (2 digit) | 03 |
| `[D01]` | Day (2 digit) | 15 |
| `[H01]` | Hour 24h (2 digit) | 14 |
| `[h1]` | Hour 12h | 2 |
| `[m01]` | Minute (2 digit) | 30 |
| `[s01]` | Second (2 digit) | 45 |
| `[f001]` | Millisecond (3 digit) | 123 |
| `[P]` | AM/PM | PM |
| `[Z]` | Timezone | +05:00 |
| `[ZN]` | Timezone name | EST |
| `[FNn]` | Day name | Monday |
| `[MNn]` | Month name | March |

### Higher-Order Functions (5)

| Function | Description |
|----------|-------------|
| `$map(array, fn)` | Map function over array. `fn(value, index, array)`. Equivalent to `array ~> $map(fn)`. |
| `$filter(array, fn)` | Filter array. `fn(value, index, array)` returns boolean. |
| `$single(array, fn?)` | Returns the one matching element. Errors if 0 or 2+ match. Optional `fn` predicate. |
| `$reduce(array, fn, init?)` | Reduce array. `fn(accumulator, value, index, array)`. |
| `$sift(obj, fn)` | Filter object entries. `fn(value, key)`. (Also listed under Object.) |

#### Common Higher-Order Patterns

```jsonata
/* Map */
Account.Order ~> $map(function($order) { $order.Product })

/* Filter */
Account.Order ~> $filter(function($o) { $o.Price > 100 })

/* Reduce to sum */
[1,2,3,4] ~> $reduce(function($acc, $val) { $acc + $val }, 0)

/* Chain map + filter */
data ~> $map(fn1) ~> $filter(fn2) ~> $sort(fn3)

/* Partial application */
$map(data, $substringBefore(?, "@"))
```

---

## Platform Functions (182+)

All custom bindings registered by the Fuuz platform. Organized by package.

### Core -- General Utility

| Function | Description |
|----------|-------------|
| `$cuid()` | Generate a CUID (collision-resistant unique identifier). |
| `$uuid()` | Generate a V4 UUID. |
| `$throw(message, info?)` | Throw a `ThrowBindingError`. `info` object attached to error's data property. |
| `$coalesce(array)` | Return first non-null/undefined value from array. |
| `$identity(value)` | Return value unchanged (identity function). |
| `$tryCatch(fn, catchFn)` | Wrap `fn` in try/catch; `catchFn(error, ...originalArgs)` called on error. Returns wrapped function. |
| `$nullIf(value, compare)` | Return `null` if value equals compare; otherwise return value. |
| `$wait(ms)` | Pause execution for `ms` milliseconds. |
| `$retry(fn, options?, failedFn?)` | Return function that retries `fn` up to `retries` times (default 5) with `delay` ms (default 1000). Optional `failedFn` runs on each failure. |
| `$cronConverter(cronString, timezone?)` | Parse cron string, return cron-converter object. Default timezone `"Etc/UTC"`. |

### Core -- Type Predicates

| Function | Description |
|----------|-------------|
| `$isArray(value)` | True if value is an array. |
| `$isString(value)` | True if value is a string. |
| `$isBoolean(value)` | True if value is a boolean. |
| `$isInteger(value)` | True if value is a safe integer. |
| `$isFloat(value)` | True if value is a finite float (integers count). |
| `$isNumber(value)` | True if value is a finite number. |
| `$isObject(value)` | True if value is a plain object. |
| `$isUndefined(value)` | True if value is undefined. |
| `$isNil(value)` | True if value is null or undefined. |
| `$isNaN(value)` | True if value is NaN. |
| `$isEmpty(value)` | True if empty array, object, or string. |
| `$isNilOrEmpty(value)` | True if undefined, null, or empty array/object/string. |
| `$isNotNilOrEmpty(value)` | Logical NOT of `$isNilOrEmpty`. |
| `$pathSatisfies(object, path, fn)` | True if predicate `fn` returns truthy for value at dot-path. |

### Core -- Conversion

| Function | Description |
|----------|-------------|
| `$arrayToCsv(array, options?)` | Convert array of objects to CSV string. Options per json-2-csv. |
| `$csvToArray(string, options?)` | Convert CSV string to array of objects. |
| `$epcToUrn(epc, options?)` | Convert hex RFID EPC to URN object `{companyPrefix, assetType, serialNum, urn}`. Default standard GRAI96. |
| `$numeral(value)` | Create numeral.js object for number formatting/parsing. |
| `$parseInt(string, base?)` | Parse string to integer with radix (default 10). Returns undefined on failure. |
| `$parseNumber(value)` | Parse value to number via numeral.js. Returns undefined on failure. |
| `$decimalToString(array, encoding?)` | Decode byte array to string. Default encoding `"utf-8"`. |
| `$numberToWords(number, locale?, options?)` | Number to written words. Supports currency options. Default locale `"en-US"`. |
| `$jsonToJsonSchema(json, options?)` | Generate JSON Schema from JSON. Options: `{required, arrays: {mode: "all"|"first"|"tuple"|"uniform"}}`. |
| `$jsonToMarkdown(array)` | Convert JSON array to markdown via json2md format. |

### Core -- String

| Function | Description |
|----------|-------------|
| `$startsWith(string, pattern)` | True if string starts with pattern. |
| `$endsWith(string, pattern)` | True if string ends with pattern. |
| `$camelCase(string)` | Convert to lowerCamelCase. |
| `$snakeCase(string)` | Convert to snake_case. |
| `$kebabCase(string)` | Convert to kebab-case. |
| `$startCase(string)` | Convert to Start Case. |
| `$lowerFirst(string)` | Lowercase first character. |
| `$upperFirst(string)` | Uppercase first character. |
| `$pluralize(word, locale)` | Pluralize word using grammatical rules. |
| `$singularize(word, locale)` | Singularize word using grammatical rules. |
| `$htmlEscape(string)` | Escape `&<>"'` to HTML entities. |
| `$htmlUnescape(string)` | Convert HTML entities back to characters. |
| `$uriEscape(string)` | URI encode (preserves `?=/&`). Uses `encodeURI`. |
| `$uriUnescape(string)` | URI decode. Uses `decodeURI`. |
| `$uriEscapeComponent(string)` | Full URI component encode. Uses `encodeURIComponent`. |
| `$uriUnescapeComponent(string)` | Full URI component decode. Uses `decodeURIComponent`. |
| `$convertCharacterSet(string, target, source?)` | Re-encode string between character encodings. Default source `"utf8"`. |
| `$parseUrl(string)` | Parse URL to object `{protocol, host, pathname, query, ...}`. |
| `$urlStringify(params, options?)` | Object to querystring. Options per `qs` npm package. |
| `$urlJoin(array)` | Join URL path segments, normalizing slashes. |
| `$removeLeadingValues(string, value)` | Remove all leading occurrences of value. |
| `$replaceLeadingValues(string, value, replacement)` | Replace all leading occurrences of value with replacement. |
| `$getContrastText(backgroundColor)` | Return `"#212121"` or `"#fff"` for contrast against background color. |
| `$evalHandlebarsTemplate(template, fields, options?)` | Evaluate Handlebars template `{{placeholders}}` with field values. |
| `$parseExpression(string)` | Parse JSONata expression to AST. |
| `$parseGraphQLStatement(string)` | Parse GraphQL statement to AST. |

### Core -- Rich Text / Markdown

| Function | Description |
|----------|-------------|
| `$markdownToHtml(string)` | Convert markdown to HTML. |
| `$richTextToMarkdown(draftContent)` | Convert RawDraftContent (Draft.js) to markdown. |
| `$markdownToRichText(string)` | Convert markdown to RawDraftContent. |
| `$plainTextToRichText(string)` | Convert plain text to RawDraftContent. |
| `$richTextToPlainText(draftContent)` | Convert RawDraftContent to plain text. |
| `$richTextToHTML(draftContent)` | Convert RawDraftContent to HTML. |

### Core -- Object

| Function | Description |
|----------|-------------|
| `$path(object, path)` | Get value at dot-path or path array. |
| `$omit(object, keys)` | Return object without specified keys. |
| `$pick(object, keys)` | Return object with only specified keys. |
| `$values(object)` | Return array of all values. |
| `$has(object, property)` | True if property exists on object. |
| `$toPairs(object)` | Convert to `[[key, value], ...]` pairs. |
| `$fromPairs(pairs)` | Convert `[[key, value], ...]` to object. |
| `$mapValues(object, fn)` | Map over values. `fn(value, key, obj)`. |
| `$mapKeys(object, fn)` | Map over keys. `fn(value, key, obj)`. |
| `$mergeDeep(array)` | Recursive deep merge of objects array. Arrays at same path concatenated. |
| `$jsonParse(string)` | Parse JSON string to value. |
| `$jsonStringify(value, options?)` | Serialize to JSON string. Options: `{space}` for indentation. |
| `$flattenObject(object, options?)` | Flatten nested object to dot-keyed single level. Options: `{delimiter, safe, maxDepth}`. |
| `$unflattenObject(object, options?)` | Reverse flatten. Options: `{delimiter, object, overwrite}`. |
| `$objectDiff(before, after, options?)` | Compare two objects. With `{detailed: true}` returns `{added, deleted, updated}`. `{ignore: [...]}` excludes paths. |

### Core -- Array

| Function | Description |
|----------|-------------|
| `$chunk(array, size)` | Split into groups of `size`. Last chunk may be smaller. |
| `$uniq(array)` | Remove duplicates (reference equality for objects). |
| `$flatten(array, depth?)` | Flatten nested arrays to `depth` (default 1). |
| `$first(array)` | First element. |
| `$last(array)` | Last element. |
| `$takeFirst(array, n?)` | First n elements. |
| `$takeLast(array, n?)` | Last n elements. |
| `$dropFirst(array, n)` | All but first n elements. |
| `$dropLast(array, n)` | All but last n elements. |
| `$partition(array, predFn)` | Split into `[matches, nonMatches]`. |
| `$groupBy(array, keyFn)` | Group by key function result. Returns `{key: [items]}`. |
| `$indexBy(array, keyFn)` | Like `$groupBy` but keeps only last item per key. |
| `$uniqBy(array, keyFn)` | Remove duplicates by key function. Keeps first occurrence. |
| `$findFirst(array, predFn)` | First element where predFn returns true. |
| `$findLast(array, predFn)` | Last element where predFn returns true. |
| `$findFirstIndex(array, predFn)` | Index of first match. |
| `$findLastIndex(array, predFn)` | Index of last match. |
| `$flatMap(array, fn)` | Map then flatten one level. |
| `$partitionBetween(array, beginFn, endFn, options?)` | Partition into groups bounded by begin/end predicates. Options: `{includeEndElement}`. |
| `$all(array, predFn)` | True if ALL elements satisfy predicate. |
| `$any(array, predFn)` | True if ANY element satisfies predicate. |

### Core -- Parallel Execution

For async operations. Default parallelism is 8.

| Function | Description |
|----------|-------------|
| `$mapParallel(array, mapFn, parallelism?)` | Map with concurrent execution. |
| `$mapValuesParallel(object, mapFn, parallelism?)` | Map object values with concurrent execution. |
| `$filterParallel(array, filterFn, parallelism?)` | Filter with concurrent predicate evaluation. |
| `$groupByParallel(array, keyFn, parallelism?)` | Group by key with concurrent key-function calls. |
| `$timesParallel(count, fn, parallelism?)` | Execute function N times in parallel (functional for-loop). |

### Core -- Predicate Filters

| Function | Description |
|----------|-------------|
| `$predicateFilter(payload, predicates, options?)` | Filter using GraphQL-style operators (`_eq`, `_in`, `_gt`, `_lt`, `_contains`, etc.). Default case-insensitive. Options: `{caseSensitiveComparison}`. |

### Core -- Schema Validation

| Function | Description |
|----------|-------------|
| `$validateSchema(value)` | True if value is a valid JSON Schema. |
| `$validateJson(data, schema)` | Validate data against JSON Schema. Returns data if valid; throws on invalid. |
| `$isValidJson(data, schema)` | True if data matches schema; false otherwise. |

### Core -- Tree

| Function | Description |
|----------|-------------|
| `$visitPreorder(treeOrForest, visitor)` | Preorder traversal of nested objects. `visitor(node, branchName, parentNode, parentBranchName)` can transform nodes. |

### Semver (28 functions)

Semantic versioning via the `semver` npm package. All functions accept optional `loose?` boolean for lenient parsing.

**Parsing:** `$semverParse(v)`, `$semverValid(v)`, `$semverClean(v)`, `$semverCoerce(v, opts)`

**Components:** `$semverMajor(v)`, `$semverMinor(v)`, `$semverPatch(v)`, `$semverPrerelease(v)`

**Mutation:** `$semverInc(v, release, loose?, id?)` -- increment by `major|minor|patch|premajor|preminor|prepatch|prerelease`

**Diff:** `$semverDiff(v1, v2)` -- returns diff type string or null

**Comparison:** `$semverCompare(v1, v2)`, `$semverRcompare(v1, v2)`, `$semverCompareLoose(v1, v2)`, `$semverCompareBuild(v1, v2)`, `$semverGt(v1, v2)`, `$semverLt(v1, v2)`, `$semverEq(v1, v2)`, `$semverNeq(v1, v2)`, `$semverGte(v1, v2)`, `$semverLte(v1, v2)`, `$semverCmp(v1, op, v2)`

**Sorting:** `$semverSort(versions)`, `$semverRsort(versions)`

**Ranges:** `$semverSatisfies(v, range)`, `$semverMaxSatisfying(versions, range)`, `$semverMinSatisfying(versions, range)`, `$semverMinVersion(range)`, `$semverValidRange(range)`, `$semverOutside(v, range, hilo)`, `$semverGtr(v, range)`, `$semverLtr(v, range)`, `$semverIntersects(r1, r2)`, `$semverSimplifyRange(versions, range)`, `$semverRangeSubset(sub, super)`, `$semverToComparators(range, v)`

### Joins (5 functions)

SQL-style joins between arrays. Accessors can be a string (field name), array of strings (composite key), or function.

| Function | Description |
|----------|-------------|
| `$crossJoin(left, right, joinedKey, options?)` | Every left paired with every right. Options: `{grouped}`. |
| `$innerJoin(left, right, leftAccessor, rightAccessor, joinedKey, options?)` | Only rows with matches in both sides. |
| `$leftOuterJoin(left, right, leftAccessor, rightAccessor, joinedKey, options?)` | All left rows; right matches where available. |
| `$leftSemiJoin(left, right, leftAccessor, rightAccessor)` | Left rows that have a right match (no right data merged). |
| `$leftAntiJoin(left, right, leftAccessor, rightAccessor)` | Left rows that have NO right match. |

### Moment.js (7 functions)

Full moment.js API available on returned objects (`.format()`, `.add()`, `.diff()`, `.isBefore()`, etc.).

| Function | Description |
|----------|-------------|
| `$moment(value?, format?, localeOrStrict?, strict?)` | Create moment instance. Accepts date strings, arrays, objects. |
| `$momentDuration(value?, unitOfTime?)` | Create duration instance. Supports ISO 8601 (`"PT8H"`), numbers, objects. |
| `$momentTz(date?, format?, timezone?)` | Create moment with specific timezone. |
| `$momentTzTimezones()` | List all available IANA timezone names. |
| `$momentTzGuess(ignoreCache?)` | Guess user's timezone. Returns `"UTC"` on backend. |
| `$momentMax(values, ignoreInvalidDates?)` | Most future moment from array. |
| `$momentMin(values, ignoreInvalidDates?)` | Most past moment from array. |

#### Common Moment Patterns

```jsonata
$moment().format("YYYY-MM-DD")                           /* Current date */
$moment(dateStr).add(7, "days").toISOString()             /* Add 7 days */
$moment(end).diff($moment(start), "hours")                /* Hours between */
$momentDuration("PT1H30M").humanize()                     /* "2 hours" */
$momentTz(dateStr, "YYYY-MM-DD", "America/New_York")      /* Parse in timezone */
```

### XML (2 functions)

| Function | Description |
|----------|-------------|
| `$jsonToXml(data, options?)` | Convert JSON to XML string. Options per xml2js Builder. |
| `$xmlToJson(xmlString, options?)` | Parse XML to JSON. Options per xml2js parser. |

### EDI (6 functions)

| Function | Description |
|----------|-------------|
| `$parseX12(string)` | Parse X12 EDI string to JSON object. |
| `$formatX12(array)` | Format parsed X12 array to structured JSON. |
| `$readEDI(format, data, options)` | Read EDI documents. Format: `"x12"` or `"edifact"`. |
| `$writeEDI(format, data, options)` | Write EDI documents. |
| `$validateEDI(format, data, options)` | Validate EDI documents. |
| `$ackEDI(format, data, options)` | Generate EDI acknowledgments. |

### Encryption (2 functions)

| Function | Description |
|----------|-------------|
| `$encryptHmac(algorithm, key, string, encoding?)` | HMAC digest. Algorithm: OpenSSL method (e.g., `"sha256"`). Default encoding `"base64"`. |
| `$hash(algorithm, string, encoding?)` | Hash string. Default encoding `"base64"`. |

### Network (1 function)

| Function | Description |
|----------|-------------|
| `$http(method, env, body?)` | HTTP request. Method: `"get"|"post"|"put"|"patch"|"delete"|"head"|"options"`. `env`: http-client config object. Returns base64-encoded response body. |

### Calendars (6 functions)

| Function | Description |
|----------|-------------|
| `$eventsBetween(start, end, calendar, options?)` | Events between two moments. Options: `{includeStart, includeEnd, assignOccurrenceIds}`. |
| `$getEventEnd(event, after?)` | End of first (or next after `after`) occurrence. |
| `$getEventsAt(moment, calendar)` | Events occurring at a specific moment. |
| `$getAvailabilityBetween(start, end, calendar, options?)` | Events categorized by availability. Options: `{exact}`. |
| `$availableAt(moment, calendar)` | True if any available event at the moment. |
| `$getTimeline(start, end, calendar, options?)` | Timeline for a time range on calendar. |

### Scheduling (2 functions)

| Function | Description |
|----------|-------------|
| `$scheduleGroup(id)` | Get schedule group. Methods: `.getCurrentSchedules()`, `.getTimeline()`, `.getEventsIntersections()`, `.getAvailabilityBetween()`, `.getCalendarIntervals()` |
| `$schedule(id)` | Get individual schedule. Methods: `.getAvailabilityBetween()`, `.getTimeline()`, `.getEventsIntersections()`, `.getEventsBetween()`, `.getExceptionsBetween()`, `.getCalendarIntervals()` |

### Unit Conversion (3 functions)

| Function | Description |
|----------|-------------|
| `$convertUnit(fromUnitId, toUnitId, value)` | Convert between units of same type. Also works as `[1,5,10].$convertUnit("km","mi")`. |
| `$getConversionFactor(fromUnitId, toUnitId)` | Get conversion factor between units (equivalent to converting 1). |
| `$getUnit(identifier)` | Retrieve Unit record by `{id}`, `{code}`, or `{name}` (case-insensitive). |

### MFGx Application Functions (12 functions)

Available in data flows and screen element transforms that have app context.

| Function | Description |
|----------|-------------|
| `$query(input)` | Execute GraphQL query. Input: `{api?, statement, variables}`. API defaults to APPLICATION. Returns `data` property. |
| `$mutate(input)` | Execute GraphQL mutation. Same interface as `$query`. |
| `$integrate(data)` | Execute integration connector. Input: `{connectionName, options?, payload[]}`. Options: `{degreeOfParallelism?, returnErrors?}`. Payload is an array of request items validated against the connector's inbound schema. Returns array of responses in same order. See `integration-service-reference.md` for all 42 connector schemas. |
| `$document(payload, options?)` | Render document via `renderDocument` mutation. Options: `{returnContent}`. |
| `$executeFlow(flowId, payload?)` | Execute orchestration flow by ID with optional payload. |
| `$executeTransform(savedTransformId, payload, bindings?)` | Execute saved transform with payload and optional bindings. |
| `$executeDataMapping(dataMappingId, payload)` | Execute data mapping by ID. |
| `$executeSavedQuery(savedQueryId, parameters?)` | Execute saved query by ID with parameters. |
| `$getCalendar(queryInput)` | Fetch calendar by ID or name. |
| `$addNotificationMessage(title, options?)` | Show UI notification. Options: `{message, data, severity, snackbar}`. Severity: `"info"|"success"|"warning"|"error"`. |
| `$executeDeviceFunction(payload)` | Execute device function. Returns `{requestId, deviceGatewayId, response, error}`. |
| `$executeDeviceGatewayFunction(payload)` | Execute device gateway function. Returns `{requestId, response, error}`. |

#### Common MFGx Patterns

```jsonata
/* Query data */
$query({
  "statement": "query($filter: JSON) { WorkOrder(filter: $filter) { edges { id name status } } }",
  "variables": { "filter": { "status": { "_eq": "Active" } } }
}).WorkOrder.edges

/* Mutate data */
$mutate({
  "statement": "mutation($input: UpdateWorkOrderInput!) { updateWorkOrder(input: $input) { workOrder { id } } }",
  "variables": { "input": { "id": id, "status": "Complete" } }
})

/* Execute flow */
$executeFlow("flow-id-here", { "orderId": id })

/* Call external API via integration connector */
$integrate({
  "connectionName": "My HTTP Connection",
  "options": { "degreeOfParallelism": 10, "returnErrors": true },
  "payload": [
    { "method": "get", "path": "/api/users" },
    { "method": "post", "path": "/api/orders", "data": { "item": "Widget", "qty": 5 } }
  ]
})

/* Show notification */
$addNotificationMessage("Success", { "severity": "success", "message": "Record saved" })

/* Logging */
$log("Processing item", { "id": id, "status": status })
```

### Frontend-Only Functions

Available only in browser context (screen transforms, onChange).

#### Clipboard

| Function | Description |
|----------|-------------|
| `$writeToClipboard(data)` | Write data to system clipboard. |
| `$readFromClipboard()` | Read data from system clipboard. |

#### PDF / File Operations

| Function | Description |
|----------|-------------|
| `$pdfToJson(base64String, options?)` | Convert base64 PDF to JSON representation. |
| `$mergePDFs(pdfArray)` | Merge array of base64 PDFs into one. Returns base64. |
| `$rotatePDF(base64String, angle)` | Rotate all pages by angle (multiple of 90). Returns base64. |

#### Navigation

| Function | Description |
|----------|-------------|
| `$navigateTo(path)` | Navigate to a screen path. |
| `$navigateBack()` | Navigate back in history. |
| `$navigateReload()` | Reload current screen. |
| `$log(message, meta?, level?)` | Console log with level. Levels: `"error"|"warn"|"info"|"verbose"|"debug"|"silly"`. Default `"info"`. |

#### Dialogs

| Function | Description |
|----------|-------------|
| `$showAlertDialog(message, options?)` | Show alert dialog. |
| `$showConfirmDialog(message, options?)` | Show confirmation dialog (returns boolean). |
| `$showFormDialog(formProps)` | Show form dialog. |
| `$showScreenDialog(props)` | Show screen dialog. |

#### File Retrieval

| Function | Description |
|----------|-------------|
| `$retrieveFileContent(fileId, encoding?)` | Download and decode a file by ID. |

---

## Programming Constructs

### Comments

```jsonata
/* Block comments only — no line comments */
Account.Order.(
  /* Calculate line total */
  Price * Quantity
)
```

### Variable Binding

Variables are bound with `:=` and prefixed with `$`. Scoped to the block they are defined in.

```jsonata
(
  $price := Product.Price;
  $tax := $price * 0.1;
  $price + $tax
)
```

**Block expressions** use `( )` with `;` separating statements. The last expression is the result.

```jsonata
(
  $name := FirstName & " " & LastName;
  $upper := $uppercase($name);
  $upper
)
```

### Context References

| Symbol | Meaning |
|--------|---------|
| `$` | Current input/scope (changes as you navigate into objects) |
| `$$` | Root input -- always refers to the top-level input, regardless of navigation depth |

```jsonata
Account.Order.Product.(
  $$.Account.Name & ": " & Description
  /* $$ reaches back to root to get Account.Name */
)
```

### Conditional Expressions

#### Ternary

```jsonata
status = "active" ? "Active" : "Inactive"
```

Falsy values: `false`, `null`, `undefined`, `0`, `""`, `[]`, `{}`.

#### Chained conditions (if/else if/else pattern)

```jsonata
(
  status = "active" ? "Active" :
  status = "pending" ? "Pending" :
  "Unknown"
)
```

#### Elvis Operator `?:`

Returns left side if truthy, otherwise right side. Note: **no space** between `?` and `:`.

```jsonata
name ?: "Anonymous"    /* "Anonymous" if name is falsy */
```

#### Null Coalescing `??`

Returns left side if not `null`/`undefined`, otherwise right side. Unlike `?:`, keeps `0`, `false`, `""`.

```jsonata
count ?? 0        /* 0 only if count is null/undefined */
count ?: 0        /* 0 if count is null/undefined/0/false/"" */
```

### Lambda Functions

```jsonata
/* Named lambda */
$fn := function($x, $y) { $x + $y };

/* Inline in higher-order functions */
$map(data, function($item) { $item.name })

/* Short form — lambda with implicit parameter via path */
Account.Order.(Price * Quantity)
```

#### Function Signatures (Type Constraints)

Optional type annotations on lambda parameters:

```jsonata
$fn := function($x, $y)<nn:n> { $x + $y }
```

Signature codes: `s`=string, `n`=number, `b`=boolean, `o`=object, `a`=array, `f`=function, `j`=JSON, `x`=any, `l`=null, `u`=undefined. `?`=optional. `<params:return>`.

### Closures

Functions capture variables from their enclosing scope:

```jsonata
(
  $multiplier := 3;
  $multiply := function($x) { $x * $multiplier };
  $multiply(5)  /* 15 */
)
```

### Partial Application

Use `?` as a placeholder to create a partially applied function:

```jsonata
$add := function($x, $y) { $x + $y };
$add5 := $add(5, ?);
$add5(3)  /* 8 */
```

Works with built-in functions too:

```jsonata
$trim := $substringBefore(?, "@");
emails ~> $map($trim)
```

### Function Chaining (`~>`)

Pipe operator passes the left result as the first argument of the right function:

```jsonata
Account.Order.Product ~>
  $filter(function($p) { $p.Price > 50 }) ~>
  $map(function($p) { $p.Description }) ~>
  $sort() ~>
  $join(", ")
```

Equivalent to nested calls:

```jsonata
$join($sort($map($filter(Account.Order.Product, ...), ...), ...))
```

### Regular Expressions

JSONata uses JavaScript regex syntax:

```jsonata
/pattern/flags
```

Flags: `i` (case-insensitive), `m` (multiline), `g` (global -- only in `$match`/`$replace`).

```jsonata
$contains(name, /^[A-Z]/)           /* Starts with uppercase */
$match(text, /(\d+)-(\w+)/g)        /* All matches with groups */
$replace(str, /\s+/g, "-")          /* Replace whitespace with dash */
$split(csv, /,\s*/)                 /* Split on comma + optional space */
```

Regex in predicates:

```jsonata
Phone[$contains(number, /^07/)]     /* Filter phones starting with 07 */
```

### Recursive Processing

JSONata supports tail-call optimization for recursive lambdas:

```jsonata
(
  $factorial := function($n) {
    $n <= 1 ? 1 : $n * $factorial($n - 1)
  };
  $factorial(5)  /* 120 */
)
```

**Important:** JSONata optimizes tail-recursive calls to prevent stack overflow. Ensure the recursive call is in tail position when processing large datasets.

### Transform Operator in Chaining

Create modified copies of data without mutation:

```jsonata
/* Update fields */
$ ~> | Account | {"Status": "Active"} |

/* Remove fields */
$ ~> | Account.Order | {} | ["InternalId"] |

/* Nested updates with path reference */
$ ~> | Account.Order.Product | {"Price": Price * 1.1} |
```

Combine with chaining for multi-step transforms:

```jsonata
$ ~> | Account | {"Name": $uppercase(Name)} |
  ~> | Account.Order | {"Total": Price * Quantity} |
```

### Common Patterns

#### Null-Safe Navigation

```jsonata
Account.Order.Product.Price          /* undefined if any step missing */
$exists(Account.Order) ? Account.Order.Product : "N/A"
```

#### Default Values

```jsonata
(Account.Name ?? "Unknown")          /* Null coalescing */
(Account.Name ?: "Unknown")          /* Elvis (also covers empty string) */
```

#### Conditional Object Construction

```jsonata
{
  "name": Name,
  "email": Email,
  "phone": $exists(Phone) ? Phone : undefined
  /* undefined fields are omitted from result */
}
```

#### Array Aggregation with Grouping

```jsonata
Account.Order.Product.{
  Description: {
    "total": $sum(Price),
    "count": $count(Price)
  }
}
```

#### Dynamic Field Names

```jsonata
$fieldName := "status";
{ $fieldName: "active" }    /* {"status": "active"} */
```

---

## Platform Evaluation Contexts

JSONata expressions are evaluated in two distinct contexts: **Screen Elements** (frontend) and **Data Flows** (backend). Each provides different context variables.

### Context Differences Summary

| Feature | Screen Elements | Data Flows |
|---------|----------------|------------|
| Root `$` | Component data (mapPropsToPayload) | Node's current payload |
| `metadata` | User, tenant, settings, URL params | Not directly available |
| `$components` | Screen component shared state | Not available |
| `$state` | Not available | Full flow state (payload, context, claims, batches) |
| `$appConfig` | Application configuration | Not available |
| UI functions | `$navigateTo`, `$showAlertDialog`, etc. | Not available |
| Evaluation | Local (browser) or remote (`__remote: true`) | Always server-side |

### Where JSONata is Used

| Context | Usage | Available Bindings |
|---------|-------|--------------------|
| Data Flow Transform nodes | Transform payloads between nodes | Core + Moment + XML + EDI + Encryption + Network + Units + Calendars + Joins + Semver + MFGx App |
| Screen Element transform props | Dynamic property evaluation | Core + Moment + $components + metadata |
| Screen Element onChange | React to user input | Core + Moment + $components + metadata + MFGx App |
| Validation expressions | Form/field validation | Core + Moment + $components + metadata |
| Table column data/style | Dynamic column rendering | Core + Moment + $components + metadata |

---

## Screen Element Context

Used in transform props, onChange handlers, validation expressions, table column data/style.

### Root Input (`$`)

The root `$` is the component-specific data, assembled by each component's `mapPropsToPayload`. Varies by component type:
- **Form inputs**: current form field values
- **Table columns**: current row data
- **Action buttons**: data context of the triggering element

### `metadata` -- Platform Metadata

Available in all frontend JSONata expressions. Assembled from Redux state.

| Path | Description |
|------|-------------|
| `metadata.user` | Current user object -- `.id`, `.firstName`, `.lastName`, `.email`, `.environments`, `.accessTypeId` |
| `metadata.tenant` | Current tenant -- `.id`, `.name`, `.tenantTypeId` |
| `metadata.userTenants` | All tenants the user belongs to -- `[].id`, `[].tenant.id`, `[].tenant.name` |
| `metadata.userRoles` | Roles -- `[].role.id`, `[].role.name`, `[].role.description`, `[].role.homeScreenId` |
| `metadata.screen` | Current screen metadata |
| `metadata.environment` | Environment name (from enterprise) |
| `metadata.platformVersion` | Platform version string |
| `metadata.settings` | Localization: `.DateFormat`, `.TimeFormat`, `.DateTimeFormat`, `.IntegerFormat`, `.FloatFormat`, `.Locale`, `.TimeZone`, `.AlwaysDisplayTimeZones` |
| `metadata.enterprise` | Enterprise -- `.id`, `.name`, `.domain`, `.maintenanceMode`, `.platformVersion`, `.environment` |
| `metadata.urlParameters` | URL path parameters (e.g., `:id` from `/screens/:id`) |
| `metadata.querystring` | URL query string parameters |

### `$components` -- Screen Component Shared State

Hierarchical store of all named screen components. Each component registers:

```
$components.<Name>.data     — Component's current data
$components.<Name>.fn       — Component's callable functions
$components.<Name>.context  — Component-specific context (e.g., row data in tables)
```

#### Table Component (`$components.MyTable`)

| Path | Description |
|------|-------------|
| `.data` | Current table row data |
| `.fn.loadData({filter, rowLimit})` | Reload data from backend |
| `.fn.search({rowsPerPage})` | Search with filter form |
| `.fn.setRows(array)` | Overwrite all rows |
| `.fn.addRows(array)` | Append rows |
| `.fn.updateRows(array)` | Update existing rows by id |
| `.fn.upsertRows(array)` | Add or update rows by id |
| `.fn.deleteRows(array)` | Delete rows by id |
| `.fn.selectRow(array)` | Select rows |
| `.fn.deselectRow(array)` | Deselect rows |
| `.fn.toggleRowSelection(array)` | Toggle selection |
| `.fn.selectAllRows()` | Select all rows |
| `.fn.deselectAllRows()` | Deselect all rows |
| `.fn.selectAllFiltered()` | Select all filtered rows |
| `.fn.deselectAllFiltered()` | Deselect all filtered rows |
| `.fn.setSelectedRows(array)` | Set exact selection |
| `.fn.setData(data)` | Set table data directly |

#### Form Component (`$components.MyForm`)

| Path | Description |
|------|-------------|
| `.data` | Current form field values |
| `.fn.setValue(field, value)` | Set a field value |
| `.fn.focus(field)` | Focus a field |
| `.fn.blur(field)` | Blur a field |
| `.fn.loadData()` | Reload form data |
| `.fn.validate()` | Validate and return errors |
| `.fn.save(saveAll?)` | Save the form |
| `.fn.delete()` | Delete form data |
| `.fn.disableField(field)` | Disable a field |
| `.fn.enableField(field)` | Enable a field |
| `.fn.getUpdateMutation()` | Get GraphQL mutation |
| `.fn.getUpdatePayload()` | Get mutation variables |
| `.fn.getVariables()` | Get all form variables |

#### Container Component (`$components.MyContainer`)

| Path | Description |
|------|-------------|
| `.fn.hide()` | Hide the container |
| `.fn.show()` | Show the container |
| `.fn.toggle()` | Toggle visibility |
| `.fn.resetHidden()` | Reset hidden fields |
| `.fn.collapse()` | Collapse the container |
| `.fn.expand()` | Expand the container |

#### Screen Element (`$components.MyScreen`)

| Path | Description |
|------|-------------|
| `.context` | Screen context data |
| `.fn.hide()` | Hide |
| `.fn.show()` | Show |
| `.fn.toggle()` | Toggle visibility |
| `.fn.showLoading()` | Show loading indicator |
| `.fn.hideLoading()` | Hide loading indicator |
| `.fn.executePageLoadAction()` | Re-execute page load action |

#### TabBar Component (`$components.MyTabBar`)

| Path | Description |
|------|-------------|
| `.tabIndex` | Current tab index (number) |
| `.tabs` | Array of tab context objects |
| `.currentTab` | Current tab context object |
| `.fn.setTab(index)` | Switch to tab by index |
| `.fn.enableTab(index)` | Enable a tab |
| `.fn.disableTab(index)` | Disable a tab |
| `.fn.showTab(index)` | Show a tab |
| `.fn.hideTab(index)` | Hide a tab |

#### Widget Element (`$components.MyWidget`)

| Path | Description |
|------|-------------|
| `.fn.setParams(params)` | Set parameters for the embedded screen |

#### Action / Flow / Split Button (`$components.MyButton`)

| Path | Description |
|------|-------------|
| `.fn.execute(payload?)` | Execute the button's action with optional payload |

#### Cards Component (`$components.MyCards`)

| Path | Description |
|------|-------------|
| `.data` | Array of card data objects |
| `.fn.loadData()` | Load/reload card data |
| `.fn.search()` | Search cards using filter form |

### `$appConfig` -- Application Configuration

The tenant's application configuration object. Available in all local (non-remote) frontend evaluations.

### `showScreenDialog` -- Dialog Function

Opens a screen in a dialog. Available in screen element context.

### Frontend-Only Functions Summary

| Function | Description |
|----------|-------------|
| `$navigateTo(pathname, options?)` | Navigate to URL. Options: `{newWindow, replace}` |
| `$navigateBack()` | Navigate back in history |
| `$navigateReload()` | Reload current screen |
| `$showAlertDialog(message, options?)` | Show alert dialog |
| `$showConfirmDialog(message, options?)` | Show confirmation dialog (returns boolean) |
| `$showFormDialog(formProps)` | Show form dialog |
| `$showScreenDialog(props)` | Show screen dialog |
| `$retrieveFileContent(fileId, encoding?)` | Download and decode a file by ID |
| `$addNotificationMessage(title, options?)` | Show UI notification |
| `$writeToClipboard(data)` | Write to clipboard |
| `$readFromClipboard()` | Read from clipboard |

---

## Data Flow Context

Used in Transform nodes, Set Context, Merge Context, Conditional nodes, and other data flow nodes.

### Root Input (`$`)

The current node's payload -- `state.payload`. This is the data flowing from the previous node.

### `$state` -- Flow Execution State

The full accumulated execution state. Available as a binding in all data flow node evaluations.

| Path | Description |
|------|-------------|
| `$state.payload` | Current payload (same as `$`) |
| `$state.context` | User-defined context set by Set Context / Merge Context nodes |
| `$state.claims` | Auth claims of the triggering user |
| `$state.batches` | Batch tracking array (Fork/Broadcast/Combine) |
| `$state.messageId` | Unique message ID for this flow execution |
| `$state.metadata` | Flow metadata (tenantId, etc.) |
| `$state.depth` | Current execution depth counter |
| `$state.nextNodes` | Array of next node IDs to execute |
| `$state.trace` | Trace entries (when tracing enabled) |
| `$state.lastError` | Error from last caught error (with catchErrorNode) |
| `$state.catchErrorNode` | ID of the error handler node |

#### `$state.context`

Free-form object that accumulates data across the flow:
- **Set Context** nodes replace the entire context
- **Merge Context** nodes deep-merge into the existing context
- Access as `$state.context.anyPath`
- Primary mechanism for passing data between nodes outside the payload

#### `$state.claims`

Authentication claims of the user or system that triggered the flow. Available for security/authorization logic.

#### `$state.lastError`

When using Try-Catch error handling: `{name, message, stack, info, node}` from the last failed node.

#### `$state.metadata`

Flow metadata: `{tenantId, enterpriseId, flowId}`.

### Node Types and JSONata Results

| Node Type | What happens with JSONata result |
|-----------|--------------------------------|
| JSONata (transform) | Result becomes new `payload` |
| If Else | Result (boolean) determines output port |
| Route | Result determines which branches to route to |
| Accept / Reject | Result (boolean) determines whether to continue |
| Set Context | Result replaces the entire `$state.context` |
| Merge Context | Result is deep-merged into `$state.context` |
| Remove From Context | Result (array of paths) removes keys from context |
| Response | Result becomes the response payload |
| Log | Result is logged as the message |

---

## Transform Props Pattern

Screen element props that accept JSONata use this object structure:

```json
{
  "__transform": "expression or { id: savedTransformId }",
  "__remote": false,
  "__language": "JSONata",
  "__dynamicFields": {
    "payload": ["fieldA", "fieldB"],
    "context": ["metadata.user"]
  },
  "__cacheKey": "unique-key",
  "__refreshInterval": 5000,
  "__fallbackValue": "default"
}
```

| Key | Description |
|-----|-------------|
| `__transform` | JSONata expression string, or `{id}` / `{name}` for saved transform |
| `__remote` | `true` to evaluate server-side (default `false`) |
| `__language` | `"JSONata"` (default) or `"JavaScript"` |
| `__dynamicFields` | Paths in payload/context that the transform depends on. When values at these paths change, the transform re-executes and the component re-renders. Shape: `{ payload: ["data.createdByUser.id"], context: [] }`. Set to `false` to evaluate once only. |
| `__cacheKey` | Unique key for storing the transform result, scoped to the element. |
| `__refreshInterval` | Milliseconds between automatic re-evaluations |
| `__fallbackValue` | Default value while transform is pending |

---

## Evaluation Pipeline Summary

### Frontend (Screen Element)

1. Component has prop with `{ __transform: "...", __remote: false }`
2. `withTransform` HOC detects dynamic props, calls `getExpressionValue()`
3. Assembles: payload (from `mapPropsToPayload`), context (`{metadata, components}`), binding functions
4. Routes to `evaluateLocalExpression` or `evaluateRemoteTransform`
5. Calls `expression.evaluate(payload, { ...context, appConfig, ...bindingFunctions })`
6. `$` = payload, all binding keys available as `$variableName`

### Backend (Data Flow)

1. Data flow node receives `{ context, node, state }`
2. Node calls `dependencies.jsonata.evaluate({ transform, payload: state.payload, bindings: { state } })`
3. Calls `expression.evaluate(payload, { state, ...bindingFunctions })`
4. `$` = current payload, `$state` = full execution state
