<a name="top"></a>

[← Index](index#top) | [BMA Analysis](Block_Markets_Africa_Analysis#top) | [Collaboration Strategy](BMA_Collaboration_Analysis#top) | [↑ Top](#)


# Dashboard - Business & Architectural Context
## Nexus Evolution Component Analysis

**Component**: Dashboard (Operational Management Portal)
**Layer**: Operational Services Layer
**Analysis Status**: ✓ COMPLETE
**Last Updated**: 2025-01-02

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## Business Overview
Dashboard is the comprehensive operational management portal for the Nexus Evolution platform, providing transaction monitoring, merchant/supplier configuration, reporting/analytics, and system administration. Built on ABP Framework (ASP.NET Core + Angular), it serves as the centralized control plane for all stakeholders involved in payment operations.

**Current Version**: NexusV4 Dashboard | **Platform**: ASP.NET Core 9.0 + Angular | **Deployment**: Web application

**Program Context**: This component represents the **Operational Management Pattern** in the Nexus Evolution platform - demonstrating how a modern web portal provides visibility, control, and analytics for complex transaction processing systems with multi-tenant isolation.

**Related Documentation**:
- Framework: `framework/Nexus_Evolution_Analysis_Framework.txt`
- Patterns: `framework/Next_Gen_Platform_Patterns.txt`
- Peer Component: `Realtime_Business_Architecture.md`

## Strategic Value

### Business Capabilities Delivered
- **Transaction Visibility**: Real-time and historical transaction search, comprehensive audit trails
- **Merchant Operations**: Streamlined onboarding, balance management, product/supplier assignment
- **Multi-Tenant Isolation**: Aggregator-based soft multi-tenancy with fine-grained access control
- **Operational Control**: Centralized configuration, user administration, permission management
- **Analytics and Reporting**: Custom dashboards, data visualization, CSV export
- **Specialized Workflows**: Fuel rebate management with stakeholder distribution
- **Compliance Support**: Complete audit trails, archive access, hierarchical user management

### Typical Deployment
- **Web Application**: Single deployment serving 50+ concurrent users
- **Shared Database**: SQL Server integrated with Realtime Switch
- **Multi-Tenant**: Aggregator-based isolation (no separate databases)
- **Use Case**: Operations teams, merchant support, system administrators, business analysts

## High-Level Architecture

```
┌───────────────────────────────────────────────────────────┐
│          Users (Browser - Angular SPA)                    │
└────────────────────┬──────────────────────────────────────┘
                     ↓ HTTPS/REST
┌────────────────────────────────────────────────────────────┐
│            DASHBOARD WEB APPLICATION                       │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Frontend (Angular + PrimeNG)                        │  │
│  │  - Auto-generated TypeScript proxies                 │  │
│  │  - Reactive forms and validation                     │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       ↓                                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Backend (ASP.NET Core Web API)                      │  │
│  │  - Application Services (TransactService, etc.)      │  │
│  │  - Domain Entities (Transact, Merchant, etc.)        │  │
│  │  - Infrastructure (EF Core + Repositories)           │  │
│  └────────────────────┬─────────────────────────────────┘  │
└────────────────────────┼───────────────────────────────────┘
                         ↓
┌────────────────────────────────────────────────────────────┐
│        SQL SERVER (Shared with Realtime Switch)            │
│  - dbo Schema (Current: Transact, Merchant, Product, etc.) │
│  - Archive Schema (Historical: ArchiveTransact, etc.)      │
└────────────────────────────────────────────────────────────┘
```

## Core Functions

### 1. Transaction Monitoring
**Business Value**: Real-time visibility into payment operations for troubleshooting and analytics

- Advanced filtering (date, merchant, supplier, product, amount, state, outcome, reference)
- Transaction details with full JSON request/response payloads
- Message audit trail (all messages associated with transaction)
- Archive access (historical data from separate schema)
- CSV export for reporting and analysis
- Aggregator filtering (non-admin users see only their merchant transactions)

### 2. Merchant Management
**Business Value**: Streamlined merchant onboarding and operational control

- Merchant CRUD with validation and audit logging
- Balance management with complete audit trail (MerchantBalanceAuditLog)
- Contact assignment (billing, technical, operations contacts)
- Product assignment (configure merchant access to products)
- Supplier mapping (authorize merchant-supplier relationships)
- Credit configuration (enable/disable real-time credit checks)
- Aggregator assignment (multi-tenant isolation, admin-only)

### 3. Supplier and Product Management
**Business Value**: Product catalog maintenance and supplier health monitoring

- Product catalog (master list of airtime, vouchers, utilities, etc.)
- Staged products (review supplier-provided products before activation)
- Supplier status dashboard (real-time health indicators per supplier)
- Product-supplier mapping

### 4. Configuration and Administration
**Business Value**: Centralized system control and user management

- Aggregator management (multi-tenant containers)
- User management with hierarchy (users manage only descendants)
- Role and permission management (fine-grained authorization)
- API token management (programmatic access)
- Session monitoring

### 5. Reporting and Analytics
**Business Value**: Customizable operational insights

- Custom dashboards (user-defined layouts)
- Widgets (charts, tables, gauges, metrics)
- Widget-feed mapping (dynamic data sources)
- Transaction analytics (volume, success rates, supplier performance)

### 6. Fuel Rebate Management
**Business Value**: Specialized workflow for fuel rebate distribution

- Beneficiary configuration (stakeholder types: Aggregator, Operator, Retailer, Driver, Region, Association)
- Wallet resolution (map stakeholders to payment wallets)
- Stake rules (fixed amounts, percentage distribution)
- Payment simulation (ConfigValidationService validates before production)

### 7. Reconciliation Management (Massmart Deployment)
**Business Value**: Comprehensive transaction reconciliation and operational reporting for retail payment operations

**Transaction Search & Analysis**:
- Advanced transaction filtering (date range, aggregator, chain, store, transaction type, product, status)
- Transaction detail view with request/response frames and JSON payloads
- Archive access for historical transaction lookup
- Lookup caching for performance (aggregators, chains, stores, transaction types)

**Dashboard & Analytics**:
- Product performance visualization (successful vs failed transactions)
- Sales value trending (today, yesterday, week, month)
- Decline analysis by product and provider
- Day-over-day and week-over-week comparisons
- Real-time metrics caching for dashboard performance

**Financial Reporting**:
- Scheduled report generation (daily, weekly, monthly)
- Custom report generation with flexible filtering
- Excel report generation from database queries using templates
- Financial report distribution via email with attachments
- Report type support: Service, Batch, Difference, Finance, VAS Light

**Data Export & Integration**:
- EasyPay reconciliation file generation:
  - Airtime transaction and batch files
  - Bill Payment transaction and batch files
  - Prepaid Utility transaction and batch files
- Generic POS reconciliation files (transaction and batch formats)
- CSV header/detail/trailer formatting per supplier specifications
- Configurable export formats and field mappings

**Secure File Transfer**:
- SFTP upload management with multiple destinations
- File transfer monitoring and status tracking
- SFTP server configuration (credentials, paths, certificates)
- File presence monitoring with scheduled checks
- Transfer progress tracking and retry logic

**SFTP Monitoring & Alerting**:
- Scheduled file presence verification on remote SFTP servers
- Missing file detection and reporting
- Multi-channel alerting:
  - Email reports with HTML formatting
  - SMS notifications via Twilio integration
  - Voice call escalation for critical missing files
- Ad-hoc monitoring execution for on-demand checks
- Monitoring report generation with detailed status

**Reconciliation Service Management**:
- Service status monitoring and health checks
- Configuration management for ReconService modules
- Schedule management for automated reports and exports
- Import source mapping configuration (chain to source system associations)

**Operational Features**:
- Frame-based transaction viewing (structured request/response data)
- Transaction type normalization and formatting
- Settlement entity and aggregator lookups
- Tender type and payment method analysis
- Real-time and batch transaction correlation

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## Key Architectural Patterns

### 1. ABP Framework Layered Architecture
- Domain Layer (entities, business logic)
- Application Layer (services, DTOs, workflows)
- Infrastructure Layer (EF Core, repositories, data access)
- Presentation Layer (Angular SPA, auto-generated proxies)
- Cross-Cutting (authentication, authorization, audit, validation)

### 2. Aggregator-Based Multi-Tenancy
- Soft multi-tenancy (single database, filtered queries)
- User aggregator assignment via ABP extra properties
- Entity aggregator filtering (15+ entities with AggregatorId FK)
- Service layer enforcement (all queries filtered for non-admin users)
- Admin override (admin users bypass filters for system-wide access)

### 3. Repository Pattern + Unit of Work
- `IRepository<TEntity, TKey>` generic interface
- Async database operations
- LINQ query support via `GetQueryableAsync()`
- Automatic transaction boundaries
- Soft delete (IsDeleted flag)

### 4. Service Base Class Pattern
- **NexusReplicationBase**: CRUD + Active-Active replication support
- **NexusUserContext**: Shared helper for aggregator operations
- Consistent GetList/Get/Create/Update/Delete with aggregator filtering
- Replicating table tracks changes for node synchronization

### 5. Permission-Based Authorization
- OmniHubPermissions (hierarchical: `Configuration.Merchants.Read/Create/Update/Delete`)
- Service method attributes: `[Authorize(OmniHubPermissions.XX)]`
- Role-permission mapping via ABP Identity

### 6. Archive Pattern
- Separate Archive schema in same database
- Separate services (ArchiveTransactService, ArchiveMessageService)
- Conditional UI routing based on date range
- Performance isolation (current operations unaffected by archive queries)

### 7. Hierarchical User Management
- Recursive parent-child via ABP CreatorId
- Delegated administration (users manage only descendants)
- CustomIdentityUserAppService overrides default to filter by hierarchy
- Admin users see all users

### 8. Reconciliation Patterns (Massmart Deployment)
- **Separate Database Context**: Dedicated DbContext for Recon database isolation
- **Memory Caching**: IMemoryCache for dashboard metrics and lookup data
- **Scheduled Processing**: Time-based report generation and SFTP monitoring
- **File Watcher Pattern**: Polling pickup directories for file transfer
- **Multi-Channel Alerting**: Email, SMS, and voice call escalation
- **Template-Based Reporting**: Excel template population from database queries
- **Export Utilities**: Reusable formatters for CSV generation (header/detail/trailer)
- **SFTP Abstraction**: WinSCP wrapper with retry logic and progress monitoring

## Role in Next-Generation Platform

Dashboard demonstrates **operational management patterns** that inform next-gen architecture:

### Patterns to Preserve
- ABP Framework layered architecture with automatic auditing
- Aggregator-based soft multi-tenancy
- Repository Pattern + Unit of Work for data access
- Service base classes for consistent development
- Archive pattern for long-term retention
- Hierarchical user management for delegated administration
- Permission-based authorization with fine-grained control
- Transaction monitoring workflows and search patterns
- Reconciliation reporting and analytics (Massmart deployment)
- Multi-channel alerting for operational monitoring
- Template-based report generation
- Secure file transfer with retry logic

### Architectural Lessons
- **Shared Database Integration**: Dashboard reads Realtime-written data - demonstrates read/write separation
- **Multi-Schema Strategy**: Separate schemas (dbo, Archive) provide performance isolation within single database
- **Auto-Generated Proxies**: ABP CLI TypeScript generation reduces manual integration work
- **Replication Support**: NexusReplicationBase enables Active-Active synchronization
- **Consistent Filtering**: Aggregator filtering applied uniformly across all services
- **Multi-Database Context**: Recon deployment demonstrates separate database integration within single application
- **Scheduled Processing**: Time-based report and monitoring workflows integrated into web application
- **File-Based Integration**: Polling file watchers provide zero-infrastructure file transfer coordination
- **Caching Strategy**: Memory caching for dashboard performance without external dependencies

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## Deployment Models

### Current State: Monolithic Web Application
```
Web Server (IIS/Kestrel - Port 44353)
    ├── Angular SPA (Static files)
    └── ASP.NET Core API
        └── EF Core DbContext
            ↓
SQL Server (Shared with Realtime)
    ├── dbo Schema (Current)
    └── Archive Schema (Historical)
```

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## Business Workflows

### Transaction Troubleshooting
1. Merchant reports failed transaction
2. Support agent searches by merchant ID and date
3. Locate transaction (State=AuthorizeFailed)
4. View full payloads and message trail
5. Confirm reversal and balance credit
6. Provide resolution to customer

### Merchant Onboarding
1. Aggregator admin creates merchant
2. Assign to aggregator, set initial balance
3. Assign products and suppliers
4. Configure credit check
5. Create merchant operator user
6. Generate API token for integration

### Supplier Health Monitoring
1. Operations manager views Supplier Status
2. Flash shows unhealthy (red indicator)
3. Query recent Flash transactions
4. Filter by Outcome=ConnectionError
5. Contact Flash support
6. Monitor recovery and circuit breaker status

### Reconciliation Report Generation (Massmart)
1. Finance team schedules monthly financial report
2. Dashboard automatically generates report on 1st of month
3. Excel workbook created from database query using template
4. Report emailed to finance distribution list with attachments
5. Local archive copy saved for audit trail

### SFTP File Monitoring (Massmart)
1. Scheduled check runs at configured time (e.g., 8 AM daily)
2. Dashboard queries SFTP servers for expected files
3. Missing file detected (Postilion extract not received)
4. Email report generated with HTML details
5. SMS sent to operations team
6. If critical, voice call escalation triggered
7. Operations investigates and resolves issue

### Supplier Reconciliation Export (Massmart)
1. ReconService generates EasyPay airtime batch file
2. File placed in Dashboard monitored pickup directory
3. Dashboard SecureFileTransfer module detects file
4. File uploaded to EasyPay SFTP server with retry logic
5. Transfer status logged and monitored
6. Confirmation displayed in Dashboard file transfer screen

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## Integration Points

### Database Integration
- **Read**: Transact, Message, SupplierStatus (written by Realtime)
- **Write**: Merchant, Product, Aggregator, User, Contact (configuration)
- **Shared**: Voucher, SupplierStatus (both read/write)
- **Replication**: NexusReplicationBase tracks changes for Active-Active sync

### Office Service
- gRPC (Port 55200): Credit management operations
- Used by both Dashboard UI and Realtime transactions

### Reconciliation Integration (Massmart Deployment)
- **Recon Database**: Separate SQL Server database for reconciliation data
  - Read: ReconTransactionsView, ReconFrame (written by ReconService)
  - Write: ReportSchedule, SftpServers, SftpMonitorSettings, ChainImportSourceMappings
- **File System Integration**:
  - SecureFileTransfer pickup directory monitoring
  - Excel template storage and report generation
  - SFTP certificate and private key management
- **External Services**:
  - SFTP servers (multiple destinations for file transfers)
  - SMTP server (email report distribution)
  - Twilio API (SMS and voice call alerting)
- **ReconService Coordination**:
  - Shared configuration management
  - Service status monitoring
  - Import source mapping synchronization

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## Operational Characteristics

**Performance**:
- Page load: p95 < 3 seconds
- Transaction query: p95 < 2 seconds (1-day range)
- Archive query: p95 < 5 seconds
- CSV export: 10,000 records in < 10 seconds
- Concurrent users: 50+

**Availability**:
- Target: 99.9% (business hours critical)
- Impact if down: Monitoring unavailable; transactions continue

**Security**:
- HTTPS required
- OAuth 2.0/OpenID Connect (OpenIddict)
- Permission-based + aggregator filtering
- EF Core prevents SQL injection
- Angular prevents XSS
- ABP anti-forgery (CSRF)
- Audit logging (ABP + custom)
- PAN masking in logs/UI

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## Summary: Strategic Recommendations

**For Business Stakeholders**: Dashboard provides centralized operational control with multi-tenant isolation, comprehensive transaction visibility, and fine-grained access control. Massmart deployment extends capabilities with reconciliation management, automated reporting, SFTP monitoring, and multi-channel alerting for retail payment operations.

**For Architects**: ABP Framework patterns, aggregator multi-tenancy, archive strategy, and replication support provide proven operational management templates. Reconciliation dimension demonstrates multi-database integration, scheduled processing, and file-based coordination patterns within web application architecture.

**For Product Owners**: Current capabilities support full operational lifecycle with multi-tenant isolation. Fuel rebate management demonstrates domain-specific workflow customization. Reconciliation management shows extensibility for industry-specific operational requirements (retail, hospitality, etc.).

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


## BMA Blockchain Integration

**Integration Touchpoint**: [Dashboard ↔ BMA Wallet Management](BMA_PaymentSwitch_Integration_Analysis#3-dashboard--bma-wallet-management)

### Dashboard as Blockchain Operations Portal

Dashboard extends to blockchain operations management:

**Merchant Onboarding**:
- Create blockchain wallet during merchant setup (custodial or non-custodial)
- Assign Smart KYC NFT tier (Basic, Standard, Enhanced)
- Auto-fund wallet with initial tokenized balance (eZAR)
- Configure product-to-blockchain routing

**Balance Management**:
- Dual tracking: SQL database + blockchain wallet
- Real-time sync status indicators
- Variance detection and reconciliation
- Multi-asset display (eZAR, tokenized airtime, loyalty tokens)

**Transaction Monitoring**:
- Enhanced detail view with blockchain fields:
  - Wallet addresses (source/destination)
  - Blockchain transaction hash (link to OpenRUN explorer)
  - Block number and confirmation time
  - Smart KYC validation status
  - Gas fees

**Reporting**:
- Blockchain transaction report (all BMA settlements)
- Wallet balance reconciliation (SQL vs blockchain variance)
- Cross-border settlement report (OpenRUN vs SWIFT comparison)

**Business Value**:
- Unified operational view (traditional + blockchain)
- Same multi-tenant isolation applies to blockchain wallets
- Existing audit logging captures blockchain operations
- Zero UI disruption for operations teams

**Technical Integration**: [Full Details](BMA_PaymentSwitch_Integration_Analysis#integration-capabilities)

---
<div align="right"><a href="#top">↑ Back to Top</a></div>


**Document Purpose**: Business and architectural reference for current system understanding
**Audience**: Business stakeholders, solution architects, operations teams
**Scope**: Functional capabilities, architectural patterns, strategic value (not implementation details)


---

<a name="top"></a>

[← Index](index#top) | [BMA Analysis](Block_Markets_Africa_Analysis#top) | [Collaboration Strategy](BMA_Collaboration_Analysis#top) | [↑ Top](#)


