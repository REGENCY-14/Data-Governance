# QUICKLOAN MOBILE ETHICAL DATA GOVERNANCE REVIEW

**Role:** Data Governance Consultant  
**Company:** QuickLoan Mobile  
**Project:** Ethical Data Governance Review

---

## DELIVERABLE 1: RISK IDENTIFICATION AND ANALYSIS

| Section | Issue/Definition | Impact | Suggested Fix/Mitigation |
|---------|------------------|--------|--------------------------|
| **1. Data Quality Risk** | Incomplete or Inconsistent Customer Data: The preprocessing service (Step 4) receives raw data with missing fields (e.g., no "Salary" field) or inconsistent formats (e.g., dates as DD/MM/YYYY and YYYY-MM-DD). | The ML model (Step 6) may make inaccurate or biased decisions based on poor inputs, or fail entirely, leading to financial loss and customer dissatisfaction. | Implement Data Validation Rules: At the point of entry (Step 2), enforce mandatory fields and format checks. During preprocessing (Step 4), implement robust data cleansing: reject, flag, or impute missing values with clear audit trails. |
| **2. Legal & Compliance Risk** | No Explicit Consent for Data Processing (Violation of Ghana's Act 843): The system collects and processes personal data (Step 1) without obtaining explicit, informed consent from the user. This is a breach of the data protection principle of "lawfulness and fairness." | Data Classification: Sensitive (Collection of contact lists and financial data without consent is a high-risk activity). Impact: Hefty fines from the Data Protection Commission, legal action from users, and severe reputational damage. | Implement a Granular Consent Management Layer: Before data transmission (between Step 1 & 2), present a clear consent form. Separate mandatory consent (for loan processing data) from optional consent (for contact list collection). Log the consent receipt. |
| **3. Bias & Fairness Risk** | Source of Bias: Proxy Discrimination via Contact List Data. The ML model (Step 6) uses the user's entire contact list as a feature. This data can act as a proxy for socioeconomic status, social network, or even ethnicity (e.g., based on naming conventions or area codes of contacts), leading to unfair denials for certain demographic groups. | The model may unfairly penalize users from less-connected or lower-income communities, even if they are creditworthy. This creates a biased feedback loop, excluding entire groups from financial services. | Monitor Model Performance Across Segments: Reject the use of contact lists entirely (Data Minimization). If used, track the model's approval rates and predictive accuracy (false positive/negative rates) across different user demographics (e.g., by location, if available) to detect and measure disparate impact. |

### 4. Storytelling / Reporting Recommendation

**Metric to Monitor:** Approval Rate Parity

**Definition:** The ratio of loan approval rates for a protected group (e.g., users from a specific region or income bracket) to the approval rate for the highest-approved group. A ratio of 1.0 indicates perfect parity.

**Visualization Type:** Grouped Bar Chart
- **X-Axis:** User Demographics (e.g., Region A, Region B, Region C)
- **Bars:** Two bars per group - one for "Approval Rate" and one for "Average Risk Score"

**Why It Matters:** This metric translates complex model fairness into a simple, visual tool that holds the business accountable for ensuring their automated decisions are not systematically disadvantaging any particular group of customers.

---

## DELIVERABLE 2: CORRECTED DATA FLOW DIAGRAM (STRUCTURED CORRECTIONS)

### Correction 1 – Between Step 1 & Step 2: User Mobile App to API Gateway

**Implemented:** Data Minimization & Consent Capture

**Was Wrong:**  
The app collected excessive personal data (including the user's entire contact list) and transmitted it without obtaining explicit, informed consent from the user.

**Correction:**
- Remove automated collection of the "Entire Contact List" as it is excessive and irrelevant to loan scoring
- Insert a clear, granular consent screen before data transmission that:
  - Lists all data collected (Name, Phone, GPS location)
  - States the purpose of each data point (e.g., "GPS location is used for fraud detection and identity verification")
  - Separates mandatory consent (for loan processing) from optional consent (for any non-essential data)
  - Requires explicit user action ("I Agree" button) before submission

**It Is Necessary:**  
This aligns with the data minimization principle and ensures compliance with Ghana's Data Protection Act (Act 843), which requires lawful, fair, and transparent processing of personal data with informed consent.

---

### Correction 2 – Between Step 6 & Step 7: Decision Service to User Mobile App

**Implemented:** Transparency & Audit Logging

**Was Wrong:**  
The ML model made automated loan decisions with no logging or audit trail, making it impossible to investigate complaints, identify bias, or explain decisions to regulators or customers.

**Correction:**
- Modify the Decision Service to generate a unique Decision ID for every application
- Log the following for each decision:
  - Input features used (e.g., income, age, location)
  - Model version number
  - Final risk score
  - Approval/decline outcome
  - Timestamp of decision
- Store this log in a secure, access-controlled audit repository

**It Is Necessary:**  
Under Act 843, data subjects have the right to understand decisions made about them. This audit trail enables explainability, supports dispute resolution, and provides evidence of fair lending practices to regulators.

---

### Correction 3 – Between Step 8 & Step 10 / Step 9: Anonymization Service to Analytics DB & 3rd-Party Partner

**Implemented:** Data Masking & Controlled Sharing

**Was Wrong:**  
Sensitive personal data was being stored in the Analytics DB for reporting and shared with a third-party partner without any anonymization, masking, or clear contractual limits, creating a high-risk data breach exposure.

**Correction:**
- Insert an Anonymization Service before data enters the Analytics DB that:
  - Strips or masks all direct identifiers (name, phone number, GPS coordinates)
  - Replaces identifiable data with a pseudonymous, non-reversible token
- For the 3rd Party data flow:
  - Limit shared data strictly to the calculated risk score and final decision outcome
  - Add a contractual clause prohibiting re-identification or further sharing
  - Never transmit raw PII to external parties

**It Is Necessary:**  
This enforces data protection by design. It ensures that even if the Analytics DB or partner systems are compromised, no individual can be re-identified, complying with Act 843's security requirements and reducing breach impact.

---

## DELIVERABLE 3: SUMMARY OF REVIEW PROCESS

My review process for QuickLoan Mobile was structured around the core principles of the data lifecycle and data classification. I started by tracing the user's data from the point of collection (the mobile app) to its final destinations (the analytics DB and third-party partner). By mapping this lifecycle, the risks became clear.

First, applying data classification, I identified that the data being collected (name, phone, GPS, contacts) falls under the Sensitive category under Ghana's Act 843. This classification immediately flagged the lack of explicit consent as a critical compliance violation. Using the lifecycle view, I could see that this sensitive data flowed unchecked through the entire system, ending up in an analytics DB and being shared with a partner without any masking, which is a direct path to a data breach.

Second, I applied the principle of data minimization at the collection stage. The inclusion of the user's entire contact list was a major red flag, not only for privacy but also as the primary source of potential algorithmic bias. By understanding how data becomes a feature in the ML model, I could pinpoint that this proxy data would likely lead to unfair outcomes. The lifecycle view helped me realize that if we don't fix the input (the contact list), we can't fix the output (the biased decision).

This directly informs my proposed metric, Approval Rate Parity. This metric acts as the "voice" of the ethical governance process. It transforms the abstract risk of bias into a concrete, measurable outcome that can be tracked on a dashboard (e.g., a grouped bar chart). By monitoring approval rates across different user demographics, we are not just hoping the model is fair; we are actively verifying it. This creates a feedback loop: if the metric shows a parity issue, it triggers an investigation that goes back to the data quality and feature selection stages of the lifecycle. It ensures that governance isn't a one-time audit but an ongoing, transparent process that keeps the business accountable for the ethical performance of its automated systems.
