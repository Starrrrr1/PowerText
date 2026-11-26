# PowerText

PowerText pairs hourly electricity demand with summaries of weather forecast discussions for eight U.S. electricity regions. This snapshot is the input table used in the primary PowerText experiments in *Diagnosing Language-Encoder Scaling in Text-Augmented Forecasting*.

## Download and verify

Download `powertext_full_split.csv.gz` and `SHA256SUMS` from [PowerText v1.0.0](https://github.com/Starrrrr1/PowerText/releases/tag/v1.0.0). Keep the original file order and `row_id` values when reproducing the experiments.

```bash
gzip -dk powertext_full_split.csv.gz
shasum -a 256 -c SHA256SUMS
```

## Coverage and splits

The table contains 104,832 hourly rows covering 2025-01-01 00:00 UTC through 2026-06-30 23:00 UTC, inclusive. Each of CISO, ERCO, ISNE, MISO, NYIS, PJM, SWPP, and TVA contributes 13,104 rows. The combination of `region_id` and `timestamp_utc` identifies a row.

The `split` column follows a chronological 60/20/20 split within each region. For the paper's primary 96-hour input and 96-hour forecast task, every target window lies entirely in its assigned split. A validation or test window can use earlier demand observations as history.

| Split | Hourly rows | Eligible forecast windows |
| --- | ---: | ---: |
| Train | 62,896 | 61,368 |
| Validation (`val`) | 20,960 | 20,200 |
| Test | 20,976 | 20,216 |

The numeric forecasting target is `actual_demand_mw` (MW). `nws_forecast_summary_gpt5` contains the weather summary, and `nws_forecast_publish_time` records the source discussion's issue time. Use training-only statistics for any normalization. The table also preserves ancillary energy columns. In particular, `demand_forecast_error_mw` depends on the realized demand.

## Sources and reuse

- Hourly electricity data come from the U.S. Energy Information Administration [EIA-930 API](https://www.eia.gov/electricity/930-content/about.html). See the [EIA reuse policy](https://www.eia.gov/about/copyrights_reuse.php) and [API terms](https://www.eia.gov/opendata/terms-of-service.php).
- Area Forecast Discussions come from the U.S. National Weather Service via the Iowa State University Iowa Environmental Mesonet [NWS text archive](https://mesonet.agron.iastate.edu/info/datasets/afos.html). See the [NWS disclaimer](https://www.weather.gov/disclaimer) and [IEM usage policy](https://mesonet.agron.iastate.edu/disclaimer.php).
- The dataset authors generated the summaries retrospectively using the request model label `gpt-5.5`. The archived metadata preserve the label and cache-write times.

## Limitations

Every recorded source discussion was issued at or before its associated hourly timestamp. The summaries were produced after the historical target period. The archive does not establish real-time receipt or the historical availability of individual demand revisions. Each representative forecast office covers its own service area, which may differ from the assigned electricity region. The snapshot retains eight zero-demand rows in NYIS whose provider-level interpretation is unverified.
