# CRS Portal — AI Briefing

> This document is a complete reference for AI tools building UI prototypes for the CRS customer portal.
> It covers both the **business context** (what CRS does, who uses it, what the portal contains) and the **design system** (colors, typography, components, layout rules).
> Everything needed to produce realistic, on-brand screens is in this file.

---

## What CRS Is

**CRS (Credit Reporting Services)** is a B2B credit data platform. CRS acts as a middleware layer between businesses and the three major US credit bureaus — Experian, TransUnion, and Equifax — as well as additional data providers such as LexisNexis, CIC, and FICO.

Businesses that need to pull credit reports, verify income, screen tenants, assess fraud risk, or furnish credit data do not connect directly to the bureaus. Instead, they onboard with CRS, receive API credentials, and query CRS's unified API platform. CRS handles bureau relationships, compliance obligations, credential management, and billing on their behalf.

The **Finstack portal** (this application) is the customer-facing dashboard where those businesses manage everything: their account, API access, products, compliance documents, billing, and support.

---

## Who the Customers Are

CRS serves B2B customers across a wide range of industries. The system categorizes them by **onboarding path**, which determines what products they can access and what compliance requirements apply:

| Onboarding Path | Description |
|---|---|
| **Generic** | Standard business customer — the most common type |
| **PreQual** | Mortgage/lending companies running pre-qualification credit checks |
| **Tenant** | Property managers and landlords screening rental applicants |
| **Individual Landlord** | Single-property landlords (lighter compliance requirements) |
| **Bank & Credit Union** | Financial institutions accessing credit data for lending decisions |
| **Employment** | Employers running background and credit checks on job applicants |
| **Bankruptcy** | Legal firms or services handling bankruptcy proceedings |
| **Housing Counseling** | HUD-approved housing counseling agencies |
| **Prescreen** | Companies running pre-screened credit offer campaigns |
| **B2C** | Businesses offering white-label credit products directly to consumers |
| **Platform Provider** | Resellers and technology partners who offer CRS access to their own customers |
| **Data Furnishing** | Companies that report consumer payment data back to the credit bureaus |
| **Debt Settlement** | Debt settlement companies assessing consumer credit profiles |
| **Non-FCRA** | Customers accessing data for purposes not covered by FCRA (e.g. analytics) |

---

## Platform Types

Beyond the onboarding path, customers also fall into a **platform type** that describes their technical integration model:

| Platform Type | Description |
|---|---|
| **B2B** | Standard direct API integration — the business calls the CRS API |
| **B2C (Full Widget)** | CRS provides a fully embedded widget the customer puts on their site |
| **B2C (Hybrid Widget / API Auth)** | Mixed: CRS widget UI but the customer handles authentication |
| **B2C (Hybrid Widget / Widget Auth)** | Mixed: CRS handles both UI and auth |
| **B2C (Full API)** | Customer builds their own UI, calls CRS API on behalf of consumers |
| **B2C (WordPress)** | WordPress plugin integration |
| **Mware Direct** | Direct middleware connection (legacy/internal) |
| **MCL** | Access via the MCL platform (a separate upstream API platform) |
| **MCL Portal** | Customers accessing CRS through the MCL portal |

---

## API Platforms

CRS operates multiple API endpoints serving different use cases. When a customer gets API credentials, they are scoped to a specific platform:

| Platform | Internal Code | What It Is |
|---|---|---|
| **CRS API** | `mware_crs` | The core credit reporting API — the primary platform for most customers |
| **Credit OrderPro** | `cop` | A structured credit order processing system, used for mortgage and lending workflows |
| **MCL API** | `mcl` | An alternative upstream platform with a different product catalog |
| **Credit Monitoring** | `cm` | API for ongoing credit monitoring and alert services |
| **Data Furnishing** | `df` | API for customers that report consumer tradeline data back to the bureaus |
| **SBSS** | `sbss` | A secondary scoring and specialty service platform |

Each customer can have credentials for multiple platforms, and each platform has separate **sandbox** and **production** environments.

---

## Data Providers / Vendors

CRS aggregates data from the following sources:

| Code | Full Name | Type |
|---|---|---|
| `exp` | Experian | Major credit bureau |
| `tuc` | TransUnion | Major credit bureau |
| `eqf` | Equifax | Major credit bureau |
| `lnr` | LexisNexis | Specialty data (identity, fraud, public records) |
| `cic` | CIC | Specialty credit data |
| `ubq` | Ubiquity | Specialty data provider |
| `fico` | FICO | Credit scoring models |
| `mcl` | MCL | Platform-level data provider |
| `pps` | PPS | Specialty provider |
| `service_link` | ServiceLink | Property and specialty data |

---

## Product Catalog

CRS organizes its products into **categories**. A product maps to a specific data pull from a specific vendor. For example, a consumer credit report from TransUnion is a different product than the same report from Equifax, even though they look similar to the end user.

### Product Categories

| Category | Description |
|---|---|
| **Consumer Credit** | Credit reports and scores for individual consumers — the core product. Used for lending, housing, employment decisions. Pulled from Experian, TransUnion, and/or Equifax. |
| **Income & Assets** | Verification of a consumer's income and asset data. Used by lenders. |
| **Business Credit** | Credit reports and risk scores for businesses (not individual consumers). |
| **Identity & Fraud** | Identity verification, fraud risk scoring, synthetic identity detection. |
| **Public Records** | Judgments, liens, bankruptcies, evictions, and other public record searches. |
| **Alternative Credit** | Non-traditional credit data — utility payments, rent history, bank data — for consumers with thin or no credit files. |
| **Prescreen** | Outbound pre-screened credit offer campaigns. Companies query a list of consumers to find pre-approved prospects (FCRA regulated). |
| **IScreen** | Inbound screening — consumers respond to an invitation to check their own eligibility. |
| **B2C Plan** | Packaged product plans for B2C white-label deployments. |
| **Merge Products** | Combined multi-bureau reports (2-bureau or 3-bureau merged credit report). |
| **Bundle** | Flexible bundled product — customer selects which bureaus to include at order time (e.g., any 1-of-3, any 2-of-3). |
| **Externally Hosted** | Products accessed via an external URL rather than the CRS API directly. |

### Inquiry Types

All credit pulls are classified as either:
- **Hard inquiry** — visible to the consumer, impacts their credit score, used for credit decisions
- **Soft inquiry** — not visible to the consumer, does not impact credit score, used for prescreen and monitoring

### FCRA vs Non-FCRA

Products are flagged as either **FCRA** (subject to Fair Credit Reporting Act regulations — requires permissible purpose, consumer rights apply) or **Non-FCRA** (analytics, alternative data not subject to FCRA consumer protections).

---

## Key Product: CreditOrder Pro

**CreditOrder Pro** (also referred to as **COP** in code) is CRS's flagship product for the mortgage and lending industry. It is a structured credit order system built on top of the CRS API.

