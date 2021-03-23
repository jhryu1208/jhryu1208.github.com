---
layout: post
title:  "[BigQuery] 알아두면 좋은 숫자형/문자형 함수"
subtitle: "[BigQuery] 알아두면 좋은 숫자형/문자형 함수"
categories: gcp
tags: bigquery
comments: true
---

프로젝트에서 실행한 쿼리 중 가장 비용이 많이 들었던 쿼리들을 출력 

```SQL
SELECT	job_id, query, 
		user_email, total_bytes_processed, 
		total_slot_ms
FROM  `region-us`.INFORMATION_SCHEMA.JOBS_BY_USER
ORDER  BY total_bytes_processed DESC
LIMIT  5 # LIMIT은 걸어두고 실행하는 것을 권장한다.
```

<br>

---

### References
- https://www.pascallandau.com/bigquery-snippets/monitor-query-costs/

