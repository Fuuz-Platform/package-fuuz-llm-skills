# Stimulsoft Reports & Dashboards — Fuuz Platform Skill

> **Purpose:** This skill provides Claude with comprehensive knowledge of Stimulsoft Reports and Dashboards for building reports, dashboards, and documents within the Fuuz Industrial Operations Platform (fuuz.com). Use this reference whenever designing report templates, configuring data bindings, writing expressions, building dashboards, or troubleshooting report rendering within Fuuz.

---

## 1. Core Concepts

### 1.1 Report Architecture

A Stimulsoft report template consists of these primary building blocks:

- **Pages** — The canvas; a template can have multiple pages. Each page has margins, orientation, columns, and watermark settings.
- **Bands** — Horizontal sections placed on a page that control *when* and *how* data is rendered. Bands are the backbone of every report.
- **Components** — Visual elements placed *inside* bands (text boxes, images, charts, barcodes, shapes, cross-tabs, tables, gauges, maps, sparklines, signatures, etc.).
- **Data Dictionary** — The data layer: connections, data sources, relations, variables, parameters, functions, resources, and business objects.
- **Expressions** — Curly-brace `{}` delimited code snippets that pull data, calculate values, and control output.

### 1.2 Report File Formats

| Extension | Description |
|-----------|-------------|
| `.mrt` | Report template (XML or JSON) |
| `.mrz` | Packed (compressed) report template |
| `.mrx` | Encrypted (password-protected) report template |

---

## 2. Band Types & Rendering Order

Bands render in a specific order. Understanding this order is critical for correct report layout.

### 2.1 Standard Bands

| Band | Purpose | Key Properties |
|------|---------|----------------|
| **ReportTitleBand** | Prints once at the very top of the report | — |
| **ReportSummaryBand** | Prints once at the very end of the report | `KeepReportSummaryTogether`, `PrintAtBottom`, `PrintIfEmpty` |
| **PageHeaderBand** | Repeats at the top of every page | `PrintOnEvenOddPages` |
| **PageFooterBand** | Repeats at the bottom of every page | `PrintOnEvenOddPages` |
| **GroupHeaderBand** | Prints at the start of each data group | `Condition`, `KeepGroupHeaderTogether` |
| **GroupFooterBand** | Prints at the end of each data group | `KeepGroupFooterTogether` |
| **HeaderBand** | Column/list header above a DataBand | `KeepHeaderTogether` |
| **FooterBand** | Column/list footer below a DataBand | `KeepFooterTogether` |
| **DataBand** | Iterates once per data row | `DataSource`, `MasterComponent`, `DataRelation`, `Sort`, `Filter`, `Columns`, `CanBreak`, `CalcInvisible` |
| **ChildBand** | Attached below a DataBand for multi-row layouts | `KeepChildTogether` |
| **EmptyBand** | Fills remaining space on a page | Multiple modes |
| **ColumnHeaderBand** | Header for columnar layouts | — |
| **ColumnFooterBand** | Footer for columnar layouts | — |
| **HierarchicalBand** | Renders tree/hierarchical data | `KeyDataColumn`, `MasterKeyDataColumn`, `ParentValue`, `Indent` |
| **OverlayBand** | Prints as a watermark/background | — |

### 2.2 Cross-Bands

Cross-bands span across the page vertically (top to bottom) and are typically used for vertical lines, borders, and backgrounds that should appear behind regular bands.

### 2.3 Rendering Order

The engine processes bands in this sequence for each page:

1. **PageHeaderBand**
2. **ReportTitleBand** (first page only)
3. **GroupHeaderBand** (at each group boundary)
4. **HeaderBand**
5. **DataBand** (iterated per row)
6. **ChildBand** (after each DataBand row)
7. **FooterBand**
8. **GroupFooterBand** (at each group boundary)
9. **ReportSummaryBand** (last page only)
10. **PageFooterBand**

---

## 3. Expressions

Expressions are the primary mechanism for displaying dynamic data, performing calculations, and controlling component behavior.

### 3.1 Syntax Rules