Key features:
- **Single applicant** credit orders — pull a report for one borrower
- **Joint applicant** credit orders — pull reports for two borrowers (e.g. co-signers, married couples) simultaneously and merge them into one response
- **Multi-bureau selection** — customer chooses which bureaus to include (Experian, TransUnion, Equifax, or any combination)
- **Bundle orders** — flexible "pull from any N bureaus" logic (e.g. pull from whichever 2 of the 3 bureaus return a hit)
- Supports **re-issue** of reports (re-pull within a time window without a new hard inquiry)

Customers interact with Credit Order Pro either through the portal's **Get Report** section (a web form UI) or programmatically via the API.

---

## Environments: Sandbox vs Production

Every customer gets two sets of API credentials:

- **Sandbox** — A testing environment with simulated bureau responses. No real credit pulls occur. Used during integration and development. Credentials are free to use.
- **Production** — The live environment. Real credit pulls occur and are billed. Requires completed onboarding and compliance approval before access is granted.

This is a hard separation — a customer cannot accidentally use production credentials in a test context. The portal clearly labels which environment any given credential or request log belongs to.

---

## Customer Onboarding

New customers go through a **multi-step compliance-driven onboarding process** before they can access production data. The steps vary by onboarding path but typically include:

1. **Contracts** — Review and e-sign the customer agreement (via DocuSign or Zoho Sign)
2. **Payment** — Add a payment method (credit card via Stripe)
3. **Documents** — Submit compliance documents (business registration, FCRA certification, end-user agreements, etc.)
4. **Inspection / Onsite visit** — For certain customer types (e.g. mortgage companies), CRS conducts a physical or virtual inspection of the customer's office and systems
5. **Compliance approval** — CRS internal staff reviews all submitted materials and approves the customer for production access

Until all steps are complete, customers are limited to sandbox access. The portal tracks progress through each step and shows the customer where they are in the process.

---

## Portal Features

Once onboarded, customers use the portal for ongoing operations. The sidebar navigation reflects the main sections:

### Onboarding
Visible during the onboarding phase. Steps appear and are checked off as completed.

### Get Report
Direct access to the **CreditOrder Pro** web form — customers can pull credit reports through a UI without writing any code. Also includes a **Files** section for batch uploads.

### Products
- **My products** — View all products the customer is subscribed to, with pricing details
- **Credentials** — View and manage API keys for each platform/environment. Copy sandbox or production credentials. Rotate or revoke keys.
- **Request logs** — A full audit trail of every API call the customer has made. Filterable by date, endpoint, status, environment, and product. Shows request/response details for debugging.
- **White label** — Configure custom branding (logo, colors) for B2C widget deployments

### Customers
For **platform providers** (resellers) — manage the sub-customers they have brought onto the platform.

### Billing
- **Invoices** — View and download monthly invoices. Synced from NetSuite.
- **Payment method** — Add, update, or remove credit card on file (via Stripe)

### Settings
- **Account** — Company information, contact details
- **Users** — Invite team members, assign roles (Admin, User), manage access
- **Support** — Submit and track support requests

---

## Billing Model

CRS uses **usage-based billing** with optional minimums and add-ons:

- **Per-report pricing** — Each API call that successfully returns data is billed at a rate agreed at signup. Prices vary by product, vendor, and volume.
- **Tiered volume pricing** — Higher usage volume unlocks lower per-unit rates
- **Monthly minimums** — Some customers have a minimum monthly spend commitment
- **Setup fees** — One-time fees for certain onboarding types
- **Free months** — Promotional periods with no billing
- **Prepaid plans** — Customers can prepay for a block of usage
- **Product add-ons** — Optional features attached to a base product (e.g. additional score models, enhanced fraud flags) billed separately
- **Wholesale vs retail** — Resellers (platform providers) are billed at wholesale rates and set their own retail rates for sub-customers

Invoices are generated monthly and synced to NetSuite. Payments are charged to the card on file via Stripe.

---

## Compliance Context

CRS operates in a heavily regulated environment. The **Fair Credit Reporting Act (FCRA)** governs how consumer credit data can be accessed and used.

Key concepts that appear throughout the portal:

- **Permissible purpose** — A legally recognized reason to access consumer credit data (e.g. "credit transaction", "employment", "tenant screening"). Customers must certify their permissible purpose and it must match the product they are using.
- **Hard vs soft inquiry** — Hard inquiries require explicit consumer consent and are disclosed to the consumer. Soft inquiries do not.
- **FCRA certification** — Customers must sign FCRA compliance certifications as part of onboarding.
- **Audit trail** — Every credit pull is logged with timestamp, product, requester, and purpose. This is non-negotiable infrastructure.
- **Consumer dispute** — Consumers have FCRA rights to dispute information. Certain report return statuses (e.g. `NoFileReturnedDueToDispute`) reflect this.

---

## Terminology Glossary

| Term | Meaning |
|---|---|
| **Customer** | A business that has an account with CRS (not an individual consumer) |
| **Consumer / Applicant** | The individual whose credit data is being requested by a CRS customer |
| **Environment** | A set of API credentials (sandbox or production) for a specific platform |
| **Product / Billing Product** | A specific data product with a code (e.g. `TUC1001` = TransUnion consumer credit report) |
| **Subcode** | A bureau-specific access code tied to a product/permissible purpose combination |
| **Add-on** | An optional enhancement to a base product (e.g. an additional credit score model) |
| **Permissible purpose** | The legal basis for a credit inquiry |
| **Onboarding path** | The customer's industry/use-case classification, determines their product eligibility |
| **Platform type** | The technical integration model (B2B API, B2C widget, etc.) |
| **MCL** | A separate upstream credit data platform that CRS also resells access to |
| **COP / CreditOrder Pro** | CRS's structured credit ordering system for mortgage/lending customers |
| **Merge report** | A combined 2- or 3-bureau credit report |
| **Bundle** | A flexible multi-bureau order where the customer picks how many bureaus at order time |
| **Hard inquiry** | A credit pull that appears on the consumer's credit file |
| **Soft inquiry** | A credit pull that does not appear on the consumer's credit file |
| **FCRA** | Fair Credit Reporting Act — the primary US federal law governing credit data |
| **Request log** | A logged record of an API call, including request, response, and metadata |
| **White label** | CRS's product rebranded under the customer's own brand for their end consumers |

---

# Finstack Design System

> The sections below cover the visual design system used in the Finstack portal.
> Where a specific component variant is not listed, use the general guidelines section to extrapolate from existing patterns — the system is internally consistent and designed to scale.

---

## Logo

Four logo assets are available. The icon (mark only) and full wordmark, each in a dark-on-light and light-on-dark variant. The brand accent colors in the mark — purple (`#8046ff`) and orange (`#FE8742`) — are always present regardless of variant; only the letterforms change between black and white.

**Usage:**
- Use the **white variant** on the dark portal background (`#080411`) — sidebar, auth pages, dark headers
- Use the **black variant** on white/light surfaces — documents, light-mode contexts
- Never recolor the purple or orange portions of the mark
- Maintain clear space around the logo equal to the height of the "C" letterform

