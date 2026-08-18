# Family Benefits & 401K Calculator — MVP Requirements

> **Revision:** Added support for annual salary and hourly wage compensation models. References to Base Salary have been generalized to Base Compensation where appropriate.

## 1. Project Overview

The **Family Benefits & 401K Calculator** is a full-stack application that helps users evaluate employment opportunities based on more than base compensation.

Users can create Compensation Packages containing salary or hourly wage information and employer-benefit information, compare multiple packages side-by-side, estimate employer-provided compensation, and visualize potential 401K growth.

The application is intended to support multiple stages of employment research. A Compensation Package may represent:

* A user's current position
* An active job offer
* A company or position the user is considering applying to
* A hypothetical compensation scenario

The application does not attempt to determine which employment opportunity is "best." Instead, it provides financial estimates and benefit information that help users evaluate tradeoffs themselves.

---

## 2. MVP Goals

The MVP will allow users to:

* Create and access a personal account.
* Create and manage employer Compensation Packages.
* Record salaried or hourly base compensation.
* Record selected employer benefits.
* Record partially known Compensation Packages.
* Calculate Estimated Annual Base Compensation for hourly positions.
* Calculate Estimated Employer-Provided Compensation.
* Compare 2–3 Compensation Packages side-by-side.
* Save and manage comparisons.
* Run estimated 401K retirement projections using a third-party projection service.
* Visualize retirement projections.
* Export saved comparisons as PDF reports.

The MVP should prioritize clear calculations, transparent assumptions, data persistence, and a focused user experience over comprehensive financial planning.

---

## 3. Core User Journey

The primary MVP workflow is:

1. User creates an account or logs in.
2. User creates a Compensation Package.
3. User selects whether the position is salaried or hourly.
4. User enters known compensation and benefit information.
5. User saves the Compensation Package.
6. User creates additional Compensation Packages as needed.
7. User selects 2–3 saved Compensation Packages for comparison.
8. Application normalizes base compensation into an annual value where necessary.
9. Application calculates and displays Estimated Employer-Provided Compensation.
10. User reviews monetary and non-monetary benefits side-by-side.
11. User may run a 401K projection against a saved Compensation Package.
12. Application displays the projection results and visualization.
13. User may save the comparison.
14. User may export a saved comparison as a PDF.

---

## 6. Required Compensation Package Information

The following fields are required to save a Compensation Package:

* Company Name
* Job Title
* Pay Type
* Required compensation fields associated with the selected Pay Type

All benefit information is optional.

A user may therefore save an incomplete Compensation Package and return later to add additional benefit information.

### Supported Pay Types

The MVP supports:

* **Annual Salary**
* **Hourly Wage**

### Annual Salary

When Pay Type is **Annual Salary**, the user must provide:

* Annual Salary

The salary must contain one annual dollar amount.

Salary ranges are not supported in MVP. When researching a position with a salary range, users may enter an estimated annual salary for calculation purposes.

### Hourly Wage

When Pay Type is **Hourly Wage**, the user must provide:

* Hourly Rate
* Expected Hours Per Week
* Expected Weeks Per Year

These values are used to calculate Estimated Annual Base Compensation.

Hourly calculations are estimates because actual hours worked may differ from the user's assumptions.

---

## 7. Base Compensation

**Base Compensation** is the application's general term for the primary compensation associated with a position.

A Compensation Package must use one of the supported Pay Types.

### Salaried Compensation

For salaried positions:

**Estimated Annual Base Compensation = Annual Salary**

Example:

Annual Salary: $125,000

Estimated Annual Base Compensation:

**$125,000**

### Hourly Compensation

For hourly positions:

**Estimated Annual Base Compensation = Hourly Rate × Expected Hours Per Week × Expected Weeks Per Year**

Example:

Hourly Rate: $32.50
Expected Hours Per Week: 40
Expected Weeks Per Year: 52

Calculation:

$32.50 × 40 × 52 = $67,600

Estimated Annual Base Compensation:

**$67,600**

Estimated Annual Base Compensation becomes the normalized annual value used by calculations that require annual base compensation.