- Delimited by curly braces: `{expression}`
- Everything outside braces is treated as literal text
- Multiple code insertions allowed in a single text component
- **Nesting of code sections is NOT allowed** — `{outer {inner}}` is invalid
- Multi-line expressions are supported (press Enter for line breaks)

### 3.2 Referencing Data

```
// Data source field
{DataSourceName.ColumnName}

// Example
{Customers.CompanyName}
{Products.UnitPrice}

// Variable
{MyVariable}

// Component property
{ComponentName.PropertyName}
{MyComponent.Text}
```

### 3.3 Calculations in Expressions

```
// Math operations
{Products.UnitPrice * Products.Quantity}

// String concatenation
{"Name: " + Customers.CompanyName}

// Mixed text and code
Company: {Customers.CompanyName}, City: {Customers.City}

// Ternary / conditional (use IIF function)
{IIF(Products.UnitsInStock > 0, "In Stock", "Out of Stock")}
```

### 3.4 Using Aliases

Variables and data columns can be referenced by Name or by Alias:

```
// By name
{MyVariable}

// By alias (if alias contains spaces, use brackets)
{[my best variable]}

// If alias has no spaces, brackets are optional
{MyBestVariable}
```

### 3.5 Built-In Functions

Stimulsoft provides a rich function library accessible from the Expression Editor. Categories include:

- **Math:** `Abs`, `Ceiling`, `Floor`, `Round`, `Truncate`, `Maximum`, `Minimum`, etc.
- **String:** `Left`, `Right`, `Mid`, `Length`, `Trim`, `Upper`, `Lower`, `Replace`, `Insert`, `Remove`, `Substring`, etc.
- **Date/Time:** `DateDiff`, `DateSerial`, `Day`, `Month`, `Year`, `Hour`, `Minute`, `Now`, `Today`, etc.
- **Print State:** `IsFirstPage`, `IsLastPage`, `PageNumber`, `TotalPageCount`, `PageNofM`, `Line`, `LineThrough`, `GroupLine`, `Column`, etc.
- **Conditions:** `IIF(condition, trueValue, falseValue)`, `Choose`, `Switch`
- **Aggregates (Totals):** See Section 6 below
- **Formatting:** Format strings for numbers, currency, dates, percentages, booleans

### 3.6 .NET Framework Methods

Any .NET method is available in expressions:

```
// String methods
{Customers.CompanyName.ToUpper()}
{Customers.CompanyName.Substring(0, 5)}
{Customers.CompanyName.Length}

// Math
{Math.Round(Products.UnitPrice, 2)}
{Math.Abs(Products.UnitsInStock)}
```

---

## 4. Data Dictionary

The Data Dictionary is the central data management hub for every report.

### 4.1 Structure

```
Dictionary
├── Connections (DB connections)
│   └── Data Sources (tables, views, stored procedures)
│       ├── Columns
│       ├── Calculated Columns
│       └── Parameters (SQL query parameters)
├── Relations (links between data sources)
├── Variables (user-defined values, can prompt user input)
├── Business Objects (code-supplied data)
├── Resources (embedded files, images, fonts)
└── Functions (built-in and user-defined)
```

### 4.2 Data Connections

Stimulsoft supports multiple connection types: SQL Server, MySQL, PostgreSQL, Oracle, OleDB, ODBC, SQLite, Firebird, MongoDB, MS Access, REST/JSON, XML, CSV, Excel, Google Sheets, and more.

**Connection String Patterns:**
```
// OleDB
Provider=SQLOLEDB.1;Integrated Security=SSPI;Initial Catalog=myDB;Data Source=myServer

// SQL Server
Data Source=myServer;Initial Catalog=myDB;User Id=myUser;Password=myPass;
```

### 4.3 Data Sources

Two creation modes:
1. **Schema-based** — Retrieve tables from the connection, select columns visually
2. **Query-based** (Skip Schema Wizard) — Write SQL directly in the Query Text field

**Query Parameters** use `@ParameterName` syntax (or `?` for unnamed/OleDB):

```sql
SELECT * FROM Products WHERE CategoryID = @CategoryID
```

### 4.4 Calculated Columns

Virtual columns computed from expressions at render time:

```
// Example: percentage growth
((Year2009 - Year2000) / Year2000) * 100
```

