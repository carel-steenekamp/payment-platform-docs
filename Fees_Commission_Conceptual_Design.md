<a name="top"></a>

[← Index](index#top) | [BMA Analysis](Block_Markets_Africa_Analysis#top) | [Collaboration Strategy](BMA_Collaboration_Analysis#top) | [↑ Top](#)

# Nexus Evolution Fees and Commission - Conceptual Design

**Component**: Fees and Commissions Subsystem  
**Status**: Conceptual Design  
**Last Updated**: 2025-11-04

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

## Overview

A rule-based engine that calculates, distributes, and reconciles fees and commissions across merchants, suppliers, agents, and platform entities for transactions processed through NexusV4.

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

## Operational Flow

### 1. Rule Configuration

Operations staff configure commission structures through a management interface. Each rule specifies:

- **Which supplier pays the commission**
- **Conditions that trigger the rule** (transaction type, amount ranges, merchant categories)
- **How the commission splits among recipients**
- **Effective date ranges** for time-bound agreements

Rules are prioritized so when multiple rules match a transaction, the system applies the highest priority rule. This accommodates overlapping business agreements and promotional periods.

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

### 2. Transaction Processing

When a transaction completes:

- The system receives the transaction details including the **internal Id** and **MerchantReference**
- The supplier responds with its **SupplierReference** and **commission amount**
- The subsystem evaluates applicable rules based on transaction attributes
- The engine calculates each recipient's share (merchant, platform, agents, partners)
- Distribution records are created with calculated amounts and the applied rule reference

**Validation**: All conditions evaluate case-insensitively where applicable. The system validates that percentage-based splits total 100% and that distributed amounts sum to the supplier's commission.

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

### 3. Distribution Management

Finance teams review calculated distributions before approval:

- View pending distributions grouped by recipient
- Verify calculations against agreements
- Approve distributions for payment processing
- Flag exceptions for investigation

**Status Tracking**: Approved distributions move to payment processing systems. The subsystem tracks status through the payment lifecycle (Pending, Approved, Paid, Failed).

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

### 4. Reconciliation

The subsystem reconciles supplier payments against calculated commissions:

- Ingests supplier payment files or API data
- Matches **SupplierReference** to internal transaction records
- Compares received amounts to calculated commissions
- Generates exception reports for discrepancies
- Provides drill-down to transaction and rule details

Finance staff investigate exceptions, adjust rules if needed, and process corrections through the standard workflow.

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

## Data Model

### CommissionRule

Stores rule definitions with supplier, conditions (JSON structure), effective dates, and priority. Rules can be versioned to maintain history when agreements change.

**Key Fields**:
- Supplier identifier
- Conditions (JSON-based for flexibility)
- Effective date range
- Priority (for conflict resolution)
- Version tracking

---

### CommissionSplit

Defines recipients and calculation methods (percentage, fixed amount, tiered) for each rule. Multiple splits per rule support complex distribution scenarios.

**Calculation Methods**:
- Percentage-based splits
- Fixed amount allocations
- Tiered structures based on thresholds

---

### CommissionTransaction

Links to platform transactions (via **Id**), stores supplier details (**SupplierReference**), total commission amount, and overall status.

**Reference Tracking**:
- Internal Id (primary key)
- MerchantReference (client-provided)
- SupplierReference (external provider)

---

### CommissionDistribution

Individual recipient allocations with amounts, applied rule reference, and payment status. These records feed into payment processing and reporting.

**Status Values**: Pending, Approved, Paid, Failed

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

## Reporting Capabilities

- **Commission income projections** by supplier and period
- **Distribution summaries** by recipient type and entity
- **Rule effectiveness analysis** (transaction coverage, match rates)
- **Reconciliation exception tracking**
- **Audit trails** showing rule application per transaction

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

## Integration Points

| System | Integration | Purpose |
|--------|-------------|---------|
| **Transaction Processing** | Triggered after supplier response received | Initiate commission calculation |
| **Supplier APIs** | Ingests commission amounts and payment data | Reconciliation and validation |
| **Payment Processing** | Outputs approved distributions for disbursement | Execute payments to recipients |
| **Accounting** | Provides journal entries | Commission income and distribution tracking |
| **Reporting** | Exports data | Financial and operational analysis |

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

## Technical Foundation

### Financial Accuracy
- **Decimal arithmetic** ensures financial accuracy
- Validation that splits total 100%
- Validation that distributed amounts sum to total commission

### Flexibility
- **JSON-based rule conditions** allow flexibility without database schema changes
- Configurable calculation methods per recipient
- Dynamic rule evaluation based on transaction attributes

### Governance
- **Time-bound rules** support agreement lifecycle management
- Priority-based conflict resolution
- **Audit trail** captures which rule version applied to each transaction
- Version control for rule changes

### Operations
- Case-insensitive condition evaluation where applicable
- Exception handling with finance team review workflow
- Reconciliation with supplier payment data

---

<div align="right"><a href="#top">↑ Back to Top</a></div>

## Business Value

### Revenue Protection
- Automated commission calculation eliminates manual errors
- Reconciliation catches supplier payment discrepancies
- Audit trail provides financial accountability

### Operational Efficiency
- Rule-based engine reduces configuration time
- Bulk processing handles high transaction volumes
- Exception workflows streamline finance review

### Business Flexibility
- JSON-based conditions support complex agreements without code changes
- Priority system handles overlapping promotional periods
- Time-bound rules automate agreement lifecycle transitions

### Compliance & Audit
- Complete transaction-to-payment audit trail
- Rule version history for agreement tracking
- Reconciliation reports for financial audits

---

<div align="right"><a href="#top">↑ Back to Top</a></div>
