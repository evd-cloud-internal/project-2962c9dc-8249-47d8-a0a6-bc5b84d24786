---
name: Home
assetId: 2e321330-654a-4569-bd02-e490bfe3f114
type: page
---

# Detections Overview

{% range_calendar
    id="date_range"
    default_range="last 7 days"
    preset_ranges=["last 7 days", "last 30 days", "last 3 months", "last 6 months", "last 12 months", "month to date", "year to date", "all time"]
/%}

```sql detection_summary
SELECT
  count(*) as total_detections,
  count(DISTINCT domain) as unique_domains,
  count(DISTINCT service_id) as unique_services
FROM postgres_public_detections
WHERE detected_at {{date_range.between}}
```

{% big_value
    data="detection_summary"
    value="total_detections"
    title="Total Detections"
    fmt="#,##0"
/%}

{% big_value
    data="detection_summary"
    value="unique_domains"
    title="Unique Domains"
    fmt="#,##0"
/%}

{% big_value
    data="detection_summary"
    value="unique_services"
    title="Unique Services"
    fmt="#,##0"
/%}

## Detections Over Time

{% bar_chart
    data="postgres_public_detections"
    x="detected_at"
    y="count(id)"
    date_grain="hour"
/%}

## Top 15 Services by Detections

{% horizontal_bar_chart
    data="postgres_public_detections"
    y="service_id"
    x="count(id)"
    x_fmt="#,##0"
    limit=15
    order="count(id) desc"
/%}

## All Services

```sql all_services
SELECT
  service_id,
  min(detected_at) as first_seen,
  count(*) as detections
FROM postgres_public_detections
GROUP BY service_id
ORDER BY detections DESC
```

{% table
    data="all_services"
    search=true
/%}