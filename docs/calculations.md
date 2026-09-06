# Family Benefits & 401K Calculator — Calculation Model

## 1. Purpose

This document defines the financial calculations, assumptions, validation rules, and calculation behavior used by the Family Benefits & 401K Calculator MVP.

The goal is to provide consistent and transparent estimates that allow users to compare Compensation Packages without presenting those estimates as guaranteed financial outcomes.

This document serves as the source of truth for calculation-related business rules used during database, API, backend, and frontend design.

---

# 2. General Calculation Principles

The MVP follows these principles:

* Calculations are estimates based on user-provided information and, where applicable, third-party projection assumptions.
* Unknown values must never automatically be treated as zero.
* Explicit zero values represent known zero amounts.
* Derived values should be calculated from their source inputs rather than entered directly by the user.
* Intermediate calculations should not be unnecessarily rounded.
* Monetary results should be displayed to two decimal places when necessary.
* Monetary inputs may contain dollars and cents.
* Monetary values cannot be negative unless a future feature explicitly requires negative values.
* Calculated values must be distinguishable from user-provided estimates.
* A monetary value should not be counted more than once in Estimated Employer-Provided Compensation.
* Invalid input should produce validation feedback rather than being silently corrected.
* Partial calculations are allowed when sufficient information exists to calculate a known portion of a Compensation Package.
* Financial projections are estimates and do not represent guaranteed financial outcomes or financial advice.

---

# 3. Estimated Annual Base Compensation

The system supports two pay types:

* Annual Salary
* Hourly Wage

Regardless of pay type, the system normalizes compensation into an Estimated Annual Base Compensation value.

This normalized value is used by downstream calculations.

## 3.1 Annual Salary

For salaried Compensation Packages:

```text
Estimated Annual Base Compensation
=
Annual Salary
```

Example:

```text
Annual Salary = $120,000

Estimated Annual Base Compensation
= $120,000
```

Annual Salary must be greater than $0.

---

## 3.2 Hourly Wage

For hourly Compensation Packages:

```text
Estimated Annual Base Compensation
=
Hourly Rate
× Expected Hours Per Week
× Expected Weeks Per Year
```

Example:

```text
Hourly Rate = $32.50
Expected Hours Per Week = 37.5
Expected Weeks Per Year = 50

$32.50 × 37.5 × 50
= $60,937.50
```

Therefore:

```text
Estimated Annual Base Compensation
= $60,937.50
```

### Hourly Input Rules

Hourly Rate:

* Must be greater than $0.
* May contain dollars and cents.

Expected Hours Per Week:

* Must be greater than 0.
* Cannot exceed 168.
* May contain decimal hours.

Decimal hours represent fractions of an hour.

Examples:

```text
37.5  = 37 hours 30 minutes
37.25 = 37 hours 15 minutes
37.75 = 37 hours 45 minutes
37.2  = 37 hours 12 minutes
```

Expected Weeks Per Year:

* Must be a whole number.
* Minimum: 1
* Maximum: 52

Hourly annual compensation must be clearly identified as an estimate because actual hours worked may vary.

---

# 4. Annual Bonus

Annual Bonus is optional.

A bonus may be entered as either:

* Annual Dollar Amount
* Percentage of Estimated Annual Base Compensation

## 4.1 Dollar Bonus

When entered as a dollar amount:

```text
Estimated Annual Bonus
=
Entered Annual Bonus Amount
```

Example:

```text
Annual Bonus = $10,000

Estimated Annual Bonus
= $10,000
```

---

## 4.2 Percentage Bonus

When entered as a percentage:

```text
Estimated Annual Bonus
=
Estimated Annual Base Compensation
× Bonus Percentage
```

Example:

```text
Estimated Annual Base Compensation = $120,000
Bonus Percentage = 10%

$120,000 × 10%
= $12,000
```

The same calculation applies to salaried and hourly Compensation Packages because both use Estimated Annual Base Compensation.

Bonus percentages may exceed 100%.

The MVP does not impose a product-level maximum bonus percentage.

A missing bonus is Unknown.

An explicitly entered zero bonus represents no expected annual bonus.

---

# 5. Employer 401K Match

Employer 401K Match Status supports:

```text
Yes
No
Unknown
```

## 5.1 Match Status — Yes

When Employer 401K Match Status is Yes, the following fields are required:

