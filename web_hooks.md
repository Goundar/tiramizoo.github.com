---
layout: page
title: Web Hooks
---
{% include JB/setup %}

The Tiramizoo API provides a simple way of tracking the status of orders you
create in the API.

You can easily avoid polling by giving an additional `web_hook_url` parameter when
creating an order. When you do so, we will `POST` you the response back to the
provided URL. To ensure that your API has processed the hook, we'll check the
response code of the `POST` request. If you return a response code other than
`2xx` we assume that the hook wasn't processed correctly and it will be
retried in a short period of time. We will retry with an
exponential backoff using the formula
`retry_count ** 4 + 15` (i.e. 15, 16, 31, 96, 271, ... seconds).
It will perform 25 retries over approximately 20 days.

Currently we support web hooks for Orders only. The response body of such web hook
request contains exactly the same fields as the response body from Get /orders/:order_uuid request.

Keep in mind that the order in which we `POST`
those status changes is not guaranteed, though usually they are ordered by the
timestamp of the status change.