### 4.5 Relations

Relations link data sources for Master-Detail reports. They define:
- **Parent Data Source** (Master)
- **Child Data Source** (Detail)
- **Key columns** in each source

Relations enable the `DataRelation` property on DataBands and `MasterComponent` linkage.

### 4.6 Variables

Variables store values that can be used in expressions. Key features:

- Can prompt the user for input at render time (`Panel Request From User`)
- Support types: String, Numeric, DateTime, Boolean, Range, List, etc.
- Can be nullable
- Can serve as SQL query parameters (`Allow using as SQL parameter`)
- Three modes: Value, Expression, Function

---

## 5. Creating Common Report Types

### 5.1 Simple List Report

1. Create a DataBand on the page
2. Set its `DataSource` property to the target data source
3. Place text components inside the DataBand with expressions: `{Customers.CompanyName}`, `{Customers.City}`
4. Add optional HeaderBand, FooterBand, ReportTitleBand, ReportSummaryBand
5. Use `EvenStyle` / `OddStyle` properties on the DataBand for alternating row colors

### 5.2 Master-Detail Report

1. Place two DataBands on the page (DataBand1 = Master, DataBand2 = Detail)
2. Set `DataSource` for each band
3. On DataBand2: set `MasterComponent` = DataBand1
4. Set `DataRelation` on DataBand2 to the relation between the two sources
5. Key properties: `PrintIfDetailEmpty`, `KeepDetails`

**Multi-level nesting:** Detail bands can themselves have Detail bands for 3+ level hierarchies.

### 5.3 Report with Groups

1. Start with a Simple List Report
2. Add `GroupHeaderBand` above the DataBand
3. Add `GroupFooterBand` below the DataBand
4. Set the `Condition` property of GroupHeaderBand (e.g., `{Customers.ContactTitle}`)
5. Put summary expressions in GroupFooterBand (e.g., `{Count()}`)
6. Key properties: `KeepGroupTogether`, `KeepGroupHeaderTogether`, `KeepGroupFooterTogether`
7. Nested groups are supported

### 5.4 Columnar Reports

**Columns on Page:**
- Set page-level column count, width, and gap
- Data flows across columns

**Columns in DataBand:**
- Set column properties on the DataBand itself
- Direction modes: `AcrossThenDown` or `DownThenAcross`
- Add ColumnHeaderBand / ColumnFooterBand as needed

### 5.5 Report with Charts

1. Place a Chart component on the page or inside a DataBand
2. Configure via the Chart Editor: Series, Area, Labels, Axes, Legend, Style
3. Series types: Bar, Line, Area, Pie, Doughnut, Scatter, Funnel, Treemap, Radar, Bubble, Candlestick, Waterfall, Histogram, Pictorial, Sunburst, and many more
4. Data binding: connect series to data sources via Value/Argument columns
5. AutoSeries, Filters, Conditions, TopN, TrendLine support

### 5.6 Cross-Tab Reports

1. Place a Cross-Tab component on the page or inside a DataBand
2. Configure via Cross-Tab Editor:
   - **Rows** — Data columns appearing as row headers
   - **Columns** — Data columns appearing as column headers
   - **Summary** — Aggregate values at intersections (Sum, Count, Avg, Min, Max, etc.)
3. Sort direction, conditions, showing totals, word wrap

### 5.7 Hierarchical Report

1. Use a HierarchicalBand instead of a standard DataBand
2. Set properties: `KeyDataColumn`, `MasterKeyDataColumn`, `ParentValue`, `Indent`
3. The band auto-indents child rows based on hierarchy depth

### 5.8 Sub-Reports

Sub-reports embed one report inside another:
- Place a SubReport component on a band
- Configure its own data source, bands, and components
- Sub-reports can receive parameters from the parent report

### 5.9 Drill-Down Reports

**Using Pages:** Set `Interaction.DrillDownEnabled` = true and `Interaction.DrillDownPage` to the detail page. Use Drill-Down Parameters to pass context.

**Using External Reports:** Set `Interaction.DrillDownReport` to the file path of the external report template.

### 5.10 Invoice Report