### Full Wordmark — Dark on Light

```svg
<svg width="219" height="76" viewBox="0 0 219 76" fill="none" xmlns="http://www.w3.org/2000/svg">
<g clip-path="url(#clip0_1_15)">
<path d="M73.3528 1.99902L63.8062 18.4569H41.3197L30.0524 37.9724L41.3197 57.4879H63.8105L73.3513 73.9355L73.3486 73.9403H44.9899C40.188 73.9403 37.7872 73.9403 35.6378 73.2418C33.7362 72.624 31.9856 71.6132 30.4996 70.2752C28.8202 68.7631 27.6197 66.6839 25.2189 62.5254L17.6299 49.3809C15.229 45.2224 14.0286 43.1433 13.5587 40.9327C13.143 38.9768 13.143 36.9555 13.5587 34.9995C14.0286 32.7891 15.229 30.7099 17.6299 26.5514L25.2189 13.4068C27.6197 9.2483 28.8202 7.16906 30.4996 5.65689C31.9856 4.31891 33.7362 3.3082 35.638 2.6903C37.7872 1.99194 40.188 1.99194 44.9899 1.99194H73.3486L73.3528 1.99902Z" fill="black"/>
<path d="M92.3859 1.99194L70.6046 38.9502H70.5982L61.8739 54.0611H43.2978L34.0098 37.9737L34.0385 37.9241H47.3016L51.7888 45.696L55.6834 38.9502H55.6768L56.0657 38.2877L56.2757 37.9241H56.2793L77.3815 1.99194H92.3859Z" fill="#8046FF"/>
<path d="M58.3673 54.0613H43.2965L33.9811 37.9243H48.8877L53.3749 45.6962L58.3673 54.0613Z" fill="#FE8742"/>
<path d="M109.097 2.01758C117.588 2.01767 124.231 4.12383 128.87 8.48185C133.561 12.758 135.878 18.4978 135.878 25.594C135.878 30.848 134.457 35.4548 131.59 39.3709L131.584 39.3773C128.934 42.9321 125.309 45.5105 120.754 47.1348L137.44 73.9958H120.275L119.997 73.5404L105.059 49.1719H96.6381V73.9958H80.6151V28.3075L96.1087 2.01816L109.097 2.01758ZM96.6381 34.5494H109.998C113.163 34.5494 115.458 33.6983 117.031 32.1259C118.7 30.4572 119.555 28.3078 119.555 25.594C119.555 22.8797 118.7 20.7707 117.044 19.1732L117.031 19.1618L117.019 19.149C115.445 17.5164 113.153 16.6375 109.998 16.6375H96.6381V34.5494Z" fill="black"/>
<path d="M165.808 1.01636C172.446 1.01637 178.245 2.69407 183.16 6.08396L183.62 6.40284C188.322 9.75175 191.297 14.5769 192.569 20.7983L192.742 21.6491L178.148 26.0967L177.898 25.1337C177.077 21.9762 175.553 19.6852 173.371 18.1694L173.361 18.1618C171.198 16.6165 168.691 15.802 165.808 15.7357C163.159 15.7358 161.029 16.3668 159.355 17.5622L159.337 17.575C157.766 18.6412 156.953 20.2261 156.953 22.4908C156.953 24.683 157.73 26.2357 159.232 27.3009C160.961 28.4093 163.664 29.4128 167.415 30.269C174.557 31.8708 179.758 33.4563 182.939 35.0445C186.986 36.9998 190.085 39.4746 192.169 42.4947C194.258 45.5223 195.292 49.0452 195.292 53.0228C195.29 59.6977 192.805 65.081 187.83 69.0763L187.832 69.0776C182.943 73.0583 176.402 74.9971 168.311 74.9972C161.024 74.9972 154.734 73.3609 149.482 70.0445L149.469 70.0368C144.24 66.6201 140.932 61.6593 139.549 55.2243L139.365 54.3672L153.018 50.2077L153.968 49.9169L154.218 50.8786C155.029 53.999 156.661 56.3244 159.104 57.9181C161.608 59.482 164.5 60.2766 167.811 60.2766C170.883 60.2766 173.365 59.6354 175.312 58.4182L175.324 58.4118C177.228 57.2577 178.168 55.5719 178.168 53.2218C178.168 52.0138 177.925 51.051 177.491 50.2919C177.072 49.5597 176.332 48.8699 175.174 48.26C173.95 47.6162 172.807 47.1427 171.746 46.8301L171.72 46.8237L171.697 46.8148C170.603 46.429 169.038 45.9695 166.986 45.4398L164.586 44.8659C156.497 42.9498 145.765 40.0735 141.921 31.9067C141.359 30.7129 140.874 29.3573 140.462 27.8467L140.459 27.8365C140.037 26.2194 139.829 24.5369 139.829 22.7918C139.829 16.1243 142.24 10.7683 147.088 6.83395C151.903 2.92636 158.173 1.01641 165.808 1.01636Z" fill="black"/>
<path d="M208.342 69.8978C208.342 71.0222 207.94 71.9867 207.135 72.7914C206.341 73.5843 205.384 73.9808 204.259 73.9808C203.123 73.9808 202.159 73.5843 201.366 72.7914C200.572 71.9985 200.177 71.034 200.177 69.8978C200.177 68.7736 200.572 67.815 201.366 67.0221C202.171 66.2173 203.135 65.8149 204.259 65.8149C205.372 65.8149 206.33 66.2173 207.135 67.0221C207.94 67.8268 208.342 68.7854 208.342 69.8978ZM201.028 69.8978C201.028 70.7736 201.348 71.531 201.988 72.1701C202.626 72.8092 203.384 73.1286 204.259 73.1286C205.147 73.1286 205.905 72.8151 206.531 72.1878C207.171 71.5487 207.49 70.7854 207.49 69.8978C207.49 68.9984 207.176 68.2351 206.549 67.6079C205.923 66.9806 205.159 66.6671 204.259 66.6671C203.372 66.6671 202.608 66.9865 201.97 67.6256C201.342 68.2529 201.028 69.0103 201.028 69.8978ZM203.709 69.667H204.525C204.679 69.667 204.809 69.6198 204.916 69.525C205.022 69.4304 205.076 69.312 205.076 69.17C205.076 68.8387 204.857 68.6729 204.418 68.6729H203.709V69.667ZM205.111 71.9037L204.136 70.4127H203.709V71.9037H202.892V67.8918H204.596C204.951 67.8918 205.253 68.0162 205.502 68.2647C205.763 68.5131 205.893 68.815 205.893 69.17C205.893 69.4423 205.81 69.6907 205.644 69.9155C205.478 70.1286 205.265 70.2706 205.005 70.3416L204.969 70.3771L206.035 71.8682V71.9037H205.111Z" fill="black"/>
</g>
<defs>
<clipPath id="clip0_1_15">
<rect width="197.784" height="75.0571" fill="white" transform="translate(10.6081)"/>
</clipPath>
</defs>
</svg>
```

