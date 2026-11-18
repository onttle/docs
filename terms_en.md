# Terms of Service

**Effective Date: October 29, 2026**

These Terms of Service ("Terms") govern the use of the content infrastructure platform Onttle ("Service"), including its dashboard, REST API, AI/RAG features, file storage, and related services, provided by Pied Piper ("Company", "we", "us"). By creating an account or otherwise using the Service, you ("User") agree to be bound by these Terms.

---

## 1. General Provisions

### Article 1 (Purpose)

These Terms set out the rights, obligations, and responsibilities of the Company and Users, and other necessary matters, in connection with the use of the Service, which provides schema management, an auto-generated CMS, a REST API, AI/RAG features, file storage, and subscription-based billing.

### Article 2 (Definitions)

| Term                  | Definition                                                                                                                                         |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Service**           | The Onttle platform, including the dashboard, REST API, SDKs, and all related features.                                                            |
| **User**              | An individual or entity that has agreed to these Terms and uses the Service, whether through the dashboard or the API.                             |
| **Dashboard User**    | A User who creates and manages Workspaces, Projects, Tables, and Content through the web dashboard.                                                |
| **External API User** | An application or system that accesses the Service using an API Key on behalf of a Dashboard User.                                                 |
| **Workspace**         | The top-level unit for managing teams, members, and billing.                                                                                       |
| **Project**           | A unit within a Workspace used to separate data by service or domain.                                                                              |
| **Table**             | A content schema defined with typed Columns within a Project.                                                                                      |
| **Content**           | Data entered and managed according to a Table schema, including Content in `DRAFT`, `PUBLISHED`, and `ARCHIVED` status.                            |
| **API Key**           | A credential issued within the scope of a Project that authorizes read or write access to the API.                                                 |
| **Plan**              | A Service offering with a defined monthly fee, monthly Credits, limits, and features, including free and paid plans.                               |
| **Subscription**      | An agreement under which a Workspace uses a Plan on a monthly basis, renewing automatically unless cancelled.                                      |
| **Billing Cycle**     | The one-month period from a billing date to just before the next, used for charging fees and granting monthly Credits.                             |
| **Credit**            | A unit used to deduct Service usage within a Billing Cycle, including monthly Credits granted by the Plan and add-on Credits purchased separately. |
| **Metric**            | A quantified measurement of Service usage (e.g., storage, API calls, AI tokens) used to calculate credit deductions.                               |

### Article 3 (Effect and Amendment of Terms)

1. These Terms take effect upon being posted on the Service or otherwise notified to Users.
2. The Company may amend these Terms where necessary in accordance with applicable law. Amended Terms will be posted on the Service, together with the effective date, at least seven (7) days before taking effect (or thirty (30) days in the case of amendments unfavorable to Users).
3. If a User does not agree to amended Terms, the User may stop using the Service and terminate the account before the effective date. Continued use of the Service after the effective date constitutes acceptance of the amended Terms.

---

## 2. Use of the Service

### Article 4 (Registration and Accounts)

1. Users must register an account with accurate and current information to use the dashboard.
2. Users are responsible for the accuracy of information provided at registration and for promptly updating it upon any change.
3. The Company may decline registration or restrict use of the Service where an applicant has previously had an account terminated for violation of these Terms, has provided false information, or where registration would violate applicable law.

### Article 5 (Workspaces and Projects)

1. A Workspace is the unit through which teams, members, roles, and billing are managed, and every Project belongs to exactly one Workspace.
2. The User who creates a Workspace is designated as its Owner and may invite other members and assign them roles (e.g., Owner, Admin, Editor, Guest) with corresponding permissions.
3. Workspace Owners and Admins are responsible for managing member access and permissions, including promptly removing members who should no longer have access.
4. Data, usage, subscription Plans, and Credit balances are managed at the Workspace level; a Workspace may contain multiple Projects that share the same Plan and Credit balance.

### Article 6 (Scope of the Service)

The Service includes, without limitation, the following features:

- Table and Content management with typed Columns and an auto-generated CMS
- REST API access to published Content
- Translation resource management
- File storage, including image optimization and OCR extraction
- Environment variables and feature flags
- Webhooks for Content lifecycle events
- AI features, including Auto-fill, column suggestions, and AI Ask
- Vector-based semantic search (RAG)

The Company may add, change, or discontinue individual features from time to time in accordance with Article 7.

