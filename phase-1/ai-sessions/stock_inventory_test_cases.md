# Stock Inventory Test Cases

## Requirement

- Stock is maintained in **pieces**.
- A case contains a predefined number of pieces.
- An order must never exceed the available stock.
- When two buyers order simultaneously, the system must prevent overselling.

## Test Data

| Parameter | Value |
|---|---:|
| Product | Product A |
| Available stock | 100 pieces |
| Case size | 10 pieces |
| Stock in cases (equivalent) | 10 cases |
| Minimum order quantity | 1 piece (assumption) |

## 1. Positive Test Cases

**Expected outcome:** Valid orders are accepted and stock is updated correctly.

| TC ID | Test scenario | Test steps | Test data | Expected result | Priority |
|---|---|---|---|---|---|
| TC_POS_01 | Order a single piece | Add 1 piece to cart and place order | Qty: 1 | Order succeeds; stock becomes 99 | High |
| TC_POS_02 | Order multiple pieces | Place an order for multiple pieces | Qty: 25 | Order succeeds; stock becomes 75 | High |
| TC_POS_03 | Order one full case | Order the exact case quantity | Qty: 10 | Order succeeds; stock becomes 90 | High |
| TC_POS_04 | Order multiple full cases | Order 5 cases | 5 × 10 = 50 pieces | Order succeeds; stock becomes 50 | High |
| TC_POS_05 | Order a partial case | Order fewer pieces than a full case | Qty: 7 | Order succeeds if partial-case ordering is supported; stock becomes 93 | Medium |
| TC_POS_06 | Order the entire available stock | Order all available pieces | Qty: 100 | Order succeeds; stock becomes 0 | High |
| TC_POS_07 | Place sequential orders within stock | Buyer 1 orders 30 pieces; Buyer 2 orders 40 pieces | Total: 70 | Both orders succeed; stock becomes 30 | High |
| TC_POS_08 | Verify stock after order confirmation | Place an order and check inventory | Qty: 20 | Stock decreases exactly once by 20 pieces | High |

## 2. Negative Test Cases

**Expected outcome:** Invalid orders are rejected without incorrectly reducing stock.

| TC ID | Test scenario | Test steps | Test data | Expected result | Priority |
|---|---|---|---|---|---|
| TC_NEG_01 | Order more than available stock | Submit an order exceeding inventory | Qty: 101 | Order is rejected; stock remains 100 | Critical |
| TC_NEG_02 | Order one piece above remaining stock | Reduce stock to 5, then order 6 pieces | Stock: 5, Qty: 6 | Order is rejected; stock remains 5 | Critical |
| TC_NEG_03 | Submit zero quantity | Attempt to order zero pieces | Qty: 0 | Validation error; no order is placed | High |
| TC_NEG_04 | Submit negative quantity | Attempt to order a negative quantity | Qty: -1 | Validation error; stock remains unchanged | High |
| TC_NEG_05 | Submit a non-integer quantity | Enter a fractional number of pieces | Qty: 2.5 | Rejected if stock is restricted to whole pieces | High |
| TC_NEG_06 | Submit an extremely large quantity | Submit a quantity exceeding available inventory and supported limits | Qty: 999999 | Request is rejected safely; no stock corruption occurs | High |
| TC_NEG_07 | Bypass UI validation | Send an order request directly through the API with excessive quantity | Qty: 101 | Server rejects the request; UI validation alone is not relied upon | Critical |
| TC_NEG_08 | Duplicate order submission | Submit the same order request twice due to a retry or double-click | Qty: 60; stock: 100 | Duplicate processing is prevented when the same idempotency key is reused; stock is not incorrectly deducted twice | Critical |

## 3. Edge and Boundary Test Cases

These tests validate stock boundaries.