Combines multiple concepts: ReportTitleBand for header/logo, DataBand for line items, GroupFooterBand for subtotals, ReportSummaryBand for grand total, and static text/images for layout.

---

## 6. Aggregate / Total Functions

### 6.1 Band-Associated Totals (Calculated During Rendering)

| Function | Description |
|----------|-------------|
| `Sum(expression)` | Sum of values |
| `Count()` | Count of rows |
| `CountDistinct(expression)` | Count of distinct values |
| `Avg(expression)` | Average value |
| `Min(expression)` | Minimum value |
| `Max(expression)` | Maximum value |
| `First(expression)` | First value |
| `Last(expression)` | Last value |
| `Median(expression)` | Median value |
| `Mode(expression)` | Mode value |

### 6.2 Syntax Variants

```
// Auto-detect band
{Sum(Products.UnitPrice)}

// Specify band explicitly
{Sum(DataBand1, Products.UnitPrice)}

// Conditional (with If suffix)
{SumIf(Products.UnitPrice, Products.UnitPrice > 10)}
{SumIf(DataBand1, Products.UnitPrice, Products.UnitsInStock > 0)}
{CountIf(Products.UnitsInStock == 0)}

// Page/Container totals (c prefix)
{cSum(Products.UnitPrice)}
{cCount(DataBand1)}

// Column totals (col prefix)
{colSum(Products.UnitPrice)}
{colCount()}

// Master-Detail totals
{Count(MasterBand:DetailBand)}
```

### 6.3 Totals NOT Associated with Bands (Totals. Prefix)

These calculate independently of rendering position, useful for percentages and grand totals:

```
// Grand total across entire data source
{Totals.Sum(Products, Products.UnitPrice)}

// Percentage of category vs total
{(Sum(DataBand2, Products.UnitsInStock) / Totals.Sum(Products, Products.UnitsInStock)) * 100}
```

Available: `Totals.Sum`, `Totals.Count`, `Totals.Avg`, `Totals.Min`, `Totals.Max`, etc.

### 6.4 Running Totals

Enable the `Running Total` flag in the Summary tab of a text component to get cumulative totals that grow row by row.

### 6.5 Data Type Variants

By default, aggregate functions return `Decimal`. Append suffixes for other types:
- `SumD()` — returns `Double`
- `SumI()` — returns `Int64`

### 6.6 Placing Totals

Totals can be placed on **any band** in the report. If placed outside a natural footer band, you must specify the DataBand name as an argument. Use `ProcessAt = End` property if the value must be calculated after all data is processed.

---

## 7. Formatting & Appearance

### 7.1 Text Formatting

Supported format categories applied via the Format property or `{value:format}` syntax:

| Category | Examples |
|----------|----------|
| **Numeric** | `{0:N2}` (1,234.56), `{0:N0}` (1,235) |
| **Currency** | `{0:C}` ($1,234.56), `{0:C0}` ($1,235) |
| **Date** | `{0:d}` (short date), `{0:D}` (long date), `{0:yyyy-MM-dd}` |
| **Time** | `{0:t}` (short time), `{0:T}` (long time) |
| **Percentage** | `{0:P}` (123,456.00%), `{0:P1}` (123,456.0%) |
| **Boolean** | Custom true/false display strings |
| **Custom** | Any .NET format string |

### 7.2 Text Component Properties

| Property | Description |
|----------|-------------|
| `WordWrap` | Wrap text within component bounds |
| `CanGrow` / `CanShrink` | Auto-resize height based on content |
| `GrowToHeight` | Bind bottom border to tallest sibling |
| `ShrinkFontToFit` | Reduce font size to fit within bounds |
| `MaxNumberOfLines` | Limit output lines |
| `ProcessingDuplicates` | Control duplicate value display (Hide, Merge, GlobalHide, etc.) |
| `HideZeros` | Suppress null/zero values |
| `Editable` | Allow editing in PDF viewer |
| `TextRotation` | Rotate text 0-360 degrees |
| `LinesOfUnderline` | Add underline lines below text |
| `ProcessAt` | When to evaluate: `Default` or `End` (end of report rendering) |

### 7.3 HTML Tags in Text

