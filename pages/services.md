---
name: Services
assetId: 5ba6ddb6-7660-48f1-8c07-51d6ee356792
type: page
---

# Services

```sql services
select distinct identifier as service_id
from all_bfbpyi
order by identifier
```

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

```sql detections_over_time
select
    toDate(d.first_detected_at) as detection_date,
    count(*) as detections
from postgres_public_detections d
inner join all_bfbpyi s on d.service_id = s.identifier
where d.service_id = {{service_id.selected}}
  and d.first_detected_at between {{date_range.start}} and {{date_range.end}}
group by detection_date
order by detection_date
```

## Detections Over Time

{% bar_chart
    data="detections_over_time"
    x="detection_date"
    y="detections"
    title="Detections Over Time"
    subtitle="Filtered by group, service, and date range"
/%}

```sql all_detections
select
    d.id,
    d.domain,
    d.rank,
    d.first_detected_at,
    d.detected_at
from postgres_public_detections d
where d.service_id = {{service_id.selected}}
  and d.first_detected_at between {{date_range.start}} and {{date_range.end}}
order by d.first_detected_at desc
```

## All Detections

{% table
    data="all_detections"
    search=true
    page_size=20
    title="Detections"
    subtitle="All detections for the selected service"
/%}