### Article 7 (Modification and Suspension of the Service)

1. The Company may modify all or part of the Service for operational or technical reasons, such as maintenance, replacement, or upgrade of systems.
2. The Company may temporarily suspend the Service in the event of maintenance, inspection, replacement of equipment or systems, a failure of communication, or other circumstances of force majeure. The Company will provide advance notice where practicable, except in cases of emergency or matters beyond its control.
3. The Company may discontinue all or part of the Service where it is no longer able to continue providing the Service for business or technical reasons, and will provide reasonable advance notice to Users in such cases.

---

## 3. User Obligations

### Article 8 (Account Management)

1. Users are responsible for maintaining the confidentiality of their account credentials and API Keys and must not permit unauthorized third parties to use them.
2. Users must notify the Company promptly if they become aware of any unauthorized use of their account or API Key, or any other security breach.
3. The Company is not liable for losses arising from a User's failure to manage their account or API Key with reasonable care, except where the Company is at fault.

### Article 9 (Prohibited Conduct)

Users must not engage in any of the following:

1. Registering false information when creating or maintaining an account;
2. Using the account, Workspace, or API Key of another person or entity without authorization;
3. Interfering with the normal operation of the Service, including through excessive requests, automated abuse, or circumvention of Rate Limits;
4. Attempting to access the Service's source code, infrastructure, or other Users' data without authorization;
5. Reverse engineering, decompiling, or disassembling the Service except as permitted by applicable law;
6. Using the Service to develop a competing product or service by improperly extracting its structure, features, or source code;
7. Uploading Content that infringes the intellectual property, privacy, or other rights of any third party; and
8. Any other act that violates applicable law or these Terms.

### Article 10 (Illegal Content)

1. Users must not register, upload, or distribute Content through the Service that is illegal, defamatory, obscene, or otherwise unlawful under applicable law.
2. The Company may remove or restrict access to Content that violates paragraph 1, and may suspend or terminate the responsible User's account, without prior notice where circumstances require immediate action.
3. Users are solely responsible for any liability arising from illegal Content they register, and the Company bears no responsibility except where caused by its own intentional or negligent conduct.

---

## 4. Content and Data

### Article 11 (Rights to User Content)

1. Ownership of Content that a User registers through Tables remains with the User or the rightful owner of that Content. The Company does not acquire ownership of User Content.
2. By registering Content, the User grants the Company a limited license to store, process, transmit, and display the Content solely to the extent necessary to provide, maintain, and improve the Service (e.g., serving Content via the REST API, generating CMS screens, or file processing).
3. This license terminates upon deletion of the Content or termination of the account, except where retention is required by law or necessary for backups, until such backups are also deleted in the ordinary course.

### Article 12 (Data Processing)

1. The Company processes Content and related data in accordance with the User's Table and Column configuration and these Terms.
2. The Company applies reasonable technical and administrative safeguards to protect User data against unauthorized access, loss, or alteration, commensurate with generally accepted industry practices.
3. Where the Company engages third-party subprocessors (e.g., cloud infrastructure, storage, or AI model providers) to perform part of the Service, the Company remains responsible for ensuring those subprocessors provide an adequate level of protection for User data.

### Article 13 (Responsibility for Content)

1. Users are solely responsible for the accuracy, legality, and appropriateness of Content they register, and for having all rights necessary to register and distribute that Content through the Service.
2. The Company does not review Content in advance and is not obligated to monitor Content, except as required by law or as reasonably necessary to operate the Service.
3. Users are responsible for any disputes arising between Users or between a User and a third party in connection with Content, and must indemnify the Company for any loss arising from such disputes to the extent caused by the User's Content or conduct.

### Article 14 (Use of Data for Provision of the Service)

1. The Company may use Content and usage data to the extent necessary to provide, secure, and improve the Service, including detecting abuse, preventing security incidents, and calculating usage Metrics.
2. The Company may generate and use de-identified or aggregated statistical data derived from Service usage for service planning, quality improvement, and similar internal purposes, provided that such data does not identify any individual User or Content.
3. Except as described in this Article or required by law, the Company does not use User Content for purposes unrelated to providing the Service without the User's consent.

---

## 5. API and Developer Services

### Article 15 (Use of the API)

1. Users may access published Content and other permitted resources through the REST API using an API Key issued within a Project.
2. The scope of API access (read or write) is determined by the permissions granted to the API Key at the time of issuance.
3. The Company may change API specifications, including request formats, response formats, and versioning, and will provide reasonable notice of changes that are not backward compatible.

