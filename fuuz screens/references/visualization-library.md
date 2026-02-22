# Fuuz Visualization Library â€” Chart Configuration Reference

This document provides a comprehensive reference for every chart type available in the Fuuz Industrial Operations Platform visualization library. Each chart entry includes its `chartType` identifier, description, configurable chart properties, and supported data structures.

The Fuuz visualization engine is powered by FusionCharts. All chart configurations follow a JSON schema where `chart` properties control appearance and behavior, and additional data structure objects (`colorRange`, `dials`, `dataset`, `categories`, `trendlines`, etc.) define the data payload.

> **Note:** Every chart type also supports a common set of `FusionCharts` constructor properties (message fonts, colors, load/error messages, etc.) which are omitted from individual entries for brevity. See the [Common FusionCharts Constructor Properties](#common-fusionchart-constructor-properties) section at the end of this document.

---

## Table of Contents

1. [Gauges & Indicators](#gauges-indicators)
   - [Angular Gauge](#angular-gauge)
   - [Horizontal LED](#horizontal-led)
   - [bulb](#bulb)
   - [cylinder](#cylinder)
   - [hbullet](#hbullet)
   - [thermometer](#thermometer)
   - [vbullet](#vbullet)
   - [vled](#vled)
2. [Single-Series Charts](#single-series-charts)
   - [Area](#area)
   - [Bar](#bar)
   - [Column](#column)
   - [Line](#line)
   - [Logarithmic Column](#logarithmic-column)
   - [Logarithmic Line](#logarithmic-line)
   - [Overlapped Bar](#overlapped-bar)
   - [inverseMSArea](#inversemsarea)
   - [inverseMSColumn2D](#inversemscolumn2d)
   - [inverseMSLine](#inversemsline)
   - [overlappedcolumn2d](#overlappedcolumn2d)
3. [Multi-Series Charts](#multi-series-charts)
   - [MSArea](#msarea)
   - [Multi-Axis Line](#multi-axis-line)
   - [Multi-series Bar](#multi-series-bar)
   - [Multi-series Column](#multi-series-column)
   - [msbar3d](#msbar3d)
   - [mscolumn3d](#mscolumn3d)
   - [mscolumnline3d](#mscolumnline3d)
   - [msstepline](#msstepline)
4. [Stacked Charts](#stacked-charts)
   - [msstackedcolumn2d](#msstackedcolumn2d)
   - [msstackedcolumn2dlinedy](#msstackedcolumn2dlinedy)
   - [stackbar2d](#stackbar2d)
   - [stackbar3d](#stackbar3d)
   - [stackedarea2d](#stackedarea2d)
   - [stackedarea2dlinedy](#stackedarea2dlinedy)
   - [stackedcolumn2d](#stackedcolumn2d)
   - [stackedcolumn2dline](#stackedcolumn2dline)
   - [stackedcolumn2dlinedy](#stackedcolumn2dlinedy)
   - [stackedcolumn3d](#stackedcolumn3d)
   - [stackedcolumn3dline](#stackedcolumn3dline)
   - [stackedcolumn3dlinedy](#stackedcolumn3dlinedy)
5. [Combination Charts](#combination-charts)
   - [mscolumn3dlinedy](#mscolumn3dlinedy)
   - [mscombi2d](#mscombi2d)
   - [mscombi3d](#mscombi3d)
   - [mscombidy2d](#mscombidy2d)
   - [mscombidy3d](#mscombidy3d)
6. [Scroll Charts](#scroll-charts)
   - [Scroll Stacked Bar](#scroll-stacked-bar)
   - [Scrollable Bar](#scrollable-bar)
   - [Scrollable Column](#scrollable-column)
   - [scrollarea2d](#scrollarea2d)
   - [scrollcombi2d](#scrollcombi2d)
   - [scrollcombidy2d](#scrollcombidy2d)
   - [scrollline2d](#scrollline2d)
   - [scrollmsstackedcolumn2d](#scrollmsstackedcolumn2d)
   - [scrollmsstackedcolumn2dlinedy](#scrollmsstackedcolumn2dlinedy)
   - [scrollstackedcolumn2d](#scrollstackedcolumn2d)
7. [Pie & Doughnut Charts](#pie-doughnut-charts)
   - [Doughnut 2D Chart](#doughnut-2d-chart)
   - [Pie](#pie)
   - [doughnut3d](#doughnut3d)
   - [multilevelpie](#multilevelpie)
   - [pie3d](#pie3d)
8. [Scatter & Bubble Charts](#scatter-bubble-charts)
   - [Error Scatter](#error-scatter)
   - [Scatter](#scatter)
   - [bubble](#bubble)
   - [selectscatter](#selectscatter)
9. [Real-Time Charts](#real-time-charts)
   - [realtimearea](#realtimearea)
   - [realtimecolumn](#realtimecolumn)
   - [realtimeline](#realtimeline)
   - [realtimelinedy](#realtimelinedy)
   - [realtimestackedarea](#realtimestackedarea)
   - [realtimestackedcolumn](#realtimestackedcolumn)
10. [Specialty Charts](#specialty-charts)
   - [Error Bar](#error-bar)
   - [Error Line](#error-line)
   - [Funnel Chart](#funnel-chart)
   - [Pareto](#pareto)
   - [Pyramid](#pyramid)
   - [box and whisker](#box-and-whisker)
   - [candlestick](#candlestick)
   - [chord](#chord)
   - [dragarea](#dragarea)
   - [dragcolumn2d](#dragcolumn2d)
   - [dragline](#dragline)
   - [dragnode](#dragnode)
   - [gantt](#gantt)
   - [heatmap](#heatmap)
   - [kagi](#kagi)
   - [marimekko](#marimekko)
   - [radar](#radar)
   - [sankey](#sankey)
   - [sunburst](#sunburst)
   - [treemap](#treemap)
   - [waterfall2d](#waterfall2d)
11. [Spark Charts](#spark-charts)
   - [sparkcolumn](#sparkcolumn)
   - [sparkline](#sparkline)
   - [sparkwinloss](#sparkwinloss)
12. [Spline Charts](#spline-charts)
   - [Multi-series Spline](#multi-series-spline)
   - [Multi-series Spline Area](#multi-series-spline-area)
   - [Single-series Spline](#single-series-spline)
   - [Single-series Spline Area](#single-series-spline-area)
13. [3D Chart Variants](#3d-chart-variants)
   - [Bar3D](#bar3d)
   - [column3d](#column3d)
   - [pareto3d](#pareto3d)
14. [Zoom Charts](#zoom-charts)
   - [zoomline](#zoomline)
   - [zoomlinedy](#zoomlinedy)
   - [zoomscatter](#zoomscatter)
15. [Other](#other)
   - [Time Series](#time-series)
16. [Common FusionCharts Constructor Properties](#common-fusionchart-constructor-properties)

---

## Gauges & Indicators

### Angular Gauge

**Chart Type:** `angulargauge`

**Description:** An angular gauge is used to show a specific value over a radial scale. The gauge is rendered with a radial scale that displays the data range. This scale can be color-coded to indicate divisions within the range. A dial is used to point to the data value. The gauge can also be rendered with multiple dials. It is often used to simulate a speedometer and on dashboards.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `lowerLimit` | number | This attribute helps you explicitly set the lower limit of the chart. If you don't specify this value, the chart automatically calculates the lower limit based on the data provided by you. |
| `numberSuffix` | string | Using this attribute, you could add suffix to all the numbers visible on the graph. For example, to represent all figures quantified as per annum on the chart, you could specify this attribute to '... |
| `showToolTip` | boolean | Whether to show tooltip for the chart? |
| `showValue` | boolean | Whether to show each dial's value? |
| `subCaption` | string | Sub-caption of the chart. |
| `upperLimit` | number | This attribute helps you explicitly set the upper limit of the chart. If you don't specify this value, the chart automatically calculates the upper limit based on the data provided by you. |

#### Data Structures

- **`colorRange`** (object): colorRange
  - `color` (array): color
- **`dials`** (object): dials
  - `dial` (array): dial
- **`trendPoint`** (object): trendPoint
  - `point` (array): point

---

### Horizontal LED

**Chart Type:** `hled`

**Description:** The horizontal LED gauge is used to indicate a specific value on a linear, horizontal scale. The gauge is visualized using a horizontally-aligned linear scale with bars that change color, or marks out different regions in different colors to indicate whether data is within preset parameters. The data value is plotted by rendering the rest of the gauge with a shadow. For example, if the data value is 90%, the remaining 10% is rendered with a shadow effect. Tick marks are shown along the top/bottom edge of the gauge to plot the data values. This gauge can be used as a fuel level or inventory level indicator on a dashboard. It can also be used in financial applications like management dashboards, factory operation output reports, and so on.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `lowerLimit` | number | This attribute helps you explicitly set the lower limit of the chart. If you don't specify this value, the chart automatically calculates the lower limit based on the data provided by you. |
| `lowerLimitDisplay` | string | This attribute allows you to display a label instead of the lower limit. For example, in a chart displaying Literacy Rate on a scale of 0 - 100%, you may need to show the label Low at the starting ... |
| `numberSuffix` | string | Using this attribute, you could add suffix to all the numbers visible on the graph. For example, to represent all figures quantified as per annum on the chart, you could specify this attribute to '... |
| `subCaption` | string | Sub-caption of the chart. |
| `tickMarkDistance` | number | Distance between the gauge and the tick marks. |
| `upperLimit` | number | This attribute helps you explicitly set the upper limit of the chart. If you don't specify this value, the chart automatically calculates the upper limit based on the data provided by you. |
| `upperLimitDisplay` | string | This attribute allows you to display a label instead of the upper limit. Upon using this attribute, the upper limit of the chart gets replaced by the label specified. Default value: If you do not s... |

#### Data Structures

- **`colorRange`** (object): colorRange
  - `color` (array): color

---

### bulb

**Chart Type:** `bulb`

**Description:** The bulb gauge is used to indicate whether the value being monitored falls within the defined limits and if it does, then which range does it belong to. The gauge is visualized as a circle filled with color. Depending on the range that the data value belongs to, the color of the bulb changes and the display value is set accordingly. The gauge can be configured as a temperature indicator

#### Data Structures

- **`colorRange`** (object): colorRange
  - `color` (array): color

---

### cylinder

**Chart Type:** `cylinder`

**Description:** The cylinder gauge is represented as a vertical cylinder, whose fill level is defined by the data value being plotted. The gauge is filled with color according to the data value to be indicated. Tick marks are shown along the left/right edge of the cylinder to plot the data values. The gauge can be used to represent inventory levels, fuel levels, and so on.

---

### hbullet

**Chart Type:** `hbullet`

**Description:** The horizontal bullet gauge is used to compare a measure against a preset value while also showing the qualitative range the measured value falls in. It also indicates if the value is good, bad or ugly, and all this in a limited space. The gauge is rendered as a horizontal bar, divided and color-coded according to the qualitative ranges decided. It is used to display a single key measure. A horizontal line, the target line, is drawn within the horizontal bar as an indicator of the measured value. This gauge can be used on a dashboard to compare the revenue earned against the targeted revenue.

---

### thermometer

**Chart Type:** `thermometer`

**Description:** A thermometer gauge is used to showcase temperature readings or as a performance indicator. The gauge is visualized to look like a thermometer, with tick marks draw along the left/right edge of the thermometer to plot the temperature values. Fill coloring is used to used to indicate the actual data value. This gauge can be used on a dashboard as a performance indicator.

---

### vbullet

**Chart Type:** `vbullet`

**Description:** The vertical bullet gauge is used to compare a measure against a preset value while also showing the qualitative range the measured value falls in. It also indicates if the value is good, bad or ugly, and all this in a limited space. The gauge is rendered as a vertical bar, divided and color-coded according to the qualitative ranges decided. It is used to display a single key measure. A vertical line, the target line, is drawn within the vertical bar as an indicator of the measured value. This gauge can be used on a dashboard to compare data like the revenue earned against the targeted revenue.

---

### vled

**Chart Type:** `vled`

**Description:** The vertical LED gauge is used to indicate a specific value on a linear, vertical scale. It is visualized using a vertically-aligned linear scale with bars that change color, or marks out different regions in different colors to indicate whether data is within preset parameters. The data value is plotted by rendering the rest of the gauge with a shadow. For example, if the data value is 90%, the remaining 10% is rendered with a shadow effect. Tick marks are shown along the left/right edge of the gauge to plot the data values. The gauge can be used as a fuel level or inventory level indicator on a dashboard. It can also be used in financial applications like management dashboards, factory operation output reports, and so on. 

#### Data Structures

- **`colorRange`** (object): colorRange
  - `color` (array): color

---

## Single-Series Charts

### Area

**Chart Type:** `area2d`

**Description:** An area chart is like a line chart in terms of how data values are plotted; data points are plotted on the chart and connected using line segments. In an area chart, however, the area between the line segments and the x-axis is filled with color. This chart is a good choice when you want to show trends over time, without focusing on specific data points. It effectively highlights the total value across a trend. For visualizing sales data, the area chart shows total sales over time. This chart works better when used to plot a small number of data points, since the entire area is colored.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `showValues` | boolean | Sets the configuration whether data values will be displayed along with the data plot on chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Bar

**Chart Type:** `bar2d`

**Description:** A 2D bar chart is used to represent quantitative information. The chart consists of horizontally aligned rectangular bars of equal width with lengths proportional to the values they represent. This helps in the instant comparison of data. One axis of the chart plots categories and the other axis represents the value scale. This chart is used for comparing data such as the GDP growth of various countries, performance of athletes in a sport, and so on.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Column

**Chart Type:** `column2d`

**Description:** A column chart is used to visualize comparative data or to show change over a period of time. The chart uses vertically aligned rectangular bars on one axis as data plots plotted against the discrete values shown on the other. On one axis, columns compare categories, while on the other they represent a discrete value. This chart can be used to show data like revenue per store, monthly footfalls in a store, earnings per quarter, monthly order volumes, and so on.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Line

**Chart Type:** `line`

**Description:** A line chart is used to represent continuous data over a continuous time span. It is generally used to show trends and relationships in data. Data values are plotted as data points that are connected using line segments. The chart is widely used to show continuous data like temperature, earnings per quarter, data for experimental sciences, and so on.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `lineThickness` | number | Thickness of the lines on the chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Logarithmic Column

**Chart Type:** `LogMSColumn2D`

**Description:** Log Charts are used to respond to skewness towards large values; i.e., cases in which one or a few points are much larger than the bulk of the data. The second is to show percent change or multiplicative factors.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `base` | number | Using this attribute, you can set the base of the logarithmic chart. The value has to be a positive numeric value apart from 1. |
| `caption` | string | Caption of the chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Logarithmic Line

**Chart Type:** `LogMSLine`

**Description:** A logarithmic multi-series line chart uses a logarithmic axis, instead of a linear one, to show the magnitude of change for multiple categories of data. Data values are plotted on the chart as data segments that are then connected using line segments. The y-axis is drawn using a logarithmic scale, with the base set to 10. This chart is used when a small number of data points have values that are much larger than most of the other data points, in the same dataset. It is also used when a multiplicative factor or a percent change has to be showcased.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `base` | number | Using this attribute, you can set the base of the logarithmic chart. The value has to be a positive numeric value apart from 1. |
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Overlapped Bar

**Chart Type:** `overlappedbar2d`

**Description:** Overlapping bars can be used to visualize two data sets on a single chart. Similar to a simple bar chart, this chart uses horizontally aligned rectangular bars on one axis as data plots plotted against the discrete values shown on the other. On one axis, bars compare categories, while on the other they represent a discrete value. The width of the bars for the data series differs which makes the chart easy to compare. Remember, both the datasets need to be of the same data type, like revenue or cost. Selecting or deselecting legend labels allows a single data set view or comparison of two data sets simultaneously.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `showLabels` | boolean | It sets the configuration whether the x-axis labels will be displayed or not. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### inverseMSArea

**Chart Type:** `inverseMSArea`

**Description:** The inverse y-axis area chart is used to visualize comparative data against a y-axis that plots the data values in the inverse order. The chart is drawn using data points connected using line segments. The area between the line segments and the x-axis is filled with color. The y-axis is rendered with the lower limit value at the top and the upper limit value at the bottom. The inverse y-axis can be used to show performance-related data for events like a sports competition, employee rating, and so on. 

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### inverseMSColumn2D

**Chart Type:** `inverseMSColumn2D`

**Description:** The inverse y-axis column 2D chart is used to visualize comparative data against a y-axis that plots the data values in the inverse order.The chart is drawn using vertically aligned rectangular bars on one axis plotted against discrete values shown on the other. The y-axis is rendered with the lower limit value at the top and the upper limit value at the bottom. The inverse y-axis can be used to show performance-related data for events like a sports competition, employee rating, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### inverseMSLine

**Chart Type:** `inverseMSLine`

**Description:** The inverse y-axis line chart is used to visualize comparative data against a y-axis that plots the data values in the inverse order. The chart is drawn using data points connected using line segments. The y-axis is rendered with the lower limit value at the top and the upper limit value at the bottom. The inverse y-axis can be used to show performance-related data for events like a sports competition, employee rating, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### overlappedcolumn2d

**Chart Type:** `overlappedcolumn2d`

**Description:** Overlapping columns can be used to visualize two data sets on a single chart. Similar to a simple column chart, this chart uses vertically aligned rectangular columns on one axis as data plots plotted against the discrete values shown on the other. On one axis, columns compare categories, while on the other they represent a discrete value. The width of the columns for the data series differs which makes the chart easy to compare.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## Multi-Series Charts

### MSArea

**Chart Type:** `MSArea`

**Description:** The multi-series area chart is rendered with data values pointed as data points that are connected using line segments. The area between the line segments and the x-axis/edges of other charts is filled with color. This chart puts emphasis on the magnitude of change over time and effectively highlights the total value across a trend. For example, when visualizing sales data, this chart can be used to show total sales over time.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`data`** (array): data
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Multi-Axis Line

**Chart Type:** `MultiAxisLine`

**Description:** A multi-axis line chart is an interactive line chart that plots data using multiple axes. This allows plotting data sets having different units and scale ranges. Data values are plotted as data points that are connected by line segments. Multiple y-axes are rendered along the left and right side of the chart. This chart can be used to plot the revenue, the orders, and the footfalls of a store on the same chart.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `alternateVGridAlpha` | number | Alpha of alternate vertical colored grid bands. |
| `caption` | string | Caption of the chart. |
| `labelDisplay` | string | Using this attribute, you can customize the alignment of data labels (x-axis labels). There are 5 options: AUTO, WRAP, STAGGER, ROTATE or NONE. By default, this attribute is set to AUTO mode which ... |
| `numVDivLines` | number | Number of vertical axis division lines |
| `subCaption` | string | Sub-caption of the chart. |
| `vDivLineAlpha` | number | Alpha of vertical axis division lines. |
| `xAxisName` | string | X-axis title of the chart. |

#### Data Structures

- **`axis`** (array): axis
  - `axisOnLeft` (boolean)
  - `defaultNumberScale` (string)
  - `numDivLines` (number)
  - `numberScaleUnit` (string)
  - `numberScaleValue` (string)
  - `numberSuffix` (string)
  - `tickWidth` (number)
  - `title` (string)
- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data

---

### Multi-series Bar

**Chart Type:** `msbar2d`

**Description:** The multi-series 2D bar chart is used for comparing data from multiple datasets. It is also used to analyze data grouped in sub-categories. It has horizontally aligned rectangular bars on one axis with discrete values shown on the other. The length of the bar is proportionate to the value it represents. Using this chart, you can visualize data like revenue for each month for the last couple of years and so on.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `placeValuesInside` | boolean | If you've opted to show data values, you can show them inside the columns using this attribute. By default, the data values show outside the column. |
| `showLabels` | boolean | It sets the configuration whether the x-axis labels will be displayed or not. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisLineColor` | string | Sets color of the x-axis of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Multi-series Column

**Chart Type:** `mscolumn2d`

**Description:** The Multi-series Column 2D chart is used to plot data for multiple datasets. It is also used to analyze and compare data points grouped in sub-categories. It has vertically aligned rectangular bars on one axis with discrete values shown on the other. The length of a column is proportionate to the values it represents. This chart can be used to visualize data such as the revenue for each month for a couple of years.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### msbar3d

**Chart Type:** `msbar3d`

**Description:** The multi-series bar 3D chart is used to compare data for multiple datasets. It is also used to analyze data grouped in sub-categories. It has horizontally aligned rectangular bars on one axis with discrete values shown on the other. The length of the bar is proportionate to the value it represents.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### mscolumn3d

**Chart Type:** `mscolumn3d`

**Description:** The multi-series column 3D chart is used to plot data for multiple datasets. It is also used to analyze and compare data points grouped in sub-categories. It has vertically aligned rectangular bars on one axis with discrete values shown on the other. The length of a column is proportionate to the values it represents

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### mscolumnline3d

**Chart Type:** `mscolumnline3d`

**Description:** The multi-series column 3D plus line chart with a single y-axis is used to plot data that belongs to multiple datasets, that need separate chart types. This chart can be used to visualize data like fixed costs, variable costs, and budgeted costs on the same chart.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### msstepline

**Chart Type:** `msstepline`

**Description:** A step line chart is used to visualize trends for a particular event that is not continuous in nature. For a given data set, this chart not only enables you to compare the magnitude and change in values at different points of the same series but also helps you discern the intermittent pattern of the trend. Data values are plotted as data points on the chart that are connected using vertical and horizontal lines to form a step-like progression. This chart can be used for showcasing a revenue and expense comparison.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category

---

## Stacked Charts

### msstackedcolumn2d

**Chart Type:** `msstackedcolumn2d`

**Description:** The multi-series stacked column 2D chart is used to compare multiple datasets, with the data plots stacked together. The chart is visualized as variables stacked on top of one another and multiple series placed side by side. This chart can be used to visualize data like the quarterly sales of various categories and sub-categories of products.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### msstackedcolumn2dlinedy

**Chart Type:** `msstackedcolumn2dlinedy`

**Description:** The multi-series stacked column 2d plus line dual y-axis chart helps analyse multiple datasets using two charts together. It is rendered as a combination of the multi-series stacked column chart and the line chart rendered on the same chart canvas with a dual y-axis. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart is used to visualize related datasets like multiple product categories vis-a-vis profits

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`lineset`** (object): lineset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackbar2d

**Chart Type:** `stackbar2d`

**Description:** The stacked bar 2D chart plots datasets horizontally grouped next to each other, instead of being stacked vertically one below the other. This chart helps in displaying the cumulative magnitude of two or more datasets. The length of each stack in a bar is proportionate to its value. Datasets in a stack are distinguished by their color; an icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. The chart is popularly used to show comparative data like the sales of different products in different regions, website traffic along with acquisition channels, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackbar3d

**Chart Type:** `stackbar3d`

**Description:** The stacked bar 3D chart plots datasets horizontally grouped next to each other, instead of being stacked vertically one below the other. This chart helps in displaying the cumulative magnitude of two or more datasets. The length of each stack in a bar is proportionate to its value. Datasets in a stack are distinguished by their color; an icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. The chart is popularly used to show comparative data like the sales of different products in different regions, website traffic along with acquisition channels, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedarea2d

**Chart Type:** `stackedarea2d`

**Description:** The stacked area 2D chart shows multiple area charts, corresponding to multiple data sets, stacked on top of one another. Data values are plotted as data points that are connected using line segments. The chart puts emphasis on the magnitude of change over time and effectively highlights the total value across a trend. It can be used to compare data like the sales figures for various products.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedarea2dlinedy

**Chart Type:** `stackedarea2dlinedy`

**Description:** The stacked area chart plus line with dual y-axis is a combination of the stacked area and line chart rendered on the same chart canvas with a dual y-axis. The stacked area chart plots datasets vertically but on top of each other instead of the clustered overlapping. The line chart is rendered with data values plotted as data points that are connected using line segments.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedcolumn2d

**Chart Type:** `stackedcolumn2d`

**Description:** The stacked column 2D chart is used to compare different data and show the composition of each item compared. The chart plots datasets vertically but on top of each other instead of the clustered side-by-side placement. This chart helps in displaying the cumulative magnitude of two or more datasets. The length of each stack in a column is proportionate to its value. Datasets in a stack are distinguished by their color; an icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. The chart is popularly used to show comparative data like the sales of different products in different regions, website traffic along with acquisition channels, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedcolumn2dline

**Chart Type:** `stackedcolumn2dline`

**Description:** The stacked column 2D plus line chart with a single y-axis is rendered as a combination of the stacked chart and the line chart. The stacked column chart plots datasets vertically but on top of each other instead of the clustered side-by-side placement. The line chart is rendered with data values plotted as data points that are connected using line segments. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart is used to visualize related datasets like multiple product categories vis-a-vis profits.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedcolumn2dlinedy

**Chart Type:** `stackedcolumn2dlinedy`

**Description:** The stacked column 2D chart plus line with dual y-axis is a combination of the stacked column 2D and line chart rendered on the same chart canvas with a dual y-axis. The stacked column chart plots datasets vertically but on top of each other instead of the clustered side-by-side placement. The line chart is rendered with data values plotted as data points that are connected using line segments.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedcolumn3d

**Chart Type:** `stackedcolumn3d`

**Description:** The stacked column 3D chart is used to compare different data and show the composition of each item compared. The chart plots datasets vertically but on top of each other instead of the clustered side-by-side placement. This chart helps in displaying the cumulative magnitude of two or more datasets. The length of each stack in a column is proportionate to its value. Datasets in a stack are distinguished by their color; an icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. The chart is popularly used to show comparative data like the sales of different products in different regions, website traffic along with acquisition channels, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedcolumn3dline

**Chart Type:** `stackedcolumn3dline`

**Description:** The stacked column 3D plus line chart with a single y-axis is rendered as a combination of the stacked chart and the line chart. The stacked column chart plots datasets vertically but on top of each other instead of the clustered side-by-side placement. The line chart is rendered with data values plotted as data points that are connected using line segments. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart is used to visualize related datasets like multiple product categories vis-a-vis profits

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### stackedcolumn3dlinedy

**Chart Type:** `stackedcolumn3dlinedy`

**Description:** The stacked column 3d plus line dual y-axis chart helps to analyse multiple datasets using two charts together. It is a combination of the stacked column 3D and line chart rendered on the same chart canvas with a dual y-axis. The stacked column chart plots datasets vertically but on top of each other instead of the clustered side-by-side placement. The line chart is rendered with data values plotted as data points that are connected using line segments. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart is used to visualize related datasets like multiple product categories vis-a-vis profits.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## Combination Charts

### mscolumn3dlinedy

**Chart Type:** `mscolumn3dlinedy`

**Description:** The multi-series column 3D plus line chart with a dual y-axis is used to plot data that belongs to multiple datasets that need separate chart types and includes data with different units. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart can be used to visualize related datasets like revenue over time for multiple product categories and its profit percentage.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### mscombi2d

**Chart Type:** `mscombi2d`

**Description:** The multi-series combination 2D single y-axis chart is used to plot data that belongs to multiple categories. The chart is rendered as a combination of the column, line, and area charts. This chart can be used to plot data like the targeted revenue, the actual revenue, and the profits earned on the same chart.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### mscombi3d

**Chart Type:** `mscombi3d`

**Description:** The multi-series combination 3D single y-axis chart is used to plot data that belongs to multiple categories. The chart is rendered as a combination of the column, line, and area charts. This chart can be used to plot data like the targeted revenue, the actual revenue, and the profits earned on the same chart.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### mscombidy2d

**Chart Type:** `mscombidy2d`

**Description:** The multi-series combination 2D dual y-axis chart is used to plot data that belongs to multiple categories and uses different units. The chart is rendered as a combination of the column, line, and area charts and a dual y-axis. The dual y-axis s in this chart adds the flexibility to use an additional variable with an axis of its own. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart can be used to plot data like the targeted revenue, the actual revenue, and the profit percent on the same chart.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### mscombidy3d

**Chart Type:** `mscombidy3d`

**Description:** The multi-series column 3D plus line chart with a single y-axis is used to plot data that belongs to multiple datasets, that need separate chart types. This chart can be used to visualize data like fixed costs, variable costs, and budgeted costs on the same chart.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## Scroll Charts

### Scroll Stacked Bar

**Chart Type:** `scrollstackedbar2d`

**Description:** A scroll stacked bar 2D chart is used to compare the cumulative magnitude of multiple data categories. The presence of the scroll interactivity allows to plot a large number of data points. The chart is rendered with bars belonging to multiple categories of data, with respect to a single quantity, stacked on top of each other. A vertical scroll bar is rendered at the left of the chart, which can be dragged to navigate through the chart. Position of scroll bar along with y-axis can be changed to right. This chart can be used to compare data like video games sales breakup for 2018 across different markets.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numVisiblePlot` | number | This attribute lets you control how the number of columns to show in the visible area of the scroll pane. For example, if you're plotting a chart with 10 columns each in 3 dataset, totaling to 30 c... |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `plottooltext` | string | Specify custom text for the tooltip    You can either specify a constant string as the tooltip text or you can use variable values from the data level by prefixing a __$__ to the attribute name, fo... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Scrollable Bar

**Chart Type:** `scrollbar2d`

**Description:** A scroll bar chart is used to visualize comparative data. The inclusion of the scroll interactivity allows the users to plot a larger number of data points on one chart (more than can be accommodated in the first view). It also facilitates easy navigation through the chart.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `plottooltext` | string | Specify custom text for the tooltip    You can either specify a constant string as the tooltip text or you can use variable values from the data level by prefixing a __$__ to the attribute name, fo... |
| `showLabels` | boolean | It sets the configuration whether the x-axis labels will be displayed or not. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`data`** (array): data
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Scrollable Column

**Chart Type:** `scrollcolumn2d`

**Description:** A scroll column chart is used to visualize comparative data or to show change over a period of time with the scroll feature. The inclusion of the scroll interactivity allows the users to plot a larger number of data points on one chart (more than can be accommodated in the first view). It also facilitates easy navigation through the chart. The chart has vertically aligned rectangular bars on one axis plotted against discrete values shown on the other. A horizontal scroll bar is rendered at the bottom of the chart, which can be dragged to navigate through the chart.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### scrollarea2d

**Chart Type:** `scrollarea2d`

**Description:** A scroll area chart is used to show the magnitude of change over a period of time. A large number of data points can be rendered on the chart owing to the scroll interactivity. The chart is rendered with data values plotted as data points that are then connected using line segments. The area between the line segments and the y-axis is filled with color. A horizontal scroll bar is rendered at the bottom of the chart to navigate through the chart. This chart can be used to plot a large number of data points like the daily temperature reading for over two months.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### scrollcombi2d

**Chart Type:** `scrollcombi2d`

**Description:** The scroll combination 2D chart is used to plot data that belongs to multiple categories. The scroll interactivity allows plotting a large number of data points, more than can be accommodated in just one view of the chart. The chart is rendered as a combination of the column, line, and area charts. A horizontal scroll bar is rendered below the x-axis to navigate through the chart. This chart can be used to plot data like the targeted revenue, the actual revenue, and the profits earned on the same chart

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### scrollcombidy2d

**Chart Type:** `scrollcombidy2d`

**Description:** The scroll combination 2D chart with a dual y-axis is used to plot data that not just belongs to multiple categories but is also of different types, for example, whole numbers and percentage. The scroll interactivity allows plotting a large number of data points, more than can be accommodated in just one view of the chart.The chart is rendered as a combination of the column, line, and area charts. The chart has two y-axes, the y-axis on the left is called the primary y-axis and the one on the right is called the secondary y-axis. A horizontal scroll bar is rendered below the x-axis to navigate through the chart. This chart can be used to plot data like the targeted revenue, the actual revenue, and the profit percent on the same chart.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### scrollline2d

**Chart Type:** `scrollline2d`

**Description:** A scroll line chart is used to show the magnitude of change over a period of time. A large number of data points can be rendered on the chart owing to the scroll interactivity of the chart. The chart is rendered with data values plotted as data points that are then connected using line segments. A horizontal scroll bar is rendered at the bottom of the chart to navigate through the chart. This chart can be used to plot a large number of data points like the monthly sales trends for two years.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### scrollmsstackedcolumn2d

**Chart Type:** `scrollmsstackedcolumn2d`

**Description:** The multi-series stacked column 2D chart with scroll is used to compare multiple datasets, with the data plots stacked together. The chart is visualized as variables stacked on top of one another and multiple series placed side by side. The presence of the scroll interactivity allows to plot a large number of data points.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### scrollmsstackedcolumn2dlinedy

**Chart Type:** `scrollmsstackedcolumn2dlinedy`

**Description:** The multi-series stacked column 2D chart with scroll is used to compare multiple datasets, with the data plots stacked together. The chart is visualized as variables stacked on top of one another and multiple series placed side by side. The presence of the scroll interactivity allows to plot a large number of data points.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`lineset`** (object): lineset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### scrollstackedcolumn2d

**Chart Type:** `scrollstackedcolumn2d`

**Description:** A scroll stacked column 2D chart is used to compare the cumulative magnitude of multiple data categories. The presence of the scroll interactivity allows to plot a large number of data points, more than can be accommodated in a single view of the chart. The chart is rendered with columns belonging to multiple categories of data, with respect to a single quantity, stacked on top of each other.A horizontal scroll bar is rendered below the x-axis to navigate through the chart. This chart can be used to compare data like the monthly revenue earned from products and services for a period of two financial years.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## Pie & Doughnut Charts

### Doughnut 2D Chart

**Chart Type:** `doughnut2d`

**Description:** A variation of the pie chart, the doughnut 2D chart has a blank space at the center to show useful information about the data being plotted. It is useful in depicting the share of constituents as part of a whole. It also enables users to highlight data points by slicing out doughnut slices. The magnitude of the dependent variable is proportional to the length of the arc on the circumference of the graph.It is used to show composition of data contributing to a 100%. Like for visualizing the population split in age-groups, contribution of product categories to bottom line.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `centerlabel` | string | This attribute sets the label at the center if we hover the cursor over the chart. It will display the value for that particular area which has been hovered. |
| `decimals` | number | Number of decimal places to which all numbers on the chart will be rounded to. |
| `defaultcenterlabel` | string | Sets the default label for the chart. This label can be seen at the center of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `subCaption` | string | Sub-caption of the chart. |

#### Data Structures

- **`data`** (array): data

---

### Pie

**Chart Type:** `pie2d`

**Description:** The pie 2D chart comprises of a circle that is divided into sectors, each representing a proportion of the summation of all values in a dataset. The chart is useful in depicting the share of constituents as part of the whole. The magnitude of the dependent variable is proportional to the length of the arc on the circumference of the graph. Radial lines are used to connect the arcs to the center of the circle, thus dividing the pie into slices. It is used to show the percentage split or contribution of for instance split of sales by product category, or market share of brands in a particular industry.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `decimals` | number | Number of decimal places to which all numbers on the chart will be rounded to. |
| `showPercentValues` | boolean | Whether to show percentage values in labels of the chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `useDataPlotColorForLabels` | boolean | Uses the same data plot color for data labels. |

#### Data Structures

- **`data`** (array): data

---

### doughnut3d

**Chart Type:** `doughnut3d`

**Description:** It is useful in depicting the share of constituents as part of a whole. It also enables users to highlight data points by slicing out doughnut slices. The magnitude of the dependent variable is proportional to the length of the arc on the circumference of the graph.It is used to show composition of data contributing to a 100%. Like for visualizing the population split in age-groups, contribution of product categories to bottom line.

#### Data Structures

- **`data`** (array): data

---

### multilevelpie

**Chart Type:** `multilevelpie`

**Description:** The multi-level pie chart is a special type of chart that allows you to show symmetrical and asymmetrical tree structures in a consolidated pie-like structure. The chart is visualized as a series of concentric circles arranged like a pie. The circles are divided into segments that represent each of the data values; the ratio of each segment is determined by the corresponding data value. This chart can be used to show organizational structures, an interlinked tree data (like friends of friends), category-wise sales break up, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category

---

### pie3d

**Chart Type:** `pie3d`

**Description:** The pie 3D chart comprises of a circle that is divided into sectors, each representing a proportion of the summation of all values in a dataset. The pie 3D chart adds a layer of aesthetics to the plain pie chart by adding depth to it. The chart is useful in depicting the share of constituents as part of the whole. The magnitude of the dependent variable is proportional to the length of the arc on the circumference of the graph. Radial lines are used to connect the arcs to the center of the circle, thus dividing the pie into slices. It is used to show the percentage split or contribution of for instance split of sales by product category, or market share of brands in a particular industry.

#### Data Structures

- **`data`** (array): data

---

## Scatter & Bubble Charts

### Error Scatter

**Chart Type:** `errorscatter`

**Description:** An error scatter chart is used to show the range of possible errors or deviations that might occur in the given data; it shows the extent of uncertainty in information relative to an average value.The chart is drawn using triangle-shaped data plots that represent the data points. Error values, both positive and negative, are shown using distinct I-shaped bars drawn on the data plots. Typically, the I-bars represent the standard deviation in a measurement indicating the degree of variance observed in a data point.This chart is commonly used in areas such as appliance testing, human resource management, weather analysis, and so on.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `halfVerticalErrorBar` | boolean | This attribute lets you set whether the chart will display half or full vertical error bars in the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | y-axis Title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line
- **`vTrendlines`** (object): vTrendlines
  - `line` (array): line

---

### Scatter

**Chart Type:** `scatter`

**Description:** The scatter chart shows the relationship between two variables by plotting them using cartesian coordinates. It displays a variable on each axis, marking the intersection of their values on the chart.When there are two numeric parameters, this chart type is the ideal one to determine how one variable impacts the other. For instance, this chart can be used to plot the relationship between income and expenditure or temperature and sales.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `baseFont` | string | This attribute lets you set the font face (family) of all the text (data labels, values etc.) on chart. If you specify the `outCnvBaseFont` attribute also, then this attribute controls only the fon... |
| `caption` | string | Caption of the chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisMaxValue` | number | The bubble/scatter chart have both x and y axis as numeric. This attribute lets you explicitly set the x-axis upper limit. If you do not specify this value, FusionCharts XT will automatically calcu... |
| `xAxisMinValue` | number | The bubble/scatter chart have both x and y axis as numeric. This attribute lets you explicitly set the x-axis lower limit. If you do not specify this value, FusionCharts XT will automatically calcu... |
| `xAxisName` | string | X-axis title of the chart. |
| `xNumberSuffix` | string | Using this attribute, you could add a suffix to all the numbers visible on the x-axis. For example, to represent all figure quantified as per annum on the x-axis, you could specify this attribute t... |
| `yAxisName` | string | Y-axis title of the chart. |
| `yNumberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the y-axis. For example, to represent all dollars figure on the y-axis, you could specify this attribute to $ to show like $... |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line
- **`vTrendlines`** (object): vTrendlines
  - `line` (array): line

---

### bubble

**Chart Type:** `bubble`

**Description:** The bubble chart is used to visualize data that is defined by three numeric variables. Two variables locate the point on the x- and y-axis and the third variable is indicated by the diameter of the bubble. Typically, this chart is used to compare and depict relationships between variables by means of positioning and proportions of the size of the bubbles/circles. It is also used to analyze patterns and trends in data.

#### Data Structures

- **`categories`** (array): categories
- **`category`** (array): category
- **`data`** (array): data
- **`dataset`** (array): dataset
- **`trendlines`** (object): trendlines
  - `line` (array): line
- **`vTrendlines`** (object): vTrendlines
  - `line` (array): line

---

### selectscatter

**Chart Type:** `selectscatter`

**Description:** A select-scatter chart is an extension of the scatter chart that allows to visually select a subset of data from the data points plotted on the chart. The chart is drawn using three sided or more data plots that represent the data points. Data points can be selected by dragging the mouse pointer to draw a rectangle around them. The selected data can be sent to the server for further processing. This chart can be used as an investment analysis tool, or to compare the products sold versus the price points earned. Scatter plots are also useful data visualization tools for illustrating a trend.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line
- **`vTrendlines`** (object): vTrendlines
  - `line` (array): line

---

## Real-Time Charts

### realtimearea

**Chart Type:** `realtimearea`

**Description:** A real-time area chart is used to show the magnitude of a trend in real-timedata is updated automatically at fixed intervals by getting new data from the server, without any page refreshes. Data values are plotted as data points that are connected using line segments. The area between the line segments and the axes/edges of other charts in the stack is filled with color, which is used to showcase the magnitude. The chart can be used to study data like the number of live visitors on a website, to be updated every 5 seconds. Use a real-time area chart when a simple part-to-whole study of trends, rather than actual values, is needed.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### realtimecolumn

**Chart Type:** `realtimecolumn`

**Description:** A real-time column chart is used to visualize comparative data or to show change over a period of time, in real-timedata is updated automatically at fixed intervals by getting new data from the server, without any page refreshes. The chart is drawn using vertically aligned rectangular bars on one axis plotted against discrete values shown on the other. The data labels along the x-axis are also updated in real-time. This chart can be used to plot data like the number of transactions done every 10 seconds, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### realtimeline

**Chart Type:** `realtimeline`

**Description:** A real-time line chart is used to show the magnitude of a trend in real-timedata is updated automatically at fixed intervals by getting new data from the server, without any page refreshes. Data values are plotted on the chart as data points that are then connected using line segments. This chart can be simulated as a real-time stock price monitor that updates after a fixed interval of time.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### realtimelinedy

**Chart Type:** `realtimelinedy`

**Description:** A real-time line chart with a dual y-axis is used to show the magnitude of trend for multiple data sets, each having different types of data, like whole numbers and percentage or whole numbers with varied ranges. The data on the chart is updated in real-timeautomatically, at fixed intervals by getting new data from the server, without any page refreshes. Data values are plotted on the chart as data points that are then connected using line segments. The primary y-axis is rendered to the right of the chart and the secondary y-axis is rendered to the left. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots.This chart can be used to study data like a company's price and stock index at regular intervals in real-time.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### realtimestackedarea

**Chart Type:** `realtimestackedarea`

**Description:** A real-time stacked area chart is used to show cumulative magnitude in real-timedata will be updated automatically at fixed intervals by getting new data from the server, without any page refreshes. Data values are plotted as data points that are then connected using line segments. The area between the line segments and the axes/edges of other charts is filled with color, which is used to showcase the magnitude. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart can be used to compare data like the number of live visitors on two or more websites, to be updated every 5 seconds. Use a real-time stacked area chart when a simple part-to-whole comparison between trends, rather than actual values, is needed.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### realtimestackedcolumn

**Chart Type:** `realtimestackedcolumn`

**Description:** A real-time stacked column 2D chart is used to compare the cumulative magnitude of multiple data categories in real-time data is updated automatically at fixed intervals by getting new data from the server, without any page refreshes. The chart is rendered with columns belonging to multiple categories of data, with respect to a single quantity, stacked on top of each other. An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots. This chart can be used to compare data like the number of live visitors on two websites, updated after regular intervals of time.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## Specialty Charts

### Error Bar

**Chart Type:** `errorbar2d`

**Description:** Error charts can be used to show the range of values that can be taken up by a data point, instead of being restricted to a point value. ... Error charts give a general idea of the accuracy of information or, conversely, the amount of variation in data from its expected value.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `halfErrorBar` | boolean | PowerCharts XT supports two types of error bars - normal error bars (half) and high-low error bars (full). Using this attribute, you can set whether to show half error bars or full error bars. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `numberSuffix` | string | Using this attribute, you could add suffix to all the numbers visible on the graph. For example, to represent all figures quantified as per annum on the chart, you could specify this attribute to '... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Error Line

**Chart Type:** `errorline`

**Description:** An error line chart is used to show the range of possible errors or deviations that might occur in the given data; it shows the extent of uncertainty in information relative to an average value. The chart is drawn using data points connected using line segments. Error values, both positive and negative, are shown using distinct I-shaped bars drawn on the data plots. Typically the I-bars represent the standard deviation in a measurement indicating the degree of variance observed in a data point. This chart is commonly used in areas such as appliance testing, human resource management, weather analysis, and so on.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `halfErrorBar` | boolean | PowerCharts XT supports two types of error bars - normal error bars (half) and high-low error bars (full). Using this attribute, you can set whether to show half error bars or full error bars. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `plottooltext` | string | This attribute works on text of the tooltip. It is used to specify the text, i.e. you can use it to give a specific constant string for every tooltip. You can also use variable values from the data... |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Funnel Chart

**Chart Type:** `funnel`

**Description:** A funnel chart is used to show streamlined data or the data at various stages in a process. The chart shows the progressive reduction of data as it passes from one phase to another, thus helping to identify the problem areas within a process. It is visualized like a funnel that is divided into multiple segments, each segment representing a stage in the process. A segment(s) height is determined by the data value that it represents. The width is determined by the segment(s) order in the stack of segments that create the funnel and has nothing to do with the data value. Segments can be sliced out in order to focus on a particular phase. This chart can be used to show data like sales conversion datastarting from dealing with unqualified prospects to making deliveries and receiving payments.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `decimals` | number | Number of decimal places to which all numbers on the chart will be rounded to. |
| `is2D` | boolean | Whether the chart will render as 2D? |
| `isHollow` | boolean | Whether the funnel slices will render as filled slices or as hollow ones? |
| `labelDistance` | number | Lets you set the horizontal distance between the data labels & funnel slices. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `plottooltext` | string | Specify custom text for the tooltip    You can either specify a constant string as the tooltip text or you can use variable values from the data level by prefixing a __$__ to the attribute name, fo... |
| `showPercentValues` | boolean | Whether to show the values as percentage or as absolute values? |
| `subCaption` | string | Sub-caption of the chart. |

#### Data Structures

- **`data`** (array): data

---

### Pareto

**Chart Type:** `pareto2d`

**Description:** The Pareto 2D chart offers a visualization that features a combination of the line chart and column chart. The chart is based on the Pareto Principle, also known as the 80/20 rule, which states that roughly 80 percent of effects come from 20% of causes. The columns show relative frequency ranging from high to low and the line chart shows cumulative frequency. Broadly, the chart is used to analyze data about the frequency of events and their causes in a process. It is also used to help determine and focus on the most significant causes.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `pYAxisName` | string | Primary Y-Axis title. |
| `sYAxisName` | string | Secondary y-axis title |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Pyramid

**Chart Type:** `pyramid`

**Description:** A pyramid chart has the form of a triangle with lines dividing it into sections. A related topic or idea is placed in each section. Because of the triangular shape, each section is a different width from the others; this width indicates a level of hierarchy among the topics. For example, the widest section may contain a general topic and the narrowest section may contain a much more specific topic from within that general topic. However, the width is not visually representative of the quantity beyond larger or smaller. Pyramid charts are put to the best use when items need to be arranged in a way that shows hierarchical structure, as well as quantity or size.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `alignCaptionWithCanvas` | boolean | Whether the caption is aligned with the canvas. Else, it will be aligned with the entire chart area |
| `borderAlpha` | number | Border alpha of the chart. |
| `caption` | string | Caption of the chart. |
| `captionOnTop` | boolean | Whether caption should be on top of the data plot area |
| `captionPadding` | number | This attribute lets you control the space (in pixels) between the sub-caption and top of the chart canvas. If the sub-caption is not defined, it controls the space between caption and top of chart ... |
| `chartLeftMargin` | number | Amount of empty space that you want to put on the left side of your chart. Nothing is rendered in this space. |
| `is2D` | boolean | Whether the chart will render as 2D? |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `numberSuffix` | string | Using this attribute, you could add suffix to all the numbers visible on the graph. For example, to represent all figures quantified as per annum on the chart, you could specify this attribute to '... |
| `plottooltext` | string | Specify custom text for the tooltip    You can either specify a constant string as the tooltip text or you can use variable values from the data level by prefixing a __$__ to the attribute name, fo... |
| `showPercentValues` | boolean | Whether to show the values as percentage or as absolute values? |
| `showValues` | boolean | Allows you to show/hide the data values along with the pyramid slices on chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `subCaptionFontSize` | number | Sets the sub-caption font size (in pixels). Besides pixels (px) values can also be defined in rem, %, em, and vw. |

#### Data Structures

- **`data`** (array): data

---

### box and whisker

**Chart Type:** `boxandwhisker2d`

**Description:** A box and whisker chart shows distribution of data into quartiles, highlighting the mean and outliers. The boxes may have lines extending vertically called â€œwhiskersâ€. These lines indicate variability outside the upper and lower quartiles, and any point outside those lines or whiskers is considered an outlier. Box and whisker charts are most commonly used in statistical analysis. For example, you could use a box and whisker chart to compare medical trial results or teachers' test scores.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### candlestick

**Chart Type:** `candlestick`

**Description:** A candlestick chart is used for analysis of equity and commodity prices.It allows showing the opening price, closing price, highest trading price, lowest trading price, and the trade volume on a single chart. The chart is visualized as a combination of the line and bar charts. The lines extending from the top and bottom of the bar data plots are called the wicks. Hence the name, candlestick chart.The price chart is rendered in the first half of the chart while the volume chart is rendered in the second half. This chart can be used to study the daily stock figures collected over a period of time.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendSet`** (array): trendSet
- **`trendlines`** (object): trendlines
  - `line` (array): line
- **`vTrendlines`** (object): vTrendlines
  - `line` (array): line

---

### chord

**Chart Type:** `chord`

**Description:** Chord diagram is a visually appealing way to represent weighted relationships in a radial layout. Entities (or categories) among which the weighted relationship exist are drawn on the circumference whereas the actual weighted relationship are drawn as links in the circular space of the chord diagram. The essence of a chord diagram is in knowing the dominant relationship and one can toggle to narrow focus on most important relationships.

#### Data Structures

- **`nodes`** (array): nodes

---

### dragarea

**Chart Type:** `dragarea`

**Description:** A drag-able area chart is similar to the area chart, with an additional feature that allows the data plots to be visually manipulated by dragging them. The chart is rendered with data values shown as data points that are then connected by line segments. The area between the line segments and the x-axis is filled with color.The data points can be dragged to visually edit the data values, which can then be processed further by sending them back to the server or to JavaScript functions present on the same page. This chart can be used for simulations, for estimations, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`category`** (array): category
- **`data`** (array): data
- **`dataset`** (array): dataset
- **`lines`** (object): lines
  - `line` (array): line

---

### dragcolumn2d

**Chart Type:** `dragcolumn2d`

**Description:** A drag-able column 2D chart is similar to the column 2D chart, with an additional feature that allows the data plots to be visually manipulated by dragging them. The chart is rendered with vertical rectangular bars. The columns can be dragged to visually edit the data values, which can then be processed further by sending them back to the server or to JavaScript functions present on the same page. This chart can be used for simulations, for estimations, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`category`** (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`lines`** (object): lines
  - `line` (array): line

---

### dragline

**Chart Type:** `dragline`

**Description:** A drag-able line chart is similar to the line chart, with an additional feature that allows the data plots to be visually manipulated by dragging them. The chart is rendered with data values shown as data points that are then connected by line segments. The data points can be dragged to visually edit the data values, which can then be processed further by sending them back to the server or to JavaScript functions present on the same page. This chart can be used for simulations, for estimations, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### dragnode

**Chart Type:** `dragnode`

**Description:** The drag node chart is a special type of chart that is rendered using nodes. The existing nodes can be dragged to be edited visually; nodes, connectors, and labels can be added as well as edited. This chart, that uses nodes, shaped as circles, rectangles, or polygons, to represent data sets, is often used to show hierarchies or flowcharts. Connectors are drawn between nodes and text labels are rendered with nodes and connectors to help identify them. Updated positions of nodes can be sent back to the server for processing. This chart is perfect for showing organizational hierarchies, network diagrams, and so on.

#### Data Structures

- **`connectors`** (object): connectors
  - `connector` (array): connector
- **`dataset`** (object): dataset
  - `data` (array): data
- **`labels`** (object): labels
  - `label` (array): label
- **`trendlines`** (object): trendlines
  - `line` (array): line
- **`vTrendlines`** (object): vTrendlines
  - `line` (array): line

---

### gantt

**Chart Type:** `gantt`

**Description:** The Gantt chart is used for showcasing timelines. It is a date/time-based chart that allows plotting tasks with their exact start and end date/time. Milestones can also be defined to assert how much of the project should be completed by when. Broadly, this chart looks like an extended version of the bar chart.The chart canvas is divided into a view pane and a data table. Tasks and their start and end date/time are listed in the view pane. Horizontal bars, representing tasks, are shown in the data table. The length of the bar represents the duration of the task. Arrows are used to connect tasks and star or a polygon is used to represent milestones. This chart can be used to show the timelines for the various phases in the construction of a new store.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`connectors`** (object): connectors
  - `connector` (array): connector
- **`dataTable`** (object): dataTable
  - `dataColumn` (array): dataColumn
- **`items`** (object): items
  - `item` (array): item
- **`milestones`** (object): milestones
  - `milestone` (array): milestone
- **`processes`** (object): processes
  - `process` (array): process
- **`tasks`** (object): tasks
  - `task` (array): task
- **`text`** (array): text
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### heatmap

**Chart Type:** `heatmap`

**Description:** A heat map chart is a specialized chart that uses colors to represent data values in a table. It is mostly used to plot large and complex data. The chart is visualized like a table with a finite number of rows and columns. Cells within the table are rendered in different colors depending upon the range in which the data value they represent belongs to. An icon legend or a gradient legend, depending on the use case, is rendered at the bottom of the chart. This chart can be used to plot data like employee attendance, performance ratings, election results, and so on.

#### Data Structures

- **`colorRange`** (object): colorRange
  - `color` (array): color
- **`columns`** (object): columns
  - `column` (array): column
- **`dataset`** (object): dataset
  - `data` (array): data
- **`rows`** (object): rows
  - `row` (array): row

---

### kagi

**Chart Type:** `kagi`

**Description:** A Kagi chart uses a series of vertical lines to illustrate the rise and fall in the supply and demand of certain assets. It is independent of time and change of direction occurs only when a specific amount is reached. The chart is visualized as a series of vertical thick and thin lines. Thick lines are drawn when the price of the underlying asset breaks above the previous high price and is interpreted as an increase in demand for the asset. Thin lines are used to represent increased stock when the price falls below the previous low. This chart can be used to show the rise and fall in the buying and selling of a company's shares.

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### marimekko

**Chart Type:** `marimekko`

**Description:** Marimekko charts are stacked column charts with columns of variable width. They are primarily used for marketing analysis.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### radar

**Chart Type:** `radar`

**Description:** A radar chart (also known as a spider chart) is a visual interpretation of data bearing multiple dimensions. The chart can be used to plot a single dataset as well as multiple datasets for comparing data by correlating and contrasting entities against predefined values. The chart is visualized using a radial grid-like structure; the values of the different categories are rendered on the chart's axis. All axes are arranged radially, with equal distances between them, while maintaining the same scale between all axes. The radar chart can be used for applications like product comparison, business analysis, and so on.

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data

---

### sankey

**Chart Type:** `sankey`

**Description:** Sankey diagram is a very useful way to represent flow in a system. The flow happens between relatable entities and are represented by coloured links. The flow could be of any measurable quantity - material, cost, energy, etc, from one node (or entity) to another. It can be drawn in different layouts and gives a quick understanding of the flow balance in the system

#### Data Structures

- **`links`** (array): links
- **`nodes`** (array): nodes

---

### sunburst

**Chart Type:** `sunburst`

**Description:** A Sunburst chart is used to visualize relationships within hierarchical data. It shows hierarchy through a series of concentric rings, where each ring corresponds to a level in the hierarchy. Each ring can have multiple segments; each segment showing the contribution of a particular dimension in that hierarchy. Focussing on a segment in the ring gives a sense of the part to the whole relationship of this dimension with respect to its parent ring segment.

#### Data Structures

- **`data`** (array): data

---

### treemap

**Chart Type:** `treemap`

**Description:** A treemap chart is used for analysis of hierarchical data. The chart is visualized using nested 2D rectangles that represent the nodes and leaves of the tree data structure. The drill-down feature of the chart allows studying data at all levels of the hierarchy by clicking a child node to drill down further. The parent node can be clicked to drill up. Quantity is assigned to a category and its area size is displayed in proportion to that quantity and to the other quantities within the same parent category in a part-to-whole relationship. Depending on the use-case, a gradient legend is rendered at the bottom of the treemap chart. The gradient legend uses sliders to filter data based on the range of values. This chart can be used to study the country-wise sales of a product; it can be drilled-down to study the region wise and city wise sales.

#### Data Structures

- **`colorRange`** (object): colorRange
  - `color` (array): color
- **`data`** (array): data

---

### waterfall2d

**Chart Type:** `waterfall2d`

**Description:** Waterfall charts help to convey how an initial value is affected by a series of intermediate positive or negative values. They are particularly useful for understanding the gradual transition in value of an item that is subject to increment or decrement. Whole columns represent the initial and the final values, while the intermediate values are expressed as floating columns. The columns are color-coded to distinguish between positive and negative values: green for positive values; red for negative; and blue for totals. Additionally, cumulative (a column that shows the sum of all columns to its left) and non-cumulative (a column that shows the sum of all columns to its left, after the last cumulative column) sum columns can also be rendered. 

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## Spark Charts

### sparkcolumn

**Chart Type:** `sparkcolumn`

**Description:** A spark column chart is similar to a column chart, except that it is a word-sized graphic used inline with text. Data values are plotted using vertical bars. Given that it is used inline with text, the size of the chart is same as that of the text surrounding it. This chart is not rendered with the x and y-axes. It is often used on executive dashboards to show several KPIs in a single view.

#### Data Structures

- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### sparkline

**Chart Type:** `sparkline`

**Description:** A spark line chart is similar to a line chart, except that it is a word-sized graphic used inline with text. Data values are plotted as data points that are then connected using line segments. Given that it is used inline with text, the size of the chart is same as that of the text surrounding it. This chart is not rendered with the x and y-axes. It is often used on executive dashboards to show several KPIs in a single view.

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### sparkwinloss

**Chart Type:** `sparkwinloss`

**Description:** A spark win loss chart is used to show the ups and downs in trends/performances as positive/win, negative/loss, and draw values. The chart is plotted with vertical columns to show the positive/win and negative/loss values; horizontal dashes is used to show the dash values. The chart can be used to show data like the score card of a competitor during a chess or maybe a boxing championship

#### Data Structures

- **`data`** (array): data

---

## Spline Charts

### Multi-series Spline

**Chart Type:** `msspline`

**Description:** A multi-series spline chart is a specialized form of the multi-series line chart that can be used for plotting data for multiple categories of data that requires the usage of curve-fitting. Like the multi-series line chart, data values are plotted as data points. However, instead of using straight line segments to connect the data points, this chart draws a fitted curve to connect the data points. This chart can be used for applications such as rendering the impulse-response for multiple systems or the development lifecycle for multiple products.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `showLabels` | boolean | It sets the configuration whether the x-axis labels will be displayed or not. |
| `showValues` | boolean | Sets the configuration whether data values would be displayed along with the data plot on chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Multi-series Spline Area

**Chart Type:** `mssplinearea`

**Description:** A multi-series spline area chart is a specialized form of the multi-series area chart that can be used for plotting data for multiple categories of data that requires the usage of curve-fitting. Like the multi-series area chart, data values are plotted as data points. However, instead of using straight line segments to connect the data points, the this chart draws a fitted curve to connect the data points. The area between the curve and the x-axis is filled with color.This chart can be used for applications such as rendering the impulse-response for multiple systems or the development lifecycle for multiple products.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `showValues` | boolean | Sets the configuration whether data values would be displayed along with the data plot on chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | y-axis Title of the chart. |

#### Data Structures

- **`categories`** (object): categories
  - `category` (array): category
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Single-series Spline

**Chart Type:** `spline`

**Description:** A spline chart is a specialized form of the line chart that can be used for plotting data that requires the usage of curve-fitting. Like the line chart, data values are plotted as data points. However, instead of using straight line segments to connect the data points, this chart draws a fitted curve to connect the data points. This chart can be used for applications such as rendering the impulse-response of a system or the development lifecycle for a product.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `lineThickness` | number | Thickness of the lines on the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `showValues` | boolean | Sets the configuration whether data values would be displayed along with the data plot on chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### Single-series Spline Area

**Chart Type:** `splinearea`

**Description:** A spline area chart is a specialized form of the area chart that can be used for plotting data that requires the usage of curve-fitting. Like the area chart, data values are plotted as data points. However, instead of using straight line segments to connect the data points, this chart draws a fitted curve to connect the data points.The area between the curve and the x-axis is filled with color. This chart can be used for applications such as rendering the impulse-response of a system or the development lifecycle for a product.

#### Chart Properties

| Property | Type | Description |
|----------|------|-------------|
| `caption` | string | Caption of the chart. |
| `numberPrefix` | string | Using this attribute, you could add prefix to all the numbers visible on the graph. For example, to represent all dollars figure on the chart, you could specify this attribute to $ to show like $40... |
| `showValues` | boolean | Sets the configuration whether data values would be displayed along with the data plot on chart. |
| `subCaption` | string | Sub-caption of the chart. |
| `xAxisName` | string | X-axis title of the chart. |
| `xaxislinethickness` | number | Sets the thickness of the x-axis line of the chart. |
| `yAxisName` | string | Y-axis title of the chart. |

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## 3D Chart Variants

### Bar3D

**Chart Type:** `bar3d`

**Description:** A bar 3D chart represents quantitative information. The chart consists of horizontally aligned rectangular bars of equal width with lengths proportional to the values they represent, something that aids in instant comparison of data. One axis of the chart plots categories and the other axis represents the value scale. The 3D bar chart is a visually enhanced version of bar 2D chart.

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### column3d

**Chart Type:** `column3d`

**Description:** A column chart is used to visualize comparative data or to show change over a period of time. The column 3d chart is a visually enhanced version of column 2d chart. The addition of another dimension adds to the visual appeal of the chart. 

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### pareto3d

**Chart Type:** `pareto3d`

**Description:** The Pareto 3D chart offers a visualization that features a combination of the line chart and column chart. The chart is based on the Pareto Principle, also known as the 80/20 rule, which states that roughly 80 percent of effects come from 20% of causes.The columns show relative frequency ranging from high to low and the line chart shows cumulative frequency. Broadly, the chart is used to analyze data about the frequency of events and their causes in a process. It is also used to help determine and focus on the most significant causes.

#### Data Structures

- **`data`** (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

## Zoom Charts

### zoomline

**Chart Type:** `zoomline`

**Description:** The zoom line chart is a special type of multi-series line chart that allows analysis of thousands of data points at macroscopic and microscopic levels. The zooming and panning features allow for this analysis to take place efficiently. It consists of multiple lines where each line represents a category and data points that belong to the category. The chart can easily plot thousands of data points, something that can produce indecipherable results if plotted on an ordinary line chart.

#### Data Structures

- **`categories`** (array): categories
- **`dataset`** (array): dataset
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### zoomlinedy

**Chart Type:** `zoomlinedy`

**Description:** With the dual y-axis, the zoom line dual y-axis chart can be used to plot data that belongs to datasets having different numeric units on the same chart, an advantage over using the conventional zoom-line chart. It consists of multiple lines plotted against two y-axes, the axis to the right of the chart is the primary y-axis and the axis to the left is the secondary y-axis. Each line in the chart represents a category and data points which belong to it. The chart's zooming and panning features allows analyzing thousands of data points at the macroscopic and microscopic levels. This chart can be used for plotting a huge amount of data like the website visits per day and the corresponding sales per day for a year.

#### Data Structures

- **`categories`** (array): categories
- **`dataset`** (array): dataset
- **`trendlines`** (object): trendlines
  - `line` (array): line

---

### zoomscatter

**Chart Type:** `zoomscatter`

**Description:** The zoom scatter chart is an extension of the standard scatter chart with the zooming and panning features. This chart is used to show the relationship between two variables by plotting them using cartesian coordinates. Owing to its zooming capability, the chart can easily display millions of data points. It displays a variable on each axis, marking the intersection of their values on the chart. When there are two numeric parameters, this chart type is the ideal one to determine how one variable impacts the other. For instance, this chart can be used to plot the relationship between income and expenditure or temperature and sales.An icon legend is rendered at the bottom of the chart to map data plots to their corresponding data categories; the icons can be clicked to show/hide data plots.

#### Data Structures

- **`categories`** (array): categories
- **`dataset`** (object): dataset
  - `data` (array): data
- **`trendlines`** (object): trendlines
  - `line` (array): line
- **`vTRendlines`** (object): vTRendlines
  - `line` (array): line

---

## Other

### Time Series

**Chart Type:** `timeseries`

**Description:** None

#### Data Structures

- **`caption`** (object): Caption
  - `text` (string): Caption
- **`subCaption`** (object): Subcaption
  - `text` (string): Subcaption
- **`yAxis`** (array): Y Axis
  - `format` (object)
  - `orientation` (string)
  - `plot` (array)
  - `title` (string)

---

## Common FusionCharts Constructor Properties

All chart types support the following `FusionCharts` constructor properties for customizing chart messages displayed during loading, errors, and empty data states.

### Base Chart Message

| Property | Type | Description |
|----------|------|-------------|
| `baseChartMessageColor` | string | This attribute allows to set a custom font color for all chart messages. |
| `baseChartMessageFont` | string | This attribute allows to set a custom font for all chart messages. |
| `baseChartMessageFontSize` | number | This attribute allows to set a custom font size for all chart messages. |
| `baseChartMessageImageAlpha` | number | This attribute allows to set a transparency for all images displayed as chart messages.   Default value: 100 |
| `baseChartMessageImageHAlign` | string | This attribute allows to set a custom horizontal alignment for all images displayed as chart messages.   Default value: middle |
| `baseChartMessageImageScale` | number | This attribute allows to set a scale for magnifying all images displayed as chart messages.   Default value: 100 |
| `baseChartMessageImageVAlign` | string | This attribute allows to set a custom vertical alignment for all images displayed as chart messages.   Default value: middle |

### Data Invalid Message

| Property | Type | Description |
|----------|------|-------------|
| `dataInvalidMessage` | string | This attribute allows to set the message to be displayed if the data to be loaded for the chart is invalid. To display an image as the chart message, prefix __I-__ to the image URL. |
| `dataInvalidMessageColor` | string | This attribute allows to set the font color for the message displayed if the specified chart data is invalid. |
| `dataInvalidMessageFont` | string | This attribute allows to set the font for the message displayed if the specified chart data is invalid. |
| `dataInvalidMessageFontSize` | number | This attribute allows to set the font size for the message displayed if the specified chart data is invalid. |
| `dataInvalidMessageImageAlpha` | number | If an image is displayed as the chart `dataInvalidMessage`, this attribute allows to set the transparency of the image.   Default value: 100 |
| `dataInvalidMessageImageHAlign` | string | If an image is displayed as the chart `dataInvalidMessage`, this attribute allows to set a custom horizontal alignment for the image.   Default value: value assigned to the `baseChartMessageImageHA... |
| `dataInvalidMessageImageScale` | number | If an image is displayed as the chart `dataInvalidMessage`, this attribute allows to set the scale for magnifying the image.   Default value: 100 |
| `dataInvalidMessageImageVAlign` | string | If an image is displayed as the chart `dataInvalidMessage`, this attribute allows to set a custom vertical alignment for the image.   Default value: value assigned to the `baseChartMessageImageVAli... |

### Data Load Error Message

| Property | Type | Description |
|----------|------|-------------|
| `dataLoadErrorMessage` | string | This attribute allows to set the message to be displayed if an error is encountered while loading chart data. To display an image as the chart message, prefix __I-__ to the image URL. |
| `dataLoadErrorMessageColor` | string | This attribute allows to set the font color for the message displayed if an error is encountered while loading chart data. |
| `dataLoadErrorMessageFont` | string | This attribute allows to set a font for the message displayed if an error is encountered while loading chart data. |
| `dataLoadErrorMessageFontSize` | number | This attribute allows to set the font size for the message displayed if an error is encountered while loading chart data. |
| `dataLoadErrorMessageImageAlpha` | number | If an image is displayed as the chart `dataLoadErrorMessage`, this attribute allows to set the transparency of the image.   Default value: 100 |
| `dataLoadErrorMessageImageHAlign` | string | If an image is displayed as the chart `dataLoadErrorMessage`, this attribute allows to set a custom horizontal alignment for the image.   Default value: value assigned to the `baseChartMessageImage... |
| `dataLoadErrorMessageImageScale` | number | If an image is displayed as the chart `dataLoadErrorMessage`, this attribute allows to set the scale for magnifying the image.   Default value: 100 |
| `dataLoadErrorMessageImageVAlign` | string | If an image is displayed as the chart `dataLoadErrorMessage`, this attribute allows to set a custom vertical alignment for the image.   Default value: value assigned to the `baseChartMessageImageVA... |

### Data Load Start Message

| Property | Type | Description |
|----------|------|-------------|
| `dataLoadStartMessage` | string | This attribute allows to set the message to be displayed when chart data begins loading. To display an image as the chart message, prefix __I-__ to the image URL. |
| `dataLoadStartMessageColor` | string | This attribute allows to set the font color for the message displayed when chart data begins loading. |
| `dataLoadStartMessageFont` | string | This attribute allows to set a font for the message displayed when chart data begins loading. |
| `dataLoadStartMessageFontSize` | number | This attribute allows to set the font size for the message displayed when chart data begins loading. |
| `dataLoadStartMessageImageAlpha` | number | If an image is displayed as the chart `dataLoadStartMessage`, this attribute allows to set the transparency of the image.   Default value: 100 |
| `dataLoadStartMessageImageHAlign` | string | If an image is displayed as the chart `dataLoadStartMessage`, this attribute allows to set a custom horizontal alignment for the image.   Default value: value assigned to the `baseChartMessageImage... |
| `dataLoadStartMessageImageScale` | number | If an image is displayed as the chart `dataLoadStartMessage`, this attribute allows to set the scale for magnifying the image.   Default value: 100 |
| `dataLoadStartMessageImageVAlign` | string | If an image is displayed as the chart `dataLoadStartMessage`, this attribute allows to set a custom vertical alignment for the image.   Default value: value assigned to the `baseChartMessageImageVA... |

### Load Message

| Property | Type | Description |
|----------|------|-------------|
| `loadMessage` | string | This attribute allows to set the message to be displayed when a chart begins to load. To display an image as the chart message, prefix __I-__ to the image URL. |
| `loadMessageColor` | string | This attribute allows to set the font color for the message displayed when a chart begins to load. |
| `loadMessageFont` | string | This attribute allows to set the font for the message displayed when a chart begins to load. |
| `loadMessageFontSize` | number | This attribute allows to set the font size for the message displayed when a chart begins to load. |
| `loadMessageImageAlpha` | number | If an image is displayed as the chart `loadMessage`, this attribute allows to set the transparency of the image.   Default value: 100 |
| `loadMessageImageHAlign` | string | If an image is displayed as the chart `loadMessage`, this attribute allows to set a custom horizontal alignment for the image.   Default value: value assigned to the `baseChartMessageImageHAlign` a... |
| `loadMessageImageScale` | number | If an image is displayed as the chart `loadMessage`, this attribute allows to set the scale for magnifying the image.   Default value: 100 |
| `loadMessageImageVAlign` | string | If an image is displayed as the chart `loadMessage`, this attribute allows to set a custom vertical alignment for the image.   Default value: value assigned to the `baseChartMessageImageVAlign` att... |

### Render Error Message

| Property | Type | Description |
|----------|------|-------------|
| `renderErrorMessage` | string | This attribute allows to set the message to be displayed if an error is encountered while rendering the chart. To display an image as the chart message, prefix __I-__ to the image URL. |
| `renderErrorMessageColor` | string | This attribute allows to set the font color for the message displayed if an error is encountered while rendering the chart. |
| `renderErrorMessageFont` | string | This attribute allows to set a font for the message displayed if an error is encountered while rendering the chart. |
| `renderErrorMessageFontSize` | number | This attribute allows to set the font size for the message displayed if an error is encountered while rendering the chart. |
| `renderErrorMessageImageAlpha` | number | If an image is displayed as the chart `renderErrorMessage`, this attribute allows to set the transparency of the image.   Default value: 100 |
| `renderErrorMessageImageHAlign` | string | If an image is displayed as the chart `renderErrorMessage`, this attribute allows to set a custom horizontal alignment for the image.   Default value: value assigned to the `baseChartMessageImageHA... |
| `renderErrorMessageImageScale` | number | If an image is displayed as the chart `renderErrorMessage`, this attribute allows to set the scale for magnifying the image.   Default value: 100 |
| `renderErrorMessageImageVAlign` | string | If an image is displayed as the chart `renderErrorMessage`, this attribute allows to set a custom vertical alignment for the image.   Default value: value assigned to the `baseChartMessageImageVAli... |

### Type Not Supported Message

| Property | Type | Description |
|----------|------|-------------|
| `typeNotSupportedMessage` | string | This attribute allows to set the message to be displayed when the specified chart type is not supported. To display an image as the chart message, prefix __I-__ to the image URL. |
| `typeNotSupportedMessageColor` | string | This attribute allows to set the font color for the message displayed when the specified chart type is not supported. |
| `typeNotSupportedMessageFont` | string | This attribute allows to set the font for the message displayed when the specified chart type is not supported. |
| `typeNotSupportedMessageFontSize` | number | This attribute allows to set the font size for the message displayed when the specified chart type is not supported. |
| `typeNotSupportedMessageImageAlpha` | number | If an image is displayed as the chart `typeNotSupportedMessage`, this attribute allows to set the transparency of the image.   Default value: 100 |
| `typeNotSupportedMessageImageHAlign` | string | If an image is displayed as the chart `typeNotSupportedMessage`, this attribute allows to set a custom horizontal alignment for the image.   Default value: value assigned to the `baseChartMessageIm... |
| `typeNotSupportedMessageImageScale` | number | If an image is displayed as the chart `typeNotSupportedMessage`, this attribute allows to set the scale for magnifying the image.   Default value: 100 |
| `typeNotSupportedMessageImageVAlign` | string | If an image is displayed as the chart `typeNotSupportedMessage`, this attribute allows to set a custom vertical alignment for the image.   Default value: value assigned to the `baseChartMessageImag... |

---

*Generated from the Fuuz Visualization Configuration Schema. For the latest configuration options, refer to the Fuuz platform documentation at [fuuz.com](https://fuuz.com).*