Text components support inline HTML formatting:

```html
<font face="Arial" size="14" color="red">Styled text</font>
<b>Bold</b> <i>Italic</i> <u>Underline</u> <s>Strikethrough</s>
<sup>Superscript</sup> <sub>Subscript</sub>
<br> Line break
<p> Paragraph
<ol><li>Ordered list</li></ol>
<ul><li>Unordered list</li></ul>
```

CSS-style attributes: `background-color`, `text-align`, `letter-spacing`, `word-spacing`, `line-height`

### 7.4 Styles

Style types: Component, Chart, Gauge, Map, Cross-Tab, Table, Cards, Indicator, Progress, Report Control

**Style Designer:** Create named styles with specific Brush, Font, Border, Text Color, etc. Apply via `EvenStyle`/`OddStyle` properties for alternating rows.

**Style Collections:** Apply predefined theme collections to entire reports.

### 7.5 Conditional Formatting

Two condition types:
1. **Value Condition** — Compare a data field against a constant using operators (Equal, NotEqual, GreaterThan, etc.)
2. **Expression Condition** — Boolean expression: `Customers.CustomerName == "MyCustomer"`

Formatting actions: Font Name/Size/Bold/Italic/Underline, Text Color, Back Color, Borders, Enable/Disable Component, Data Bar, Color Scale, Icon Set.

Multiple conditions supported per component, evaluated in order.

---

## 8. Charts (Detailed)

### 8.1 Chart Structure

```
Chart
├── Area (plot background, axes, grid lines, interlacing)
│   ├── X Axis (labels, range, ticks, title)
│   ├── Y Axis (labels, range, ticks, title)
│   ├── X Top Axis
│   └── Y Right Axis
├── Series (data binding, type, appearance)
│   ├── Values / Arguments
│   ├── Filters
│   ├── Conditions
│   ├── Top N
│   ├── Trend Lines
│   └── Series Labels
├── Legend (title, alignment, marker, direction, visibility)
├── Title
├── Constant Lines
├── Strips
└── Style
```

### 8.2 Series Types (Partial List)

**Bar/Column:** Clustered, Stacked, FullStacked, Waterfall, Histogram, Pareto, Pictorial
**Line:** Line, StepLine, Spline, StackedLine, FullStackedLine
**Area:** Area, StepArea, SplineArea, StackedArea, FullStackedArea
**Pie/Doughnut:** Pie, Pie3D, Doughnut
**Financial:** Candlestick, Stock
**Other:** Scatter, Bubble, Radar, Funnel, Treemap, Sunburst, Range, RangeBar, Gantt

### 8.3 Series Data Binding

Series connect to data via:
- **Data Source** + **Value Column** + **Argument Column**
- **List of Values** (manual entry)
- **AutoSeries** mode (auto-detect from data)

### 8.4 Key Chart Properties

- `ColorEach` — Different color per data point
- `ReverseHorizontal` / `ReverseVertical` — Flip axes
- `StartFromZero` — Force Y axis to start at zero
- `AllowApplyStyle` — Enable/disable style inheritance
- `CutPieList` — Explode pie slices

---

## 9. Cross-Tab Component

### 9.1 Structure

| Part | Description |
|------|-------------|
| **Rows** | Data columns serving as row headers |
| **Columns** | Data columns serving as column headers |
| **Summary** | Aggregate function applied at each row/column intersection |

### 9.2 Summary Types

Sum, Count, Average, Min, Max, CountDistinct, Median, Mode, First, Last, and more.

### 9.3 Configuration

- **Sort Direction** per row/column
- **Show Totals** for rows/columns
- **Conditions** for conditional formatting within cells
- **Word Wrap** for cell content
- **Data Source** property for direct data binding

---

## 10. Table Component

Tables provide a grid-based alternative to DataBands:

- Define **Rows** and **Columns** visually
- Set expressions in individual cells
- `DataSource`, `MasterComponent`, `Relation` properties for data binding
- `AutoWidth` / `AutoWidthType` / `FixedWidth` for column sizing
- `CellType` for special cell behaviors
- Tables support Headers, Footers, and Grouping

---

## 11. Barcodes

### 11.1 Supported Types

