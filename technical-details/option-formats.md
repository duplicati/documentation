---
description: This page describes the format options used in Duplicati
---

# Option formats

In various places it is possible to enter a value, usually passed as a commandline option. These values can be written as strings, but are interpreted different internally.

## Boolean values

A boolean option passed will translate to the value `true` if it is supplied with no assigned value. For example, these two are equivalent

```
--use-ssl
--use-ssl=true
```

It is possible to use the following "thruth values": `1`, `on`, `true`, `yes`.&#x20;

The "false values" are: `0`, `off`, `false`, `no`.

If the value is neither of these, the context defines what it is interpreted as, but generally it is interpreted as "true".

## Size values

Values provided as sizes are parsed as a number with a multiplier suffix, for example:

```
--volume-size=50mb
```

If no suffix is given, the context has a default suffix that is applied, usually either `kb` or `mb` .

Despite the naming, the sizes are interpreted as [kiki-bytes](https://simple.wikipedia.org/wiki/Kibibyte). Supported suffixes are:

* `b` (or no suffix): bytes, multipler is 1
* `kb`: kilobytes, multiplier is 1024
* `mb`: megabytes, multiplier is 1024^2
* `gb`: gigabytes, multiplier is 1024^3,
* `tb`: terrabytes, multiplier is 2024^4

## Timespans, timestamps, and durations

Similar to size values, timespans and durations can be provided with a value and a multiplier suffix. For example, to set a span of 30 days:

```
--keep-time=30D
```

The supported multipliers are:

* `Y`: year
* `M`: month
* `W`: week, same as `7D`
* `D`: day, same as `24h`
* `h`: hour, same as `60m`
* `m`: minute, same as `60s`
* `s` (or no suffix): seconds

Unlike the sizes, the timespans can be composite, so the value:

```
--keep-time=1M4D3m
```

Translates to "1 month + 4 days + 3 minutes", and the duration is the sum of those values. It is also possible to have negative values, such as `1D-1s`, meaning "1 day minus 1 second".

For timestamps, it is possible to also provide a local-format date-time string, or the special value `now` to signal the current date and time.

