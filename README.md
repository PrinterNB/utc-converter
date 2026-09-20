# Time Zone → UTC Converter

A single-file web page that converts a local time in any IANA time zone to UTC.

**Live version: <https://utc.parkerbrown.photos>**

## Features

- **Full DST handling.** Conversions are correct across daylight saving time
  transitions, including the two tricky cases:
  - **Spring-forward gaps** — the exact wall time doesn't exist; the page says so
    and shows the closest equivalent UTC instant.
  - **Fall-back ambiguity** — the wall time occurs twice; the page says so and
    shows the first occurrence.
- **Full IANA zone list**, populated from `Intl.supportedValuesOf("timeZone")`,
  with the most common zones (plus UTC) pinned at the top.
- **Always current** — zone rules come from the OS/browser's time zone database
  (via `Intl`), so if a country changes its DST rules, the page stays accurate
  without an update.
- **No dependencies, no build step.** The entire app is one `index.html` file.

## Running locally

Just open `index.html` in a browser. Or, if you prefer serving it:

```sh
python -m http.server 8000
# → http://localhost:8000
```

## How the conversion works

`Intl.DateTimeFormat` is used twice:

1. Given a UTC timestamp, read the zone's wall clock (via `formatToParts` with
   explicit numeric fields) and reassemble it as a `Date.UTC` value.
2. Invert that with a few fixed-point iterations to find the UTC timestamp whose
   wall clock in the target zone matches the entered time — iterating enough
   times to be correct across DST transitions.