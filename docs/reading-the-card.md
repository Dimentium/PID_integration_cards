# Reading the card

[← Back to README](../README.md)

Each row is one departure. From left to right:

| Column       | Shown                    | Meaning |
|:-------------|:-------------------------|:--------|
| Line         | always                   | Line number in the colour of the transport type. Night lines are black. |
| Destination  | always                   | Final stop of the trip. Below it the train number for trains, and icons for the vehicle (see below). |
| Platform     | with 2+ boards, or `show_platform: true` | Platform code of the stop the departure leaves from. |
| Time         | `time_format: both` or `absolute` | Scheduled departure time. |
| Delay        | `show_delay: true`       | Delay in minutes, or a calendar icon when there are no real-time data. Empty means on time. |
| Countdown    | `time_format: both` or `relative` | Minutes until the scheduled departure; delay is shown separately. |

## Countdown

| Shown        | Meaning |
|:-------------|:--------|
| `5 min`      | Leaves in 5 to 6 minutes. The value is rounded down, so the card never promises more time than there is. |
| `<1 min`     | Leaves within a minute. |
| `now`        | The scheduled departure time has passed. |
| `at stop`    | The vehicle is standing at the stop. |
| `Canceled`   | The trip is cancelled. The row is greyed out and the time crossed out. |
| *(empty)*    | The scheduled departure is in an hour or more, so the time column says when. With `time_format: relative` the scheduled time is shown instead. |

With `color_countdown: true`, departures within two minutes use the theme's primary colour when no custom thresholds are active.

When the integration provides a positive `walking_time_offset`, the card derives thresholds from it. The global modifiers default to yellow `+5`, red `+1`, and hide `/2`; for example, an offset of 10 minutes gives thresholds of 15, 11, and 5 minutes. You can change the modifiers in the card editor. A stop's or device's `countdown` settings override each derived threshold; set a manual threshold to `0` to disable it. The hide threshold does not hide canceled trips or a vehicle already at the stop. Set `color_countdown: false` to disable colors without disabling threshold-based hiding. By default, the integration filters departures inside a positive walking-time offset. Enable `include_departures_within_walking_offset` in that board's integration options to pass them to the card.

The integration fetches data from the API once a minute; the card recalculates the countdown every 15 seconds in between, so it keeps counting down even without new data.

## Stop name, direction, and route filters

The direction is the trip destination supplied by the integration. The optional stop name is the exact stop name supplied by the integration, shown below the direction. Both can be toggled in the card editor.

Route filters are configured separately for each stop under `stop_settings`. An `allowlist` shows only the route codes in its `routes` list; a `denylist` hides those route codes. Without a filter, the stop's routes are unchanged.

Set `departure_window_minutes` at the card level to show only trips scheduled within that many minutes. A stop or device can override it in `stop_settings`; use `0` to remove the window for that stop. The window and countdown use scheduled departure time.

Countdown settings use the same per-stop object. Values are minutes to the scheduled departure; delay does not shift the countdown or its color thresholds. For example, with yellow at 8, red at 4, and hide at 2, a countdown at or below 8 minutes is yellow, at or below 4 is red, and at or below 2 is hidden.

## Delay

| Shown            | Meaning |
|:-----------------|:--------|
| *(empty)*        | On time (less than a minute late). |
| orange `+2`      | 1 to 4 minutes late. |
| red `+7`         | 5 or more minutes late. |
| blue `−1`        | Early. |
| calendar icon    | No real-time data for this trip, the times are from the timetable. |

The time column always shows the **scheduled** time, the same way PID departure boards at stops do: `18:06 +2` means scheduled at 18:06 and running two minutes late. The countdown is based on the scheduled time and does not include the delay.

## Icons below the destination

| Icon                    | Meaning |
|:------------------------|:--------|
| wheelchair              | Wheelchair accessible vehicle |
| snowflake               | Air conditioned vehicle |
| moon                    | Night line |
| two arrows              | Substitute service |

Hover over an icon (or long-press on a touch screen) to see its description. The icons are hidden in compact mode and on narrow cards.

## Header

The header shows the stop name, or the common part of the names when several boards are selected (*Palmovka* for *Palmovka A* and *Palmovka B*). A different title can be set with `title`.

A **cloud icon** in the top right corner warns about the data:

| Shown                         | Meaning |
|:------------------------------|:--------|
| cloud icon with a time        | The data have not been updated for more than three minutes. The time is when they were last updated, so the departures shown may be out of date. |
| cloud icon, *Data unavailable* | The integration has no data for the board, e.g. because the API is unreachable or the board failed to load. With several boards, the departures of the others are still shown. |

When there are no departures to show, the card says why: *No upcoming departures* when the timetable is empty (e.g. at night), *Departure data are unavailable* when the integration has no data.

## Service alerts

When PID publishes a service alert (infotext) for the stop, it is shown in an orange box under the header. Long texts are shortened to two lines; tap the box to read the whole text. The English version is shown when your Home Assistant language is not Czech or Slovak and PID provides one.

## Details

Tap a departure to open its details with all the data the integration provides, e.g. the last stop the vehicle passed.
