# Automated-Third-Party-Access-Control-Risk-Mitigation-Project-AWS-Focused-
Inadequate Third Party Vendor access controls and automated least-privilege access using AWS services


**Project Goal:** Reduce the inherent risk score of TPV data exposure from High (15) to Low (4).

Access Control is the set of mechanisms (like usernames, passwords, MFA, and defined permissions) that determines:

> Authentication: Who the TPV user is (verifying identity).

> uthorization: What resources (data, applications, servers) that user is allowed to access and what actions they can perform (read, write, delete).

Project Phases: 

1_Risk_Assessment/ (The "What If") What are we protecting? Who or what is threatening it? and How bad is the risk right now?

Risk ID,Risk Scenario,Likelihood (L) (1-5),Impact (I) (1-5),Inherent Risk Score (L x I),Recommended Response
R-TPV-001,TPV credential compromise leads to customer data exfiltration.,3 (Possible),5 (Catastrophic),15 (High),Mitigate (Reduce L and I)
R-TPV-002,Lack of automated deprovisioning leaves old TPV accounts active.,4 (Likely),3 (Serious),12 (High),Mitigate (Reduce L)
R-TPV-003,TPV introduces vulnerable code into the production environment.,2 (Unlikely),4 (Major),8 (Medium),Mitigate (Improve Code Review)

## 📂 1_Risk_Assessment/

This directory contains the documentation for the initial phase of the project: identifying the scope, mapping threats, and quantifying the inherent risk of Third-Party Vendor (TPV) access before any mitigation controls are implemented.

| File Name | Purpose | Example Content for TPV Access Control |
| :--- | :--- | :--- |
| **`1.1_Scope_and_Assets.md`** | Defines the boundaries of the assessment and the key assets being protected. | **Scope:** The assessment is limited to TPV access to the FinTech Global Customer Transaction Data Platform hosted on AWS. **Critical Assets:** 1. Anonymized Customer Transaction Data (Stored in S3). 2. API Endpoints used by the TPV's fraud detection service. 3. AWS IAM Users/Roles used by the TPV. **Boundary:** Only TPV activity; internal employee access is out of scope. |
| **`1.2_Threat_Model.md`** | Documents the potential threats and vulnerabilities specific to the scope. | **Vulnerability:** TPV utilizes long-lived access keys and has wide-ranging permissions on the S3 bucket (Read/Write). **Threat:** TPV staff account is compromised via phishing, leading to unauthorized data exfiltration or modification. **Impact Area:** Confidentiality and Integrity of customer data. |
| **`1.3_Inherent_Risk_Analysis.md`** | The quantitative or qualitative analysis of the risk **before** any new controls are applied (**Inherent Risk**). | See the table below for the calculation of the top risk (R-TPV-001). This score is the baseline for mitigation efforts. |

---

### Excerpt from `1.3_Inherent_Risk_Analysis.md` (Top Risk Calculation) 

| Risk ID | Risk Scenario | Likelihood (L) (1-5) | Impact (I) (1-5) | Inherent Risk Score (L x I) | Recommended Response |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **R-TPV-001** | TPV credential compromise leads to customer data exfiltration. | 3 (Possible) | 5 (Catastrophic) | **15 (High)** | Mitigate |

1.1_Scope_and_Assets.md: Define the boundaries and sensitive assets.

1.2_Threat_Model: Diagram the flow of data and where the threats exist.

1.3_Inherent_Risk_Analysis: The spreadsheet showing the initial calculation of likelihood, impact, and inherent risk scores before mitigation.


2_Mitigation_Strategy/ (The "How To Fix It") security controls.

2.1_Control_Selection: Document your decision to Mitigate the risk. List the specific NIST SP 800-53 or CIS Control you selected and why.

2.2_Mitigation_Plan.md: The project plan. This is the most valuable part for an AWS Risk Specialist.

## 📂 2_Mitigation_Strategy/ Folder Structure

This directory outlines the strategic plan for addressing the critical risks identified in the assessment phase, focusing on control selection and detailed implementation planning.

| File Name | Purpose | Example Content for TPV Access Control |
| :--- | :--- | :--- |
| **`2.1_Control_Selection.md`** | Documents the selected risk response (Mitigation) and the specific security frameworks or controls chosen to address the high-risk scenarios. | **Risk Response:** Mitigation was chosen for R-TPV-001 (High Risk) to reduce both likelihood and impact. **Framework Alignment:** Controls align with **NIST SP 800-53 (AC-3: Access Enforcement)** and **AWS Well-Architected Framework (Security Pillar)**. **Control Objective:** Implement Least-Privilege Access and Mandate Strong Authentication for TPVs. |
| **`2.2_Mitigation_Plan.md`** | A detailed, step-by-step plan for implementing the new security controls, including technology used and responsible teams. | **Phase 1: Privilege Reduction (Reducing Impact):** 1. Create new, non-human **IAM Roles** for the TPV service instead of human IAM Users. 2. Restrict the S3 Bucket Policy to only allow `s3:GetObject` (Read-Only) permissions, eliminating the unnecessary `s3:PutObject` (Write) access. **Phase 2: Authentication Hardening (Reducing Likelihood):** 1. Implement a **Just-in-Time (JIT) Access** solution using AWS tools (e.g., AWS IAM Identity Center) to grant temporary TPV access for maintenance only. 2. Enforce MFA using Conditional Access Policies for all TPV human users. |

