---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.13.1
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Julian Day

The **Julian Day** is a concept invented by historians and astronomers to provide a continuous count of days since a particular starting point. Julian Day 0 is set to be the day starting on January 1, 4713 BC in the [proleptic Julian calendar](https://en.wikipedia.org/wiki/Proleptic_Julian_calendar) at 12:00 PM (noon) in UTC. This corresponds to November 24, 4714 BC in the [proleptic Gregorian calendar](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar).

Julian days are counted as integers continuously until the present time. This makes it very easy to compare relative times of events and do arithmetic between days.

## Julian Period

The Julian day is a single day in the Julian Period, a period of time 7,980 years long. The Julian Period is defined as the product of three cycles in the Julian calendar:

1. The solar cycle (28 years)
2. The lunar cycle (19 years)
3. The indiction cycle (15 years)

A Julian Period starts when each of these cycles is on its first day, on the same day. Counting backwards, and assuming that the Julian calendar extends infinitely in both directions in time, the previous time that this occurred was in the year 4713 BCE.

### The Solar Cycle

The Julian calendar is the calendar imposed by Julius Cesar during the Roman Republic. It had a leap year every 4th year, with no exceptions. This gave the average length of a year as 365.25 days, keeping the days of the year roughly in sync with the length of the solar year. Since there are 7 days in a week, it takes 28 years for a leap day to happen on every day of the week, giving the length of the [solar cycle](https://en.wikipedia.org/wiki/Solar_cycle_(calendar)).

### The Lunar Cycle

As we know, the moon goes through phases as it orbits around the earth. The [lunar, or **metonic**, cycle](https://en.wikipedia.org/wiki/Metonic_cycle) of 19 years is approximately the time for the lunar phase to repeat on the same day of the year.

### The Indiction Cycle

An [indiction](https://en.wikipedia.org/wiki/Indiction) is the periodic reassessment of property for a land tax, used in the Middle Ages. At that time, the reassessment occurred every 15 years.

## Calculating Julian Days

The Julian calendar was introduced in 46 BC by Julius Cesar, with leap years occuring every four years. This gives the average length of a year as 365.25 days, which is slightly longer than the [solar year](https://en.wikipedia.org/wiki/Tropical_year) of 365.2422 days. Therefore, over time, the day on which the equinox will occur shifts.

In 1582, Pope Gregory XIII imposed a modified version of the Julian calendar. The Gregorian calendar, which is still in use today, corrects the average length of the year to 365.2425 days by skipping leap years under certain conditions:

> The Gregorian leap year rule is: Every year that is exactly divisible by four is a leap year, except for years that are exactly divisible by 100, but these centurial years are leap years if they are exactly divisible by 400. For example, the years 1700, 1800, and 1900 are not leap years, but the year 2000 is. [Source](https://web.archive.org/web/20190919024611/https://aa.usno.navy.mil/faq/docs/calendars.php)

Since the Julian Period was introduced in 1583, it used the Julian calendar rather than the Gregorian calendar, which wouldn't be adopted everywhere until the mid-1700s. This means that we need some formula to convert a given Gregorian date into a Julian Day.

A Gregorian calendar date must have 3 integer parts to calculate the Julian Day Number ($\mathrm{JDN}$):

1. The month number, $M$, varying from 1-12.
2. The day of the month, $D$, varying from 1-31 depending on the month.
3. The year, $Y$, using [astronomical year numbering](https://en.wikipedia.org/wiki/Astronomical_year_numbering). Astronomical year numbering is the same as the Gregorian year for all after 1 AD. However, the Gregorian calendar does not include a Year 0, whereas the astronomical year numbering does. Therefore:

   * AD 2 = Astronomical Year 2
   * AD 1 = Astronomical Year 1
   * 1 BC = Astronomical Year 0
   * 2 BC = Astronomical Year -1
   * 3 BC = Astronomical Year -2

To simplify the calculation of the Julian Day Number, we will define the following constants:

:::{math}
:label: eq:julian-day-constants
\begin{aligned}
  A &= \mathrm{INT}\left(\frac{M - 14}{12}\right) \\
  B &= 1461\left(Y + 4800 + A\right) \\
  C &= 367\left(M - 2 - 12A\right) \\
  E &= \mathrm{INT}\left(\frac{Y + 4900 + A}{100}\right)
\end{aligned}
:::

where $\mathrm{INT}$ indicates that integer division should be done. This means that the remainder of the division is discarded, or in other words, the result is rounded towards zero. Then, $\mathrm{JDN}$ can be found by:

:::{math}
:label: eq:julian-day-number
\mathrm{JDN} = \mathrm{INT}\left(\frac{B}{4}\right) + \mathrm{INT}\left(\frac{C}{12}\right) - \mathrm{INT}\left(\frac{3E}{4}\right) + D - 32075
:::

Sample implementations of this algorithm can be found in many places online, and for many programming languages. See:

* <https://web.archive.org/web/20140902005638/http://mysite.verizon.net/aesir_research/date/jdimp.htm>
* <https://archive.org/details/131123ExplanatorySupplementAstronomicalAlmanac/page/n315/mode/2up>, Page 604
* <https://web.archive.org/web/20201207022832/https://craftofcoding.files.wordpress.com/2013/07/cs_langjuliandates.pdf>
* <https://dl.acm.org/doi/10.1145/364096.364097>

## Julian Dates

Related to the Julian Day, the **Julian Date** ($\mathrm{JDT}$) is a decimal number that includes the Julian Day number in the whole part, and the fraction of the day towards the next Julian Day Number in the decimal. Remember that Julian Days start at 12:00 PM (noon) UTC, so 6:00 PM UTC would be JDN + 0.25 and 6:00 AM UTC would be the JDN of the previous Gregorian date + 0.75.

## Example

Here we'll give an example converting Gregorian dates to JDN and JDT.

### Python

One possible Python implementation of the conversion from Gregorian date to JDN is:

```{code-cell} ipython3
def gregorian_to_julian_day_number(month, day, year):
    """Convert the given proleptic Gregorian date to the equivalent Julian Day Number."""
    if month < 1 or month > 12:
        raise ValueError("month must be between 1 and 12, inclusive")
    if day < 1 or day > 31:
        raise ValueError("day must be between 1 and 31, inclusive")
    A = int((month - 14) / 12)
    B = 1461 * (year + 4800 + A)
    C = 367 * (month - 2 - 12 * A)
    E = int((year + 4900 + A) / 100)
    JDN = int(B / 4) + int(C / 12) - int(3 * E / 4) + day - 32075
    return JDN

from datetime import date
today = date(year=2020, month=12, day=7)
jdn = gregorian_to_julian_day_number(today.month, today.day, today.year)
```

```{code-cell} ipython3
:tags: [remove-cell]
from functools import partial
from myst_nb import glue as myst_glue
glue = partial(myst_glue, display=False)
glue("julian-date-jdn", f"{jdn:,d}")
glue("julian-date-today", today.strftime("%B %e, %Y"))
```

The Julian day number corresponding to {glue:text}`julian-date-today` is {glue:text}`julian-date-jdn`. We can confirm this with the NASA calculator at <https://core2.gsfc.nasa.gov/time/julian.html>.

:::{note}
**Note**: In Python 3, integer division (with two slashes `//`) is also known as _floor division_, meaning the result is rounded towards negative infinity. However, the `int()` built-in function rounds floating point numbers towards zero, so in the function above, we use floating point division (with a single slash `/`) and then convert to an integer.
:::

Then, we need to convert the JDN plus a time into the JDT.

```{code-cell} ipython3
def gregorian_to_julian_date(dt):
    """Convert a Gregorian date to a Julian Date."""
    JDN = gregorian_to_julian_day_number(dt.month, dt.day, dt.year)
    JDT = JDN + (dt.hour - 12) / 24 + dt.minute / 1_440 + dt.second / 86_400 + dt.microsecond / 86_400_000_000
    return JDT

from datetime import datetime, time, timezone
evening = datetime.combine(today, time(hour=18, minute=12, second=43, microsecond=674805), tzinfo=timezone.utc)
jdt_evening = gregorian_to_julian_date(evening)
morning = evening.replace(hour=6, minute=0, second=0, microsecond=0)
jdt_morning = gregorian_to_julian_date(morning)
```

```{code-cell} ipython3
:tags: [remove-cell]
glue("julian-date-evening", evening.strftime("%B %e, %Y at %-I:%M:%-S.%f %p %Z"))
glue("julian-date-morning", morning.strftime("%B %e, %Y at %-I:%M:%-S.%f %p %Z"))
glue("julian-date-next-day", evening.replace(day=evening.day + 1).strftime("%B %e"))
glue("julian-date-jdt_evening", f"{jdt_evening:,.4f}")
glue("julian-date-jdt_morning", f"{jdt_morning:,.4f}")
glue("julian-date-jdt_evening-fraction", jdt_evening % 1)
glue("julian-date-jdt_morning-fraction", jdt_morning % 1)
glue("julian-date-morning-time", morning.strftime("%-I:%M %p"))
glue("julian-date-previous-day", morning.replace(day=morning.day - 1).strftime("%B %e"))
```

We first set the time to {glue:text}`julian-date-evening` using `timezone.utc`. Since this time is before 12:00 PM UTC on {glue:text}`julian-date-next-day`, the JDN is still {glue:text}`julian-date-jdn`. The fraction of the day completed is about {glue:text}`julian-date-jdt_evening-fraction:.2f`, so the complete Julian date is {glue:text}`julian-date-jdt_evening`.

If the time is replaced with {glue:text}`julian-date-morning-time` but the date is still {glue:text}`julian-date-today`, then exactly {glue:text}`julian-date-jdt_morning-fraction` of a Julian date has passed, so the JDT is {glue:text}`julian-date-jdt_morning`. However, notice that the JDN is reduced by one, because {glue:text}`julian-date-morning-time` on {glue:text}`julian-date-today` is part of the Julian day that began on {glue:text}`julian-date-previous-day` at 12:00 PM (noon).

The reverse conversion is also useful, going from JDT to proleptic Gregorian date and time.

```{code-cell} ipython3
from datetime import timedelta

def julian_day_number_to_gregorian(jdn):
    """Convert the Julian Day Number to the proleptic Gregorian Year, Month, Day."""
    L = jdn + 68569
    N = int(4 * L / 146_097)
    L = L - int((146097 * N + 3) / 4)
    I = int(4000 * (L + 1) / 1_461_001)
    L = L - int(1461 * I / 4) + 31
    J = int(80 * L / 2447)
    day = L - int(2447 * J / 80)
    L = int(J / 11)
    month = J + 2 - 12 * L
    year = 100 * (N - 49) + I + L
    return year, month, day

def julian_date_to_gregorian(jd):
    """Convert a decimal Julian Date to the equivalent proleptic Gregorian date and time."""
    jdn = int(jd)
    if jdn < 1_721_426:
        raise ValueError("Julian Day Numbers less than 1,721,426 are not supported, "
                         "because Python's date class cannot represent years before "
                         "AD 1.")
    year, month, day = julian_day_number_to_gregorian(jdn)
    offset = timedelta(days=(jd % 1), hours=+12)
    dt = datetime(year=year, month=month, day=day, tzinfo=timezone.utc)
    return dt + offset

gregorian_evening = julian_date_to_gregorian(jdt_evening)
gregorian_morning = julian_date_to_gregorian(jdt_morning)
```

```{code-cell} ipython3
:tags: [remove-cell]
glue("julian-date-gregorian_evening", gregorian_evening.strftime("%B %e, %Y at %-I:%M:%-S.%f %p %Z"))
glue("julian-date-gregorian_morning", gregorian_morning.strftime("%B %e, %Y at %-I:%M:%-S.%f %p %Z"))
```

{numref}`tab:julian-date-conversion-results` shows that the reverse conversion gives back nearly identical results. The difference is most likely due to floating point error in the microsecond calculation.

:::{table} Julian date conversion results using an evening and a morning time.
:name: tab:julian-date-conversion-results

| Original | Julian Date | Converted |
|----------|-------------|-----------|
| {glue:text}`julian-date-evening` | {glue:text}`julian-date-jdt_evening` | {glue:text}`julian-date-gregorian_evening` |
| {glue:text}`julian-date-morning` | {glue:text}`julian-date-jdt_morning` | {glue:text}`julian-date-gregorian_morning` |
:::

Note that `julian_day_number_to_gregorian()` only gives dates in the proleptic Gregorian calendar, and does not account for the change from Julian to Gregorian calendar in 1582. This also means that the minimum Gregorian date is November 24, 4714 BC, corresponding to a Julian Day Number of 0.

```{code-cell} ipython3
print(julian_day_number_to_gregorian(0))
```

Note that since there is no Year 0, 4714 BC is the same as Year -4713.

### MATLAB

MATLAB has a function called [`juliandate`](https://www.mathworks.com/help/matlab/ref/datetime.juliandate.html), which takes a [`datetime`](https://www.mathworks.com/help/matlab/ref/datetime.html) instance and does the conversion to the Julian Date automatically. Note that you should specify the local time zone if you use `datetime('now')` to ensure that the correct Julian Day Number is returned.

```matlab
>> format longG
>> t1 = datetime('2020-12-07 18:12:43.674805');
>> t1.TimeZone = 'UTC';
>> t1

t1 = 

  datetime

   07-Dec-2020 18:12:43

>> juliandate(t1)

ans =

          2459191.25883883
```

This gives the same result as the Python code above. MATLAB also allows the reverse conversion, by using `datetime()` with the Julian Date:

```matlab
>> jd1 = juliandate(datetime('2020-12-07 18:12:43.674805'));
>> datetime(jd1,'ConvertFrom','JulianDate')

ans = 

  datetime

   07-Dec-2020 18:12:43
```

Note that the default time zone is UTC, to convert to a different time zone, the `'TimeZone'` argument to `datetime()` must be supplied:

```matlab
>> datetime(jd1,'ConvertFrom','JulianDate','TimeZone','America/New_York')

ans = 

  datetime

   07-Dec-2020 13:12:43
```

MATLAB also uses the proleptic Gregorian calendar for dates prior to 1583, so JDT of 0 returns the same date as Python.

```matlab
>> datetime(0,'ConvertFrom','JulianDate')

ans = 

  datetime

   24-Nov--4713 12:00:00
```