The application should retain the original compensation inputs so hourly users can still see their hourly rate, expected hours, and expected weeks.

### MVP Hourly Compensation Limitations

The MVP does not model:

* Overtime
* Overtime multipliers
* Shift differentials
* Tips
* Commissions
* Irregular schedules
* Seasonal schedules
* Other variable compensation structures

These may be considered for future releases.

---

## 8. Annual Bonus

Annual Bonus is optional.

Users may enter a bonus as either:

* Annual dollar amount
* Percentage of Base Compensation

For salaried positions, percentage-based bonuses are calculated against Annual Salary.

For hourly positions, percentage-based bonuses are calculated against **Estimated Annual Base Compensation**.

### Salaried Example

Annual Salary: $120,000
Bonus: 10%

Estimated Annual Bonus:

$120,000 × 10% = **$12,000**

### Hourly Example

Hourly Rate: $32.50
Expected Hours Per Week: 40
Expected Weeks Per Year: 52

Estimated Annual Base Compensation:

$32.50 × 40 × 52 = $67,600

Bonus: 5%

Estimated Annual Bonus:

$67,600 × 5% = **$3,380**

---

## 11. 401K Employer Benefits

401K information describes what the employer provides and is separate from the user's personal retirement contribution assumptions.

### 401K Status

A Compensation Package supports:

* Yes
* No
* Unknown

If a 401K benefit is available, the user may enter:

* Employer Match Percentage
* Employer Match Limit Percentage
* Optional Vesting Period

Example employer policy:

> Employer matches 100% of employee contributions up to 5% of eligible compensation.

### Estimated Employer Contribution

For compensation-comparison purposes, the application calculates the **maximum available employer 401K contribution**, assuming the employee contributes enough to receive the full available match.

For salaried positions, the MVP uses Annual Salary as the annual compensation basis.

For hourly positions, the MVP uses Estimated Annual Base Compensation as the annual compensation basis.

Example hourly calculation:

Hourly Rate: $32.50
Expected Hours Per Week: 40
Expected Weeks Per Year: 52

Estimated Annual Base Compensation:

$67,600

Employer Match:

100% up to 4%

Maximum Estimated Employer Contribution:

$67,600 × 4% = **$2,704**

The user's actual contribution percentage is not stored as part of the Compensation Package.

Because employer plans may define eligible compensation differently, these calculations are estimates based on the compensation information supplied to the application.

### MVP Limitations

MVP does not support:

* Multi-tier matching formulas
* Non-elective employer retirement contributions
* Complex employer contribution formulas
* Vesting forfeiture calculations
* Employer-specific definitions of eligible compensation

---

## 12. Health Benefits

A Compensation Package supports one health-insurance coverage scenario in MVP.

### Coverage Types

Supported coverage types are:

* Employee Only
* Employee + Spouse
* Employee + Child
* Family

Coverage type is required only when health-benefit information is provided.

If health information has not been entered, its status remains **Unknown**.

### Health Benefit Fields

The user may enter:

* Employee Monthly Premium
* Employer Monthly Contribution
* Annual Deductible
* Employer HSA Contribution

### Health Calculations

The application calculates:

**Employee Annual Premium**

Employee Monthly Premium × 12

and:

**Employer Annual Health Contribution**

Employer Monthly Contribution × 12

Employer health contributions and Employer HSA contributions are included in Estimated Employer-Provided Compensation.

Employee premiums are displayed as costs but are **not deducted** from Estimated Employer-Provided Compensation.

Annual deductibles are displayed for comparison purposes but do not affect compensation calculations.

---

## 13. Parental Leave

MVP supports one simplified parental-leave scenario per Compensation Package.

Users may enter:

* Paid Leave Weeks
* Percentage of Base Compensation Paid
* Additional Unpaid Leave Weeks

Zero paid weeks indicates that the user has explicitly indicated no paid parental leave.

Missing parental-leave information means the benefit is **Unknown**.

### Estimated Leave Value

The application may calculate approximate base compensation protected during paid leave.

For salaried positions:

