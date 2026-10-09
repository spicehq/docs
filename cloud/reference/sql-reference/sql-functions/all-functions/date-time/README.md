# Date/Time

| Function Name                                           | Description                                                                                        |
| ------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| [CONVERT\_TIMEZONE](../conversion/convert\_timezone.md) | Convert timestamp to the specified timezone.                                                       |
| [CURRENT\_DATE](current\_date.md)                       | Returns the current date of the system.                                                            |
| [CURRENT\_DATE\_UTC](current\_date\_utc.md)             | Returns the current date of the system based on the UTC timezone.                                  |
| [CURRENT\_TIME](current\_time.md)                       | Returns the current time for the system.                                                           |
| [CURRENT\_TIMESTAMP](current\_timestamp.md)             | Returns the current timestamp for the system in UTC time only.                                     |
| [DATEDIFF](datediff.md)                                 | Compares two dates or timestamps and returns the difference in days.                               |
| [DATE\_ADD](date\_add.md)                               | Returns the sum of two expressions of time as another expression of time.                          |
| [DATE\_DIFF](date\_diff.md)                             | Returns the difference between two expressions of time as another expression of time.              |
| [DATE\_PART](date\_part.md)                             | Return subfields such as year or hour from date or timestamp values.                               |
| [DATE\_SUB](date\_sub.md)                               | Returns the difference of two expressions of time as another expression of time.                   |
| [DATE\_TRUNC](date\_trunc.md)                           | Truncates the date or timestamp to the indicated precision.                                        |
| [DAY](day.md)                                           | Returns the day of month of the date or timestamp.                                                 |
| [DAYOFMONTH](dayofmonth.md)                             | Returns the day of month of the date or timestamp.                                                 |
| [DAYOFWEEK](dayofweek.md)                               | Returns the day of the week (from 1 to 7) of the date or timestamp.                                |
| [DAYOFYEAR](dayofyear.md)                               | Returns the day of the year (from 1 to 366) of the date or timestamp.                              |
| [EXTRACT](extract.md)                                   | Extracts the specified date or time part from the date or timestamp.                               |
| [HOUR](hour.md)                                         | Extracts the hour number (from 0 to 23) for a given time or timestamp.                             |
| [LAST\_DAY](last\_day.md)                               | Returns the last day of the month for the specified date or timestamp.                             |
| [MINUTE](minute.md)                                     | Extracts the minute number (from 0 to 59) for a given time or timestamp.                           |
| [MONTH](month.md)                                       | Extracts the month number (from 1 to 12) for a given date or timestamp.                            |
| [MONTHS\_BETWEEN](months\_between.md)                   | Returns the number of months between two date or timestamp values.                                 |
| [NEXT\_DAY](next\_day.md)                               | Returns the date or timestamp of the first specified day of week that occurs after the input date. |
| [QUARTER](quarter.md)                                   | Extracts the quarter number (from 1 to 4) for a given date or timestamp.                           |
| [SECOND](second.md)                                     | Extracts the second number (from 0 to 59) for a given date or timestamp.                           |
| [TIMESTAMPADD](timestampadd.md)                         | Add (or subtract) an interval of time from a date/timestamp value or column.                       |
| [TIMESTAMPDIFF](timestampdiff.md)                       | Return the amount of time between two date or timestamp values                                     |
| [TO\_DATE](to\_date.md)                                 | Converts the input expressions to the corresponding date.                                          |
| [TO\_TIME](../conversion/to\_time.md)                   | Converts the input expressions to the corresponding time.                                          |
| [TO\_TIMESTAMP](to\_timestamp.md)                       | Converts the input expressions to the corresponding timestamp.                                     |
| [UNIX\_TIMESTAMP](unix\_timestamp.md)                   | Returns the Unix epoch time representation of an ISO 8601 timestamp.                               |
| [WEEK](week.md)                                         | Extracts the week number (from 0 to 53) for a given date or timestamp.                             |
| [WEEKOFYEAR](weekofyear.md)                             | Returns the week of year of the date or timestamp.                                                 |
| [YEAR](year.md)                                         | Extracts the year for a given date or timestamp.                                                   |

### Date/Time Formatting <a href="#datetime-formatting" id="datetime-formatting"></a>

| Format Element | Description                  | Example         |
| -------------- | ---------------------------- | --------------- |
| AD/BC          | Era indicator                | AD, BC          |
| AMPM           | Meridian indicator           | AM, PM          |
| CC             | Century indicator (0-99)     | 19              |
| WW             | Week of year (0-52)          | 4, 43           |
| D              | Day of week (1-7)            | 6               |
| DY             | Abbreviated day name of week | Tue, Fri        |
| DAY            | Full day name of week        | Tuesday, Friday |
| YYYY           | Four digits of year          | 1996            |
| YY             | Last two digits of year      | 96              |
| DDD            | Day of year (1-366)          | 5, 245          |
| MM             | Month (1-12)                 | 8               |
| MON            | Abbreviated month name       | Mar, Oct        |
| MONTH          | Full month name              | March, October  |
| DD             | Day of month (1-31)          | 24              |
| HH/HH12        | Hour of day (1-12)           | 4               |
| HH24           | Hour of day (0-23)           | 21              |
| MI             | Minutes (0-59)               | 22              |
| SS             | Seconds (0-59)               | 54              |
| FFF            | Milliseconds                 | 121             |
| TZD            | Timezone abbreviation        | UTC, PST        |
| TZO            | Timezone offset              | +02:00, -0800   |

Only the following characters are allowed in a format pattern: `- / , . ; :`. If you need to let a value pass through to the output unmodified you can surround it with `"` (for example `"T"`).

If you convert a date to text, numeric values are zero padded for you. For example, `MM` returns `04` for April.

\
