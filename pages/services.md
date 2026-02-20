---
name: Services
assetId: 5ba6ddb6-7660-48f1-8c07-51d6ee356792
type: page
---

# Detections by Service

{% text_input
    id="service_id"
    title="Service ID"
    placeholder="Enter a service ID (e.g. apache, nginx, cloudflare)..."
    initial_value="apache"
/%}

{% range_calendar
    id="date_range"
    title="Date Range"
    default_range="last 12 months"
/%}

## Detections Over Time

{% line_chart
    data="postgres_public_detections"
    x="detected_at"
    y="count(*)"
    date_grain="day"
    where="service_id = '{{service_id}}'"
    date_range={
        date="detected_at"
        range={{date_range}}
    }
    title="Daily Detections"
/%}

## 100 Most Recent Detections

```sql recent_detections
select id, domain, detected_at, rank, first_detected_at
from postgres_public_detections
where service_id = '{{service_id}}'
  and detected_at {{date_range.between}}
order by detected_at desc
limit 100
```

{% table
    data="recent_detections"
    order="detected_at desc"
    page_size=20
/%}