* Employer Match Percentage
* Employer Match Limit Percentage

### Employer Match Percentage

* Must be greater than 0%.
* Decimals are allowed.
* Values greater than 100% are allowed.

### Employer Match Limit

* Minimum: 1%
* Maximum: 100%
* Decimals are allowed.

A 0% match limit is not valid when Employer 401K Match Status is Yes.

If the employer does not provide a match, the user should select No instead.

---

## 5.2 Maximum Available Employer 401K Contribution

The MVP estimates the maximum employer contribution by assuming the employee contributes enough to receive the full available employer match.

```text
Maximum Available Employer 401K Contribution
=
Estimated Annual Base Compensation
× Employer Match Limit Percentage
× Employer Match Percentage
```

Example:

```text
Estimated Annual Base Compensation = $120,000
Employer Match Percentage = 50%
Employer Match Limit = 6%

$120,000 × 6% × 50%
= $3,600
```

Therefore:

```text
Maximum Available Employer 401K Contribution
= $3,600
```

The employee's actual contribution percentage is not used in this Compensation Package calculation. Employee contribution percentage is used when running a retirement projection.

---

## 5.3 Match Status — No

When Employer 401K Match Status is No:

```text
Maximum Available Employer 401K Contribution
= $0
```

This represents an explicit known zero.

---

## 5.4 Match Status — Unknown

When Employer 401K Match Status is Unknown:

```text
Maximum Available Employer 401K Contribution
= Unknown
```

Unknown must not be converted to $0.

---

## 5.5 Vesting Period

Employer 401K vesting information may be stored and displayed.

Vesting does not alter Maximum Available Employer 401K Contribution in the MVP.

The MVP does not attempt to determine how much of an employer contribution the employee would retain based on employment duration.

---

## 5.6 Complex Match Formulas

Multi-tier employer match formulas are out of scope for MVP.

Example:

```text
100% of the first 3%
+
50% of the next 2%
```

The MVP supports one Employer Match Percentage and one Employer Match Limit Percentage.

---

# 6. Medical Insurance

Medical Insurance Status supports:

```text
Yes
No
Unknown
```

When Medical Insurance Status is Yes, Coverage Type is required.

Supported Coverage Types:

* Employee Only
* Employee + Spouse
* Employee + Child
* Family

Medical monetary fields may individually remain Unknown.

Supported fields include:

* Employee Monthly Premium
* Employer Monthly Contribution
* Annual Deductible
* Employer HSA Contribution

Monetary values may contain dollars and cents and cannot be negative.

Explicit $0 values are allowed.

---

## 6.1 Annual Employee Medical Premium

```text
Annual Employee Medical Premium
=
Employee Monthly Premium × 12
```

Example:

```text
Employee Monthly Premium = $250

$250 × 12
= $3,000
```

Annual Employee Medical Premium is displayed as an employee cost.

It is not deducted from Estimated Employer-Provided Compensation.

---

## 6.2 Employer Annual Medical Contribution

```text
Employer Annual Medical Contribution
=
Employer Monthly Contribution × 12
```

Example:

```text
Employer Monthly Contribution = $600

$600 × 12
= $7,200
```

Employer Annual Medical Contribution is included in Estimated Employer-Provided Compensation.

---

## 6.3 Employer HSA Contribution

Employer HSA Contribution is included in Estimated Employer-Provided Compensation.

Example:

```text
Employer HSA Contribution = $1,500

Employer-provided value included
= $1,500
```

---

## 6.4 Medical Deductible

Annual Deductible is displayed for comparison purposes.

It is not included in Estimated Employer-Provided Compensation because it represents potential healthcare cost exposure rather than guaranteed employer compensation or guaranteed employee spending.

---

## 6.5 Medical Insurance — No

When Medical Insurance Status is No, employer medical and HSA contribution values are treated as explicit $0 values for the employer-provided compensation calculation.

---

## 6.6 Medical Insurance — Unknown

When Medical Insurance Status is Unknown, employer medical and HSA contribution values remain Unknown.

They must not be converted to $0.

---

# 7. Dental Insurance

Dental Insurance Status supports:

```text
Yes
No
Unknown
```

When Dental Insurance Status is Yes, Coverage Type is required.

Supported Coverage Types:

* Employee Only
* Employee + Spouse
* Employee + Child
* Family

