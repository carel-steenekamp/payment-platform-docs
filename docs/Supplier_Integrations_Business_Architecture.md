# Supplier Integrations - Business Architecture

**Last Updated**: 2025-11-03

---

## Overview

The Realtime Switch connects to 13+ payment service providers, offering diverse products across mobile services, utilities, bill payments, financial services, and vouchers. Each integration follows consistent patterns while supporting supplier-specific business rules.

---

## Payment Service Providers

| Supplier | Products | Business Model | Status |
|----------|----------|----------------|--------|
| **BMA (Figment)** | Fuel Rebate Payments | Multi-stakeholder distribution | ✓ Production |
| **BlueLabelMobile** | Airtime, Data Bundles | Prepaid mobile services | ✓ Production |
| **BlueLabelBillPayment** | Bill Payments | Multi-biller aggregation | ✓ Production |
| **BlueLabelUtilities** | Prepaid Utilities | Token generation | ✓ Production |
| **EasyPay** | Bills, Utilities, Financial | Payment aggregator | ✓ Production |
| **TymeBank** | Money Transfers, Bank Payments | Digital banking | ✓ Production |
| **Flash** | Vouchers, Airtime, Cash | Multi-product services | Infrastructure |
| **GiftCard** | Gift Cards | Issuance and redemption | ✓ Production |
| **InstantMoney** | Cash Transfers | Money transfer service | ✓ Production |
| **PayLater** | Buy-Now-Pay-Later | Point of sale credit | ✓ Production |
| **SmartCall** | Telecommunications | Telecom services | ✓ Production |
| **Stellr** | Payment Processing | Gateway services | ✓ Production |
| **WiCode** | QR Payments | QR-based solutions | ✓ Production |

---

## Product Categories

### Mobile Services
Fixed/variable airtime vouchers (Vodacom, MTN, Cell C, Telkom), direct mobile recharge, data bundles

### Utility Payments
Prepaid electricity tokens, water recharges, municipal prepayments

### Bill Payments
Recurring bills, traffic fines, licenses, account validation

### Financial Services
Bank transfers, instant money (cash collection), buy-now-pay-later, bulk disbursements

### Fuel Services
Multi-stakeholder rebate distributions, wallet settlements, configurable stake rules

### Vouchers
Gift cards, prepaid vouchers, cash withdrawal PINs, QR payments

---

## Transaction Patterns

### Lifecycle
Authorization → Confirmation → Settlement with reversal support

### Reference Tracking
- **Merchant Reference**: Client-provided identifier
- **Switch Reference (Id)**: Internal primary key
- **Supplier Reference**: External provider identifier

### Error Handling
Standardized outcomes, automatic retry, supplier monitoring, complete audit trail

---

## Integration Benefits

### Merchant Value
Single integration for multiple suppliers, consistent handling, automatic retry, real-time monitoring

### Customer Value
Wide product range, real-time processing, multiple payment options, instant delivery

### Operational Value
Centralized management, automated reconciliation, transaction tracking, zero-code supplier addition

---

## Technical Integration

All supplier integrations implemented as plugin modules within the Realtime Switch:
- Plugin architecture with declarative module registration
- Hot-reload configuration without service restart
- Circuit breakers for supplier failure isolation
- Store-and-forward resilience at all layers
- Complete message audit trail with sensitive data masking

---

<div align="right"><a href="INDEX.md">↑ Back to Index</a></div>