### Article 16 (API Key Management)

1. API Keys are confidential credentials, and Users are solely responsible for their secure storage and use.
2. Users must immediately revoke and reissue any API Key that is suspected of being exposed or compromised.
3. The Company is not liable for any damages arising from a User's failure to manage an API Key with reasonable care, except where the Company is at fault.
4. The Company may invalidate an API Key without prior notice where it reasonably suspects the Key is being used for unauthorized or abusive purposes.

### Article 17 (Rate Limits and Usage Limits)

1. The Company may apply rate limits, quota limits, and other usage restrictions to the API and other features of the Service to ensure stable operation of the Service for all Users.
2. Applicable limits may vary depending on the User's plan, and current limits are described on the relevant documentation or pricing pages.
3. The Company may reject or delay requests that exceed applicable limits, and is not liable for any resulting inconvenience except where caused by the Company's fault.

### Article 18 (Prohibition of API Abuse)

Users must not use the API to:

1. Send an excessive volume of requests disproportionate to normal use in a manner that impairs the Service for other Users;
2. Circumvent Rate Limits or usage restrictions through the use of multiple accounts, API Keys, or automated tooling;
3. Collect data through the API for purposes other than those disclosed at the time the Content was intended to be used; or
4. Otherwise use the API in a manner that violates these Terms or applicable law.

The Company may restrict, suspend, or revoke API access without prior notice where a User's use of the API falls under any of the foregoing.

---

## 6. AI and RAG Services

### Article 19 (Nature of AI-Generated Output)

1. AI features of the Service, including Auto-fill, column suggestions, AI Ask, and vector-based semantic search, generate output using machine learning models and may produce results that are probabilistic, incomplete, or context-dependent.
2. AI-generated output is provided as a reference or productivity aid and does not constitute professional, legal, financial, or other expert advice.

### Article 20 (No Guarantee of Accuracy)

1. The Company does not warrant that AI-generated output is accurate, complete, reliable, or fit for any particular purpose.
2. Users are responsible for independently reviewing and verifying AI-generated output before relying on it or incorporating it into published Content or downstream systems.
3. The Company may improve, retrain, or change the underlying AI models at any time, which may change the characteristics of generated output.

### Article 21 (Processing of User Data for AI Features)

1. Where a User uses AI or vector search features, the relevant Content and queries may be processed by the Company or its AI model subprocessors solely to generate the requested output.
2. The Company does not use User Content to train models shared with other Users or third parties, except with the User's explicit consent.
3. Usage of AI features is measured in Metrics (e.g., AI tokens) and is subject to the billing terms in Section 7.

### Article 22 (Responsibility for AI Output)

1. Users are solely responsible for any decisions made or actions taken based on AI-generated output, and for any Content published using such output.
2. The Company is not liable for damages arising from a User's reliance on AI-generated output, except where such damages result from the Company's intentional misconduct or gross negligence.

---

## 7. Fees and Payment

### Article 23 (Plans and Subscriptions)

1. The Service is offered in multiple plans, including a free plan with limited features or usage and paid plans with expanded features, limits, and support. Paid plans are provided as monthly subscriptions on a per-Workspace basis.
2. The monthly fee, monthly Credits, limits, and features of each plan are specified on the Service's pricing page and may be changed subject to prior notice under Article 3. Fee changes apply from the first Billing Cycle that begins after the effective date of the change.
3. The User selects a plan when creating a Workspace. If a paid plan is selected, the User must register a payment method and the fee for the first Billing Cycle is charged immediately; the free plan may be used without registering a payment method.
4. For plans provided under a separate agreement (e.g., Enterprise), the fees, limits, and payment terms are governed by that agreement, which prevails over these Terms in the event of a conflict.

### Article 24 (Billing Cycle and Automatic Payment)

1. Subscription fees for paid plans are charged in advance for each Billing Cycle (one month) and renew automatically on each billing date unless the User cancels the subscription.
2. The billing date is based on the date the subscription started; where a subscription starts on the 29th, 30th, or 31st, the billing date is the 28th of each month. Details such as the time of charge are specified in the Service's documentation.
3. On each billing date, the Company automatically charges the subscription fee to the User's default payment method and, upon completion, notifies the User by email or otherwise of the amount charged, the billing period, and the Credits granted.
4. The User may register multiple payment methods per Workspace and designate one as the default. Payments are processed through a payment service provider designated by the Company, and the Company does not itself store card numbers or other payment method details.
5. Prorated charges for plan upgrades and purchases of add-on Credits are charged to the default payment method immediately.
6. The User may change or remove registered payment methods. However, a valid default payment method must be maintained while subscribed to a paid plan, and changes to payment methods do not affect fees already incurred.

