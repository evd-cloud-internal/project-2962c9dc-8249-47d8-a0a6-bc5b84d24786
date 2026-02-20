---
name: Domains
assetId: 661832d2-d946-4c27-bfe4-b2bd5bdc8337
type: page
---

# Domain Lookup

{% text_input
    id="domain_search"
    title="Domain"
    placeholder="Enter a domain (e.g. heloa.app)"
    icon="globe"
/%}

{% big_value
    data="detections"
    value="count(service_id)"
    title="Services Detected"
/%}

{% big_value
    data="detections"
    value="min(first_detected_at)"
    title="First Seen"
/%}

{% big_value
    data="deltas"
    value="count(service_id)"
    title="Total Changes"
/%}

```sql detections
select
    service_id,
    first_detected_at
from postgres_public_detections
[[where domain = '{{domain_search}}']]
order by detected_at desc
```

## Current Detections

{% table data="detections" %}
    {% dimension value="service_id" link="concat('/shovel/domains?service_id=', service_id)"
    link_new_tab=true /%}
{% /table %}

```sql deltas
select
    service_id,
    delta_type,
    detected_at,
    detected_date
from postgres_public_detection_deltas
[[where domain = '{{domain_search}}']]
order by detected_at desc
```

## Detection Changes

{% table data="deltas" %}
    {% dimension value="service_id" link="concat('/org_01JXAKQQVRM397QNJTFC12YWE0/shovel/domains?service_id=', service_id)" /%}
{% /table %}