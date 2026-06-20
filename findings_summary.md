# Findings Summary — Delhi (Anand Vihar) Air Quality, 2023

**Dataset:** 8,760 hourly readings (Jan 1 – Dec 31, 2023) · PM2.5, PM10, NO2, SO2, CO, O3 · Anand Vihar monitoring station

## Data quality issues found and fixed
| Issue | Found | Fix applied |
|---|---|---|
| Missing readings | 2,494 null values across pollutants (incl. two multi‑day sensor outages: Mar 10–11 maintenance, Aug 5–7 waterlogging) | Time‑based interpolation after reindexing to a complete hourly grid |
| Rows missing entirely | 18 hours never made it into the export | Reindexed to full 8,760‑hour grid so gaps are explicit, not silently skipped |
| Duplicate timestamps | 80 double‑logged rows (some with conflicting sensor values) | Exact duplicates dropped; conflicting duplicates resolved by averaging |
| Inconsistent units | 241 rows (Jul 1–10) logged PM2.5/PM10 in mg/m³ instead of µg/m³ | Detected via the unit column and multiplied back ×1000 |
| Impossible values | 7 negative readings + several decimal‑shift spikes (e.g., one PM2.5 reading of 15,816 µg/m³) | Negative values and readings above a domain‑sanity cap (1,500 µg/m³ for PM2.5) set to null, then interpolated |

Result: a 8,760‑row, zero‑null, single‑unit cleaned CSV ready for analysis.

## When is the air worst?

- **Worst month: November (337 µg/m³ mean PM2.5)** — over 8x the level of the cleanest month. December and January follow (177 and 199 µg/m³).
- **Best month: August (39 µg/m³)** — still more than 2.5x the WHO guideline, just far better than winter.
- **Worst hour: 8–9 PM** (evening traffic combined with the boundary layer collapsing as temperatures drop). A secondary peak occurs at 7–9 AM. The cleanest air is consistently around **2 PM**, when daytime heating lifts the inversion layer.
- **Weekday vs weekend:** weekdays average **124 µg/m³** vs **114 µg/m³** on weekends — a modest but consistent traffic signature, smaller than the month‑to‑month swing.
- **Safety limits:** PM2.5 exceeded the WHO 24‑hour guideline (15 µg/m³) on **100% of days** in the year. It exceeded India's own (much looser) national standard (60 µg/m³) on **75% of days**, dropping to 0% in July–August and rising back to 100% by October.

## Most plausible reason

Delhi's winter spike is best explained by the overlap of three things that all get worse at once from late October through January: **post‑monsoon crop‑residue (stubble) burning** in neighboring states, **falling temperatures that trap a shallow boundary layer** close to the ground (so traffic and heating emissions can't disperse), and **calm winds** (our data shows winter wind speeds roughly half of pre‑monsoon levels). The Diwali firecracker period (mid‑November) sits right in the middle of this window, which is why November is the single worst month rather than December or January. The diurnal pattern supports the traffic story too: NO2 (a direct traffic marker) peaks sharply at rush hours while O3 — which requires sunlight to form — peaks at the opposite time of day, around 2 PM.

## Caveats
- The two multi‑day sensor outages were filled by interpolation, which assumes a gradual change across the gap — treat values inside those specific windows (Mar 10–11, Aug 5–7) as lower‑confidence estimates, not real readings.
- This is a single station (Anand Vihar), one of Delhi's more heavily‑trafficked sites — citywide averages would likely be somewhat lower.

## Suggested next steps
- Compare Anand Vihar against a cleaner Delhi station (e.g., Lodhi Road) on the same axes to quantify how much location matters.
- Extend to a forecasting model (e.g., predict next‑day PM2.5 from weather + recent trend) using this cleaned dataset as the training input.