### Full Wordmark — Light on Dark

```svg
<svg width="219" height="76" viewBox="0 0 219 76" fill="none" xmlns="http://www.w3.org/2000/svg">
<g clip-path="url(#clip0_11_200)">
<path d="M73.3528 1.99902L63.8062 18.4569H41.3197L30.0524 37.9724L41.3197 57.4879H63.8105L73.3513 73.9355L73.3486 73.9403H44.9899C40.188 73.9403 37.7872 73.9403 35.6378 73.2418C33.7362 72.624 31.9856 71.6132 30.4996 70.2752C28.8202 68.7631 27.6197 66.6839 25.2189 62.5254L17.6299 49.3809C15.229 45.2224 14.0286 43.1433 13.5587 40.9327C13.143 38.9768 13.143 36.9555 13.5587 34.9995C14.0286 32.7891 15.229 30.7099 17.6299 26.5514L25.2189 13.4068C27.6197 9.2483 28.8202 7.16906 30.4996 5.65689C31.9856 4.31891 33.7362 3.3082 35.638 2.6903C37.7872 1.99194 40.188 1.99194 44.9899 1.99194H73.3486L73.3528 1.99902Z" fill="white"/>
<path d="M92.3859 1.99194L70.6046 38.9502H70.5982L61.8739 54.0611H43.2978L34.0098 37.9737L34.0385 37.9241H47.3016L51.7888 45.696L55.6834 38.9502H55.6768L56.0657 38.2877L56.2757 37.9241H56.2793L77.3815 1.99194H92.3859Z" fill="#8046FF"/>
<path d="M58.3673 54.0613H43.2965L33.9811 37.9243H48.8877L53.3749 45.6962L58.3673 54.0613Z" fill="#FE8742"/>
<path d="M109.097 2.01758C117.588 2.01767 124.231 4.12383 128.87 8.48185C133.561 12.758 135.878 18.4978 135.878 25.594C135.878 30.848 134.457 35.4548 131.59 39.3709L131.584 39.3773C128.934 42.9321 125.309 45.5105 120.754 47.1348L137.44 73.9958H120.275L119.997 73.5404L105.059 49.1719H96.6381V73.9958H80.6151V28.3075L96.1087 2.01816L109.097 2.01758ZM96.6381 34.5494H109.998C113.163 34.5494 115.458 33.6983 117.031 32.1259C118.7 30.4572 119.555 28.3078 119.555 25.594C119.555 22.8797 118.7 20.7707 117.044 19.1732L117.031 19.1618L117.019 19.149C115.445 17.5164 113.153 16.6375 109.998 16.6375H96.6381V34.5494Z" fill="white"/>
<path d="M165.808 1.01636C172.446 1.01637 178.245 2.69407 183.16 6.08396L183.62 6.40284C188.322 9.75175 191.297 14.5769 192.569 20.7983L192.742 21.6491L178.148 26.0967L177.898 25.1337C177.077 21.9762 175.553 19.6852 173.371 18.1694L173.361 18.1618C171.198 16.6165 168.691 15.802 165.808 15.7357C163.159 15.7358 161.029 16.3668 159.355 17.5622L159.337 17.575C157.766 18.6412 156.953 20.2261 156.953 22.4908C156.953 24.683 157.73 26.2357 159.232 27.3009C160.961 28.4093 163.664 29.4128 167.415 30.269C174.557 31.8708 179.758 33.4563 182.939 35.0445C186.986 36.9998 190.085 39.4746 192.169 42.4947C194.258 45.5223 195.292 49.0452 195.292 53.0228C195.29 59.6977 192.805 65.081 187.83 69.0763L187.832 69.0776C182.943 73.0583 176.402 74.9971 168.311 74.9972C161.024 74.9972 154.734 73.3609 149.482 70.0445L149.469 70.0368C144.24 66.6201 140.932 61.6593 139.549 55.2243L139.365 54.3672L153.018 50.2077L153.968 49.9169L154.218 50.8786C155.029 53.999 156.661 56.3244 159.104 57.9181C161.608 59.482 164.5 60.2766 167.811 60.2766C170.883 60.2766 173.365 59.6354 175.312 58.4182L175.324 58.4118C177.228 57.2577 178.168 55.5719 178.168 53.2218C178.168 52.0138 177.925 51.051 177.491 50.2919C177.072 49.5597 176.332 48.8699 175.174 48.26C173.95 47.6162 172.807 47.1427 171.746 46.8301L171.72 46.8237L171.697 46.8148C170.603 46.429 169.038 45.9695 166.986 45.4398L164.586 44.8659C156.497 42.9498 145.765 40.0735 141.921 31.9067C141.359 30.7129 140.874 29.3573 140.462 27.8467L140.459 27.8365C140.037 26.2194 139.829 24.5369 139.829 22.7918C139.829 16.1243 142.24 10.7683 147.088 6.83395C151.903 2.92636 158.173 1.01641 165.808 1.01636Z" fill="white"/>
<path d="M208.342 69.8978C208.342 71.0222 207.94 71.9867 207.135 72.7914C206.341 73.5843 205.384 73.9808 204.259 73.9808C203.123 73.9808 202.159 73.5843 201.366 72.7914C200.572 71.9985 200.177 71.034 200.177 69.8978C200.177 68.7736 200.572 67.815 201.366 67.0221C202.171 66.2173 203.135 65.8149 204.259 65.8149C205.372 65.8149 206.33 66.2173 207.135 67.0221C207.94 67.8268 208.342 68.7854 208.342 69.8978ZM201.028 69.8978C201.028 70.7736 201.348 71.531 201.988 72.1701C202.626 72.8092 203.384 73.1286 204.259 73.1286C205.147 73.1286 205.905 72.8151 206.531 72.1878C207.171 71.5487 207.49 70.7854 207.49 69.8978C207.49 68.9984 207.176 68.2351 206.549 67.6079C205.923 66.9806 205.159 66.6671 204.259 66.6671C203.372 66.6671 202.608 66.9865 201.97 67.6256C201.342 68.2529 201.028 69.0103 201.028 69.8978ZM203.709 69.667H204.525C204.679 69.667 204.809 69.6198 204.916 69.525C205.022 69.4304 205.076 69.312 205.076 69.17C205.076 68.8387 204.857 68.6729 204.418 68.6729H203.709V69.667ZM205.111 71.9037L204.136 70.4127H203.709V71.9037H202.892V67.8918H204.596C204.951 67.8918 205.253 68.0162 205.502 68.2647C205.763 68.5131 205.893 68.815 205.893 69.17C205.893 69.4423 205.81 69.6907 205.644 69.9155C205.478 70.1286 205.265 70.2706 205.005 70.3416L204.969 70.3771L206.035 71.8682V71.9037H205.111Z" fill="white"/>
</g>
<defs>
<clipPath id="clip0_11_200">
<rect width="197.784" height="75.0571" fill="white" transform="translate(10.6081)"/>
</clipPath>
</defs>
</svg>
```