**Estimated Weekly Base Compensation = Annual Salary ÷ 52**

For hourly positions:

**Estimated Weekly Base Compensation = Hourly Rate × Expected Hours Per Week**

Estimated Paid Leave Value is then:

**Estimated Weekly Base Compensation × Paid Leave Weeks × Paid Percentage**

### Salaried Example

Annual Salary: $130,000

Estimated Weekly Base Compensation:

$130,000 ÷ 52 = $2,500

12 weeks at 100%:

$2,500 × 12 × 100% = **$30,000**

### Hourly Example

Hourly Rate: $30
Expected Hours Per Week: 40

Estimated Weekly Base Compensation:

$30 × 40 = $1,200

12 weeks at 100%:

$1,200 × 12 × 100% = **$14,400**

Parental-leave value is displayed separately from Estimated Employer-Provided Compensation.

It is **not added to annual compensation**, because paid leave generally represents compensation already included in Base Compensation.

---

## 15. Estimated Employer-Provided Compensation

The primary calculated compensation metric is called:

**Estimated Employer-Provided Compensation**

This terminology is used instead of presenting the result as definitive "Total Compensation."

Where sufficient information is available, the estimate may include:

* Estimated Annual Base Compensation
* Estimated Annual Bonus
* Maximum Available Employer 401K Contribution
* Employer Annual Health Contribution
* Employer HSA Contribution
* Other Compensation

For salaried positions:

**Estimated Annual Base Compensation = Annual Salary**

For hourly positions:

**Estimated Annual Base Compensation = Hourly Rate × Expected Hours Per Week × Expected Weeks Per Year**

The estimate does not include:

* Employee Health Premium
* Health Insurance Deductible
* Parental Leave Value
* Pension Value
* Unknown Benefits
* Notes

Calculations must distinguish between:

* Information not provided / Unknown
* A benefit explicitly entered as zero or not offered

Missing information must never silently be treated as a confirmed $0 benefit.

Hourly Estimated Employer-Provided Compensation must be clearly identified as an estimate because actual hours worked may differ.

---

## 18. Comparison Display

Comparisons display Compensation Packages side-by-side.

The comparison should present relevant information such as:

* Company
* Job Title
* Pay Type
* Annual Salary or Hourly Rate
* Expected Hours Per Week for hourly packages
* Expected Weeks Per Year for hourly packages
* Estimated Annual Base Compensation
* Annual Bonus
* Maximum Employer 401K Match
* Health Coverage Type
* Employer Health Contribution
* Employee Health Premium
* Annual Deductible
* Employer HSA Contribution
* Pension Availability
* Parental Leave
* Other Compensation
* Estimated Employer-Provided Compensation

This allows salaried and hourly opportunities to be compared using a normalized annual estimate while preserving visibility into how that estimate was derived.

For example:

|                       |    Company A |       Company B |
| --------------------- | -----------: | --------------: |
| Pay Type              |       Salary |          Hourly |
| Salary / Rate         | $75,000/year |     $32.50/hour |
| Expected Schedule     |            — | 40 hrs × 52 wks |
| Estimated Annual Base |      $75,000 |         $67,600 |
| Bonus                 |       $3,750 |          $3,380 |
| Max 401K Match        |       $3,750 |          $2,704 |
| Other Benefits        |            … |               … |

Benefits that are not included in Estimated Employer-Provided Compensation remain visible separately.

The application does **not** automatically select, rank, or recommend a winning Compensation Package in MVP.

---

## 23. 401K Retirement Projection

Users may run a retirement projection against a saved Compensation Package.

The projection uses employer information already contained in the package, including:

* Estimated Annual Base Compensation
* Employer 401K Match
* Employer Match Limit

For salaried Compensation Packages, Estimated Annual Base Compensation comes directly from Annual Salary.

For hourly Compensation Packages, Estimated Annual Base Compensation is calculated from:

**Hourly Rate × Expected Hours Per Week × Expected Weeks Per Year**

Additional projection assumptions are supplied by the user as required by the selected retirement-projection service.

