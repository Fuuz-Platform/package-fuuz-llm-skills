# Time and Schedule Bindings

Fuuz provides extensive time/date manipulation and production schedule functions through custom JSONata bindings.

**Note:** All moment functions are available in both JSONata and JavaScript transforms. In JavaScript, you can also use the `moment` object directly (e.g., `moment()` in addition to `$moment()`).

## Moment Functions

### Basic Construction
```jsonata
$moment()                                                    /* Current time */
$moment("01/01/2019 09:00:00AM", "MM/DD/YYYY hh:mm:ssA")  /* Parse with format */
$momentTz("01/01/2019 09:00:00AM", "MM/DD/YYYY hh:mm:ssA", "America/Detroit")  /* With timezone */
```

### Timezone Operations
```jsonata
$momentTzGuess()          /* Guess current timezone */
$momentTzTimezones()      /* List all available timezones */
```

### Time Manipulation
```jsonata
$moment().subtract(1, 'days').startOf('day')    /* Previous day start */
$moment().subtract(1, 'days').endOf('day')      /* Previous day end */
$moment().subtract(1, 'month').startOf('month') /* Previous month start */
$moment().subtract(1, 'month').endOf('month')   /* Previous month end */
$moment().startOf('month')                       /* Current month start */
$moment().endOf('month')                         /* Current month end */
$moment().startOf('hour')                        /* Current hour start */
$moment().endOf('week')                          /* End of week */
$moment().startOf('year')                        /* Start of year */
```

### Formatting and Conversion
```jsonata
$moment().format('YYYY-MM-DD')                   /* Custom format */
$moment().unix()                                 /* Unix timestamp (seconds) */
$moment().valueOf()                              /* Milliseconds since epoch */
$moment().toISOString()                          /* ISO 8601 string (not usually needed - $moment() already returns ISO) */
```

### Duration Functions
```jsonata
$momentDuration('PT8H').asMilliseconds()                /* ISO duration to ms */
$momentDuration(28800000, "milliseconds").asHours()    /* Convert ms to hours */
```

### Min/Max Comparisons
```jsonata
$momentMax([
  $moment("2030-12-15").subtract(1, "days"),
  $moment("2030-12-15").add(10, "days"),
  $moment("2030-12-15").subtract(5, "days")
])  /* Returns latest date */

$momentMin([...])  /* Returns earliest date */
```

### Milliseconds Conversion
```jsonata
$fromMillis(1510067557121)  /* Convert Unix timestamp to moment */
```

## Schedule and Schedule Group Functions

### Schedule Operations
```jsonata
$schedule("scheduleId")  /* Get schedule by ID */

$schedule("scheduleId").getAvailabilityBetween(
  $moment(),
  $moment().add(1, 'day'),
  { "inclusive": false }
)  /* Check availability in time window */

$schedule("scheduleId").getCalendarIntervals({
  "includeExceptions": true
})  /* Get calendar intervals */

$schedule("scheduleId").getEventsBetween(
  $moment(),
  $moment().add(1, 'week'),
  { "inclusive": false }
)  /* Get events in range */

$schedule("scheduleId").getEventsIntersections(
  $moment(),
  $moment().add(1, 'hour'),
  { "inclusive": false }
)  /* Get overlapping events */

$schedule("scheduleId").getExceptionsBetween(
  $moment(),
  $moment().add(1, 'month'),
  { "inclusive": false }
)  /* Get exceptions (downtime, holidays) */

$schedule("scheduleId").getTimeline(
  $moment().startOf('week'),
  $moment().endOf('week'),
  { "inclusive": false }
)  /* Get timeline view */
```

### Schedule Group Operations
```jsonata
$scheduleGroup("scheduleGroupId")  /* Get schedule group by ID */

$scheduleGroup("scheduleGroupId").getAvailabilityBetween(
  $moment(),
  $moment().add(1, 'day'),
  { "inclusive": false }
)  /* Check group availability */

$scheduleGroup("scheduleGroupId").getCalendarIntervals({
  "includeExceptions": true
})  /* Get calendar intervals for group */

$scheduleGroup("scheduleGroupId").getCurrentSchedules(
  $moment(),
  $moment(),
  { "inclusive": true }
)  /* Get active schedules at current time */

$scheduleGroup("scheduleGroupId").getEventsIntersections(
  $moment(),
  $moment().add(1, 'shift'),
  { "inclusive": false }
)  /* Get overlapping events across all schedules */

$scheduleGroup("scheduleGroupId").getTimeline(
  $moment().startOf('day'),
  $moment().endOf('day'),
  { "inclusive": false }
)  /* Get combined timeline for all schedules */
```

## Common Patterns

### Get Current Shift Schedule
```jsonata
(
  $now := $moment();
  $scheduleGroupIds := $distinct($.workcenters.scheduleGroupId);
  $currentScheduleByGroup := $merge($scheduleGroupIds.(
    $groupId := $;
    $currentSchedules := $scheduleGroup($groupId).getCurrentSchedules($now, $now, { "inclusive": true });
    $activeSchedule := $currentSchedules[0];
    { ($groupId): $activeSchedule.scheduleId }
  ));
  $currentScheduleByGroup
)
```

### Calculate Previous Hour Window
```jsonata
(
  $prevHourStart := $moment().startOf('hour').subtract(1, 'hour');
  $prevHourEnd := $moment($prevHourStart).add(1, 'hour');
  {
    "periodStart": $moment($prevHourStart),
    "periodEnd": $moment($prevHourEnd)
  }
)
```

### Timezone-Aware Time Boundaries
```jsonata
(
  $timezone := $.setting.edges[0].node.defaultValue;
  $nowLocal := $moment().tz($timezone);
  $hourStartLocal := $nowLocal.startOf('hour').subtract(1, 'hour');
  $hourEndLocal := $moment($hourStartLocal).add(1, 'hour');
  {
    "periodStart": $moment($hourStartLocal),
    "periodEnd": $moment($hourEndLocal)
  }
)
```

## Important Notes

1. **$moment() Returns Formatted Dates**: No need to call `.toISOString()` - `$moment()` already returns properly formatted ISO dates for GraphQL
2. **Context Shifting**: Remember that `$` changes context in chained operations - use `$$` for root or capture first: `$now := $moment()`
3. **Inclusive Parameter**: Controls whether boundary times are included in range queries
4. **Duration Format**: Supports ISO 8601 duration format (e.g., "PT8H" for 8 hours)
5. **Schedule Groups**: Useful for workcenters that have multiple shifts or rotating schedules
