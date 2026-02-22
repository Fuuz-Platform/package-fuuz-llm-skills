---
name: fuuz-jsonata-bindings
description: "Reference for all 294 custom JSONata bindings in the Fuuz Industrial Operations Platform. Use this skill whenever building, editing, or debugging Fuuz application expressions — including Screen Designer event handlers, Data Flow transforms, form validations, table operations, navigation logic, calendar/scheduling expressions, EDI processing, file manipulation, or any JSONata expression within the Fuuz platform. Triggers: any mention of Fuuz bindings, Fuuz expressions, Fuuz JSONata, Fuuz transforms, Fuuz screen actions, or when writing code that runs inside the Fuuz platform."
---

# Fuuz Platform — Custom JSONata Bindings

This skill documents all **294 custom bindings** available in the Fuuz Industrial Operations Platform (v3.0+). These extend standard JSONata ([docs.jsonata.org](https://docs.jsonata.org/)) with industrial automation, UI interaction, and enterprise integration capabilities.

## Rules

1. All custom bindings use the `$` prefix (e.g., `$uuid()`, `$moment()`).
2. Bindings are scoped by execution context:
 - **Frontend** — Only available in Screen Designer event handlers and UI expressions.
 - **Backend** — Only available in server-side Data Flows.
 - **Shared** — Available in both Frontend and Backend contexts.
3. Standard JSONata functions from [docs.jsonata.org](https://docs.jsonata.org/) remain fully supported alongside these custom bindings.
4. Screen Designer component bindings use `$components.{ComponentName}.fn.{method}()` — replace `Table1`, `Form1`, `Container1`, `ActionButton1` with actual component names.
5. `$moment()` returns a Moment.js object — chain methods like `.format()`, `.add()`, `.diff()` per Moment.js docs.
6. `$query()` and `$mutate()` accept GraphQL statements — the Fuuz platform uses GraphQL for data operations.
7. Parallel bindings (`$mapParallel`, `$filterParallel`, etc.) accept an optional concurrency limit as the last argument.

## Binding Index

| Context | Category | Count |
|---------|----------|-------|
| Frontend | Clipboard | 2 |
| Frontend | Navigation & Dialogs | 7 |
| Frontend | Screen Designer (Tables, Forms, Containers) | 30 |
| Backend | EDINation (X12/EDIFACT) | 8 |
| Shared | Core Utilities | 145 |
| Shared | Extended JSONata | 56 |
| Shared | Moment (Date/Time) | 12 |
| Shared | Calendars & Scheduling | 7 |
| Shared | Joins (SQL-style) | 5 |
| Shared | Files (PDF) | 3 |
| Shared | XML | 2 |
| Shared | Encryption | 2 |
| Shared | EDI | 2 |
| Shared | Integration & Execution | 13 |

---

## Frontend Bindings

> These bindings only work in Screen Designer event handlers and UI-side expressions.

### Clipboard

- `$writeToClipboard(data)` — Write datato clipboard
- `$readFromClipboard()` — Read data from clipboard

### Navigation & Dialogs

- `$addNotificationMessage(title, options)` — Addsa notification messageto the UI for the current user, optionally displayinga snackbar. Severity can be info, error, warning, or success. Note: This bindingis frontend-only.
- `$navigateBack()` — Navigate back one page.
- `$navigateTo(pathname,options)` — Navigateto the pathname provided.options: {newWindow: Opena new window and navigateto pathname, if setto true.replace: Replace the pathnameof the pageto a new one, if setto true.}
- `$navigateReload()` — Reload the current page.
- `$showAlertDialog(message,options)` — Showan alert dialog, it operates identicallyto the alert HOC function options : {title: String,icon: Stringor Object,buttonColor: String}
- `$showConfirmDialog(message,options)` — Show Confirm Dialog, it operates identicallyto the confirm HOC function .options:{title : String,delayTimeinSeconds : Number,icon : Objector String,buttonColor: String}
- `$showFormDialog(object)` — Show Form Dialog, it operates identicallyto the showFormDialog HOC function. It displaysa form from the provided json object.

### Screen Designer Components

Replace `Table1`, `Form1`, `Container1`, `ActionButton1` with the actual component name in your screen.

**Table Operations:**

- `$components.{Table}.fn.loadData({filter: { field : {condition : value}}, rowLimit: 500})` — Load data from backendin a table, with filter and paginationfilter and rowLimit are optional parameters
- `$components.{Table}.fn.search( {rowsPerPage : 500})` — Search datain a table baseon the filter providedin filter formrowsPerPageis optional parameter
- `$components.{Table}.fn.setData([{id : 1, field1 : "value1", field2 : "value2"},{id : 2, field1 : "value1", field2 : "value2"}])` — Set custom datain a table, overwrite the entire table dataid fieldis requireto set datain the table. field1, field2, field3 are optional fields which can be setas columnsin table
- `$components.{Table}.fn.addRows([{id : 1, field1 : "value1", field2 : "value2"},{id : 2, field1 : "value1", field2 : "value2"}])` — Add custom rowsto the end of table. id fieldis requireto set datain the table. field1 field2 field3 are optional fields which can be addas columnsin table
- `$components.{Table}.fn.updateRows([{id : 1, field1 : "value1", field2 : " value2"},{id : 2, field1 : "value1", field2 : "value2"}])` — Match rows basedon "id" from that object and update rows alreadyin the table. id fieldis requireto set datain the table. field1 field2 field3 are optional fields which can be addas columnsin table
- `$components.{Table}.fn.upsertRows([{id : 1, field1 : "value1", field2 : " value2"},{id : 2, field1 : "value1", field2 : "value2"}])` — Match rows basedon "id" from that object and add new rows that aren't alreadyin the table, also update rows that are alreadyin the table. id fieldis requireto set datain the table. field1 field2 field3 are optional fields which can be addas columnsin table
- `$components.{Table}.fn.deleteRows([id1,id2,id3])` — Delete row in a tableby idid1,id2,id3 are row ids to delete rowsin the table
- `$components.{Table}.fn.selectRow([id1,id2,id3])` — Select row in a table. id1, id2, id3 are row ids to select rowsin the table
- `$components.{Table}.fn.deselectRow([id1,id2,id3])` — Deselect selectionof rowin a table. id1, id2, id3 are row ids to deselect rowsin the table.
- `$components.{Table}.fn.toggleRowSelection([id1,id2,id3])` — Toggle selection row in a table. id1, id2, id3 are row ids to deselect rowsin the table.
- `$components.{Table}.fn.selectAllRows()` — Select all rows, regardlessof filtering and rows that are not visible due to grouping being enabled and their groups not expanded.
- `$components.{Table}.fn.deselectAllRows()` — Clear all row selections, regardlessof filtering.
- `$components.{Table}.fn.selectAllFiltered()` — Select all filtered rows.
- `$components.{Table}.fn.deselectAllFiltered()` — Clear all filtered selections.
- `$components.{Table}.fn.setSelectedRows([id1,id2,id3])` — Set selectionof rowsby id(s) in a table.

**Form Operations:**

- `$components.{Form}.fn.setValue(field, data)` — Set valueof fieldin a form. fieldis the data pathof the form field,datais the valueto setin the form field
- `$components.{Form}.fn.focus(field)` — Set cursor focusof fieldin a form. fieldis the data pathof the form field.
- `$components.{Form}.fn.blur(field)` — Blur cursor focusof fieldin a form. fieldis the data pathof the form field.
- `$components.{Form}.fn.loadData()` — Reload datain a form
- `$components.{Form}.fn.getUpdateMutation()` — Get mutation query for update datain a form. It will genereate mutation query for the formif and onlyif the Data Model for the formis set.
- `$components.{Form}.fn.getUpdatePayload()` — Get payload for mutationof datain a form
- `$components.{Form}.fn.validate()` — Validate form and return arrayof errors
- `$components.{Form}.fn.save(isSaveAll  false)` — Save the detailsof a form. By defaultit save modified fields only. If wantto modify all the fields pass trueto save function
- `$components.{Form}.fn.getVariables()` — Get all the variablesof the form
- `$components.{Form}.fn.delete()` — Delete the form data

**Container Operations:**

- `$components.{Container}.fn.hide()` — Container visibility set to hide
- `$components.{Container}.fn.show()` — Container visibility set to show
- `$components.{Container}.fn.toggle()` — Container visibility toggle
- `$components.{Container}.fn.resetHidden()` — Container visibility reset all the hidden fields

**Action Button:**

- `$components.{ActionButton}.fn.execute({field:value})` — Execute actionof other action button. Replace ActionButton1 with action button name,Field and value are the optional parameters for the action button, you can pass any numberof parameters.

---

## Backend Bindings

> These bindings only work in server-side Data Flows.

### EDINation (X12 & EDIFACT)

- `$readEDI(string,object,object)` — Readsan EDI Document with EDINations API and returns JSON
- `$writeEDI(string,object,object)` — Writes JSON EDI documentto EDINation returning EDI String
- `$validateEDI(string,object,object)` — Validates JSON EDI document against EDINation API
- `$ackEDI(string,object,object)` — Writesan EDI document and returnsan ACK

---

## Shared Bindings

> Available in both Frontend (Screen Designer) and Backend (Data Flows) contexts.

### Core Utilities (145 bindings)

**ID Generation:**

- `$cuid()` — Generatesa CUID.
- `$uuid()` — Generatesa V4 UUID.

**Error Handling & Control Flow:**

- `$throw(string, object?)` — Throwsa ThrowBindingError which halts executionof the transform. The error will use the provided stringas message and the data property will include any propertieson the object passedto the second parameter.
 - Example: `$throw("Oh no, something broke!", { "moreDetail": true })`
- `$coalesce(array)` — Returns the first value from the array thatis not nullor undefined.
- `$identity(any)` — Returns the valueit is given.
- `$tryCatch(function, function)` — Will returna new function that takes the same argumentsas first function and will run in a try catch block. The catching function will haveto be either anonymousor havea signature matching the trying function with the additionof a starting error object
- `$nullIf(any, any)` — Returns nullif the given valueis equalto the comparison value. Otherwise, it returns the value.
- `$cronConverter(string, string?)` — Returns the object from cron-converter.
- `$wait(number)` — Pause execution for the given numberof milliseconds.
- `$retry(function, object?, function?)` — Returnsa functionto retrya numberof times. Options object accepts retries and delay (ms) between retries. Optional functionto return whena re try fails.
 - Example: `$retry(function() { $http("get", {"url": "https://testnotreal.com" }) }, {"retries": 5, "delay": 1000})`

**Type Checking:**

- `$isArray(any JSON type)` — Determines whether the given valueis an array.
- `$isString(any JSON type)` — Determines whether the given valueis a string.
- `$isBoolean(any JSON type)` — Determines whether the given valueis a boolean.
- `$isInteger(any JSON type)` — Determines whether the given valueis an integer number.
- `$isFloat(any JSON type)` — Determines whether the given valueis a floating point number. Integer numbers also countas floating point numbersas JSON only has a singular number type.
- `$isNumber(any JSON type)` — Determines whether the given valueis a number.
- `$isObject(any JSON type)` — Determines whether the given valueis an object.
 - Example: `$isObject({})`
- `$isUndefined(any JSON type)` — Determines whether the given valueis undefined.
- `$isNil(any JSON type)` — Determines whether the given valueis nullor undefined.
- `$isNaN(any JSON type)` — Determines whether the given valueis NaN.
- `$isEmpty(any JSON type)` — Determines whether the given valueis an empty array, object, or string.- `$isNilOrEmpty(any JSON type)` — Determines whether the given valueis undefined, null, or an empty array , object, or string.

**String Case & Formatting:**

- `$pluralize(string, string)` — Returns the provided stringin pluralized form, using standard grammatical rules for the provided locale.
- `$singularize(string, string)` — Returns the provided stringin singular form, using standard grammatical rules for the provided locale.
- `$camelCase(string)` — Returns the provided stringin lowerCamelCase.
- `$snakeCase(string)` — Returns the provided stringin snake_case.
- `$kebabCase(string)` — Returns the provided stringin kebab-case.
- `$lowerFirst(string)` — Returns the provided string with the first character changedto lowercase.
- `$upperFirst(string)` — Returns the provided string with the first character changedto uppercase.
- `$startCase(string)` — Returns the provided stringin Start Case.

**String Matching:**

- `$startsWith(string, pattern)` — Checksto seeif string starts with pattern
- `$endsWith(string, pattern)` — Checksto see string ends with pattern
- `$removeLeadingValues(string, pattern)` — Removes specified leading values from string. This will loop until stringno longer begins with pattern
- `$replaceLeadingValues(string, pattern, replacement)` — Stringis whatis being modified. If string begins with pattern, it willbe replacedby replacement. If pattern appears multiple timesin a row, it willbe replaced for each timeit appears.

**HTML & URI Encoding:**

- `$htmlEscape(string)` — Converts the characters "&", "<", ">", '"', and "'" in stringto their corresponding HTML entities.
- `$htmlUnescape(string)` — Converts the HTML entities &amp;, &lt;, &gt;, &quot;, and &#39; in stringto their corresponding characters.
- `$uriEscape(string)` — URI escapes the given string. Does not handle the following characters: ?, =, /, and &.
- `$uriUnescape(string)` — Reverses URI escapingon the given string. Does not handle the following characters: ?, =, /, and &.
- `$uriEscapeComponent(string)` — URI escapes the given string, including the following characters: ?,  , /, and &.
- `$uriUnescapeComponent(string)` — Reverses URI escapingon the given string, including handlingof the following characters escape sequences: ?, =, /, and &.

**Rich Text & Markdown:**

- `$markdownToHtml(string)` — Convert markdown contentto HTML.
- `$richTextToMarkdown(object)` — Convert serialized RawDraftContentto Markdown
- `$markdownToRichText(string)` — Converts Markdownto serializable RichText objects
- `$plainTextToRichText(string)` — Converts plain textto serializable RichText objects
- `$richTextToPlainText(object)` — Convert serialized RawDraftContentto plain text.
- `$richTextToHTML(object)` — Convert serialized RawDraftContentto HTML.

**Number Formatting:**

- `$numeral(value)` — Convertsa JSON valueto a Numeral.js object for parsingor formatting. Documentationis locatedat http://numeraljs.com/.
- `$parseInt(string, number?)` — Convertsa stringto a number using the defaultor a specified base (radix, default 10)
- `$decimalToString(array<number>, string?)` — Decodesa decimal array intoa string using the specified encoding (defa ultsto utf-8). Fora listof supported encodings checkhttps://encoding.spec.whatwg.org/#encodi ngs
- `$parseNumber(value)` — Convertsa JSON valueto a number. Returns undefinedif the provided valueis unableto be parsed intoa number.

**CSV & Data Parsing:**

- `$arrayToCsv(array<object>, object?)` — Convertsan arrayof objects intoa CSV string. Documentationon options are locatedat https:// www.npmjs.com/package/json-2-csv.
- `$csvToArray(string, object?)` — Convertsa csv string intoan arrayof objects. Documentationon options are locatedat https:// www.npmjs.com/package/json-2-csv.
- `$epcToUrn(string, object?)` — Convertsan RFID tags EPC valueto a URN value. Default standardis setto GRAI96. User defined parsing logic can be implemented using the userDefi ned objectin options.

**JSON Serialization:**

- `$jsonParse(string)` — Parsesa string representationof a JSON value into the valueit represe nts.
- `$jsonStringify(any JSON type, object?)` — Serializesa JSON value intoa string.
 - Example: `$jsonStringify({ "value": true }, { "space": 2 })`

**JSON Schema Generation:**

- `$jsonToJsonSchema(json, options)` — Convertsa JSON object into JSON Schema, lists which fields are required and finds most compatible type for array.
- `$jsonToJsonSchema(json, options)` — Convertsa JSON object into JSON Schema and classifies array typeby first element.
- `$jsonToJsonSchema(json, options)` — Convertsa JSON object into JSON Schema and lists typeof each elementi n an array.
- `$jsonToJsonSchema(json, options)` — Convertsa JSON object into JSON Schemaif all elementsin arraysto be the same type. If not, an erroris thrown.

**Object Manipulation:**

- `$path(object, string | array<string>)` — Returns the valueat the provided pathin the object.
 - Example: `$path({"parent":{"child":[1,2,3]}},"parent.child")`
- `$omit(object, array<string>)` — Returns the object with the provided properties omitted.
 - Example: `$omit({"parent":{"child":[1,2,3],"name":"Daphne"}},"parent.name")`
- `$pick(object, array<string>)` — Returns the object with only the provided properties included.
 - Example: `$pick({"parent":{"child":[1,2,3],"name":"Daphne"}},"parent.name")`
- `$values(object)` — Returnsan arrayof valuesin the given object.
 - Example: `$values({"a":1,"b":2,"c":3})`
- `$mapValues(object, function)` — Returnsan arrayof valuesin the given object basedon the function.
 - Example: `$mapValues({"a":1,"b":2,"c":3}, function($v){$v})`
- `$mapKeys(object, function)` — Returnsan arrayof keysin the given object basedon the function.< /td>$mapKeys({"a":1,"b":2,"c":3}, function($v) { $v })
 - Example: `$mapKeys({"a":1,"b":2,"c":3}, function($v) { $v })`
- `$mergeDeep(array<object>)` — Returnsan object which includes all properties from the provided objects, recursively merged. In the event that multiple objects contain the same property, properties from objects with higher array indexes override properties from lower array indexes.
- `$has(object, string)` — Returns trueor false dependingon whether the provided property existsin the provided object.
 - Example: `$has({"parent":{"child":[1,2,3],"name":"Daphne"}},"parent.name")`
- `$toPairs(object)` — Returnsan arrayof key-value pair arrays, in the shapeof [ [ key1, value1 ], [ key2, value2] ].
 - Example: `$toPairs({ "key": "value" })`
- `$fromPairs(array<array>)` — Transformsan arrayof key value pairs intoan object.

**Object Flatten & Diff:**

- `$flattenObject(object,options)` — Flattens the object - it'll returnan object one level deep, regardlessof how nested the original object was.options:{delimiter: Usea custom delimiter for (un)flattening your objects, insteadof {.},safe: When ena bled, will preserve arrays and their contents. Thisis disabledby default. ,maxDepth: Maxi mum numberof nested objectsto flatten.}
- `$unflattenObject(object,options)` — Unflattensan object whichis flattend.options: {delimiter: Usea cus tom delimiter for (un)flattening your objects, insteadof {.},object: When enabled , arrays will not be created automatically when calling unflatten.,overwrite: When enab led, existing keysin the unflattened object may be overwrittenif they can not holda newly encountered nested value.,}
- `$objectDiff(object, object, options)` — Compares two objects and returns the differences.options: {detailed: [boolean] Showa breakdownof which properties were added, removed, and changed.ignore: [arrayof arrays] Exclude key paths from comparison.}
 - Example: `$objectDiff({ "a":1, "b":2, "foo ":{"bar":true} },{ "a":1, "b":3, "c": 4, "foo":{"bar":false} },{"detailed": true,"ignore": [["foo","b ar"]]})`

**Array Manipulation:**

- `$chunk(array, number)` — Createsan arrayof elements split into groups the lengthof size. If array can't be split evenly, the final chunk willbe the remaining elements.< /p>
- `$uniq(array)` — Returns the provided array with duplicate values removed. Equalityis determinedby reference for objects.
- `$partition(array, function)` — Createsan arrayof elements split into two groups, the firstof whichc ontains elements predicate returns truthy for, the secondof which contains elements predicate returns falsey for. The predicateis invoked with one a rgument: (value).
- `$groupBy(array, function)` — Takesan array and a key function, returnsan object. All values with the same key are addedto an array propertyin the object named basedon the key.
- `$indexBy(array, function)` — Similarto $groupBy, except the properties contain only the last matching element for the key.
- `$uniqBy(array, function)` — Similarto $uniq, except this function takesa function that returns the valueto determine uniquenessby.
- `$flatten(array, number?)` — Givenan arrayof arrays, this will flatten the arraysto the given depth. Depth defaultsto 1.
- `$first(array)` — Returns the first elementof the array.
- `$last(array)` — Returns the last elementof the array.
- `$takeFirst(array, number?)` — Returnsan array containing the firstn element from the given array.
- `$takeLast(array, number?)` — Returnsan array containing the lastn element from the given array.- `$findFirst(array, function)` — Return the first element where the predicate function returns true.< /td>$findFirst([obj1, obj2, obj3], myPredFunc)
- `$findLast(array, function)` — Return the last element where the predicate function returns true.$findLast([obj1, obj2, obj3], myPredFunc)
- `$findFirstIndex(array, function)` — Return the first index where the predicate function returns true.$findFirstIndex([obj1, obj2, obj3], myPredFunc)
- `$findLastIndex(array, function)` — Return the last Index where the predicate function returns true.$findLastIndex([obj1, obj2, obj3], myPredFunc)
- `$flatMap(array, function)` — Returnsa new transformed array but flatend.
- `$dropFirst(array, number)` — Returns all but the firstn elementsof the given list
- `$dropLast(array, number)` — Returns all but the lastn elementsof the given list
- `$partitionBetween(array, function, function, object)` — Partitionsan array basedon beginning and ending predicate functions, c ollecting all values between matching elements.
 - Example: `$partitionBetween([{ "name": "ST" }, { "name": "N1" }, { "name": "SE" }] , function ($i) { $i.name "ST" }, function ($i) { $i.name "SE" }, { "includ eEndElement": true })`
- `$all(array, function)` — Returns trueif all elementsof the array satisfy the predicate function and false otherwise.
- `$any(array, function)` — Returns trueif any elementof the array satisfies the predicate function and false otherwise.
- `$jsonToMarkdown(array<object>)` — Convertsa JSON inputto markdown. Documentation:https://www.npmjs.com/package/json2md$jsonToMarkdown([{"h1":"John"} , {"blockquote":30} , {"h3":"New York"}]) - Example: `$jsonToMarkdown([{"h1":"John"} , {"blockquote":30} , {"h3":"New York"}])=`

**Validation & Schema:**

- `$validateSchema(any JSON type)` — Takesa JSON object and validates thatit is a JSON schema object.$validateSchema({"id":1,"label":2})
- `$validateJson(any JSON type, json schema)` — Takes JSON data and a schema and validates that the JSON matches the sch ema, throwing any validation errors.
- `$isValidJson(any JSON type, json schema)` — Takes JSON data and a schema and returnsa boolean indicatingif the dat a matches the schema.

**Parallel Execution:**

- `$mapParallel(object, function, number?)` — Takesan array and a mapping function, returnsa new array where the values have been replaced with the outputof the mapping function. The default degreeof parallelismis 8.
 - Example: `$mapParallel([{ "id": 1, "name": "test 1" }, { "id": 1, "name": "test 2" }], function ($v) { $v.name}, 2);`
- `$mapValuesParallel(object, function, number?)` — Takesan object and a mapping function, returnsa new object where the v alues have been replaced with the outputof the mapping function. The default degreeof parallelismis 8.
 - Example: `$mapValuesParallel({ "hello": "John", "goodbye": "Joan" }, function ($v) { $v  "John" ? "Joan" : $v }, 2);`
- `$filterParallel(array, function, number?)` — Takesan array and a predicate function, returnsan array. The resulting array contains all the values for which the predicate returns true. Thede fault degreeof parallelismis 8.
- `$groupByParallel(array, function, number?)` — Takesan array and a key function, returnsan object. All values with the same key are addedto an array propertyin the object named basedon the key. The default degreeof parallelismis 8.
- `$timesParallel(number, function, number?)` — Executesa function N numberof times, in parallel. Results are collected as an array. Thisis essentiallya functional versionof a for loop. The default degreeof parallelismis 8.

**URL Manipulation:**

- `$parseUrl(string)` — Parsea url intoa JSON Object.
- `$urlStringify(any JSON type, optional json options)` — Takes JSON data and optionally options from the qs libraryin json forma tand returns the JSON data formattedas querystring.
- `$urlJoin(array)` — Joinan arrayof url paths usinga url join. The joined url will remove any duplicate / in the path and add themif they are not provided.

**Semver (Semantic Versioning):**

- `$semverParse(any)` — Attemptto parsea stringas a semantic version, returning eithera SemVer objector null.
- `$semverValid(string)` — Return the parsed version, or nullif it's not valid. The optional boolean argument will let you chooseto interpret versionsor ranges loosely- `$semverClean(string, boolean?)` — Cleana stringto be a valid semverif possible. This will returna clea ned and trimmed semver version. If the provided versionis not valida null willbe returned. This does not work for ranges. The optional boolean argu ment will let you chooseto interpret versions loosely
- `$semverInc(string, string, boolean?, string?)` — Return the version incrementedby the release type (major, premajor, minor, preminor, patch, prepatch, or prerelease), or nullif it's not valid. T he third argumentis an optional additional identifier string argumen tthat will append the valueof the stringas a prerelease identifier$semverInc('1.2.3', 'prerelease', 'beta')
- `$semverDiff(string, string, boolean?)` — Returns difference between two versionsby the release type (major, prem ajor, minor, preminor, patch, prepatch, or prerelease), or nullif the vers ions are the same. An optional third argument can be passedin as a boolean which will let you chooseto interpret versionsor ranges loosely
- `$semverMajor(string, boolean?)` — Return the major version number. Optionally, includea booleanto interp ret the version loosely
- `$semverMinor(string, boolean?)` — Return the minor version number. Optionally, includea booleanto interp ret the version loosely
- `$semverPatch(string, boolean?)` — Return the patch version number. Optionally, includea booleanto interp ret the version loosely
- `$semverPrerelease(string)` — Returnsan arrayof prerelease components, or nullif none exist.$semverPrerelease('1.2.3-alpha.1')
- `$semverCompare(string, string, boolean?)` — Return 0 if v1 = v2, or 1 if v1 is greater, or -1 if v2 is greater. Sortsin ascending orderif passedto Array.sort(). An optional third argu ment can be passedin as a boolean which will let you chooseto interpretv ersionsor ranges loosely
- `$semverRcompare(string, string, boolean?)` — The reverseof compare. Sortsan arrayof versionsin descending orderw hen passedto Array.sort(). An optional third argument can be passedin as a boolean which will let you chooseto interpret versionsor ranges loosely- `$semverCompareLoose(string, string)` — Compare while interpreting versions and ranges loosely. Be more forgiving about not-quite-valid semver strings. (Any resulting output will alwaysb e 100% strict compliant, of course.) For backwards compatibility reasons, i f the options argumentis a boolean value insteadof an object, it is inter pretedto be the loose param
- `$semverCompareBuild(string, string)` — The sameas compare but considers build when two versions are equal. Sorts in ascending orderif passedto Array.sort(). v2 is greater. Sortsin as cending orderif passedto Array.sort().
- `$semverSort(array<string>, boolean?)` — Sortsan arrayof semver entriesin ascending order using compareBuild() . Additionally, an optional argument can be passedin as a boolean whichwi ll let you chooseto interpret versionsor ranges loosely
- `$semverRsort(array<string>, boolean?)` — Sortsan arrayof semver entriesin descending order using compareBuild( ). Optionally, an additional argument can be passedin as a boolean whichw ill let you chooseto interpret versionsor ranges loosely
- `$semverGt(string, string, boolean?)` — Greater than comparison. The third argumentis optional and canbe passed in as a boolean which will let you chooseto interpret versionsor ranges loosely
- `$semverLt(string, string, boolean?)` — Less than comparison. The third argumentis optional and canbe passedi n as a boolean which will let you chooseto interpret versionsor rangeslo osely
- `$semverEq(string, string, boolean?)` — Equality comparison. The third argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loo sely
- `$semverNeq(string, string, boolean?)` — Inequality comparison. The third argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor rangesl oosely
- `$semverGte(string, string, boolean?)` — Greater-thanor equal-to comparison. The third argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versions or ranges loosely
- `$semverLte(string, string, boolean?)` — Less-thanor equal-to comparison. The third argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosely
- `$semverCmp(string, string, string, boolean?)` — Passin a comparison string, and it'll call the corresponding semver com parison function. "==" and "!=" do simple string comparison, bu tare included for completeness. Throwsif an invalid comparison stringis provided. The fourth argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosely
- `$semverCoerce(string, string)` — Coercesa stringto SemVerif possible.
- `$semverSatisfies(string, string, boolean?)` — Return trueif the version satisfies the range. The third argumentis optional and canbe passedin as a boolean which will let you chooseto inter pret versionsor ranges loosely
- `$semverMaxSatisfying(array<string>, string, boolean?)` — Return the highest versionin the list that satisfies the range, or nullif noneof themdo. The third argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosel y
- `$semverMinSatisfying(array<string>, string, boolean?)` — Return the lowest versionin the list that satisfies the range, or nullif noneof themdo. The third argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosely- `$semverToComparators(string, string, boolean?)` — Mostly just for testing and legacy API reasons. The third argumentis optional and canbe passedin as a boolean which will let you chooseto inter pret versionsor ranges loosely
- `$semverMinVersion(string, boolean?)` — Return the lowest version that can possibly match the given range. Thes econd argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosely
- `$semverValidRange(string, boolean?)` — Return the valid rangeor nullif it's not valid. The second argumentis optional and canbe passedin as a boolean which will let you chooseto in terpret versionsor ranges loosely
- `$semverOutside(string, string, string, boolean?)` — Return trueif the versionis outside the boundsof the rangein either the highor low direction. The hilo argument mustbe either the string '> ;' or '<'. (Thisis the function calledby gtr and ltr.). The fourth arg umentis optional and canbe passedin as a boolean which will let you choose to interpret versionsor ranges loosely
- `$semverGtr(string, string, boolean?)` — Return trueif versionis greater than all the versions possiblein the range. The third argumentis optional and canbe passedin as a boolean whi ch will let you chooseto interpret versionsor ranges loosely
- `$semverLtr(string, string, boolean?)` — Return trueif versionis less than all the versions possiblein the ran ge. The third argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosely
- `$semverIntersects(string, string, boolean?)` — Return trueif anyof the ranges comparators intersect. The third argument is optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosely
- `$semverSimplifyRange(array<string>, string)` — Returna "simplified" range that matches the same itemsin versions listas the range specified. Note thatit does not guarantee thatit would match the same versionsin all cases, only for the set of versions provided. Th is is useful when generating rangesby joining together multiple versionsw ith || programmatically, to provide the user with somethinga bit more ergo nomic. If the provided rangeis shorterin string-length than the generated range, then thatis returned.
- `$semverRangeSubset(string, string, string?)` — Return trueif the subRange rangeis entirely containedby the superRange range. The fourth argumentis optional and canbe passedin as a boolean which will let you chooseto interpret versionsor ranges loosely

**Parsing Utilities:**

- `$parseGraphQLStatement(string)` — Parsea GraphQL statementto the AST format
- `$parseExpression(string)` — Parsea JSONata expressionto the AST format

**Other Core:**

- `$pathSatisfies(object, string | array<string>, function)` — Givenan object, a path, anda predicate function, this function willre turn trueif the predicate returns true for the valueat the given path.
 - Example: `$pathSatisfies({ "name": "Henry Nord" }, "name", function ($v) { $v  "Henry Nord" })`
- `$visitPreorder(array | object, function)` — Usesa preorder traversalto visit each elementof a JSON object and all ows recursive replacementof any nested objects.
 - Example: `$visitPreorder(tree, function ($v) {( $merge([$v, { "hello": "world" }]) ; )})`
- `$convertCharacterSet(string, string, string?)` — Convertsa string froma source encodingto a target encoding.

### Extended JSONata (56 bindings)

Enhanced implementations of standard JSONata functions. Refer to [docs.jsonata.org](https://docs.jsonata.org/) for base behavior; the Fuuz platform extends these with additional capabilities.

**String:**

- `$string(arg)` — Casts the arg parameterto a string using the following casting rules
- `$length(str)` — Returns the numberof charactersin the string str.
- `$substring(str, start[, length])` — Returnsa string containing the charactersin the first parameter str st artingat position start (zero-offset).
- `$substringBefore(str, chars)` — Returns the substring before the first occurrenceof the character seque nce charsin str.
- `$substringAfter(str, chars)` — Returns the substring after the first occurrenceof the character sequence charsin str.
- `$uppercase(str)` — Returnsa string with all the charactersof str convertedto uppercase.< /p>
- `$lowercase(str)` — Returnsa string with all the charactersof str convertedto lowercase.< /p>
- `$pad(str, width [, char])` — Returnsa copyof the string str with extra padding, if necessary, so th at its total numberof charactersis at least the absolute valueof the wid th parameter.
- `$contains(str, pattern)` — Returns trueif stris matchedby pattern, otherwiseit returns false.
- `$split(str, separator [, limit])` — Splits the str parameter intoan arrayof substrings.
- `$join(array[, separator])` — Joinsan arrayof component strings intoa single concatenated stringwi th each component string separatedby the optional separator parameter.- `$match(str, pattern [, limit])` — Applies the str stringto the pattern regular expression and returnsan arrayof objects, with each object containing information about each occurr enceof a match withing str.
- `$replace(str, pattern, replacement [, limit])` — Finds occurrencesof pattern within str and replaces them with replacement.
- `$eval(expr [, context])` — Parses and evaluates the string expr which contains literal JSONor a JS ONata expression using the current contextas the context for evaluation.

**Numeric:**

- `$number(arg)` — Casts the arg parameterto a number.
- `$abs(number)` — Returns the absolute valueof the number parameter, i.e. if the numberi s negative, it returns the positive value.
- `$floor(number)` — Returns the valueof number rounded downto the nearest integer thatis smalleror equalto number.
- `$ceil(number)` — Returns the valueof number roundedup to the nearest integer thatis gr eater thanor equalto number.
- `$round(number [, precision])` — Returns the valueof the number parameter roundedto the numberof decimal places specifiedby the optional precision parameter.
- `$power(base, exponent)` — Returns the valueof base raisedto the powerof exponent (baseexponent) .
- `$sqrt(number)` — Returns the square rootof the valueof the number parameter.
- `$random()` — Returnsa pseudo random number greater thanor equalto zero and lessth an one (0 =C3=A2=E2=80=B0=C2=A4 n < 1)
- `$formatNumber(number, picture [, options])` — Casts the numberto a string and formatsit to a decimal representationas specifiedby the picture string.
- `$formatBase(number [, radix])` — Casts the numberto a string and formatsit to an integer representedin the number base specifiedby the radix argument.
- `$formatInteger(number, picture)` — Casts the numberto a string and formatsit to an integer representationas specifiedby the picture string.
- `$parseInteger(string, picture)` — Parses the contentsof the string parameterto an integer (as a JSON number) using the format specifiedby the picture string.
- `$sum(array)` — Returns the arithmetic sum of an arrayof numbers. It is an errorif the input array containsan item which isn't a number.
- `$max(array)` — Returns the maximum numberin an arrayof numbers. It is an errorif the input array containsan item which isn't a number.
- `$min(array)` — Returns the minimum numberin an arrayof numbers. It is an errorif the input array containsan item which isn't a number.
- `$average(array)` — Returns the mean valueof an arrayof numbers. It is an errorif the inp ut array containsan item which isn't a number.

**Array:**

- `$count(array)` — Returns the numberof itemsin the array parameter.
- `$append(array1, array2)` — Returns and array containing the valuesin array1 followedby the valuesin array2.
- `$sort(array [, function(left, right)])` — Returnsan array containing all the valuesin the array parameter, buts orted into order.
- `$reverse(array)` — Returnsan array containing all the values from the array parameter, butin reverse order.
- `$shuffle(array)` — Returnsan array containing all the values from the array parameter, but shuffled into random order.
- `$zip(array1, ...)` — Returnsa convolved (zipped) array containing grouped arraysof valuesf rom the array1 ... arrayN arguments from index 0, 1, 2, etc.
- `Array slice` — Returnsa portionof the specified array.

**Object:**

- `$keys(object)` — Returnsan array containing the keysin the object. If the argumentis a n arrayof objects, then the array returned containsa de-duplicated listo f all the keysin allof the objects.
- `$lookup(object, key)` — Returns the value associated with key in object. If the first argumenti s an arrayof objects, then all of the objectsin the array are searched, a nd the values associated with all occurrencesof key are returned.
- `$spread(object)` — Splitsan object containing key/value pairs intoan arrayof objects, ea ch of which has a single key/value pair from the input object.
- `$merge(array<object>)` — Mergesan arrayof objects intoa single object containing all the key/v alue pairs from eachof the objectsin the input array.
- `$each(object, function)` — Returnsan array containing the values returnby the function when applied to each key/value pairin the object.

**Boolean & Existence:**

- `$boolean(arg)` — Casts the argumentto a Boolean.
- `$exists(arg)` — Returns Boolean trueif the arg expression evaluatesto a value, or false if the expression does not match anything (e.g. a pathto a non-existent field reference).

**Date/Time:**

- `$now([picture [, timezone]])` — Generatesa UTC timestampin ISO 8601 compatible format and returnsit a s a string. All invocationsof $now() withinan evaluationof an expression will all return the same timestamp value.
- `$fromMillis(number [, picture [, timezone]])` — Convert the number representing milliseconds since the Unix Epoch (1 Jan uary, 1970 UTC) to a formatted string representationof the timestampas sp ecifiedby the picture string.
- `$toMillis(timestamp [, picture])` — Converta timestamp stringto the numberof milliseconds since the Unix Epoch (1 January, 1970 UTC) as a number.

**Higher-Order:**

- `$map(array, function(value [, index [, array]])` — Returnsan array containing the resultsof applying the function parameter to each valuein the array parameter.
- `$filter(array, function(value [, index [, array]]))` — Returnsan array containing only the valuesin the array parameter that satisfy the function predicate (i.e. function returns Boolean true when pas sed the value).
- `$reduce(array, function(i, j) [, init])` — Returnsan aggregated value derived from applying the function parameter successivelyto each valuein arrayin combination with the resultof the previous applicationof the function.
- `$sift(object, function(value [, key [, object]]))` — Returnsan object that contains only the key/value pairs from the object parameter that satisfy the predicate function passedin as the second parameter.

**Encoding:**

- `$base64encode(str)` — Convertsan ASCII stringto a base 64 representation.
- `$base64decode(str)` — Converts base 64 encoded bytesto a string, usinga UTF-8 Unicode codepage.

**Other:**

- `` — Normalizes and trims all tabs, carriage returns, line feeds, contiguous whitespaces, Trailing and leading whitespaces charactersin str
- `` — Returns Boolean NOT on the argument. argis first castto a boolean.- `` — Returns the numberof milliseconds since the Unix Epoch (1 January, 1970 UTC) as a number. All invocationsof $millis() withinan evaluationof an expression will all return the same value.

### Moment — Date/Time (12 bindings)

Returns Moment.js objects. Chain `.format()`, `.add()`, `.subtract()`, `.diff()`, `.startOf()`, `.endOf()`, etc.

- `$moment(string?, string?, string? | boolean?, boolean?)` — Returnsa moment.js instanceof the given date string.
 - Example: `$moment("01/01/2019 09:00:00AM", "MM/DD/YYYY hh:mm:ssA" )`
 - Also: Returns begining of day
 - Also: Returns end of day
 - Also: Returns start of Last Month
 - Also: Returns end of Last Month
 - Also: Returns start of Current Month
 - Also: Returns end of Current Month
- `$momentTz(string?, string?, string?)` — Returnsa moment.js instanceof the given date string, with the timezone assumedto be the given timezone.
 - Example: `$momentTz( "01/01/2019 09:00:00AM", "MM/DD/YYYY hh:mm:ssA", "America/Det roit" )`
- `$momentTzTimeZones()` — Returns Moment Timezones
 - Example: `$momentTzTimezones()`
- `$momentTzGuess(boolean?)` — Moment Tz guess uses the Internationalization API (Intl. DateTimeFormat() .resolvedOptions().timeZone) in supported browsersto determine the user's time zone. You can optionally passa boolean that will ignore cached values . This function will only work correctly when ran througha frontend transf orm. When this functionis ranin the backendit will return UTC.
 - Example: `$momentTzGuess()`
- `$momentMax(array, boolean?)` — Moment Max receivesan arrayof $moment objectsor moment compatible values and returns the maximum (most distant future) value. You can optionally passa boolean that will ignore invalid dates.
 - Example: `$momentMax([$moment("2030-12-15").subtract(1, "days"), $moment("2030-12- 15").add(10, "days"), $moment("2030-12-15").subtract(5, "days")])`
- `$momentMin(array, boolean?)` — Moment Min receivesan arrayof $moment objectsor moment compatible values and returns the minimum (most distant past) value. You can optionallyp assa boolean that will ignore invalid dates.
 - Example: `$momentMin([$moment("2030-12-15").subtract(1, "days"), $moment("2030-12- 15").add(10, "days"), $moment("2030-12-15").subtract(5, "days")])`

### Calendars & Scheduling (7 bindings)

For working with Fuuz production calendars (shifts, availability, events).

- `$eventsBetween(momentObject,momentObject,object)` — Returns the events for a calendar between two dates.
 - Example: `$eventsBetween($moment(),$moment(),calendar)`
- `$getEventEnd(object)` — Returns the endof the first occurrenceof an event.
 - Example: `$getEventEnd(event)`
- `$getEventEnd(object,momentObject)` — Returns the endof the next occurrenceof an event after the given moment.
 - Example: `$getEventEnd(event,$moment())`
- `$getEventsAt(momentObject, object)` — Returns the events occurringin the given moment.
 - Example: `$getEventsAt($moment("2000-07-23T18:36:00.000Z"), calendar)`
- `$getEventsAt(string, object)` — Returns the events occurringin the given moment.
 - Example: `$getEventsAt("2000-07-23T18:36:00.000Z", calendar)`
- `$getAvailabilityBetween(momentObject,momentObject,object)` — Returns the events for a calendar between two dates, categorizedby avai lability.
 - Example: `$getAvailabilityBetween($moment(), $moment(), calendar)`
- `$availableAt(momentObject, calendar)` — Returns trueif thereis at least one event markedas availablein theg iven moment.
 - Example: `$availableAt($moment(), calendar)`

### SQL-Style Joins (5 bindings)

Join two arrays using SQL-style semantics. Keys can be a string (field name), array of strings, or a comparison function.

- `$crossJoin(array, array, string, object?)` — Cross-joins the left and right arrays. Every valuein the left array wil l be joinedto every valuein the right array.
 - Example: `$crossJoin( left, right, 'joinedKey', { 'grouped': false } )`
- `$innerJoin(array, array, string | array<string> | function, string | array<string> | function, string, object?)` — Performsan inner join between the left and right arrays. The accessors can be eithera path string (separatedby periods), an arrayof path elements, or a function. Only values from the left side witha matchon the right side willbe returned.
 - Example: `$innerJoin( left, right, 'leftKey', 'rightKey', 'joinedKey', { 'grouped' : false } )`
- `$leftAntiJoin(array, array, string | array<string> | function, string | array<string> | function)` — Performsan anti semi-join against the left array. Only values from the left side withouta matchon the right side willbe kept.
 - Example: `$leftAntiJoin( left, right, 'leftKey', 'rightKey')`
- `$leftOuterJoin(array, array, string | array<string> | function, st ring | array<string> | function, string, object?)` — Performsan outer join between the left and right arrays. The accessors can be eithera path string (separatedby periods), an arrayof path elements, or a function. All values from the left side willbe returned, regardless of whether thereis a matchon the right side.
 - Example: `$leftOuterJoin( left, right, 'leftKey', 'rightKey', 'joinedKey', { 'grou ped': false } )`
- `$leftSemiJoin(array, array, string | array<string> | function, string | array<string> | function)` — Performsa semi-join against the left array. Only values from the lefts ide witha matchon the right side willbe kept.
 - Example: `$leftSemiJoin( left, right, 'leftKey', 'rightKey' )`

### Files — PDF (3 bindings)

- `$pdfToJson(string, object?)` — Convertsa PDF file into JSON
- `$mergePDFs([string])` — Merges multiple base-64 encoded PDFs intoa single document.
- `$rotatePDF(string, angle)` — Rotatesa base-64 encoded PDF to a specified anglein degrees. Angle mustbe a multipleof 90.

### XML (2 bindings)

- `$jsonToXml(object | array<object>,object?)` — Returnsan XML string representationof the given JSON objector array. For optionsgo to https://www.npmjs.com/package/xml2js
- `$xmlToJson(string,object?)` — Returnsa JSON representationof the given XML string. For optionsgo to https://www.npmjs.com/package/xml2js

### Encryption (2 bindings)

- `$encryptHmac(algorithm, key, string, encoding?)` — Returnsa digest encrypted with the key basedon the supplied algorithmin the supplied (optional, default base64) encoding. Algorithm can be any supported OpenSSL method.
- `hash(algorithm, string, encoding?)` — Returnsa hashof the string basedon the algorithm providedin the supplied (optional, default base64) encoding. Algorithm can be any supported OpenSSL method.

### EDI Utilities (2 bindings)

- `$parseX12(string)` — Convertsan X12 EDI string intoa JSON object.
- `$formatX12([string])` — Convertsa parsed X12 EDI arrayof objects intoa formatted JSON object. Includes parseX12 binding.

### Integration & Execution (13 bindings)

For GraphQL queries/mutations, external integrations, flow execution, and device communication.

- `$log(message,meta,level)` — Logsa statement witha 'message' of a 'level' and optionallya 'meta' object. The level argumentis optional; valid values include ('error', 'warn','info','verbose','debug','silly'); invalid values defaultto 'info'. This objectis storedas a JSONB blob within postgres. Check your console log.
- `$getCalendar(object)` — Returns MFGx Calendar and eventsby the id
 - Example: `$getCalendar({"id":id})`
- `$getCalendar(object)` — Returns MFGx Calendar and eventsby the calendars name
 - Example: `$getCalendar({"name":calendarName})`
- `$query(object)` — Returns relevant informationto the GraphQL query and variables provided - Example: `$query({"statement":'',"variables":{}})`
- `$document(object[, options])` — Generates and returnsan MFGx document basedon theid and payloadin a base64 encoded PDF string
 - Example: `$document({"documentDesignId": "","payload": {},"name": "","documentRenderFormatId": undefined,"culture": undefined}, { "returnContent": true })`
- `$mutate(object)` — Returns relevant informationto the GraphQL mutation and variables provided
 - Example: `$mutate({"statement":'',"variables":{}})`
- `$integrate(object)` — Executes the request provided and returns relevant information
 - Example: `$integrate({"Request": {"connectionName": "","payload": []}})`
- `$integrate(array)` — Executes the request provided and returns relevant information
- `$executeFlow(string,object)` — Executesa request/response flow basedon theid and payload and returns the valueof the response node.
- `$executeTransform(string,object)` — Executesa saved transform basedon theid of the transform. Returning the evaluationof the response.
- `$executeDeviceGatewayFunction(object)` — Executesa device gateway function.
 - Example: `$executeDeviceGatewayFunction({"deviceGatewayId": "","functionId": "","request": {}})`
- `$executeDeviceFunction(object)` — Executesa device function.
 - Example: `$executeDeviceFunction({"deviceId": "","functionId": "","request": {}})`
- `$executeDeviceFunction(object)` — Executesa device function.
 - Example: `$executeDeviceFunction({"requestId": "","deviceId": "","functionId": "","request": {} ,"functionTimeoutSeconds": 10})`

---

*Fuuz Industrial Operations Platform • JSONata Custom Bindings Reference v2.0 • [fuuz.com](https://fuuz.com)*