| TC ID | Test scenario | Test steps | Test data | Expected result | Priority |
|---|---|---|---|---|---|
| TC_EDGE_01 | Order exactly the available stock | Submit an order equal to current inventory | Stock: 100, Qty: 100 | Order succeeds; stock becomes 0 | Critical |
| TC_EDGE_02 | Order one piece below available stock | Submit an order for one less than inventory | Stock: 100, Qty: 99 | Order succeeds; stock becomes 1 | High |
| TC_EDGE_03 | Order one piece above available stock | Submit an order for one more than inventory | Stock: 100, Qty: 101 | Order is rejected; stock remains 100 | Critical |
| TC_EDGE_04 | Place an order when stock is zero | Attempt to order a product with no inventory | Stock: 0, Qty: 1 | Order is rejected | Critical |
| TC_EDGE_05 | Place an order when stock is one piece | Order the last available piece | Stock: 1, Qty: 1 | Order succeeds; stock becomes 0 | Critical |
| TC_EDGE_06 | Convert cases to pieces correctly | Order 3 cases | Case size: 10; Qty: 3 cases | System deducts 30 pieces, not 3 or 300 | Critical |
| TC_EDGE_07 | Handle stock not divisible by case size | Order cases when stock is 25 pieces | Case size: 10; order: 3 cases | Order is rejected because 30 pieces are unavailable | Critical |
| TC_EDGE_08 | Change case size configuration | Update case size and submit a new case order | New case size: 12; Qty: 2 cases | System deducts 24 pieces using the applicable valid configuration | High |
| TC_EDGE_09 | Two buyers order the final stock | Two buyers each order 1 piece when stock is 1 | Stock: 1; Qty: 1 each | Only one order succeeds; stock never becomes negative | Critical |
| TC_EDGE_10 | Stock changes between cart and checkout | Buyer adds 80 pieces to cart; another buyer purchases 30 from stock of 100; first buyer checks out | First: 80; second: 30 | First order is rejected or adjusted according to the defined policy; stock must not become negative | Critical |

## 4. Concurrent-Order Test Cases

These are the most important tests for preventing overselling.

| TC ID | Test scenario | Test steps | Test data | Expected result | Priority |
|---|---|---|---|---|---|
| TC_CON_01 | Two buyers order simultaneously within stock | Buyer A and Buyer B each order 30 pieces at the same time | Stock: 100; total: 60 | Both succeed; remaining stock is 40 | Critical |
| TC_CON_02 | Two buyers compete for insufficient stock | Buyer A and Buyer B simultaneously order 60 pieces each | Stock: 100; total: 120 | At most one order succeeds; stock remains non-negative | Critical |
| TC_CON_03 | Concurrent orders equal total stock | Two buyers simultaneously order 50 pieces each | Stock: 100; total: 100 | Both may succeed; final stock is 0 | Critical |
| TC_CON_04 | Multiple buyers compete for the last pieces | Five buyers simultaneously order 1 piece each | Stock: 3 | Exactly 3 orders succeed; 2 are rejected; stock becomes 0 | Critical |
| TC_CON_05 | Simultaneous case orders | Two buyers each order 6 cases | Stock: 100; case size: 10 | Only one order can succeed in full, unless partial fulfillment is explicitly supported; stock must not become negative | Critical |
| TC_CON_06 | Concurrent requests through the API | Send multiple parallel order requests for the same product | Stock: 100; 10 requests × 20 pieces | No more than 5 full orders succeed; stock remains non-negative | Critical |
| TC_CON_07 | Concurrent orders for different products | Two buyers order different products at the same time | Product A: 10; Product B: 20 | Each product's inventory is updated independently and accurately | High |
| TC_CON_08 | Concurrent order and cancellation | One buyer places an order while another cancels a previously confirmed order | Defined starting stock and order quantity | Stock reflects each transaction exactly once, without lost or duplicate updates | High |

## 5. Actual Result and Execution Status

Fill these fields when executing the test cases in the application.

| TC ID | Actual result | Status |
|---|---|---|
| TC_POS_01 | To be updated during execution | Not Run |
| TC_NEG_01 | To be updated during execution | Not Run |
| TC_EDGE_01 | To be updated during execution | Not Run |
| TC_CON_02 | To be updated during execution | Not Run |
| TC_CON_04 | To be updated during execution | Not Run |
| TC_CON_06 | To be updated during execution | Not Run |

## 6. Key QA Validations

For concurrent-order testing, validate all three layers:

1. **UI:** The buyer receives a clear out-of-stock or insufficient-stock message.
2. **API:** The server validates current stock at the time of order processing, not just when the product is added to the cart.
3. **Database:** Inventory updates are atomic or transactionally protected, so concurrent requests cannot cause overselling.

### Critical Acceptance Criteria

For every valid execution, the final available stock must never be negative, and the total quantity of successfully allocated orders must never exceed the available inventory, accounting for any explicitly supported stock reservations and cancellations.

## 7. Questions and Assumptions to Confirm

- Is partial-case ordering allowed, or must buyers order only full cases?
- Is inventory deducted when the buyer places an order, when payment succeeds, or when the seller approves the order?
- Are partial fulfilment or automatic quantity adjustment supported when stock changes before checkout?
- What should happen when an order is cancelled or a payment fails?
- Does the system use stock reservations while an order is pending?
- Should duplicate submissions be protected through an idempotency key or equivalent mechanism?

**Assumptions used:** Stock is tracked in whole pieces, the example case size is 10 pieces, and the initial available stock is 100 pieces. Any behavior not specified in the requirement should be confirmed with the product owner.