### Article 25 (Credits)

1. Credits are a unit used to measure and deduct Service usage within a Billing Cycle and do not in themselves constitute cash or a refundable monetary value.
2. **Monthly Credits**: At the start of each Billing Cycle, the monthly Credits set for the plan are granted to the Workspace. When a new Billing Cycle begins, the Workspace's Credit balance is reset to the plan's monthly Credits, and any Credits remaining from the previous cycle (including add-on Credits) do not roll over and expire.
3. **Add-on Credits**: Where Credits run short within a Billing Cycle, the User may purchase add-on Credits. Add-on Credits are added to the balance immediately upon purchase and are valid only within the Billing Cycle in which they were purchased. The conversion rate between Credits and the amount paid, and the per-purchase limit, are specified in the Service's documentation.
4. Service usage is aggregated as Metrics such as data storage, API calls, file processing, and AI tokens, and Credits are deducted according to the unit price of each Metric. Metric definitions and unit prices are specified on the Service's pricing and documentation pages, and requests that consume Credits check the balance before execution.
5. Credit aggregation and deduction are managed at the Workspace level, and all Projects within a Workspace share the same Credit balance.
6. For plans without a Credit limit, usage is measured but Credits are not deducted, and add-on Credits cannot be purchased.

### Article 26 (Plan Changes and Cancellation)

1. **Upgrades** take effect immediately. The Company charges the prorated fee difference for the remainder of the current Billing Cycle to the default payment method and immediately grants the proportionate difference in monthly Credits.
2. **Downgrades** take effect from the next billing date; until then, the User continues to use the current plan and Credits. No fee difference is refunded for a downgrade.
3. The User may **cancel** the subscription at any time from the Workspace billing settings. Cancellation takes effect at the end of the current Billing Cycle, after which the Workspace is converted to the free plan. Fees paid up to the time of cancellation are not refunded except as provided in Article 28.
4. A scheduled downgrade or cancellation may be revoked before it takes effect, in which case the current plan renews as usual.
5. When a Workspace is converted to the free plan, some features, such as creating or modifying resources exceeding the free plan's limits, may be restricted, but the User's data is not deleted.
6. Plans cannot be changed while a recurring payment is past due.

### Article 27 (Overage and Payment Failure)

1. Where a Workspace's Credit balance within a Billing Cycle is insufficient for a request, the request is not processed and the Company notifies the User. The Company does not automatically charge for overage or automatically replenish Credits without the User's separate consent.
2. The User may resume use of the Service by waiting for the next Billing Cycle, purchasing add-on Credits, or upgrading the plan. The User's data and settings are retained even when requests are not processed due to insufficient Credits.
3. Requests exceeding plan limits (such as the number of members, Projects, or Tables, storage capacity, or file size) may be rejected, and the User may resolve this by reducing resources or upgrading the plan.
4. If a recurring payment fails, the subscription becomes past due, and the Company notifies the User and retries the payment according to the schedule set out in the Service's documentation. If all retries fail, the Workspace is converted to the free plan and the Company notifies the User.
5. If the User updates the payment method while past due and a retry succeeds, a new Billing Cycle begins and monthly Credits are granted.

### Article 28 (Refunds)

1. Refunds of subscription fees and add-on Credit purchases are handled in accordance with applicable law, including the Act on the Consumer Protection in Electronic Commerce, and this Article.
2. Because subscription fees are charged in advance for each Billing Cycle, fees for the remaining period are generally not refunded where the User cancels or downgrades during a Billing Cycle; the User may continue to use the current plan until the end of the Billing Cycle.
3. Credits already consumed, Credits that expired at the end of a Billing Cycle (including monthly and add-on Credits), and fees for services already rendered are non-refundable.
4. The Company refunds the relevant amount in the following cases:
   - Where the Service was not properly provided due to the Company's fault
   - Where a payment was made contrary to the User's intent, such as a payment error or duplicate charge
   - Where the User requests a refund within 7 days of the payment date without having used any Credits of the relevant Billing Cycle
   - Where a refund is required by applicable law