### Icon / Mark Only — Dark on Light

```svg
<svg width="81" height="80" viewBox="0 0 81 80" fill="none" xmlns="http://www.w3.org/2000/svg">
<g clip-path="url(#clip0_1_39)">
<path d="M62.1974 4.83627L52.8516 20.9401H30.8383L19.808 40.0358L30.8383 59.1316H52.8559L62.1961 75.2254L62.1935 75.23H34.4312C29.7304 75.23 27.38 75.23 25.2759 74.5466C23.4144 73.9421 21.7006 72.953 20.2458 71.6439C18.6017 70.1642 17.4265 68.1297 15.0761 64.0606L7.64679 51.1989C5.29641 47.1299 4.12124 45.0955 3.66127 42.9325C3.25427 41.0186 3.25427 39.0408 3.66126 37.1269C4.12123 34.964 5.29641 32.9295 7.64678 28.8605L15.0761 15.9986C17.4265 11.9296 18.6017 9.8951 20.2458 8.41546C21.7006 7.10625 23.4144 6.11729 25.2759 5.51268C27.3802 4.82935 29.7304 4.82935 34.4313 4.82935H62.1935L62.1974 4.83627Z" fill="black"/>
<path d="M80.8302 4.82935L59.5071 40.9926H59.5009L50.9602 55.7784H32.7748L23.6822 40.0371L23.7102 39.9886H36.6942L41.0871 47.5933L44.8999 40.9926H44.8934L45.2741 40.3443L45.4797 39.9886H45.4832L66.1415 4.82935H80.8302Z" fill="#8046FF"/>
<path d="M47.5272 55.7786H32.7733L23.6541 39.9888H38.247L42.6399 47.5935L47.5272 55.7786Z" fill="#FE8742"/>
</g>
<defs>
<clipPath id="clip0_1_39">
<rect width="80" height="70.5454" fill="white" transform="translate(0.901306 4.72437)"/>
</clipPath>
</defs>
</svg>
```

### Full Logotype Files (SVG + PDF)

The full CRS logotype (the wide "CRS®" wordmark with integrated checkmark) is available in three color variants. These are the official brand files — use them for headers, splash screens, closing slides, and anywhere the full logotype is needed.

| Variant | Use | SVG Path | PDF Path |
|---|---|---|---|
| **Full Color** | Default — black type with purple checkmark (`#8046FF`) and orange accent (`#FE8742`) on light backgrounds | `~/.claude/shared/crs-logos/crs-logotype-full.svg` | `~/.claude/shared/crs-logos/crs-logotype-full.pdf` |
| **Black** | All-black monochrome — for documents, fax, single-color print | `~/.claude/shared/crs-logos/crs-logotype-black.svg` | `~/.claude/shared/crs-logos/crs-logotype-black.pdf` |
| **White** | All-white — for dark backgrounds (`#080411`), dark headers, auth screens | `~/.claude/shared/crs-logos/crs-logotype-white.svg` | `~/.claude/shared/crs-logos/crs-logotype-white.pdf` |

**Logotype dimensions:** native viewBox is `1336 × 500` (roughly 2.67:1 aspect ratio). Scale proportionally.

**When to use which:**
- **Full Color** is the default for web, presentations, and marketing materials on light backgrounds
- **Black** for single-color contexts (printing, faxes, stamps)
- **White** on dark portal backgrounds (`#080411`), dark slides, or dark headers
- Never recolor the purple (`#8046FF`) or orange (`#FE8742`) portions of the Full Color mark

---

## Brand Overview

Finstack is the customer-facing identity of the CRS credit reporting portal. The visual language is dark, sophisticated, and enterprise-grade: a near-black deep-purple background (`#080411`) acts as the primary canvas, with white content surfaces (`#ffffff`) floating on top of it. The brand accent is a vivid electric purple (`#8046ff`), used sparingly for highlights and interactive brand moments. Typography is geometric and clean (Euclid Square / Poppins), set in a tight line-height at a slight letter-spacing — precise, not casual. The overall tone is that of a trustworthy B2B SaaS dashboard: no gradients, no decorative illustration, no playfulness. Every element serves a function.

There are two distinct surface modes:
- **Dark surfaces** — the portal chrome, sidebar, and auth screens use `#080411` as base
- **Light surfaces** — content cards, modals, forms, and data areas use `#ffffff` as base

---

## Colors

### Brand / Primary

| Hex | Name | Use |
|---|---|---|
| `#8046ff` | Purple 500 | Brand accent, highlights, semantic brand indicator |
| `#6638cc` | Purple 600 | Brand dark variant |
| `#4d2a99` | Purple 700 | — |
| `#331c66` | Purple 800 | — |
| `#1a0e33` | Purple 900 | **Primary button** background, outline button text/border |
| `#06040d` | Purple 1000 | Primary button hover state |
| `#e6daff` | Purple 100 | Secondary button background |
| `#ccb5ff` | Purple 200 | Secondary button hover background |
| `#b390ff` | Purple 300 | Brand-tinted text on dark surfaces |
| `#f6f2ff` | Purple 70 | Brand light badge background |
| `#f7f5fc` | Purple 50 | Very light brand tint |

### Grayscale

| Hex | Name | Use |
|---|---|---|
| `#080411` | Gray 1000 | **Main portal background** (dark canvas) |
| `#14111d` | Gray 950 | Nav item hover background |
| `#211d29` | Gray 900 | Nav item active, modal header background, dark border |
| `#393641` | Gray 800 | Secondary dark surface, secondary text/icons |
| `#524f58` | Gray 700 | Medium-dark border (e.g. alerts on dark bg) |
| `#6b6870` | Gray 600 | Medium border, tertiary text/icons |
| `#838188` | Gray 500 | Muted text/icons |
| `#9c9ba0` | Gray 400 | Placeholder text, quaternary text |
| `#b5b4b8` | Gray 300 | Focus border on light input |
| `#cecdcf` | Gray 200 | Disabled/hint text |
| `#dad9db` | Gray 150 | Light separator |
| `#e6e6e7` | Gray 100 | **Light border** — card outlines, dividers on white bg |
| `#f3f2f3` | Gray 50 | Neutral badge background, disabled input background |
| `#f8f7f8` | Gray 30 | Dropdown item hover background |
| `#ffffff` | Gray 0 | **Content surface** — cards, modal body, input background |

### Semantic (Status Colors)

Each status has three tiers: **bright** (icons/indicators), **dark** (text), **light** (background tint).

| Status | Bright | Dark | Light |
|---|---|---|---|
| Success | `#00df81` | `#12843a` | `#e5f3ee` |
| Error | `#fa3a55` | `#e05b58` | `#fcefee` |
| Warning | `#edf200` | `#8e9100` | `#fbfce5` |
| Info | `#408efc` | `#58a7e0` | `#eef6fc` |
| Neutral | `#b5b4b8` | `#838188` | `#f3f2f3` |
| Brand | `#8046ff` | `#6638cc` | `#f6f2ff` |

