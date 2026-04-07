# 📦 StockFlow -- Backend Case Study (Bynry)

## 👤 Candidate: Adwait Bhavthankar

This submission is focused not just on solving the problem, but
demonstrating: - Strong backend fundamentals - Real-world system
thinking - Clear communication of decisions and trade-offs

I have intentionally explained my reasoning in detail so it reflects how
I think during actual development.

------------------------------------------------------------------------

# 🧩 Part 1: Code Review & Debugging

## 🔍 Problematic Code Analysis (Line-by-Line Thinking)

### ❌ Issue 1: No Input Validation

Line: data\['name'\], data\['sku'\], data\['price'\]

**Technical Problem:** - Direct dictionary access → KeyError if missing

**Production Impact:** - API crashes (500 error) - No clear message to
client

**Best Practice:** - Validate inputs before usage - Return 400 for bad
requests

------------------------------------------------------------------------

### ❌ Issue 2: SKU Uniqueness Not Enforced

Line: sku=data\['sku'\]

**Technical Problem:** - No uniqueness validation - No DB constraint

**Business Impact:** - SKU is identity → duplicates break: - inventory
tracking - order mapping

**Decision:** - Add UNIQUE constraint + API validation

------------------------------------------------------------------------

### ❌ Issue 3: Multiple DB Commits (Atomicity Violation)

Lines: db.session.commit() db.session.commit()

**Technical Problem:** - Breaks transaction atomicity

**Real-world Scenario:** - Product saved - Inventory fails →
inconsistent system

**Fix:** - Single transaction

------------------------------------------------------------------------

### ❌ Issue 4: No Error Handling

**Problem:** - No rollback

**Impact:** - Partial writes - Hard debugging

------------------------------------------------------------------------

### ❌ Issue 5: Incorrect Data Modeling

Line: warehouse_id in Product

**Problem:** - Violates requirement (multi-warehouse support)

**Design Fix:** - Product independent - Inventory maps product ↔
warehouse

------------------------------------------------------------------------

### ❌ Issue 6: No Data Validation (Quantity)

**Problem:** - Negative or invalid values allowed

**Impact:** - Incorrect alerts and reporting

------------------------------------------------------------------------

### ❌ Issue 7: Optional Fields Not Handled

**Problem:** - Missing fields crash API

------------------------------------------------------------------------

### ❌ Issue 8: No Idempotency

**Problem:** - Retry request → duplicate data

**Real-world constraint:** - Network retries are common

------------------------------------------------------------------------

## ✅ Final Fix Philosophy

Instead of just fixing syntax: - Ensured atomic transactions - Improved
data model - Added validation and safeguards

------------------------------------------------------------------------

# 🗄️ Part 2: Database Design

## 🧱 Schema (Designed for Scalability)

Tables: - Company - Warehouse - Product - Inventory - Supplier -
ProductSupplier - InventoryLog - Sales

------------------------------------------------------------------------

## 🧠 Design Thinking

### 1. Separation of Concerns

-   Product ≠ Inventory
-   Allows multi-warehouse scaling

------------------------------------------------------------------------

### 2. Business Alignment

-   SKU unique → real-world identifier
-   Suppliers mapped flexibly

------------------------------------------------------------------------

### 3. Observability

-   InventoryLog → track stock changes

------------------------------------------------------------------------

### 4. Scalability Considerations

Indexes: - SKU - product_id + warehouse_id - created_at (sales queries)

------------------------------------------------------------------------

## ❓ Ambiguities Identified (Communication Skill)

I would ask:

-   What defines "recent sales"?
-   Threshold per product or warehouse?
-   Can inventory be negative?
-   Multiple suppliers allowed?

This ensures: - Correct implementation - Alignment with business needs

------------------------------------------------------------------------

# 🚨 Part 3: Low Stock Alerts API

## 📌 Endpoint

GET /api/companies/{company_id}/alerts/low-stock

------------------------------------------------------------------------

## 🧠 Approach (Step-by-Step Thinking)

1.  Fetch company warehouses\
2.  Fetch inventory per warehouse\
3.  Compare with threshold\
4.  Filter by recent sales\
5.  Estimate stockout\
6.  Attach supplier

------------------------------------------------------------------------

## 💡 Key Technical Considerations

### 1. Avoid N+1 Query Problem

-   Use joins instead of loops (future improvement)

------------------------------------------------------------------------

### 2. Handling Edge Cases

-   No sales → skip
-   Zero division → handled
-   Missing supplier → return null safely

------------------------------------------------------------------------

### 3. Performance Thinking

-   Large datasets → pagination needed
-   Frequent alerts → caching possible

------------------------------------------------------------------------

## ⚠️ Business Understanding

-   Alerts should not spam → filter inactive products
-   Include supplier → improves reorder UX
-   Multi-warehouse support → real-world requirement

------------------------------------------------------------------------

# 🧠 Overall Problem-Solving Approach

Instead of just coding, I focused on:

-   Understanding incomplete requirements
-   Asking the right questions
-   Designing for scale and correctness

------------------------------------------------------------------------

# 🗣️ Communication & Collaboration

If this were a real project: - I would clarify requirements early -
Discuss schema trade-offs with team - Validate assumptions before
implementation

------------------------------------------------------------------------

# 📈 Improvements (If Extended)

-   Add caching layer (Redis)
-   Optimize queries using joins
-   Add authentication & rate limiting
-   Async processing for alerts

------------------------------------------------------------------------

# 📝 Final Thoughts

This problem reflects real-world backend challenges: - Incomplete
requirements - Need for trade-offs - Balancing simplicity vs scalability

I aimed to: - Keep solution clean and understandable - Show reasoning
clearly - Avoid over-engineering

Happy to discuss: - Alternative designs - Trade-offs - Improvements