---

### Key Mitigation Control Examples (R-TPV-001)

The implementation of the following controls is expected to reduce the likelihood (L) from 3 to 1 and the impact (I) from 5 to 2.

| Control Type | Control Description | Risk Factor Addressed | Target L/I Reduction |
| :--- | :--- | :--- | :--- |
| **Preventative** | Enforce **Principle of Least Privilege** by restricting TPV S3 access to `s3:GetObject` (Read-Only) only. | Impact (I) | Reduced from 5 to **2** |
| **Preventative** | Enforce **MFA** for all TPV accounts and utilize **IAM Roles** with session tags for service-to-service communication. | Likelihood (L) | Reduced from 3 to **1** |

**Expected Residual Risk Score:** $1 \times 2 = \mathbf{2 (Low)}$


Example Control: Mitigate Access Risk by implementing a Least-Privilege IAM Policy and enforcing MFA for all TPV accounts.

Example Tool: Use AWS Access Analyzer to review and fine-tune permissions.


3_Implementation_Documentation/ (The "Proof")
Show you can execute the plan.

3.1_New_IAM_Policy_Code.json: Actual code for a restrictive IAM policy or a Terraform/CloudFormation script to deploy the control.

3.2_Training_Materials.pdf: A sample of the security training you would require the TPV to complete.

## 📂 3_Implementation_Documentation/ Folder Structure

This phase documents the deployment of the selected controls, providing evidence of the **Preventative** and **Detective** measures now in place to protect the customer data platform.

| File Name | Purpose | Example Content for TPV Access Control |
| :--- | :--- | :--- |
| **`3.1_Restrictive_IAM_Policy.json`** | The AWS IAM policy code used to enforce the Principle of Least Privilege (the core preventative control). | See JSON code block below. This policy grants only read access to the specific S3 bucket and denies all other S3 actions, reducing the **Impact** of a credential compromise. |
| **`3.2_MFA_Conditional_Access_Logic.md`** | Documentation of the logic used to enforce strong authentication (MFA). | **Logic:** A Conditional Access Policy was created within AWS Identity Center (or using an IAM Policy with a `Condition` key) to explicitly deny access to any TPV IAM Role that does not have a present and valid MFA token. This reduces the **Likelihood** of an unauthorized login. |
| **`3.3_Training_and_Change_Log.md`** | Record of communication, training, and deployment steps. | **Change Log:** Deployed new IAM Role `TPV-FraudDetection-ReadOnly` and attached `3.1_Restrictive_IAM_Policy.json` on [Date]. Old IAM role deprovisioned. **Training:** TPV leadership was required to complete a security briefing focusing on MFA use and data handling policies prior to receiving new credentials. |

---

### Excerpt from `3.1_Restrictive_IAM_Policy.json` (See it at the end) 


##  4_Monitoring_and_Reporting/ Folder Structure

This final phase focuses on measuring the effectiveness of the deployed controls and establishing a process for continuous oversight, confirming the achieved **Residual Risk Score**.

| File Name | Purpose | Example Content for TPV Access Control |
| :--- | :--- | :--- |
| **`4.1_Residual_Risk_Analysis.md`** | The final analysis showing the risk score *after* controls have been implemented. | **R-TPV-001 Residual Risk:** The restrictive IAM policy (reduced Impact) and MFA enforcement (reduced Likelihood) lowered the risk. **New Score:** Likelihood (L) 1 x Impact (I) 2 = **2 (Low)**. This is below the organizational Risk Tolerance Threshold (typically 8). |
| **`4.2_KRIs_and_KPIs.md`** | Defines the Key Risk Indicators (KRIs) and Key Performance Indicators (KPIs) for ongoing monitoring. | **KPIs (Control Effectiveness):** 1. Percentage of TPV IAM Roles utilizing Least Privilege (Target: 100%). 2. Average time-to-deprovision TPV access upon contract end (Target: <24 hours). **KRIs (Risk Status):** 1. Number of failed TPV login attempts (Threshold: 5 per week). 2. Instances of TPV attempts to access non-authorized S3 buckets (Threshold: 0). |
| **`4.3_Audit_and_Review_Schedule.md`** | Defines the frequency of formal control review. | **IAM Policy Audit:** Automated monthly review via AWS Config/Access Analyzer. **Full Vendor Risk Assessment:** Performed annually, or immediately following any significant security incident on either side. |

---

### Final Residual Risk Calculation Table

This table summarizes the project's success:

| Risk ID | Inherent Risk (L x I) | Mitigation Control(s) | Residual Risk (L x I) | Status |
| :--- | :---: | :--- | :---: | :--- |
| **R-TPV-001** | **15 (High)** | Least Privilege IAM Policy & MFA Enforcement | **2 (Low)** | Accepted/Managed |
| R-TPV-002 | 12 (High) | Automated De-provisioning | 3 (Low) | Accepted/Managed |



This policy implements the "Read-Only" control for the TPV access:

````json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadAccessToSpecificBucket",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::fintech-customer-data-anonymized",
        "arn:aws:s3:::fintech-customer-data-anonymized/*"
      ]
    },
    {
      "Sid": "DenyAllOtherS3Actions",
      "Effect": "Deny",
      "NotAction": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalArn": "arn:aws:iam::[*private info*]:role/TPV-FraudDetection-ReadOnly"
        }
      }
    }
  ]
}