Supported MVP fields:

* Employee Monthly Premium
* Annual Deductible

Premium and deductible may remain Unknown even when Dental Insurance Status is Yes.

## 7.1 Annual Employee Dental Premium

```text
Annual Employee Dental Premium
=
Employee Monthly Dental Premium × 12
```

Annual Employee Dental Premium is displayed as an employee cost.

It is not included in Estimated Employer-Provided Compensation.

Dental deductible is informational only and is not included in Estimated Employer-Provided Compensation.

The MVP does not calculate an employer-provided monetary value for dental insurance.

---

# 8. Vision Insurance

Vision Insurance Status supports:

```text
Yes
No
Unknown
```

When Vision Insurance Status is Yes, Coverage Type is required.

Supported Coverage Types:

* Employee Only
* Employee + Spouse
* Employee + Child
* Family

Supported MVP fields:

* Employee Monthly Premium
* Annual Deductible

Premium and deductible may remain Unknown even when Vision Insurance Status is Yes.

## 8.1 Annual Employee Vision Premium

```text
Annual Employee Vision Premium
=
Employee Monthly Vision Premium × 12
```

Annual Employee Vision Premium is displayed as an employee cost.

It is not included in Estimated Employer-Provided Compensation.

Vision deductible is informational only and is not included in Estimated Employer-Provided Compensation.

The MVP does not calculate an employer-provided monetary value for vision insurance.

---

# 9. Parental Leave

Parental Leave Status supports:

```text
Yes
No
Unknown
```

Yes means the employer has a known parental-leave policy. It does not necessarily mean that the employer provides paid parental leave.

Supported fields:

* Paid Leave Weeks
* Percentage of Base Compensation Paid
* Additional Unpaid Leave Weeks

---

## 9.1 Paid Leave Weeks

Paid Leave Weeks:

* May contain decimals.
* May be 0.
* Cannot be negative.

An explicit 0 means the employer provides no paid weeks under the policy being modeled.

A missing value remains Unknown.

---

## 9.2 Percentage Paid

Percentage of Base Compensation Paid:

* Must be a whole-number percentage.
* Minimum: 0%
* Maximum: 100%.

Example:

```text
100%  Valid
80%   Valid
60%   Valid

66.5% Invalid
```

---

## 9.3 Additional Unpaid Leave

Additional Unpaid Leave Weeks:

* May contain decimals.
* May be 0.
* Cannot be negative.

---

## 9.4 Salaried Weekly Base Compensation

For salaried Compensation Packages:

```text
Weekly Base Compensation
=
Annual Salary ÷ 52
```

---

## 9.5 Hourly Weekly Base Compensation

For hourly Compensation Packages:

```text
Weekly Base Compensation
=
Hourly Rate × Expected Hours Per Week
```

---

## 9.6 Estimated Paid Parental Leave Value

```text
Estimated Paid Parental Leave Value
=
Weekly Base Compensation
× Paid Leave Weeks
× Percentage Paid
```

Example:

```text
Annual Salary = $104,000
Paid Leave = 12 weeks
Percentage Paid = 100%

Weekly Base Compensation
= $104,000 ÷ 52
= $2,000

Estimated Paid Parental Leave Value
= $2,000 × 12 × 100%
= $24,000
```

Estimated Paid Parental Leave Value is displayed separately.

It is not added to Estimated Employer-Provided Compensation because doing so would double-count compensation already represented by Annual Salary or Estimated Annual Base Compensation.

---

# 10. Pension Benefits

Pension Status supports:

```text
Yes
No
Unknown
```

Supported MVP information:

* Pension Type
* Vesting Period
* Plan Details / Formula

Pension Type may include:

* Defined Benefit
* Cash Balance
* Other
* Unknown

Plan Details / Formula is optional free-form information describing the employer's pension policy.

Example:

```text
1.5% × years of service × final average salary
```

The MVP does not calculate an estimated monetary pension value.

When Pension Status is Yes:

```text
Estimated Pension Value
= Not Calculated
```

When Pension Status is No:

```text
Estimated Pension Value
= Not Applicable
```

When Pension Status is Unknown:

```text
Estimated Pension Value
= Unknown
```

Pension benefits are not included in Estimated Employer-Provided Compensation.

Advanced pension modeling is outside MVP scope.

---

# 11. Other Compensation