**Linear:** Code128, Code39, Code93, EAN-13, EAN-8, UPC-A, UPC-E, ISBN, ITF-14, Codabar, Postnet, Pharmacode, Plessey, MSI, Code11, and many more.

**2D:** QR Code, DataMatrix, PDF417

### 11.2 Usage

1. Place a Barcode component
2. Set the barcode type
3. Bind the value to a data expression (e.g., `{Products.ProductID}`)
4. Configure size, show/hide text, colors

---

## 12. Interactive Features

### 12.1 Bookmarks

- Set `Interaction.Bookmark` on components to create navigation bookmarks
- Expression-based hierarchical bookmarks: `%\{Country}\{CompanyName}\{Phone}`
- Bookmarks appear in a tree panel in the viewer

### 12.2 Hyperlinks

- **Internal (Bookmark):** `#{BookmarkName}` — navigates within report
- **Internal (Tag):** `##{TagValue}` — navigates using Tag property
- **External:** Direct URL, file path, or email link

### 12.3 Drill-Down

- `Interaction.DrillDownEnabled` = true
- `Interaction.DrillDownPage` = target page (same template)
- `Interaction.DrillDownReport` = external .mrt file
- Parameters passed via `Interaction.DrillDownParameters`

### 12.4 Dynamic Sorting

Enable dynamic column sorting in the viewer preview, allowing end users to click column headers to sort.

### 12.5 Dynamic Collapsing

Enable expandable/collapsible groups in the viewer.

### 12.6 Editing

Components with `Editable` = true can be modified by the user in the viewer.

---

## 13. Sub-Reports

- Self-contained report units embedded within a parent report
- Can have their own data sources, bands, and components
- Support parameter passing from the parent
- Placement: on Page, on DataBand (iterated per row), or as Master-Detail

---

## 14. Panels & Side-by-Side Reports

- **Panels** are containers that can hold bands and other components
- Enable **side-by-side reports** (two independent data lists on the same page)
- Panels can be placed on pages, bands, or other panels
- Support auto-sizing and cloning

---

## 15. Page & Component Layout

### 15.1 Autosize

| Property | Behavior |
|----------|----------|
| `CanGrow` | Component height increases to fit content |
| `CanShrink` | Component height decreases when content is short |
| `AutoWidth` | Component width adjusts to content |
| `GrowToHeight` | Bottom border aligns with tallest sibling component |
| `ShiftMode` | Components below automatically shift when above components resize |

### 15.2 Page/Column Breaks

- `NewPageBefore` / `NewPageAfter` — Force page breaks
- `NewColumnBefore` / `NewColumnAfter` — Force column breaks
- `BreakIfLessThan` — Break if remaining space is less than specified
- `SkipFirst` — Skip the first break occurrence

### 15.3 Pagination

System variables: `{PageNumber}`, `{TotalPageCount}`, `{PageNofM}`

`ResetPageNumber` property resets page numbering at group boundaries.

### 15.4 Component Breaking

Long content can break across pages:
- Text: `CanBreak` property
- Panels: break at band boundaries
- RichText, Images: breakable with specific settings

---

## 16. Dashboards

### 16.1 Overview

Dashboards are a distinct viewing mode in Stimulsoft. A dashboard is a special page type that contains interactive dashboard elements that auto-stretch to fill the viewer.

### 16.2 Creating Dashboards

Use **File > New > Blank Dashboard** to create a new dashboard page. Dashboard elements are placed on the canvas and auto-layout to fill the viewer.

### 16.3 Dashboard Elements

- **Chart Elements** — Interactive charts with filtering
- **Table Elements** — Data grids
- **Indicator Elements** — KPI gauges and indicators
- **Progress Elements** — Progress bars
- **Cards Elements** — Metric cards
- **Combo Box** — Dropdown filter controls (stretches width only)
- **Date Picker** — Date range filter
- **Tree View Box** — Hierarchical filter
- **List Box** — List-based filter
- **Image** — Static or dynamic images
- **Text** — Labels and titles
- **Panel** — Container for grouping elements
- **Region Map / Online Map** — Geographic visualizations
- **Pivot Table** — Interactive cross-tab