Alpha variants (for dark backgrounds): add 12% opacity to the dark tone — e.g. `rgba(18, 132, 58, 0.12)` for success.

### Text & Icon Hierarchy

Use these when choosing text or icon colors. Always pick the lowest-contrast option that still meets WCAG AA:

| Role | Hex | Use |
|---|---|---|
| Primary | `#14111d` | Main body text, primary icons on light bg |
| Secondary | `#393641` | Secondary text, secondary icons |
| Tertiary | `#6b6870` | Helper text, muted labels |
| Quaternary | `#9c9ba0` | Placeholder text, disabled labels |
| Disabled/hint | `#cecdcf` | Greyed-out, non-interactive |
| On dark | `#ffffff` | Any text or icon on a dark surface |
| Brand accent | `#b390ff` | Brand-tinted text/icons on dark surfaces |

### Special

| Hex | Use |
|---|---|
| `#56596c` | Neutral button text (dark blue-gray) |
| `rgba(0,0,0,0.25)` | Modal backdrop overlay |
| `rgba(0,0,0,0.10)` | Dropdown shadow |
| `rgba(0,0,0,0.12)` | Elevated element shadow (datepicker, cards) |

---

## Typography

**Font stack:** `"Euclid Square", "Poppins", Arial, sans-serif`
**Base rules (apply to all text):** `line-height: 1.2` (120%), `letter-spacing: 0.03em` (3%)
**Exception:** Form field labels use `letter-spacing: 0` despite being uppercase.

### Type Scale

| Class | Size | Weight | Transform | Use |
|---|---|---|---|---|
| `.fs-subtitle-1` | 20px | 500 | — | Section titles, page headings |
| `.fs-subtitle-2` | 18px | 500 | — | Sub-section headings, card titles |
| `.fs-body-1` | 16px | 400 | — | Primary body text |
| `.fs-body-2` | 13px | 400 | — | Secondary body text, table content |
| `.fs-body-3` | 12px | 400 | — | Small body text, chip labels |
| `.fs-overline-0` | 14px | 500 | UPPERCASE | Section eyebrow labels |
| `.fs-overline-1` | 10px | 400 | UPPERCASE | Small section labels |
| `.fs-overline-2` | 8px | 500 | UPPERCASE | Micro labels (rare) |
| `.fs-label` | 11px | 400 | UPPERCASE | Form field titles (no letter-spacing) |
| `.fs-caption-1` | 12px | 400 | — | Captions, helper text |
| `.fs-caption-2` | 9px | 400 | — | Fine print |
| `.fs-input-text` | 16px | 400 | — | Input field entered values |
| `.fs-button-large` | 16px | 400 | — | Large button labels |
| `.fs-button-medium` | 14px | 400 | — | Standard button labels |

### Heading Hierarchy

The design system does not define explicit `.h1`–`.h5` classes. Use the scale above:
- **Page title:** `.fs-subtitle-1` (20px/500) with primary text color `#14111d`
- **Section heading:** `.fs-subtitle-2` (18px/500)
- **Card/widget title:** `.fs-overline-0` (14px/500/UPPERCASE) in tertiary color `#6b6870`
- **Field label:** `.fs-label` (11px/400/UPPERCASE) in tertiary color `#6b6870`

---

## Spacing & Layout

### Spacing Scale

Base unit is **4px**. All spacing is a multiple of 4.

| Token | Value | Typical use |
|---|---|---|
| `space-1` | 4px | Icon-to-text gap, tight chip padding |
| `space-2` | 8px | Button padding (vertical), small element gaps |
| `space-3` | 12px | Form element internal padding |
| `space-4` | 16px | Standard content padding, gap between form fields |
| `space-5` | 20px | — |
| `space-6` | 24px | Section internal padding, gap between groups |
| `space-8` | 32px | Between sections |
| `space-10` | 40px | — |
| `space-12` | 48px | Major section breaks |
| `space-16` | 64px | — |
| `space-20` | 80px | — |
| `space-24` | 96px | Page-level top/bottom padding |

### Border Radius

| Token | Value | Use |
|---|---|---|
| `radius-1` | 4px | Buttons, inputs, small chips, badges, tags |
| `radius-2` | 8px | Dropdowns, cards, small modals |
| `radius-3` | 12px | Modals |
| `radius-4` | 16px | Large cards, panels |
| `radius-5` | 20px | Extra-large containers |
| `radius-6` | 24px | — |
| `radius-full` | 9999px | Pills, avatars, circular icon buttons |

### Borders

- Default border width: **1px**
- Accent/focus border width: **2px**
- Dashed pattern: `4px dash, 4px gap` (for drop zones, placeholder outlines)

### Layout Structure

The portal uses a **sidebar + main content** layout:

- **Sidebar:** dark surface (`#080411`), fixed left, full height
- **Content area:** white surface (`#ffffff`), scrollable right panel
- **Page padding:** `24px` all sides (minimum)
- **Content max-width:** constrained; avoid full-bleed content inside cards
- **Cards:** white (`#ffffff`), `border-radius: 8px`, `border: 1px solid #e6e6e7`, padding `24px`
- **Touch targets:** minimum **44px** height for any interactive element

### Auth Pages

Auth screens (login, sign-up, MFA) use a separate dark-mode layout: full-screen dark background with a video background layer, frosted-glass style container centered on screen, no sidebar.

---

## Components

### Button

Buttons use `border-radius: 4px`, `padding: 8px 16px`, `font-size: 14px`, `font-weight: 400`, `letter-spacing: 0.42px`. Icons inside buttons are **14px**.

Focus ring (all types): `box-shadow: 0 0 0 2px #408efc`

| Variant | Background | Border | Text | Hover bg |
|---|---|---|---|---|
| `primary` | `#1a0e33` | `#1a0e33` | `#ffffff` | `#06040d` |
| `primary-dark` | `#ffffff` | `#ffffff` | `#1a0e33` | `#f8f7f8` |
| `secondary` | `#e6daff` | `#e6daff` | `#1a0e33` | `#ccb5ff` |
| `outline-primary` | transparent | `#1a0e33` | `#1a0e33` | transparent |
| `outline-secondary` | transparent | `#e6daff` | `#1a0e33` | transparent |
| `danger` | `#fa3a55` | `#fa3a55` | `#ffffff` | `#e8334d` |
| `outline-danger` | transparent | `#fa3a55` | `#fa3a55` | `rgba(#fa3a55, 0.1)` |
| `success` | `#00df81` | `#00df81` | `#ffffff` | `#12843a` |
| `neutral` | `#e6e6e7` | `#e6e6e7` | `#56596c` | `#cecdcf` |

**Disabled state:** `opacity: 0.5`, `cursor: not-allowed`, `pointer-events: none`
**Inactive state (shows as disabled but still clickable):** `opacity: 0.5`, `cursor: pointer`

**Icon modifiers:**
- Icon-only: `padding: 8px` (square)
- Icon on one side only: add `2px` to the opposite side padding (`padding-left/right: 10px`)