5. Refunds are, in principle, made to the original payment method, and Credits corresponding to the refunded amount are revoked. Depending on the policies of the payment service provider and card issuer, it may take some time for a refund to be reflected.
6. Refund requests may be submitted through the Service's customer support channel (support@onttle.com) and are processed within a reasonable period after verification.

---

## 8. Service Restriction and Termination

### Article 29 (Restriction of Use)

1. The Company may restrict a User's use of all or part of the Service where the User violates these Terms, engages in Prohibited Conduct, or where such restriction is necessary to protect the Service or other Users.
2. Where practicable, the Company will notify the User of the reason for and scope of any restriction in advance or without undue delay thereafter.

### Article 30 (Suspension of Accounts)

1. The Company may suspend a User's account where there are reasonable grounds to believe the User has violated these Terms, engaged in illegal activity, or created a risk to the security or stability of the Service.
2. The Company will provide the User an opportunity to explain or remedy the circumstances, except where immediate suspension is necessary to prevent harm or is required by law.
3. Suspension does not relieve the User of any accrued payment obligations under Section 7.

### Article 31 (Termination of Contract)

1. A User may terminate their account and these Terms at any time through the account settings or by contacting the Company's support channel.
2. The Company may terminate these Terms with respect to a User where the User materially breaches these Terms and fails to cure the breach within a reasonable period after notice, or where the User's account has been suspended for an extended period without resolution.
3. Upon termination, the User's right to access the Service ends, and the Company may delete the User's Content and account data after any period required by law or necessary for backups, in accordance with Article 11.

---

## 9. Disclaimer and Limitation of Liability

### Article 32 (Service Interruption)

1. The Company is not liable for damages arising from a temporary suspension or interruption of the Service due to maintenance, system failure, communication failure, or other circumstances beyond the Company's reasonable control, except where caused by the Company's intentional misconduct or gross negligence.
2. The Company will use reasonable efforts to restore the Service promptly and, where practicable, to notify Users of significant interruptions.

### Article 33 (Data Loss)

1. Users are responsible for maintaining independent backups of critical Content and data, in addition to any backups maintained by the Company.
2. The Company is not liable for loss of Content or data resulting from a User's own actions, use of third-party integrations, or circumstances beyond the Company's reasonable control, except where caused by the Company's intentional misconduct or gross negligence.

### Article 34 (Third-Party Services)

1. The Service may integrate with or rely on third-party services, including cloud infrastructure, payment processors, and AI model providers.
2. The Company is not responsible for the availability, accuracy, or conduct of third-party services, except to the extent the Company is at fault in its selection or oversight of such third parties.

### Article 35 (AI Output)

As set out in Article 22, the Company is not liable for damages arising from a User's reliance on AI-generated output, except where such damages result from the Company's intentional misconduct or gross negligence.

### Article 36 (General Limitation of Liability)

1. To the maximum extent permitted by applicable law, the Company's aggregate liability arising out of or relating to the Service shall not exceed the amount of fees actually paid by the User to the Company in the six (6) months preceding the event giving rise to the claim.
2. This limitation does not apply to damages caused by the Company's intentional misconduct or gross negligence, or to liability that cannot be limited or excluded under applicable law.

---

## 10. Personal Information

### Article 37 (Relationship with the Privacy Policy)

1. The Company processes Users' personal information in accordance with its separately published Privacy Policy.
2. Where these Terms and the Privacy Policy conflict with respect to the handling of personal information, the Privacy Policy governs.

---

## 11. Governing Law and Dispute Resolution

### Article 38 (Governing Law and Jurisdiction)

1. These Terms are governed by the laws of the Republic of Korea, without regard to its conflict-of-laws principles.
2. Any dispute arising out of or relating to these Terms or the Service shall first be addressed through good-faith consultation between the Company and the User.
3. If a dispute cannot be resolved through consultation, it shall be submitted to the competent court having jurisdiction under applicable procedural law.

---

## Company Information

| Item                     | Detail                |
| ------------------------ | --------------------- |
| Company                  | Pied Piper            |
| Representative / Contact | Hoon Choi (CEO / CTO) |
| Email                    | support@onttle.com    |

---

_These Terms are also published in Korean. In the event of any discrepancy between the Korean and English versions, the Korean version shall prevail, except where prohibited by applicable law._
