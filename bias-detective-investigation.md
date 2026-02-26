# BIAS INVESTIGATION REPORT
## TalentMatch AI – HireScore System

**To:** TalentMatch AI Leadership  
**From:** QA Engineer  
**Date:** November 2025  
**Subject:** Findings and Recommendations regarding Bias in HireScore

---

## EXECUTIVE SUMMARY

**Overall Bias Severity:** 🔴 **HIGH** 

The HireScore system exhibits strong disparities across gender, region, and university background, both inherited from historical data and amplified by the model.

### Primary Affected Groups

- **Women** (only 11% in top 100 vs. 28% in historical hires)
- **Candidates from regions outside Greater Accra/Ashanti** (9% in top 100 vs. 15% in historical hires)
- **Graduates of universities outside the top three** (6% in top 100 vs. 5% in historical hires – already underrepresented)

### Top 3 Recommended Actions

1. **Immediately remove proxy features** (location of previous employment, university attended, LinkedIn connections) from the scoring model.

2. **Collect and incorporate negative examples** (unsuccessful applications) into the training dataset to address sampling bias.

3. **Implement weekly demographic monitoring** of scores and top-ranked candidates, with a dashboard for rapid detection of disparities.

---

## DETAILED FINDINGS

### 1. Bias Type Analysis

#### 1.1 Historical Bias

**Present:** Yes

**Evidence:** The training data (10,000 historical successful hires) shows a disproportionate representation:
- 72% male, 28% female
- 85% from Greater Accra/Ashanti
- 95% from University of Ghana, KNUST, or Ashesi

These figures reflect past hiring decisions that favoured certain demographics, and the model learns these patterns as "ideal" candidates.

**Disadvantaged groups:** Women, candidates from other regions, graduates of other institutions.

---

#### 1.2 Sampling Bias

**Present:** Yes

**Evidence:** The training dataset consists only of successful hires – no records of rejected applicants. This means the model has never seen examples of unsuccessful candidates, so it cannot learn to distinguish between good and poor fits. It simply memorises the profile of past hires, which were already biased. Additionally, the applicant pool may have changed over time (e.g., more applicants from Northern Region), but the training data does not reflect that.

**Underrepresented groups:** All applicants who were not hired historically, especially those from groups that were already less likely to be hired.

---

#### 1.3 Measurement Bias

**Present:** Yes

**Evidence:** The feature "LinkedIn connections count" is likely measured differently across groups. Candidates from rural areas or less connected professional networks may have fewer LinkedIn connections not because they are less qualified, but because LinkedIn adoption is lower in those regions. This penalises them unfairly. Similarly, "Number of professional references" may be harder to obtain for first-generation professionals or those from smaller communities.

**What's being measured unfairly:** Professional network strength is being approximated by metrics that are not equally valid across regions and socioeconomic backgrounds.

---

#### 1.4 Proxy Bias

**Present:** Yes

**Evidence:** Several features correlate strongly with protected attributes, acting as proxies:

- **University attended** → proxies for socioeconomic status and region (e.g., Ashesi attracts more affluent students; UG/KNUST are concentrated in Accra/Ashanti).
- **Location of previous employment** → direct proxy for region of origin.
- **Previous company names** → may proxy for region (local vs. international companies) and for gender (certain industries are gender-skewed).
- **LinkedIn connections** → proxies for urban/rural and professional network access.

These proxies cause the model to penalise candidates simply for belonging to a certain group, even if they have equivalent skills.

---

### 2. Bias Pipeline Mapping

```
[Historical Hiring Decisions]
         ↓
[Training Data Collection] → Bias Point #1: Only successful hires included; 
                              reflects historical gender/region skew
         ↓
[Feature Selection] → Bias Point #2: Inclusion of proxy features 
                      (university, location, LinkedIn connections)
         ↓
[Model Training] → Bias Point #3: Model learns and amplifies correlations 
                   between proxies and success
         ↓
[Deployment & Scoring] → Bias Point #4: Scoring thresholds create disparate 
                         impact (top 100: 89% male, 91% Accra/Ashanti)
         ↓
[Biased Outcomes]
```

---

### 3. Feature Analysis

| Feature | Potential Bias | Risk Level |
|---------|----------------|-----------|
| Years of experience | Neutral if measured consistently | 🟢 Low |
| University attended | Proxy for socioeconomic status, region | 🔴 High |
| Previous company names | Proxy for region, industry gender skew | 🟡 Medium |
| Skills listed | Generally neutral, but may reflect unequal access to training | 🟢 Low |
| Number of professional references | Measurement bias – network disparities | 🟡 Medium |
| LinkedIn connections count | Measurement bias – differential platform adoption | 🔴 High |
| Location of previous employment | Direct proxy for region | 🔴 High |
| Extracurricular activities | May favour those with leisure time/resources | 🟡 Medium |
| Age (derived) | Could lead to age discrimination | 🟡 Medium |

---

## MITIGATION PLAN

### Immediate Actions (This Week)

| Action | Owner | Success Metric |
|--------|-------|----------------|
| Remove proxy features from the scoring model: location of previous employment, university attended, LinkedIn connections | Data Science | Updated model without these features deployed |
| Adjust scoring thresholds temporarily: manually review any candidate from underrepresented groups whose score is within 0.5 points of the top-100 cutoff, and include them in the shortlist | HR/Ops | Increased diversity of shortlists this week |
| Implement weekly monitoring of demographic breakdowns: average scores by gender/region, top-100 composition | QA/Engineering | Automated dashboard in place, first report by end of week |

---

### Short-Term Actions (Next 1–3 Months)

| Action | Owner | Success Metric |
|--------|-------|----------------|
| Collect new training data including historical unsuccessful applications (at least 5,000) and actively seek applications from underrepresented regions/universities | Data Engineering / HR | New dataset with balanced representation |
| Retrain model using techniques like reweighting or adversarial debiasing to reduce reliance on proxies; include negative examples | Data Science | Model's demographic parity improves by 20% on validation set |
| Introduce human oversight for final candidate lists: a diverse panel reviews top candidates and can flag potential bias | Product / HR | Reduction in biased outcomes flagged in weekly reviews |

---

### Long-Term Actions (Next 6–12 Months)

| Action | Owner | Success Metric |
|--------|-------|----------------|
| Adopt a fairness definition – recommend Equal Opportunity (ensuring qualified candidates from all groups have similar true positive rates). This balances merit and fairness without forcing strict demographic quotas. | Leadership / Ethics Board | Formal policy documented; model optimised for equal opportunity |
| Change client use guidelines: encourage clients to use HireScore as a screening tool only, not as the sole decision-maker. Provide a "diversity dashboard" showing the demographic composition of the ranked list. | Product / Sales | Client agreements updated; dashboard usage tracked |
| Increase transparency: publish an annual bias audit report, disclose features used, and allow applicants to request explanation of their score. | Legal / Comms | Public report released; complaint mechanism in place |

---

## CONCLUSION

HireScore currently exhibits **high bias** due to:
- Historical patterns in hiring data
- Sampling issues (only successful hires)
- Proxy features that amplify disparities

The proposed actions are **practical, role-appropriate, and phased** to address the most critical issues immediately while building a fairer system over the next year. Regular monitoring and a clear fairness definition will ensure long-term accountability.