**Usage pattern:**
```html
<!-- Primary -->
<button class="fs-btn fs-btn--primary">Save</button>

<!-- With left icon -->
<button class="fs-btn fs-btn--danger fs-btn--icon-left-only">
  <span class="material-icons-outlined">delete</span>
  <span>Delete</span>
</button>

<!-- As link -->
<a href="/path" class="fs-btn fs-btn--secondary">View details</a>
```

---

### Input Field

Full input structure (label + input + helper text):

```html
<div class="fs-input-field">
  <!-- Label row -->
  <div class="fs-input-field__overline">
    <div class="fs-input-field__label-group">
      <span class="fs-input-field__title">Email address</span>
      <!-- Optional required marker -->
      <span class="fs-input-field__required">*</span>
    </div>
    <!-- Optional right-side link -->
    <a class="fs-input-field__link">Forgot?</a>
  </div>

  <!-- Input box -->
  <div class="fs-input">
    <!-- Optional left icon -->
    <span class="fs-input__icon">
      <span class="material-icons-outlined">search</span>
    </span>
    <input class="fs-input__input" type="text" placeholder="Enter value…" />
    <!-- Optional right icon/action -->
    <button class="fs-input__trailing-icon">
      <span class="material-icons-outlined">close</span>
    </button>
  </div>

  <!-- Optional helper text -->
  <span class="fs-input-field__helper">We'll never share your email.</span>
</div>
```

**Input box specs:** `height: 32px`, `padding: 4px 4px 4px 8px`, `border: 1px solid #e6e6e7`, `border-radius: 4px`, `background: #ffffff`
**Input text:** `font-size: 15px`, `font-weight: 400`, `color: #14111d`
**Placeholder color:** `#9c9ba0`
**Focus border:** `#b5b4b8`
**Icon color (left/right):** `#6b6870`

**Variants:**
- `.fs-input--error` — border `#fa3a55` (both default and focus)
- `.fs-input--success` — border `#00df81`
- `.fs-input--disabled` — background `#f3f2f3`, border `#e6e6e7`, cursor `not-allowed`, text `#6b6870`
- `.fs-input--dark` — background `#080411`, border `#211d29`, focus border `#524f58`, text `#ffffff`
- `.fs-input--notes` — textarea variant, `height: auto`, internal padding `8px`
- `.fs-input--multiselect` — wraps chips, `height: auto`, `min-height: 32px`

**Label title:** `font-size: 11px`, `font-weight: 400`, UPPERCASE, `color: #6b6870`, no letter-spacing
**Required marker:** `font-size: 10px`, `color: #fa3a55`, UPPERCASE
**Helper text:** `font-size: 11px`, `color: #6b6870`
**Right-side link:** `font-size: 10px`, UPPERCASE, `color: #408efc`

**Form error message:**
```html
<div class="fs-form-error">
  <span class="material-icons-outlined">error_outline</span>
  <span class="fs-form-error__text">This field is required.</span>
</div>
```
Specs: `padding: 12px 16px`, `background: #fcefee`, `border-radius: 4px`, icon `20px` / `#e05b58`, text `14px` / `#e05b58`

---

### Checkbox

Uses Material Icons as the visual checkbox (no native browser checkbox appearance):
- Unchecked: `check_box_outline_blank` icon, color `#14111d`
- Checked: `check_box` icon (filled), color `#14111d`
- Icon size: `20px`
- Label: `.fs-body-3` (12px), color `#9c9ba0`
- Gap between icon and label: `4px`

Dark mode: icon color `#6b6870` unchecked → `#ffffff` checked; label `#6b6870`

---

### Select / Dropdown

Follows the same structure as `.fs-input` but with `cursor: pointer` and a trailing chevron icon (`expand_more`). Dropdown list appears below:
- Background: `#ffffff`
- `border-radius: 8px`
- `border: 1px solid #e6e6e7`
- Shadow: `0 4px 12px rgba(0,0,0,0.1)`
- Item height: `32px`, padding `8px 12px`
- Item hover: background `#f8f7f8`

---

### Modal

Header: `background: #211d29`, text `#ffffff`, padding `16px 24px`
Body: `background: #ffffff`, padding `24px`
Footer (actions): `background: #ffffff`, padding `16px 24px`, border-top `1px solid #e6e6e7`
Overall `border-radius: 12px`
Backdrop: `rgba(0,0,0,0.25)`

**Sizes (width):**

| Size | Width |
|---|---|
| `sm` | ~400px |
| `md` | ~600px |
| `lg` | ~800px |
| `xl` | ~1000px |
| `xxl` | ~1200px |

---

### Alert / Toast

Structure: icon + title + optional message, `border-radius: 8px`, padding `12px 16px`

| Type | Background | Border-left | Icon color | Text color |
|---|---|---|---|---|
| success | `#e5f3ee` | `#00df81` | `#00df81` | `#12843a` |
| error | `#fcefee` | `#fa3a55` | `#fa3a55` | `#e05b58` |
| warning | `#fbfce5` | `#edf200` | `#8e9100` | `#8e9100` |
| info | `#eef6fc` | `#408efc` | `#408efc` | `#58a7e0` |

---

### Badge / Status Indicator

Small colored dot or capsule used to communicate status.

**Dot badge** — `8px × 8px` circle, `border-radius: 9999px`:
- success: `#00df81`
- error: `#fa3a55`
- warning: `#edf200`
- info: `#408efc`
- neutral: `#b5b4b8`

**Label badge (capsule)** — `border-radius: 9999px`, `padding: 2px 8px`, `.fs-body-3` (12px) text:
- Use semantic **light** color as background and semantic **dark** color as text

**Outline badge** — same shape, transparent background, `1px` border using the bright semantic color

**Number badge (notification)** — small circle with count, `min-width: 16px`, `height: 16px`, `border-radius: 9999px`, `font-size: 9px`, `font-weight: 500`

---

### Pill / Tag

Used for selected filters, categories, or multi-select chips. `border-radius: 4px`, `padding: 2px 6px`, `.fs-body-3` (12px), background `#f3f2f3`, text `#14111d`. Optionally includes a `close` icon (16px) on the right.

---

### Accordion

Collapsed row: full-width, `padding: 12px 16px`, border-bottom `1px solid #e6e6e7`
Expanded: content area revealed below the header, same border treatment
Arrow icon: `expand_more`, rotates 180° when expanded (`transition: transform 0.2s ease`)
Code block variant: dark surface background (`#211d29`), monospace text, `border-radius: 8px`

---

### Table

- Header row: background `#f8f7f8`, text `.fs-label` (11px/UPPERCASE/`#6b6870`), `border-bottom: 1px solid #e6e6e7`
- Data rows: white background, `.fs-body-2` (13px/`#14111d`)
- Row hover: background `#f8f7f8`
- Row border: `border-bottom: 1px solid #e6e6e7`
- Cell padding: `12px 16px`

---

### Page Header

