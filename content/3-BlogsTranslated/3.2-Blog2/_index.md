---
title: "Blog 2"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---


# Advanced analytics using Amazon CloudWatch Logs Insights

**Authors:** Joe Alioto & Dot Ho | **Date:** 27 AUG 2025 | **Category:** Amazon CloudWatch, Observability, Logs, Analytics

Effective log management and analysis are critical for maintaining system stability, security, and high performance. Amazon CloudWatch Logs Insights has long been a powerful tool for searching, filtering, and analyzing log data from multiple log groups. The addition of OpenSearch Piped Processing Language (PPL) and OpenSearch SQL support provides greater flexibility and familiarity for log analytics.

Whether you are a developer debugging application issues, a security analyst investigating threats, or an operations manager monitoring system performance — these new capabilities empower you to:

- Use AI-powered natural language query generation and automatic result summarization — reducing the time from raw logs to insights.
- Easily **correlate** logs using JOINs across multiple log groups.
- Leverage a rich function library for processing and analyzing log data (JSON parsing, mathematical functions, string manipulation...) without needing external tools.
- Correlate data from various log sources — ideal for complex systems, microservices, and distributed architectures.

---

## Key capabilities of PPL & SQL in CloudWatch Logs Insights

### Familiar SQL-like query language

You can use SQL to analyze logs instead of learning a separate domain-specific language. Example:

```sql
SELECT eventName, COUNT(*) as count
FROM loggroupname
GROUP BY eventName
ORDER BY count DESC
LIMIT 10
```

---

### Correlation capabilities — JOIN across multiple log groups

You can join data from different log groups to obtain a more complete picture. Example:

```sql
SELECT a.transaction_id,
       a.error_message AS application_error,
       a.timestamp AS application_timestamp,
       i.error_message AS infrastructure_error,
       i.timestamp AS infrastructure_timestamp
FROM application_logs a
LEFT JOIN infrastructure_logs i ON a.transaction_id = i.transaction_id
```

This is especially useful in microservices environments, where a transaction often traverses multiple services.

---

### Function library for processing & analyzing complex log data

CloudWatch Logs Insights supports JSON functions, mathematical functions, string functions, and more. For example:

```sql
SELECT
  JSON_EXTRACT_SCALAR(message, '$.user_id') as user_id,
  AVG(CAST(JSON_EXTRACT_SCALAR(message, '$.response_time') AS DOUBLE)) as avg_response_time
FROM loggroupname
GROUP BY JSON_EXTRACT_SCALAR(message, '$.user_id')
```

This allows deeper analysis without exporting logs elsewhere.

---

### Advanced queries: subqueries & advanced aggregations

You can write subqueries or perform advanced aggregations to analyze uncommon patterns or behaviors:

```sql
SELECT user_id, api_calls
FROM (
   SELECT user_id, COUNT(*) as api_calls
   FROM api_logs
   GROUP BY user_id
) subquery
WHERE api_calls > (SELECT AVG(api_calls) * 2 FROM subquery)
```

---

## Fast query generation & analysis using AI + on-demand anomaly detection

### Natural language query generation & summarization

You can type a simple English command such as:  
> "Get api count by eventSource and eventName and sort it by most to least"  

→ CloudWatch Logs Insights automatically generates a valid query (PPL / SQL / Logs Insights QL). After execution, the system can **summarize the results** in natural language — helping you quickly understand insights without scanning hundreds or thousands of log lines.

### On-demand anomaly detection

A notable feature: you can detect unusual patterns instantly — without prior setup:

- Use `pattern @message | anomaly` to identify anomalies.
- Returned fields include: `@description`, `@anomalyLogSamples`, `@priority`, `@priorityScore`, `@patternString`.
- You can also use `compare` to compare patterns across time ranges — useful for investigating differences between current and historical logs.

Typical use cases:

- Security monitoring: detect unusual access, login failures...
- Application health monitoring: detect error spikes, latency issues...
- Infrastructure monitoring: identify abnormal resource usage...

---

## Correlation — subqueries & JOINs across multiple log groups

With JOINs and subqueries, you can correlate data across log groups — enabling end-to-end transaction tracing, cross-service debugging, and multi-service event investigation.

**Example:** find all orders whose payment status is "completed".

**SQL:**

```sql
SELECT correlationId, timestamp
FROM `/orders/prod` o
WHERE EXISTS (
  SELECT 1
  FROM `/payments/prod` p
  WHERE p.correlationId = o.correlationId
    AND p.paymentStatus = 'completed'
)
ORDER BY timestamp DESC
LIMIT 10;
```

**PPL:**

```ppl
fields timestamp, correlationId
| where `correlationId` IN [
    search source=`/payments/prod`
    | where paymentStatus="completed"
    | fields `correlationId`
  ]
| head 10
```

You can also use JOINs to combine full payment + order data (orderId, paymentStatus, amount, payment method…).

When working with correlation, pay attention to appropriate time ranges, indexing common fields, performance on large datasets, and handling missing or null correlationId values.

---

## Conclusion

The enhancements to CloudWatch Logs Insights through PPL & SQL support significantly expand its log management and analytics capabilities:

- More accessible using familiar query languages (SQL).  
- Deeper analytics through function libraries & advanced aggregations.  
- Cross-log-source correlation enables stronger cross-service debugging & analysis.  
- Generative AI shortens the time from raw logs → actionable insights.  
- On-demand anomaly detection enables fast and early issue detection.  

Logs Insights is no longer just a log search tool — it is a **comprehensive log analytics platform**, ideal for distributed systems, microservices architectures, and environments requiring high observability.

For production use, consider defining indexes on frequently queried fields to improve performance, and regularly review saved queries as your system evolves.