### 16.4 Dashboard Interactivity

- **Filtering:** Click chart segments, map regions, or list items to filter all other elements
- **Multi-select filtering:** Toggle to filter by multiple segments simultaneously
- **Clear filters:** Reset button per element
- **Drill-down:** Navigate through data hierarchy levels
- **Full screen:** View individual elements or entire dashboard in full screen
- **View Data:** Inspect raw data behind any element
- **Sort:** Dynamic sorting per element via toolbar
- **Refresh:** Update dashboard data

### 16.5 Dashboard Controls

Controls appear in the upper-right corner:
- **Refresh** — Update data
- **Open** — Open saved report
- **Edit** — Modify in designer (requires `CalculationMode = Interpretation`)
- **Full Screen** — Expand to full viewport
- **Save/Export** — Save as PDF, Excel, PNG

### 16.6 Exporting Dashboards

Dashboards can be exported to: PDF, Excel (data), PNG image.

---

## 17. Data Transformation

### 17.1 Available Transformations

| Transformation | Description |
|----------------|-------------|
| **Join Tables** | Combine multiple data sources |
| **Grouping** | Group rows by column values |
| **Using Functions** | Apply aggregate functions (Sum, Count, Avg, etc.) |
| **Filtering** | Filter rows by conditions |
| **Skip and Limit** | Skip N rows, limit to N rows |
| **Replace Values** | Substitute specific values |
| **Running Total** | Cumulative calculations |
| **Show Percentage** | Calculate percentages |
| **Sorting** | Order data by columns |

Data transformations are configured in the Data Dictionary and produce derived data sources.

---

## 18. Export Formats

### 18.1 Supported Formats

| Category | Formats |
|----------|---------|
| **Fixed Layout** | PDF, XPS, PowerPoint |
| **Web** | HTML, HTML5, MHT |
| **Text/Document** | Word (DOCX), ODT, RTF, TXT |
| **Spreadsheet** | Excel (XLS, XLSX), Excel XML, CSV, DBF, XML, SYLK |
| **Image** | BMP, GIF, PNG, TIFF, JPEG, PCX, EMF, SVG |

### 18.2 Key Export Settings

- **Image Quality** — JPEG compression 0.0 - 1.0
- **Image Resolution** — DPI (default 100; 300 for print)
- **Embedded Fonts** — Include fonts in PDF for portable rendering
- **PDF/A Compliance** — Long-term archival standard
- **Digital Signature** — Sign PDFs with certificates
- **Encryption** — Password-protect PDFs with user/owner passwords
- **Page Selection** — Export all, current, or specific pages

---

## 19. Report Designer Reference

### 19.1 Key Tabs

| Tab | Purpose |
|-----|---------|
| **Home** | Clipboard, font, alignment, borders, style, layout tools |
| **Insert** | Add bands, components (text, image, chart, barcode, etc.) |
| **Page** | Page settings, columns, watermark |
| **Layout** | Alignment, sizing, spacing, ordering of components |

### 19.2 Report Template Properties

- `CalculationMode` — Compilation or Interpretation
- `ReportCulture` — Locale for formatting
- `RefreshTime` — Auto-refresh interval
- `RetrieveOnlyUsedData` — Optimize by loading only needed data
- `ScriptLanguage` — C# or VB.NET

### 19.3 Hotkeys (Key Shortcuts)

| Action | Shortcut |
|--------|----------|
| Preview | F5 |
| Save | Ctrl+S |
| Undo | Ctrl+Z |
| Redo | Ctrl+Y |
| Copy | Ctrl+C |
| Paste | Ctrl+V |
| Delete | Delete |
| Select All | Ctrl+A |

---

## 20. Right-to-Left (RTL) Support

Stimulsoft supports RTL text rendering for Arabic, Hebrew, and other RTL languages across:
- Text Components
- Text In Cells
- Cross-Tab
- Charts
- Columnar layouts

---

## 21. System Variables

