# AI Session: Piece vs Case in E-commerce Product Pricing

## Session Goal
Understand the difference between **Piece** and **Case** in an e-commerce product catalog and identify relevant QA test scenarios.

## 1. Product Table Shared

| SKU | Product | Category | Piece | Case | Tax |
|---|---|---|---:|---:|---:|
| SKU-0289 | Chef's Butter | Dairy | ₹249.99 | 12 × ₹2759.89 (as displayed) | 5% |

## 2. Piece vs Case

- **Piece:** The price of one individual unit of a product.
- **Case:** A bulk pack containing a specified number of individual pieces. In this example, the case appears to contain 12 pieces, but the exact meaning of the displayed case price should be confirmed.

## 3. Pricing Calculation Check

If one piece costs ₹249.99 and a case contains 12 pieces:

₹249.99 × 12 = **₹2,999.88**

The displayed value is **₹2,759.89**, so it does not match the simple calculation above. This should be treated as a potential discrepancy, not automatically as a confirmed defect, until the product/business rules are checked.

## 4. QA Test Scenarios

1. Verify that the individual piece price is displayed correctly.
2. Verify that the configured case size is 12 pieces.
3. Verify that the case price follows the configured pricing rules.
4. Verify that the 5% tax is calculated and displayed correctly.
5. Confirm whether the displayed case price includes or excludes tax.
6. Confirm whether `12 × ₹2,759.89` means one case contains 12 pieces at a total of ₹2,759.89, or whether it represents 12 cases at ₹2,759.89 each.
7. Verify that cart and checkout totals use the correct unit/case price and tax rules.
8. Verify that inventory is reduced in the correct unit (pieces or cases) after an order, according to the business rules.

## 5. Questions for the Product Owner

- Does one case contain exactly 12 pieces?
- Is the case price the total price for all 12 pieces, or is it a per-case unit price shown alongside a quantity?
- Is the case price discounted compared with buying 12 individual pieces?
- Are prices shown before or after tax?
- How should inventory be stored and deducted when a buyer orders a case?

## 6. Working Assumption

For the initial calculation only, assume that one piece costs ₹249.99 and one case contains 12 pieces. Under that assumption, the straightforward case total is ₹2,999.88 before any separately applied tax or discount. Confirm the actual business rules before treating the displayed amount as a defect.

## 7. Key Takeaway

**Piece = one individual unit. Case = a bulk pack containing multiple pieces.** The exact case-price interpretation and tax treatment must be confirmed from the product requirements.