Hourly retirement projections are estimates because actual annual earnings may vary based on hours worked.

The exact third-party projection provider is a technical implementation decision and is not defined by the MVP product requirements.

---

## 28. MVP Data Limits

The MVP establishes the following application limits:

| Resource                              |                        Limit |
| ------------------------------------- | ---------------------------: |
| Compensation Packages per user        |                           25 |
| Saved Comparisons per user            |                           10 |
| Compensation Packages per comparison  |                          2–3 |
| Other Compensation items per package  |                            5 |
| Health coverage scenarios per package |                            1 |
| Pay types                             | Annual Salary or Hourly Wage |

These limits are business rules and should not rely exclusively on frontend validation.

---

## 29. MVP Financial Transparency Principles

Financial calculations should follow these principles:

1. Clearly distinguish calculated values from user-provided estimates.
2. Clearly distinguish unknown information from confirmed zero values.
3. Do not double-count benefits already represented by Base Compensation.
4. Do not assign artificial monetary values to benefits that cannot be reasonably valued with available information.
5. Clearly communicate when an estimate is based on incomplete data.
6. Clearly identify annualized hourly compensation as an estimate.
7. Clearly disclose assumptions used in retirement projections.
8. Do not present calculations as guaranteed financial outcomes.
9. Do not recommend a particular employer or Compensation Package.

---

## 30. Post-MVP Backlog

The following capabilities are intentionally excluded from MVP and may be considered for future releases.

### Compensation

* Salary ranges
* Overtime calculations
* Overtime multipliers
* Shift differentials
* Tips
* Commissions
* Irregular work schedules
* Seasonal compensation
* Equity/RSU vesting modeling
* PTO valuation
* Stock purchase plans
* Tuition reimbursement modeling
* Childcare benefit modeling
* Commuter benefits
* Recurring/monthly custom compensation
* One-time compensation
* Advanced custom benefit types

### Retirement

* Multi-tier 401K matching formulas
* Non-elective employer contributions
* Internal retirement projection engine
* IRS contribution limits
* Catch-up contributions
* Detailed vesting modeling
* Saved retirement scenarios
* Side-by-side retirement scenarios

### Health Insurance

* Multiple health-plan tiers per Compensation Package
* Dynamic health-plan switching during comparisons

### Parental Leave

* Multi-stage paid-leave policies

### Pensions

* Pension value modeling
* Defined-benefit formulas
* Years-of-service projections
* Retirement income projections

### Comparisons

* Historical comparison snapshots
* Version history
* Duplicate comparisons
* Archived comparisons
* Favorite/pinned comparisons
* Comparison change history
* Create Compensation Packages from within the comparison workflow

### Sharing

* Secure read-only links
* Expiring links
* Share-link revocation
* Sharing permissions

### Accounts

* Password reset
* Social authentication
* Profile management
* Account deletion

### Forms

* Autosave
* Draft recovery
* Expiring drafts

### Family Financial Planning

* Two-earner household modeling
* Child savings
* College savings
* Tax optimization
* Social Security estimates
* HSA/FSA modeling
* Bank integrations
* Brokerage integrations

---

## 31. MVP Definition of Success

The MVP is successful when an authenticated user can:

1. Create and save multiple Compensation Packages.
2. Create either salaried or hourly Compensation Packages.
3. Enter partial or complete compensation and benefit information.
4. See hourly wages normalized into Estimated Annual Base Compensation.
5. Understand the assumptions used to annualize hourly compensation.
6. Understand which benefit information is known, unknown, or explicitly zero.
7. View Estimated Employer-Provided Compensation.
8. Compare 2–3 saved Compensation Packages side-by-side, including comparisons between salaried and hourly opportunities.
9. Save and manage comparisons.
10. Reuse Compensation Packages across multiple comparisons.
11. Run an estimated 401K retirement projection against a saved package.
12. View retirement projection data visually.
13. Export a saved comparison as a PDF.
14. Access only their own Compensation Packages and comparisons.

The MVP should provide enough information for users to understand meaningful differences between employment compensation packages without attempting to make employment or financial decisions on their behalf.
