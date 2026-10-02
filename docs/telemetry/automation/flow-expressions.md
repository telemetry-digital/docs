---
title: Flow expressions
slug: flow-expressions
sidebar_position: 6
tags: [automation, flows, expressions, templates]
---

Flow nodes compute with a small, safe expression language. It has no loops, no variables you can assign, no access
to files, the network or anything outside the message — and limits on size and work. It is checked when you save,
so a typing error shows up as a problem on the node before the flow runs.

```
value * 1.8 + 32
msg.temp > msg.limit + 2 && quality == 'ok'
if(value > 80, 'hot', 'ok')
fixed(value, 1)
fmtTime(ts, 'HH:mm')
hour() >= 22 || weekday() >= 6
coalesce(msg.x, 0)
```

## Where expressions are used

- **Expression** settings: *Condition*, *Switch* rules, *Change* (set), *Math*, *Split*, *Counter*, *Latch*,
  *True for*, *Store value*, *Device command*, *Device attribute*, *Entity action*, *Debug*, *Manual inject*.
- **Templates**: text with `{{ expression }}` placeholders in *Template*, *Notify*, *Incident*, *HTTP request*,
  *MQTT out* and *Camera mark*.

## Values

| Type | Written as | Notes |
|---|---|---|
| number | `42`, `-3.5`, `1e3`, `.5` | decimal numbers |
| text | `'on'` or `"on"` | `\n` new line, `\t` tab, `\'` and `\"` quotes |
| true/false | `true`, `false` | the results of comparisons |
| null | `null` | "no value" — a missing field is null |

**Truth**: `null`, `0`, an empty text and `false` count as false; everything else as true.

## Names

| Name | Value |
|---|---|
| `value` | the message's value |
| `topic` | the message's topic |
| `ts` | the message's time in seconds since 1970, or null |
| `quality` | the quality flag of a stored value |
| `source` | the source's name, or its kind |
| `msg.<field>` | a field of the message, null when missing |
| `flow.<name>` | flow memory, null when not set |

Any other name is an error:
`unknown name x (use value, topic, ts, quality, source, msg.<field> or flow.<name>)`. A field name has no further dots: `msg.a.b` is not allowed.

!!! note "Flow memory"
    Flow memory is kept across restarts, but no node of the current library writes to it, so `flow.<name>` reads
    null. Keep values in message fields, a *Counter* or a *Latch* instead.

## Operators

From the weakest to the strongest binding:

| Operators | Meaning |
|---|---|
| `c ? a : b` | `a` when `c` is true, otherwise `b` |
| two vertical bars (or) | true when either side is true |
| `&&` (and) | true when both sides are true |
| `==`, `!=` | equal, not equal |
| `<`, `<=`, `>`, `>=` | comparison |
| `+`, `-` | addition, subtraction; `+` joins texts when either side is text |
| `*`, `/`, `%` | multiplication, division, remainder |
| `^` | power, right to left: `2^3^2` is `2^9`; `-2^2` is `-4` |
| `!`, `-` in front | not, negative |

Parentheses group as usual. `&&`, `||`, `?:` and `if()` evaluate only the side they need.

**Comparisons in detail:**

- `==` compares numbers numerically even when one side is a numeric text: `'5' == 5` is true. `null` equals only
  `null`. Otherwise texts are compared as texts.
- `<`, `>`, … compare two texts alphabetically, otherwise numbers. A comparison with `null` or with a text that is
  not a number is **false** — `value > 8` is false when the value is missing.
- Arithmetic with something that is not a number is an **error** (*not numbers*), as is a division by zero. A node
  whose expression fails writes an error and stops that message.

## Functions

| Function | Result |
|---|---|
| `abs(x)` | absolute value |
| `min(a, b, …)`, `max(a, b, …)` | smallest or largest of 2 to 8 numbers |
| `round(x)`, `round(x, d)` | rounded to `d` decimals (0–9, default 0) |
| `floor(x)`, `ceil(x)` | rounded down or up |
| `sqrt(x)`, `pow(x, y)` | square root, power |
| `clamp(x, lo, hi)` | `x` kept between `lo` and `hi` |
| `log(x)`, `log10(x)`, `exp(x)` | natural and decimal logarithm, e to the power of `x` |
| `fixed(x, d)` | text with exactly `d` decimals (0–9), halves rounded away from zero: `fixed(2.345, 2)` is `'2.35'` |
| `len(t)` | number of characters |
| `lower(t)`, `upper(t)`, `trim(t)` | lower case, upper case, without surrounding spaces |
| `contains(t, s)`, `startsWith(t, s)`, `endsWith(t, s)` | true or false |
| `replace(t, a, b)` | every `a` in `t` replaced by `b` |
| `substr(t, start)`, `substr(t, start, n)` | part of a text, `start` counted from 0 |
| `str(x)` | as text |
| `num(x)` | as number, or null when it is not one |
| `bool(x)` | true or false by the truth rule |
| `now()` | the current time in seconds since 1970 |
| `hour()`, `minute()`, `day()`, `month()`, `year()` | parts of the current time; with an argument `hour(ts)` of that time |
| `weekday()`, `weekday(ts)` | 1 = Monday … 7 = Sunday |
| `fmtTime(ts, layout)` | time as text; the layout uses `YYYY`, `MM`, `DD`, `HH`, `mm`, `ss`, for example `'DD.MM.YYYY HH:mm'` |
| `if(c, a, b)` | `a` when `c` is true, otherwise `b` |
| `coalesce(a, b, …)` | the first of 2 to 8 values that is not null |
| `isNull(x)` | true when `x` is null |

Time functions use the **organization's time zone**. Calling a function with the wrong number of arguments is a
problem found on save: *round() takes 1–2 arguments*.

## Templates

A template is text with placeholders: `{{ expression }}`. Each placeholder is evaluated and written into the text.

```
{{ topic }} is {{ fixed(value, 1) }} °C at {{ fmtTime(ts, 'HH:mm') }}
Door {{ if(value, 'opened', 'closed') }} — {{ msg.from }} → {{ value }}
```

- Whole numbers are written without decimals; other numbers with up to six significant digits.
- `null` is written as nothing.
- Every `{{` needs a matching `}}`; at most 50 placeholders are checked on save.
- A template is at most 4 000 characters; the finished text at most 4 096 bytes.

## Limits

| Limit | Value |
|---|:---:|
| Length of one expression | 1 000 characters |
| Parts of one expression | 300 |
| Nesting depth | 60 |
| Evaluation steps | 10 000 |
| Length of a text result | 4 096 bytes |

[Automation rules](automation-rules.md) use a different, purely numeric expression language — see their page.
