# DATA ACCESS DECISION SIMULATOR

---

## REQUEST 1: MARKETING CAMPAIGN

**From:** Sarah Owusu, Marketing Manager

**Decision:** Conditionally Approve (Modified Scope)

### Lifecycle Stage
Use and Share (using stored student data for a new business purpose)

### Justification

#### 1. Data Classification Conflict
- Emails → **CONFIDENTIAL**
- Names & Enrollments → **INTERNAL**
- Sarah only has **INTERNAL** access, not **CONFIDENTIAL**.

#### 2. Least Privilege Principle
- Access must be limited to only what is necessary.
- A "full student database" exceeds what is required for a referral campaign.
- Marketing does not require raw PII if anonymized or opt-in filtered data can achieve the same goal.

#### 3. Purpose Limitation (Ghana DPA)
- Student data must only be used for the purpose it was originally collected for.
- If students did not consent to marketing campaigns, using emails may violate data protection law.

### Safeguards Required for Conditional Approval

- **Provide opt-in students only** (students who consented to marketing).
- **Share email list only**, not full database.
- **Data minimization:** remove phone numbers unless strictly required.
- **Provide data through secure CRM export** (not raw database dump).
- **Temporary, time-bound access** (auto-expire after campaign).
- **Log all access activity.**
- **NDA acknowledgment reminder.**

### Who to Consult

- Data Protection Officer (DPO)
- Legal/Compliance team
- Head of IT Security

### Action Steps

1. Verify marketing consent records.
2. Generate filtered list (opt-in students only).
3. Remove unnecessary attributes.
4. Provide secure, time-limited access.

---

## REQUEST 2: ANALYTICS PARTNERSHIP

**From:** David Mensah, Head of Product

**Decision:** Deny (Current Form) - Require Structured Conditional Process

### Lifecycle Stage
Share (external data transfer to third party)

### Justification

#### 1. Major Compliance Red Flags
- **CONFIDENTIAL** student profiles involved.
- **Cross-border data transfer** (US-based company).
- **AWS database login credentials shared directly** (serious security violation).
- Ghana Data Protection Act requires:
  - Lawful basis for processing
  - Adequate safeguards for international transfers
  - Data Processing Agreement (DPA)

#### 2. Least Privilege Violation
- Providing raw AWS credentials gives **excessive access**.
- No role-based restriction.
- No separation of **INTERNAL vs CONFIDENTIAL** data.

#### 3. Security Risks
- Credential sharing is **prohibited**.
- No mention of encryption, anonymization, or audit logging.

### Controls Required If Data Must Be Shared

- **No direct database credentials.**
- **Create restricted IAM role** (read-only, scoped datasets).
- **Anonymize or pseudonymize** student data.
- **Share aggregated data** where possible.
- **Encrypted transfer** (TLS + encryption at rest).
- **Data Processing Agreement (DPA contract).**
- **Cross-border transfer assessment.**
- **Vendor security assessment.**
- **Access monitoring and logging.**

### Documentation Required

- Signed Data Processing Agreement
- Data Transfer Impact Assessment
- Vendor Security Review
- Board/Executive approval (if required)
- Updated privacy notice (if processing purpose changes)

### Action Steps

1. **Revoke any shared credentials immediately.**
2. **Escalate to Legal and DPO.**
3. Conduct vendor risk assessment.
4. Draft formal DPA.
5. Implement anonymized data pipeline if approved.

---

## REQUEST 3: ARCHIVE & DELETION

**From:** Comfort Asante, Customer Support Lead

**Decision:** Approve (With Compliance Checks)

### Lifecycle Stage
Destroy (and partial Archive, if legally required)

### Justification

#### 1. Right to Erasure (Ghana DPA)
Data subjects can request deletion when:
- Data is no longer necessary.
- No legal/contractual obligation exists.
- Students completed course 6 months ago.
- No pending payments → No contractual retention need.

#### 2. Legal Considerations
- Financial records may need retention for tax/audit purposes.
- Some minimal records may be retained for:
  - Fraud prevention
  - Legal compliance
  - Regulatory reporting

#### 3. Data Minimization
- Only legally required data may be archived.
- All other PII must be deleted.

### What Can Be Retained?

- **Transaction records** (if tax law requires retention).
- **Minimal audit logs** (hashed or pseudonymized).
- **Records required by accounting regulations.**

All retained data must:
- Be archived securely.
- Be restricted from operational use.
- Be documented in retention policy.

### Process Customer Support Should Follow

1. **Verify identity** of requester.
2. **Confirm no pending obligations.**
3. **Trigger formal deletion workflow.**
4. **Remove:**
   - Account credentials
   - Profile data
   - Learning activity logs
   - Support history
5. **Archive legally required financial records.**
6. **Confirm deletion in writing.**
7. **Log request in compliance register.**

---

## SUMMARY DECISION MATRIX

| Request | From | Decision | Risk Level | Key Action |
|---------|------|----------|-----------|-----------|
| **Marketing Campaign** | Sarah Owusu | ✅ Conditionally Approve | Medium | Verify consent, provide filtered data only |
| **Analytics Partnership** | David Mensah | ❌ Deny (Current Form) | **CRITICAL** | Revoke credentials immediately, escalate to Legal/DPO |
| **Archive & Deletion** | Comfort Asante | ✅ Approve | Low | Verify identity, delete non-essential data, archive legally required records |
