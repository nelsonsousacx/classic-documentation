---
template: sidebyside
endpoint: customer_profile/567/678/oeu1234.5678
endpoint_domain: https://vis.optimizely.com/api/
type: POST
title: Write customer profile
anchor: update-customer_profile
request:
  data:
    "Life-time value": 10
    "most_viewed_category": "jeans"
response: 201 Created (empty response body)
---

Create or update a single customer profile.  The `dcp_service_id`, `dcp_datasource_id`, and `customer_id_in_datasource` are required in the URL.

The request data must be a JSON object, where each key is the `name` of an
[attribute](#dcp_attributes) and each `value` must conform to the attribute's datatype and format.

#### Note:
- The specified attribute value overwrites any existing value specified earlier.
- The request may contain a subset of defined [attributes](#dcp_attributes).
- If a key does not correspond to a registered [attribute](#dcp_attributes) `name`, the write
  will fail
- If a value does not respect the [attribute's](#dcp_attributes) `datatype`/`format`, the write
  will fail
