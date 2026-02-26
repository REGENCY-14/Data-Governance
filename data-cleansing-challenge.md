# DATA QUALITY ASSESSMENT REPORT
## MedTrack Ghana

**Prepared by:** Junior Developer

---

## 1. EXECUTIVE SUMMARY

An evaluation of the patient appointment dataset revealed multiple data quality issues affecting system performance and business operations. The most critical problems include inaccurate contact information, missing patient details, inconsistent formatting, duplicate patient identifiers, and invalid data entries.

These weaknesses are directly contributing to SMS reminder failures, incorrect reporting metrics, and billing inconsistencies. Immediate data governance controls and validation mechanisms are required to stabilize operations and improve data reliability.

---

## TASK 1: IDENTIFICATION OF DATA QUALITY ISSUES

Below are documented examples of violations across all six data quality dimensions.

### 1. Accuracy

**Example:**
```
P002, Ama Serwa, 244789012, 15/10/2025, dr. osei, paid  
P002, Ama Serwa, 0244789012, 2025-10-15, Dr. Osei, Paid
```

The same patient (P002 – Ama Serwa) has two different phone numbers:
- `244789012`
- `0244789012`

The second appears correct (10 digits starting with 0). The first is missing the leading zero.

**Violation:** One of these phone numbers is factually incorrect, meaning the stored data does not accurately reflect the patient's real contact information.

### 2. Completeness

**Example:**
```
P004,, 0555234567, 2025-10-17, Dr. Mensah, Paid
```

The PatientName field is empty.

**Violation:** Required information is missing.

### 3. Consistency

**Examples:**
- `Dr. Osei` vs `dr. osei`
- `Paid` vs `paid`
- Mixed date formats:
  - `2025-10-15`
  - `15/10/2025`
  - `10/16/2025`

**Violation:** The same type of information is represented in different formats.

### 4. Timeliness

**Example:**
```
10/16/2025 (MM/DD/YYYY format)
```

If the system expects DD/MM/YYYY or ISO format, this date could be misinterpreted as June 10 instead of October 16 depending on system configuration.

**Violation:** Incorrectly interpreted dates may cause reminders to be sent too early, too late, or not at all.

### 5. Validity

**Example:**
```
244789012
```

This phone number does not follow Ghana's 10-digit mobile number format beginning with 0.

**Violation:** The value does not conform to defined format rules.

### 6. Uniqueness

**Example:**
```
P001, Kwame Mensah, 0244123456, 2025-10-15, Dr. Osei, Paid  
P001, Kwame Mensah, 0244123456, 2025-10-20, Dr. Adjei, Pending
```

The same PatientID appears multiple times without a separate AppointmentID.

**Violation:** Lack of a unique appointment identifier causes duplication ambiguity and risks double-counting.

---

## TASK 2: BUSINESS IMPACT ASSESSMENT

| Quality Issue | Operational Problem | Most Affected Function |
|---------------|-------------------|----------------------|
| Inaccurate phone numbers | SMS reminders sent to wrong number | Operations |
| Missing patient name | Delays during check-in | Clinical |
| Inconsistent doctor/payment formats | Incorrect aggregation in reports | Operations |
| Date misinterpretation | Reminders sent at wrong time | Operations |
| Invalid phone format | SMS gateway rejects number | Operations |
| Duplicate PatientIDs | Inflated patient counts | Finance & Operations |

### Detailed Impact Explanation

#### 1. SMS Failures
Inaccurate or invalid phone numbers prevent SMS reminders from being delivered. This increases missed appointments and operational inefficiencies.

#### 2. Incorrect Reports
Duplicate PatientIDs inflate patient statistics and distort performance metrics, leading to poor decision-making.

#### 3. Billing Issues
Inconsistent PaymentStatus values (e.g., `Paid` vs `paid`) may cause billing queries to misclassify transactions, affecting financial reporting.

---

## TASK 3: RECOMMENDED SOLUTIONS

### Critical Issue 1: Inaccurate & Invalid Phone Numbers

**Technical Solution:**
- Enforce backend validation:
  - Must be 10 digits
  - Must start with 0
  - Use regex validation
- Add frontend input masking
- Implement duplicate phone verification for same PatientID

**Responsible:**
- Backend Developer + QA Team

**Verification:**
- Attempt to save invalid numbers → system rejects
- Run audit query → zero non-compliant phone numbers
- Monitor SMS delivery success rate improvement

---

### Critical Issue 2: Duplicate Patient Records

**Technical Solution:**
- Introduce **AppointmentID** as primary key
- Keep **PatientID** as foreign key
- Add composite uniqueness constraint on `(PatientID, AppointmentDate, DoctorName)`

**Responsible:**
- Database Administrator + Backend Developer

**Verification:**
- Run duplicate detection query → zero unintended duplicates
- Validate reporting totals against cleaned dataset

---

### Critical Issue 3: Data Standardization (Consistency Problem)

**Technical Solution:**
- Convert DoctorName to standardized format (Title Case)
- Use ENUM for PaymentStatus (`Paid`, `Pending`, `Failed`)
- Store AppointmentDate as DATE datatype in ISO format
- Replace free-text fields with dropdown selections

**Responsible:**
- Backend Developer + Product Team

**Verification:**
- No lowercase doctor names in database
- PaymentStatus contains only predefined values
- All dates stored in ISO format

---

## CONCLUSION

The dataset contains violations across all six data quality dimensions: **accuracy**, **completeness**, **consistency**, **timeliness**, **validity**, and **uniqueness**.

These issues are directly responsible for:
- SMS delivery failures
- Inaccurate reporting
- Billing errors at MedTrack Ghana

By implementing validation rules, enforcing database constraints, and standardizing input formats, the organization can significantly improve operational efficiency, financial accuracy, and data reliability.

### Immediate Priorities

1. **Fix phone number validation**
2. **Introduce proper uniqueness constraints**
3. **Standardize data entry controls**
