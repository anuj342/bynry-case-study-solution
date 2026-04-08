# StockFlow - Inventory Management System Case Study

## Project Overview

This is a comprehensive backend engineering solution for the **StockFlow** inventory management system case study, submitted for the Backend Engineering Intern position at Bynry Inc. The submission demonstrates proficiency in code review, database design, and API implementation across three integrated parts.

---

## 📋 Project Structure

### Part 1: Code Review & Debugging
**Objective:** Identify and fix bugs in an inventory product creation endpoint.

#### Key Issues Identified:
- ❌ No input validation (KeyError on missing fields)
- ❌ No unique SKU enforcement (potential duplicates)
- ❌ Incorrect product model design (warehouse_id should not be in Product)
- ❌ Flawed inventory logic (creates duplicates instead of updating)
- ❌ No transaction management (inconsistent database state)
- ❌ No error handling (500 crashes on invalid input)
- ❌ Missing data type validation (price and quantities)
- ❌ No warehouse existence checks

#### Production Impact:
- **Crashes:** 500 errors on invalid data
- **Data Inconsistency:** Duplicate products/SKUs, incorrect inventory counts
- **Business Logic Violations:** Products tied to wrong warehouses
- **Poor UX:** Unclear error messages and silent failures
- **Scalability Issues:** No handling of concurrent requests

#### Solution Highlights:
```python
✅ Comprehensive input validation with clear error messages
✅ SKU uniqueness enforcement
✅ Proper data type validation with type coercion
✅ Warehouse existence verification
✅ Inventory upsert logic (update if exists, create if new)
✅ Transaction management with rollback on error
✅ Try-catch blocks for IntegrityError and general exceptions
✅ Proper JSON responses with appropriate HTTP status codes
```

---

### Part 2: Database Design
**Objective:** Design a normalized relational schema for a multi-warehouse inventory system.

#### Tables Designed:

| Table | Purpose |
|-------|---------|
| `companies` | Organizational hierarchy |
| `warehouses` | Physical storage locations tied to companies |
| `suppliers` | Product suppliers with contact info |
| `product_types` | Product categories with configurable thresholds |
| `products` | Product catalog (SKU unique globally) |
| `inventory` | Quantities per product per warehouse |
| `inventory_logs` | Audit trail of inventory changes |
| `product_bundles` | Many-to-many relationships for bundle products |

#### Key Design Decisions:

✅ **Full Normalization** - Eliminated redundancy through proper separation of concerns
✅ **Referential Integrity** - Foreign keys with CASCADE deletes for data consistency
✅ **Scalability** - Indexes on frequently queried fields (SKU, inventory combos)
✅ **Multi-warehouse Support** - Products can exist in multiple warehouses via inventory table
✅ **Audit Trail** - inventory_logs table for compliance and debugging
✅ **Product Bundles** - Many-to-many table with quantities for composite products
✅ **Flexible Thresholds** - Per product-type low-stock thresholds with sensible defaults

#### Identified Gaps (Questions for Product Team):
- Bundle inventory management strategy
- User roles and permissions model
- Order/transaction tracking requirements
- Definition of "recent sales activity" timeframe
- Product categorization beyond types
- Reporting and analytics requirements

---

### Part 3: API Implementation
**Objective:** Implement a low-stock alert endpoint for multi-warehouse inventory management.

#### Endpoint:
```
GET /api/companies/{company_id}/alerts/low-stock
```

#### Response Format:
```json
{
  "alerts": [
    {
      "product_id": 1,
      "product_name": "Gadget X",
      "sku": "SKU-001",
      "warehouse_id": 5,
      "warehouse_name": "Warehouse East",
      "current_stock": 8,
      "threshold": 10,
      "days_until_stockout": 5,
      "supplier": {
        "id": 2,
        "name": "Supplier Co",
        "contact_email": "contact@supplier.com"
      }
    }
  ],
  "total_alerts": 1
}
```