| Variable | Returns |
|----------|---------|
| `{PageNumber}` | Current page number |
| `{TotalPageCount}` | Total pages in rendered report |
| `{PageNofM}` | "Page N of M" formatted string |
| `{Line}` | Current row number within the DataBand |
| `{LineThrough}` | Row number across all pages |
| `{GroupLine}` | Row number within current group |
| `{Column}` | Current column index (columnar layouts) |
| `{IsFirstPage}` | Boolean: is this the first page? |
| `{IsLastPage}` | Boolean: is this the last page? |
| `{ReportAlias}` | Report alias string |
| `{ReportAuthor}` | Report author string |
| `{ReportDescription}` | Report description string |
| `{ReportName}` | Report name string |
| `{Today}` | Current date |
| `{Time}` | Current time |

---

## 22. Fuuz-Specific Guidance

### 22.1 Data Integration Patterns

When building reports for Fuuz, data typically comes from:
- **Fuuz REST API endpoints** — JSON data sources connected via REST adapter
- **Direct database connections** — SQL Server, PostgreSQL, or MongoDB connections to the Fuuz data model
- **Fuuz platform variables** — Parameters passed from Fuuz screens (e.g., Work Order ID, Machine ID, Date Range)

### 22.2 Common Fuuz Report Types

| Report Type | Approach |
|-------------|----------|
| **OEE Dashboard** | Dashboard page with Indicator (OEE %), Chart (trend over time), Table (breakdown by machine) |
| **Production Log** | Master-Detail: Work Orders (master) -> Production Entries (detail) with GroupFooter totals |
| **Quality Report** | Grouped report by Inspection Point with conditional formatting (Pass/Fail) |
| **Downtime Analysis** | Cross-Tab: Machines (rows) x Downtime Reasons (columns) with Sum of Duration |
| **Work Order Traveler** | Invoice-style: ReportTitle with header info, DataBand for operations, signature component |
| **Inventory Report** | Simple list with conditional formatting for low stock, subtotals by category |
| **Shift Summary** | Groups by Shift with aggregates for production counts, scrap, and efficiency |

### 22.3 Mobile-Friendly Reports

For Fuuz mobile screen templates:
- Use percentage-based column widths where possible
- Keep cross-tabs to 3-5 columns maximum
- Use `ShrinkFontToFit` for variable-width displays
- Prefer charts and indicators over dense tables
- Test at common mobile breakpoints

### 22.4 Best Practices for Fuuz Reports

1. **Always define Relations** between data sources to enable proper Master-Detail filtering
2. **Use Variables** for parameters passed from Fuuz screens (Work Order ID, Date Range, Machine ID, etc.)
3. **Set `RetrieveOnlyUsedData = true`** to optimize query performance against the Fuuz database
4. **Use conditional formatting** to highlight OEE thresholds, quality failures, and overdue work orders
5. **Include drill-down** from summary dashboards to detail reports for operator-level investigation
6. **Design for export** — Fuuz users frequently export to PDF and Excel; test both formats
7. **Use Totals functions** appropriately — band-associated for section summaries, `Totals.*` for grand totals and percentages
8. **Apply consistent styles** across all Fuuz reports using Style Collections for brand consistency

---

## 23. Troubleshooting Quick Reference

| Issue | Solution |
|-------|----------|
| Expression returns blank | Check data source is connected and column names match exactly (case-sensitive in C#) |
| Totals show 0 | Ensure the function's DataBand argument is correct; check `CalcInvisible` if band is hidden |
| Master-Detail shows all detail rows | Set the `DataRelation` property on the Detail DataBand |
| Page numbers wrong after groups | Use `ResetPageNumber` property on GroupHeaderBand |
| Cross-tab columns misaligned | Check `WordWrap` and column width settings |
| Chart shows no data | Verify Series data binding: correct DataSource, Value Column, and Argument Column |
| PDF fonts look wrong | Enable `EmbeddedFonts` in PDF export settings |
| Report too slow | Enable `RetrieveOnlyUsedData`, optimize SQL queries, reduce image resolution |
| Dashboard elements not filtering | Ensure all elements share the same data source or have proper relations |

---

*This skill is based on the Stimulsoft Reports and Dashboards User Manual and tailored for the Fuuz Industrial Operations Platform. For the complete Stimulsoft documentation, refer to https://www.stimulsoft.com/en/documentation*