---
name: Home
assetId: 2e321330-654a-4569-bd02-e490bfe3f114
type: page
---

# Detections Overview

```sql detection_summary
SELECT
  count(*) as total_detections,
  count(DISTINCT domain) as unique_domains,
  count(DISTINCT service_id) as unique_services
FROM postgres_public_detections
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

## Last Hour

```sql last_hour_summary
SELECT
  count(*) as total_detections,
  count(DISTINCT domain) as unique_domains,
  count(DISTINCT service_id) as unique_services
FROM postgres_public_detections
WHERE detected_at >= now() - INTERVAL 1 HOUR
```

{% big_value
    data="last_hour_summary"
    value="total_detections"
    title="Detections (Last Hour)"
    fmt="#,##0"
/%}

{% big_value
    data="last_hour_summary"
    value="unique_domains"
    title="Domains (Last Hour)"
    fmt="#,##0"
/%}

{% big_value
    data="last_hour_summary"
    value="unique_services"
    title="Services (Last Hour)"
    fmt="#,##0"
/%}

```sql last_hour_by_minute
SELECT
  toStartOfMinute(detected_at) as minute,
  count(*) as detections
FROM postgres_public_detections
WHERE detected_at >= now() - INTERVAL 1 HOUR
GROUP BY minute
ORDER BY minute
```

{% bar_chart
    data="last_hour_by_minute"
    x="minute"
    y="detections"
    y_fmt="#,##0"
    title="Detections by Minute (Last Hour)"
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
  count(*) as detections,
  count(DISTINCT domain) as unique_domains,
  min(detected_at) as first_seen,
  max(detected_at) as last_seen
FROM postgres_public_detections
GROUP BY service_id
ORDER BY detections DESC
```

{% table
    data="all_services"
    search=true
%}
    {% dimension
        value="service_id"
    /%}
    {% measure
        value="sum(detections)"
        title="Detections"
        fmt="#,##0"
        sort="desc"
    /%}
    {% measure
        value="sum(unique_domains)"
        title="Unique Domains"
        fmt="#,##0"
    /%}
    {% dimension
        value="first_seen"
    /%}
    {% dimension
        value="last_seen"
    /%}
{% /table %}