Consistent section above main content:
- Title: `.fs-subtitle-1` (20px/500), primary text `#14111d`
- Subtitle/breadcrumb: `.fs-body-2` (13px), tertiary text `#6b6870`
- Actions (buttons): right-aligned
- Bottom border: `1px solid #e6e6e7`
- Padding: `16px 24px`

---

### Progress Bar

- Container: `height: 8px`, `border-radius: 9999px`, background `#e6e6e7`
- Fill: `border-radius: 9999px`, background semantic bright color (success, info, or brand purple)

---

### Spinner / Loading

- Circle, `border: 2px solid #e6e6e7`, `border-top-color: #1a0e33`
- Animation: 360° rotation, `0.6s linear infinite`
- Sizes: `16px` (small), `24px` (default), `40px` (large)

---

### Empty State

- Centered, stacked layout: icon → title → description → optional action button
- Icon: outlined Material Icon, `48px`, color `#9c9ba0`
- Title: `.fs-subtitle-2` (18px/500), `#14111d`
- Description: `.fs-body-2` (13px), `#6b6870`
- Padding: `48px` vertical

---

### Tooltip

- Background: `#211d29` (dark)
- Text: `#ffffff`, `.fs-caption-1` (12px)
- `border-radius: 4px`, `padding: 6px 10px`
- Arrow pointer toward the target element
- Max width: `200px`

---

### Credential Tile

Card-style tile for displaying API credentials:
- Background: `#ffffff`, `border: 1px solid #e6e6e7`, `border-radius: 8px`
- Padding: `16px`
- Title: `.fs-overline-0` (14px/UPPERCASE), `#6b6870`
- Value: `.fs-body-1` (16px), `#14111d`, monospace for API keys
- Copy-to-clipboard trailing icon button

---

### Sidebar Navigation

- Background: `#080411` (portal dark)
- Item: full width, `padding: 10px 16px`, `border-radius: 4px`, text `.fs-body-2` (13px), color `#9c9ba0`
- Hover: background `#14111d`, text `#ffffff`
- Active: background `#211d29`, text `#ffffff`, optional left accent border `2px solid #8046ff`
- Icon: `20px`, same color as text

---

## Icons

Use **Material Icons Outlined** exclusively. Never use filled variants or other icon libraries.

```html
<span class="material-icons-outlined">search</span>
<span class="material-icons-outlined">add_circle_outline</span>
<span class="material-icons-outlined">delete</span>
```

**Standard sizes:**
- `16px` — chips, small inline indicators
- `20px` — inputs, dropdowns, sidebar nav
- `24px` — page-level actions, empty states background icons
- `48px` — empty state hero icon

Icons inside buttons are always **14px**.

---

## General Guidelines

These rules govern the whole system. If a specific value is not listed for a new component, derive it from these principles.

### Radius rule
All corners are multiples of 4px. Small interactive elements (buttons, inputs, chips, tooltips) use `4px`. Mid-size containers (dropdowns, cards, small modals) use `8px`. Full modals use `12px`. Large panels use `16px`. Circular elements use `9999px`. **Never use odd or non-multiple-of-4 values.**

### Spacing rule
All padding and gaps are multiples of 4px. Default content padding inside cards/panels is `24px`. Default gap between sibling form fields is `16px`. Default gap between icon and text is `4px`. Default gap between grouped elements (label + input) is `6px`. Default gap between sections is `32px`.

### Color selection rule
When choosing a color for a new element, first check if a semantic alias exists (success/error/warning/info/neutral/brand). If not, fall back to the grayscale scale. Never invent new hex values — pick the nearest token. On dark surfaces, use white text (`#ffffff`) and light borders (`#211d29`). On light surfaces, use primary text (`#14111d`) and light borders (`#e6e6e7`).

### Typography rule
Labels (above inputs, section eyebrows) are always uppercase, 11px, weight 400, tertiary color `#6b6870`. Body content is 13–16px, weight 400, primary or secondary color. Headings are 18–20px, weight 500. **Never bold (700) anything.** The heaviest weight used in the design system is 500 (medium).

### Border rule
Borders are always `1px`. The only `2px` use is the focus ring shadow (`box-shadow: 0 0 0 2px #408efc`). Dividers between sections use `1px solid #e6e6e7` on light bg, `1px solid #211d29` on dark bg.

### Dark mode / dark surface rule
When rendering any component on the dark portal background (`#080411`):
- Swap white backgrounds → `#080411` or `#211d29`
- Swap light borders → `#211d29` or `#524f58`
- Swap dark text → `#ffffff` or `#b5b4b8`
- Input dark variant: background `#080411`, border `#211d29`, text `#ffffff`
- The `.fs-btn--primary-dark` variant exists for this use case

### Transition rule
All interactive state changes (hover, focus, active) use a consistent `0.15s ease` transition on `background-color`, `border-color`, and `box-shadow`. The accordion arrow uses `0.2s ease` on `transform`. No other durations.

### Elevation / Shadow rule
Elements that float above the page (dropdowns, tooltips, datepickers) use `box-shadow: 0 4px 12px rgba(0,0,0,0.10)`. Elevated cards or panels use `rgba(0,0,0,0.12)`. Never use colored shadows. Modal backdrop is a flat `rgba(0,0,0,0.25)` overlay.

### Density rule
The system is **compact** by default. Input height is `32px`. Button height is ~`32px` (8px vertical padding + 14px line-height). Table rows are compact with `12px 16px` padding. Do not add extra vertical breathing room unless building a marketing/landing page context.

### Focus accessibility rule
Every interactive element must have a visible focus state. Use `box-shadow: 0 0 0 2px #408efc` (info-bright blue). Never remove outlines without replacing them.

### Voice & copy rule
- Sentence case everywhere: "Add a new step", not "Add A New Step"
- Page title must match the sidebar nav item label exactly
- Button labels are verbs: "Save", "Delete", "Confirm" — not "Click here to save"
- Error messages say what went wrong, not just "Error"
- No exclamation points in UI labels
- No marketing language inside dashboard views

---

## Quick Reference Card

```
BRAND PURPLE     #8046ff   (accent)
PRIMARY BUTTON   #1a0e33   (bg) / #ffffff (text)
PORTAL BG        #080411   (dark canvas)
CONTENT SURFACE  #ffffff   (cards, modals, inputs)
LIGHT BORDER     #e6e6e7
PRIMARY TEXT     #14111d
TERTIARY TEXT    #6b6870
PLACEHOLDER      #9c9ba0
FOCUS RING       #408efc   box-shadow 0 0 0 2px

FONT             "Euclid Square", "Poppins", Arial, sans-serif
LINE HEIGHT      1.2 (120%)
LETTER SPACING   0.03em (3%)

BASE UNIT        4px
BUTTON RADIUS    4px
CARD RADIUS      8px
MODAL RADIUS     12px

SUCCESS          #00df81 / #e5f3ee
ERROR            #fa3a55 / #fcefee
WARNING          #edf200 / #fbfce5
INFO             #408efc / #eef6fc
```