A Compensation Package may contain up to five Other Compensation items.

Each item contains:

* Description
* Estimated Annual Value

Description is required when an item is added.

Estimated Annual Value is required when an item is added.

Estimated Annual Value:

* May contain dollars and cents.
* Must be greater than or equal to $0.
* Is considered a user-provided estimate.

Example:

```text
Tuition Assistance   $5,000
Wellness Stipend       $600
Phone Stipend        $1,200
```

## 11.1 Total Other Compensation

```text
Total Other Compensation
=
Sum of all Other Compensation item values
```

Example:

```text
$5,000 + $600 + $1,200
= $6,800
```

Total Other Compensation is included in Estimated Employer-Provided Compensation.

If there are no Other Compensation items:

```text
Total Other Compensation
= $0
```

Other Compensation does not use a Yes / No / Unknown status.

Benefits without a defensible annual monetary estimate should be documented in Notes instead.

Monthly, recurring-frequency, one-time, equity-vesting, and similar advanced compensation modeling are outside MVP scope.

---

# 12. Estimated Employer-Provided Compensation

Estimated Employer-Provided Compensation represents the known annual monetary value provided by the employer that the MVP can reasonably estimate.

It is calculated as:

```text
Estimated Employer-Provided Compensation
=
Estimated Annual Base Compensation
+ Estimated Annual Bonus
+ Maximum Available Employer 401K Contribution
+ Employer Annual Medical Contribution
+ Employer HSA Contribution
+ Total Other Compensation
```

Example:

```text
Estimated Annual Base Compensation       $120,000
Estimated Annual Bonus                     12,000
Maximum Employer 401K Contribution          6,000
Employer Annual Medical Contribution        8,400
Employer HSA Contribution                   1,500
Other Compensation                          2,000
                                          --------
Estimated Employer-Provided Compensation $149,900
```

---

## 12.1 Values Not Included

The following values are not included:

* Employee Medical Premium
* Medical Deductible
* Employee Dental Premium
* Dental Deductible
* Employee Vision Premium
* Vision Deductible
* Estimated Paid Parental Leave Value
* Pension Value
* 401K Vesting Period
* Notes

These values may still be displayed when comparing Compensation Packages.

---

# 13. Partial Compensation Estimates

Unknown values must not be converted to zero.

If one or more components of Estimated Employer-Provided Compensation are Unknown, the system calculates the known portion and identifies the result as a partial estimate.

Example:

```text
Estimated Annual Base Compensation       $120,000
Estimated Annual Bonus                     10,000
Employer 401K Contribution                Unknown
Employer Annual Medical Contribution        8,400
Employer HSA Contribution                 Unknown
Other Compensation                          2,000
```

Known value:

```text
$120,000 + $10,000 + $8,400 + $2,000
= $140,400
```

The system may represent this logically as:

```text
value: $140,400
calculationStatus: Partial
```

The UI should identify which relevant components remain Unknown.

The exact visual representation of a partial estimate is a frontend design decision.

A calculation is Complete only when every component relevant to the calculation is known, including explicit zero values.

---

# 14. 401K Retirement Projection

401K retirement growth projections are separate from the employer 401K match calculation.

The MVP will use a third-party retirement projection service rather than implementing a complete internal retirement-growth calculation engine.

## 14.1 Compensation Package Inputs

The retirement projection may use information from a saved Compensation Package, including:

* Estimated Annual Base Compensation
* Employer Match Percentage
* Employer Match Limit Percentage

## 14.2 User Projection Inputs

The user provides projection-specific information such as:

* Current 401K Balance
* Employee Contribution Percentage
* Current Age
* Retirement Age

Projection years are derived from:

```text
Projection Years
=
Retirement Age - Current Age
```

A separate Projection Years field is not required.

---

# 15. Projection Rate of Return

When supported, the selected third-party retirement projection service should provide the projection methodology or default expected annual rate of return.

The MVP should avoid requiring the user to independently determine an expected annual rate of return when the provider supplies a documented assumption.

The rate or methodology used by the projection should be displayed to the user when available.

Example:

```text
Projection Assumptions

Annual Rate of Return: 7%
Source: Projection provider assumption
```

The displayed assumption does not represent a guaranteed investment return.

Custom user-defined rate-of-return overrides are outside MVP scope unless required by the selected provider.

---

# 16. Third-Party Projection Architecture