#### Algorithm:

1. **Scope Data** - Filter inventories for company's warehouses only
2. **Check Threshold** - Identify items below their threshold
3. **Calculate Sales Trend** - Sum quantity sold in last 30 days
4. **Calculate Metrics:**
   - Average daily sales = Total sold / 30 days
   - Days until stockout = ⌈Current stock / Avg daily sales⌉
5. **Filter Alerts** - Only include items with recent sales activity

#### Key Features:

✅ Company-scoped data access (isolation and security)
✅ Recent sales filtering (30-day window)
✅ Intelligent days-to-stockout calculation
✅ Configurable thresholds per product type
✅ Supplier contact information in response
✅ Proper error handling (404 for missing company, 500 for DB errors)
✅ Zero-division protection (skips products with no sales)
✅ Null-safe supplier handling

---

## 🔧 Technology Stack

- **Framework:** Flask (Python)
- **ORM:** SQLAlchemy
- **Database:** PostgreSQL (SQL DDL provided)
- **API Style:** RESTful JSON

---

## 📊 Key Metrics & Assumptions

| Metric | Value | Rationale |
|--------|-------|-----------|
| Recent Sales Window | 30 days | Standard business definition |
| Default Threshold | 10 units | Conservative default |
| Days to Stockout Rounding | Ceiling (⌈⌉) | Better to alert early |
| Paid Dates | UTC | Standardization |
| Threshold Scope | Per Product Type | Flexibility and practicality |

---

## 🎯 Problem-Solving Approach

### 1. **Code Review Strategy**
- Traced through execution flow to find logic gaps
- Considered data consistency and edge cases
- Evaluated production impact and scalability
- Applied defensive programming principles

### 2. **Database Design Strategy**
- Applied normalization to eliminate redundancy
- Used surrogate keys for scalability
- Added audit trails for compliance
- Designed for multi-tenancy (companies → warehouses)

### 3. **API Implementation Strategy**
- Scoped queries for data isolation
- Optimized for read-heavy workloads
- Handled null cases and edge conditions
- Provided actionable response data

---

## 📝 Assumptions Made

### Part 1:
- Warehouse model exists for validation
- Price is optional (defaults to 0)
- initial_quantity is additive to existing inventory

### Part 2:
- Product types table added for configurable thresholds
- Bundles are many-to-many relationships with quantities
- Inventory logs include reason field for auditing

### Part 3:
- Sales table exists with date field
- "Recent" = last 30 days
- Average daily sales = total_sold / 30
- Supplier information is optional
- No pagination (assume small dataset)
- UTC timezone for all timestamps

---

## 🚀 How to Use This Solution

### Part 1: Apply the Fixed Code
Replace the buggy endpoint with the provided fixed implementation to prevent crashes and data corruption.

### Part 2: Use the Schema
Initialize your database with the provided SQL DDL. Adapt column types and constraints based on your specific requirements.

### Part 3: Implement the API
Use the Flask/SQLAlchemy implementation as a reference. Adapt to your actual table structure and add authentication/authorization as needed.

---

## ✨ Key Takeaways

1. **Validation is Critical** - Always validate inputs to prevent crashes and inconsistencies
2. **Data Normalization** - Proper schema design supports scalability and maintainability
3. **Business Logic in Code** - Translate requirements into precise algorithms
4. **Error Handling** - Graceful degradation and clear error messages improve user experience
5. **Thoughtful Assumptions** - Document assumptions when working with incomplete requirements

---

## 📞 Ready for Discussion

This solution is prepared for the live technical session. The approach is pragmatic, considers production constraints, and demonstrates:
- Problem-solving under incomplete information
- Attention to data integrity and consistency
- Scalable architecture thinking
- Clear communication through documentation

---

**Submission Date:** April 8, 2026  
**Timeframe:** 90-minute case study  
**Candidate:** Backend Engineering Intern Applicant  
**Company:** Bynry Inc
