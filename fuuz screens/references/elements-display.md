# Display

Display elements for charts, timers, calendars, and event monitoring.

---

### Calendar (`CalendarInput`)

Calendar type input.

**Exposed Functions:** `fn.save`
**Exposed State:** `data` (calendar object)

Note: Conditionally excluded from toolbox when scheduling is excluded.

**Display** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `padding` | slider (0–96, step 8) | Element padding. |
| `width` | text | Element width. |
| `height` | text | Element height. |
| `alignItems` | combobox | Alignment. Options: start \| end \| center. |
| `visible` | transform (smartInput: checkbox) | Visibility based on logic. |
| `initialView` | options | Initial calendar view. Options: dayGridMonth \| timeGridWeek \| timeGridDay \| listWeek. |
| `useBasicTitleFormat` | checkbox | Use basic title format. |

**Query** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `calendarSetting` | combobox | Calendar source. Options: Saved Calendar \| Dynamic Calendar. |
| `calendarId` | combobox | Saved calendar to load. Visible when Saved Calendar. |
| `calendarTransformRemote` | checkbox | Run calendar transform remotely. Visible when Dynamic Calendar. |
| `calendarEvents` | transform | Payload for the calendar. Visible when Dynamic Calendar. |
| `calendarParameters` | jsonata | Parameters for the calendar. Visible when Dynamic Calendar. |
| `autoSave` | checkbox | Auto-save calendar events. Visible when Saved Calendar. |
| `onChange` | jsonata | Transform run on calendar event changes. Visible when Saved Calendar. |
| `resourceView` | checkbox | Enable resource view. |
| `resources` | transform (smartInput: select, dataModel: CalendarResource) | Resources. Visible when resourceView. |

**Defaults:** `{ padding: 8, width: "100%", height: "auto", alignItems: "start", autoSave: true, resourceView: false, useBasicTitleFormat: false }`

---

### Chart (`Chart`)

Dynamic chart element. Queries and displays data in a chart/graph format.

**Exposed Functions:** `fn.loadData`, `fn.search`

**Display** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `padding` | slider (0–96, step 8) | Element padding. |
| `width` | text | Element width. |
| `height` | text | Element height. |
| `allowFullscreen` | checkbox | Allow fullscreen mode. |

**Chart** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `chartConfiguration` | chart | Chart configuration editor. dataPath: chart. |
| `autoRefreshDuration` | duration (mode: timeNoSeconds) | Auto-refresh frequency. Minimum 1 minute. When empty, loads once on page load. |

**Defaults:** `{ padding: 8, width: "400px", height: "300px", chart: {}, allowFullscreen: true, autoRefreshDuration: "" }`

---

### Timer (`Timer`)

Timer display. Configure whether to count up or down and how much time.

**Exposed Functions:** `fn.start`, `fn.pause`, `fn.reset`
**Exposed State:** `expired`, `seconds`, `totalSeconds`

**Display** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `padding` | slider (0–96, step 8) | Element padding. |
| `width` | text | Element width. |
| `height` | text | Element height. |
| `formatString` | text | Time display format. Placeholder: hh:mm:ss. |
| `align` | options | Horizontal alignment. Options: left \| center \| right. |
| `variant` | options | Font size. Options: h1 \| h2 \| h3 \| h4 \| h5 \| h6 \| subtitle1 \| subtitle2 \| body1 \| body2 \| button \| caption \| overline. |
| `color` | color | Text color. |
| `showProgressBar` | switch | Show progress bar of elapsed time. |

**Configuration** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `useDateFields` | switch | Use begin/end dates vs. single duration value. |
| `durationTransform` | jsonata (smartInput: duration) | Timer duration (seconds or moment duration string). Visible when useDateFields = false. |
| `startDateTimeTransform` | jsonata (smartInput: datetime) | Start date/time. Visible when useDateFields = true. |
| `endDateTimeTransform` | jsonata (smartInput: datetime) | End date/time. Visible when useDateFields = true. |

**Behavior** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `countUpMode` | switch | Count up (vs. count down). |
| `autoStart` | switch | Start timer automatically on page load. |
| `flashWhenExpired` | switch | Flash timer value when expired. |
| `onTimerExpiredTransform` | jsonata | Transform run when timer expires. |

**Defaults:** `{ padding: 8, width: "100%", height: "auto", align: "left", showProgressBar: false, useDateFields: false, countUpMode: false, autoStart: true, flashWhenExpired: true }`

---

### Event Console (`EventConsoleAdapter`)

Console for displaying streaming event data from a topic or subscription.

**Exposed Functions:** `fn.subscribe`

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `label` | text | Label displayed at the top of the console. |
| `bindingKey` | transform (smartInput: text) | Binding key for event data subscription. |
| `subscribeOnMount` | checkbox | Start subscription when screen loads. |

**Display** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `padding` | slider (0–96, step 8) | Element padding. |
| `width` | text | Element width. |
| `height` | text | Element height. |

**Defaults:** `{ padding: 8, width: "500px", height: "650px", bindingKey: null, label: "Event Console", subscribeOnMount: false }`