Angular must not directly communicate with the external retirement projection provider.

The expected application flow is:

```text
Angular Application
        ↓
Node / Express API
        ↓
Retirement Projection Service
        ↓
Third-Party Projection API
        ↓
Normalize Provider Response
        ↓
Application Projection Response
        ↓
Angular Visualization
```

The backend is responsible for:

* Protecting provider credentials.
* Validating projection requests.
* Communicating with the provider.
* Handling provider timeouts and errors.
* Normalizing provider-specific responses.
* Returning an application-owned response contract.

Angular should not depend directly on the third-party provider's response format.

---

# 17. Projection Results

The normalized projection should support a summary and year-by-year projection data suitable for visualization.

Conceptually:

```text
Projection
├── Starting Balance
├── Projected Retirement Balance
├── Projection Years
├── Provider Assumptions
└── Yearly Projection
    ├── Year
    └── Projected Balance
```

The exact API response contract will be defined during API design.

---

# 18. Projection Failure Behavior

If the third-party provider:

* Is unavailable,
* Times out,
* Returns an error, or
* Returns unusable projection data,

the application must not fabricate a retirement projection.

Projection failure must not modify or invalidate the user's saved Compensation Package or saved comparisons.

An internal fallback retirement projection engine is outside MVP scope.

---

# 19. Saving Retirement Projections

Individual retirement projection scenarios and projection results are not persisted in the MVP.

Users may run a projection against a saved Compensation Package and view the resulting projection.

Saved projection scenarios, named projection assumptions, and projection history are post-MVP functionality.

---

# 20. Precision and Rounding

Calculations should retain appropriate numeric precision internally.

Intermediate calculations should not be rounded solely for display purposes.

Final monetary values are rounded to the nearest cent when required for display.

Example:

```text
Calculated value:
$60,937.4375

Displayed:
$60,937.44
```

Values must not automatically be rounded upward to the next whole dollar.

Database numeric representation will be determined during database design.

---

# 21. Unknown vs. Explicit Zero

Unknown and zero represent different business states.

Example:

```text
Employer 401K Match = Unknown
```

means the user does not know whether an employer contribution exists.

It does not mean:

```text
Employer 401K Contribution = $0
```

Similarly:

```text
Paid Parental Leave = Unknown
```

is different from:

```text
Paid Parental Leave = 0 weeks
```

Calculations must preserve this distinction.

---

# 22. Derived Values

Users should not manually enter values that can be reliably derived from existing inputs.

Examples of derived values include:

* Estimated Annual Base Compensation
* Estimated Annual Bonus when entered as a percentage
* Maximum Available Employer 401K Contribution
* Annual Employee Medical Premium
* Employer Annual Medical Contribution
* Annual Employee Dental Premium
* Annual Employee Vision Premium
* Estimated Paid Parental Leave Value
* Total Other Compensation
* Estimated Employer-Provided Compensation
* Projection Years

Derived values should be recalculated when their source inputs change.

---

# 23. Financial Transparency

The MVP must clearly communicate that:

* Compensation calculations are estimates.
* Hourly annual compensation depends on expected hours and weeks supplied by the user.
* Other Compensation values are user-provided estimates.
* Unknown values may cause Estimated Employer-Provided Compensation to be partial.
* Retirement projections depend on assumptions and third-party methodology.
* Retirement projections do not guarantee future investment performance.
* The application does not provide investment, tax, or financial advice.

The application should provide users with the information used to produce an estimate rather than presenting calculated values as guaranteed outcomes.

---

# 24. Calculation Features Outside MVP Scope

The following calculation capabilities are outside the MVP:

* Multi-tier 401K match formulas
* Non-elective employer retirement contributions
* Detailed 401K vesting calculations
* Internal retirement projection fallback engine
* Saved retirement projection scenarios
* Custom retirement return assumptions unless required by the provider
* Advanced pension valuation
* Pension retirement-income projections
* Multiple medical-plan tiers per Compensation Package
* Employer monetary valuation of dental insurance
* Employer monetary valuation of vision insurance
* Multi-stage parental-leave policies
* PTO valuation
* Equity / RSU vesting calculations
* Overtime calculations
* Shift differentials
* Tips
* Commissions
* Irregular or seasonal compensation modeling
* Tax calculations
* Social Security projections
* Investment recommendations
