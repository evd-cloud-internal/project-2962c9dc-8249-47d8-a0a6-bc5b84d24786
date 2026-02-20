---
name: Services
assetId: 5ba6ddb6-7660-48f1-8c07-51d6ee356792
type: page
---

# Detections by Service

```sql groups
select distinct g as group_id
from all_bfbpyi
array join groups as g
order by g
```

```sql services
select distinct identifier as service_id
from all_bfbpyi
array join groups as g
where g = {{group_filter}}
order by identifier
```

{% dropdown
    id="group_filter"
    data="groups"
    value_column="group_id"
    title="Group"
/%}

{% dropdown
    id="service_id"
    data="services"
    value_column="service_id"
    title="Service"
/%}

{% range_calendar
    id="date_range"
    title="Date Range"
    default_range="last 12 months"
/%}

## Detections Over Time

```sql group_services
select identifier as service_id
from all_bfbpyi
array join groups as g
where g = {{group_filter}}
```

{% line_chart
    data="postgres_public_detections"
    x="detected_at"
    y="count(*)"
    date_grain="day"
    where="service_id in (select service_id from {{group_services}})"
    filters=["service_id"]
    date_range={
        date="detected_at"
        range={{date_range}}
    }
    title="Daily Detections"
/%}

## Detections by Service

```sql service_not_selected
select 1 where {{service_id}} = ''
```

{% if data="service_not_selected" %}

```sql group_aggregate
select
    d.service_id,
    count(*) as detection_count,
    min(d.detected_at) as earliest_detection,
    max(d.detected_at) as latest_detection
from postgres_public_detections d
where d.service_id in (select service_id from {{group_services}})
  and d.detected_at {{date_range.between}}
group by d.service_id
order by detection_count desc
```

{% table data="group_aggregate" /%}

{% /if %}

## 100 Most Recent Detections

```sql recent_detections
select id, service_id, domain, detected_at, rank, first_detected_at
from postgres_public_detections
where service_id in (select service_id from {{group_services}})
  and detected_at {{date_range.between}}
order by detected_at desc
limit 100
```

{% table
    data="recent_detections"
    filters=["service_id"]
    page_size=20
/%}