[← Platform Overview](../README.md#top) | [BMA Analysis](Block_Markets_Africa_Analysis.md) | [Collaboration Strategy](BMA_Collaboration_Analysis.md) | [↑ Top](#)

# Realtime Switch (NexusV4) - Business & Architectural Overview

**Component**: Realtime Transaction Switch
**Platform**: .NET 9.0 Windows Service
**Last Updated**: 2025-01-02

---

## Executive Overview

Realtime is the central transaction orchestration engine that connects multiple merchant acquisition channels to multiple supplier/provider APIs. It functions as an intelligent message bus providing dynamic routing, protocol translation, state management, and comprehensive resilience for real-time payment and value-added service transactions.

**Current Deployment**: On-premises Windows Service with Active-Active HA configuration

**Current Capacity**:
- 500+ TPS sustained throughput
- 100+ concurrent merchants
- 14+ supplier integrations
- 99.9% availability with dual-node HA

**Key Value**:
- **Multi-Channel Support**: Single platform handles POS terminals, ATMs, web merchants, payment APIs
- **Multi-Provider Routing**: Dynamic routing to 14+ suppliers without merchant code changes
- **Enterprise Resilience**: Zero transaction loss via Store-and-Forward, circuit breakers, automatic failover
- **Operational Maturity**: Hot-reload configuration, comprehensive health monitoring, HSM security

---

## Role in Next-Generation Architecture

Realtime Switch demonstrates **central orchestration patterns** that can inform next-generation platform design:

### Proven Patterns to Consider
1. **Plugin Architecture**: Declarative module registration enables rapid supplier onboarding without core system changes
2. **Message-Driven Architecture**: Asynchronous, decoupled communication provides scalability and fault isolation
3. **Context as Universal Object**: Standardized transaction representation simplifies multi-protocol integration
4. **Base Class Abstractions**: BaseMerchant/BaseSupplier framework accelerates new integration development
5. **Timer-Driven Operations**: Automated health monitoring, token refresh, and catalog sync reduce operational overhead
6. **Hot-Reload Configuration**: FileWatcher pattern enables zero-downtime configuration updates
7. **Store-and-Forward**: Database-backed resilience with automatic retry ensures zero transaction loss
8. **Circuit Breakers**: Per-supplier failure isolation prevents cascading failures
9. **Active-Active HA**: Dual-node replication provides high availability without manual failover

### Architectural Lessons
- **Separation of Concerns**: Merchant modules handle protocols, supplier modules handle APIs, switch handles routing
- **State Machine Complexity**: Multi-phase transactions (Initial/Authorize/Confirm/Reverse) require careful state management
- **Credit Management**: Pre-transaction balance checking prevents over-spending (Office gRPC integration)
- **Health Monitoring**: Centralized supplier status tracking enables intelligent routing decisions
- **Comprehensive Audit**: Full request/response payload storage enables troubleshooting and compliance

---

## Functional Overview

### Merchant Channels (Inbound - 5 Types)
- **OmniSocket** (Port 54300): Legacy POS terminals via JSON over TCP
- **Postilion** (Port 54200): ATM/Bank POS networks via ISO8583
- **WebMerchant** (Port 55101): Modern merchants via REST API
- **Figment** (Port 55103): Payment platforms via REST API
- **Electrum** (Various ports): POS integration gateways via REST API

### Supplier Integrations (Outbound - 14+ Providers)
- **Flash**: Vouchers, airtime, utilities, bill payment (REST + OAuth)
- **BlueLabel**: Airtime and data bundles (SOAP/REST)
- **InstantMoney**: Cash transfers and withdrawals (REST)
- **PayLater**: Buy-Now-Pay-Later credit (REST)
- **SmartCall, Stellr, WiCode**: Prepaid services (REST/SOAP)
- **GiftCard**: Gift card management
- **BMA, EasyPay, TymeBank**: Specialized payment services

### Core Functions

**Dynamic Routing**:
- Plugin-based configuration (Invocations.cs) maps merchants to suppliers
- One merchant can route to multiple suppliers based on product type
- Bidirectional routing: Suppliers route responses back to originating merchants

**Protocol Translation**:
- Converts between ISO8583 (ATM/POS), REST (web), SOAP (legacy), proprietary socket formats
- Context object provides universal transaction representation
- Each module handles specific protocol transformations

**State Management**:
- Multi-phase transaction lifecycle: Initial → Authorize → Confirm → Reverse
- State machine: Default → Initialized → Authorized → PendConfirm → WaitConfirm → Confirmed
- Supports two-phase commit workflows and reversals

**Credit Management** (Office Service gRPC):
- Pre-transaction balance checking: Debit merchant balance before supplier call
- Post-transaction adjustment: Credit back if supplier fails
- Insufficient funds prevention with email alerting
- Real-time balance operations across all supplier modules

**Resilience**:
- **Store-and-Forward**: Database-backed queue for failed transactions (60s retry, 3 max attempts)
- **Circuit Breakers**: Per-supplier failure isolation (Open/Closed/HalfOpen states)
- **Supplier Health Monitoring**: Centralized status tracking with timer-driven health checks
- **Active-Active HA**: Dual nodes with automatic database replication

**Security**:
- **HSM Integration** (Port 1500): PIN encryption, key management, MAC generation
- **Sensitive Data Masking**: Automatic PAN/PIN masking in logs
- **Audit Trail**: Complete request/response payloads stored in database

**Product Catalog**:
- Timer-driven synchronization from suppliers
- Dynamic product availability updates
- Hot-reload without service restart

---

## Architectural Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│              MERCHANT CHANNELS (Inbound)                │
├─────────────────────────────────────────────────────────┤
│ OmniSocket │ Postilion │ WebMerchant │ Figment │ Electrum│
│  (Socket)  │ (ISO8583) │   (REST)    │ (REST)  │  (REST) │
└──────────────────────────┬──────────────────────────────┘
                           ↓
┌──────────────────────────────────────────────────────────┐
│              REALTIME SWITCH (NexusV4)                    │
│  ┌────────────────────────────────────────────────────┐  │
│  │ BaseMerchant Modules                               │  │
│  │ - Protocol handling (ISO8583, REST, Socket)        │  │
│  │ - Message reception                                │  │
│  │ - Pending request management                       │  │
│  └────────────────────┬───────────────────────────────┘  │
│                       ↓                                   │
│  ┌────────────────────────────────────────────────────┐  │
│  │ Context (Universal Transaction Object)             │  │
│  │ - Routing, State, Payloads                         │  │
│  └────────────────────┬───────────────────────────────┘  │
│                       ↓                                   │
│  ┌────────────────────────────────────────────────────┐  │
│  │ Message Bus (CausalNexus Framework)                │  │
│  │ - Asynchronous routing via MultiChannel            │  │
│  └────────────────────┬───────────────────────────────┘  │
│                       ↓                                   │
│  ┌────────────────────────────────────────────────────┐  │
│  │ BaseSupplier Modules                               │  │
│  │ - External API clients (REST/SOAP)                 │  │
│  │ - Timer-driven operations (health, token, catalog) │  │
│  │ - SAF management                                   │  │
│  │ - Circuit breaker integration                      │  │
│  └────────────────────┬───────────────────────────────┘  │
│                       ↓                                   │
│  ┌────────────────────────────────────────────────────┐  │
│  │ Infrastructure Modules                             │  │
│  │ - Office (gRPC credit management)                  │  │
│  │ - HSM (cryptographic operations)                   │  │
│  │ - Replication (Active-Active HA)                   │  │
│  │ - SupplierStatus (health monitoring)               │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────┬───────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│            SUPPLIER APIS (Outbound - 14+)                │
├─────────────────────────────────────────────────────────┤
│ Flash │ BlueLabel │ InstantMoney │ PayLater │ SmartCall │
│ Stellr │ WiCode │ GiftCard │ BMA │ EasyPay │ TymeBank   │
└─────────────────────────────────────────────────────────┘
```

### Key Components

**Plugin System (Invocations.cs)**:
- Declarative routing configuration maps merchants to suppliers
- Example: `new ModuleInvocation<WebMerchantModule>(OutputTo<FlashModule>(), OutputTo<PayLaterModule>())`
- Bidirectional: Suppliers route responses back to originating merchants

**Context (Universal Transaction Object)**:
- Identity: Transaction ID, timestamp
- Routing: ProductType, TransactType, MessageType, MerchantModuleId, SupplierModuleId
- State: Outcome, TransactState (Authorized, PendConfirm, Confirmed, etc.)
- Payloads: InitialRequest, AuthorizeRequest, SupplierRequest, SupplierResponse, MerchantResponse (JSON)
- Merchant Data: MerchantId, MerchantReference, Amount (cents), CreditCheck flag

**BaseMerchant<T>** (Inbound Protocol Handlers):
- Protocol-specific implementations (ISO8583, REST, Socket)
- `MessageReceiver()` for incoming messages
- `SendToSupplierModule()` for routing to suppliers
- Pending request management (ConcurrentDictionary for async request/response matching)

**BaseSupplier<T>** (Outbound API Clients):
- External API client management (HTTP, SOAP)
- Timer-driven operations: TokenTimer (OAuth refresh), HandshakeTimer (health checks), ProductTimer (catalog sync), StatusTimer (availability updates)
- `SendToMerchantModule()` for response routing
- SAF management for failed transactions
- Circuit breaker integration

**Office Service** (gRPC - Port 55200):
- Credit Management: `UpdateBalance(merchantId, amount)` - Debit/credit with validation
- Voucher Management: PostVoucher, GetVoucher, PutVoucher
- Alerting: Email alerts when balance thresholds reached
- Used by supplier modules for pre-transaction credit checks

**HSM Service** (TCP - Port 1500):
- PIN encryption/decryption (ISO Format 0/1/3)
- Key management (master, working, PIN keys)
- MAC generation and validation
- Used by Postilion module for secure payment processing

### Configuration Architecture

**Three-Tier Hot-Reload Configuration**:

1. **Base Configuration** (`_BaseMerchant.json`, `_BaseSupplier.json`):
   - Database connection strings
   - Logging configuration (level, retention)
   - Active node designation (ActiveA or ActiveB)

2. **Module Configuration** (e.g., `Flash.json`, `WebMerchant.json`):
   - Module-specific settings (API URLs, credentials, timeouts)
   - Circuit breaker thresholds
   - Timer intervals

3. **Profile Configuration**:
   - Environment-specific overrides (Dev, Test, UAT, Prod)

**FileWatcher Pattern**:
```csharp
private readonly FileWatcher<FlashSettings> _fileWatcher = new();

protected override void HandleStartCompleted() {
    _fileWatcher.Initialize(ModuleId, LoadSettings, LogError);
}

private void LoadSettings(FlashSettings settings) {
    ModuleSettings = settings;
    ResetTimers();  // Reconfigure without restart
}
```

### Resilience Architecture

**Store-and-Forward (SAF)**:
- Database-backed queue (Transact table)
- State machine: PendConfirm → WaitConfirm → Confirmed
- Configurable retry: AdviceInterval (60s), AdviceMaxRetries (3), AdviceMaxConcurrent (100)
- Automatic expiry: AdviceMaxDays (7 days)
- Supports both confirmations and reversals

**Circuit Breakers**:
- Per-supplier failure tracking
- States: Closed (normal) → Open (failing) → HalfOpen (testing recovery)
- Detects: HTTP errors, timeouts, task cancellations
- Configurable: Failure threshold, reset timeout

**Supplier Status Monitoring**:
- Centralized SupplierStatusModule tracks health per supplier, per node
- Database-backed status (StatusActiveA, StatusActiveB columns)
- Timer-driven health checks in each supplier module
- Merchants query status before routing transactions

**Active-Active HA**:
- Dual nodes (ActiveA, ActiveB) with ReplicationModule
- Database synchronization between nodes
- Each transaction tagged with originating node
- Load distribution and automatic failover

### Message Flow Architecture

**Standard Transaction Flow**:

1. **Inbound**: Merchant module receives request (HTTP, Socket, ISO8583)
2. **Context Creation**: Create Context with routing information (ProductType, TransactType, MessageType, SupplierModuleId)
3. **Credit Check** (if enabled): Office gRPC call to debit merchant balance
4. **Route to Supplier**: EmitMessage(context) → Switch routes via MultiChannel attribute → Supplier receives
5. **External API Call**: Supplier calls external provider API
6. **Response Routing**: Supplier sets MerchantModuleId, EmitMessage(context) → Merchant receives
7. **Return to Caller**: Merchant returns response in original protocol format
8. **Database Audit**: Context persisted to Transact table with full payloads

**If Supplier Fails**:
- Store Context in database (State = PendConfirm)
- TickTimer retrieves PendConfirm transactions (every 60s)
- Retry with exponential backoff
- If max retries exceeded: State = ConfirmFailed
- Credit back merchant balance via Office gRPC (if debited)

---

## Functional Capabilities

### Merchant Channels
- **OmniSocket**: Legacy POS terminals (JSON over TCP, stateful connections)
- **Postilion**: ATM/Bank POS (ISO8583, stateful connections, HSM integration)
- **WebMerchant**: Modern web/mobile merchants (REST API, stateless)
- **Figment**: Payment processing platforms (REST API, stateless)
- **Electrum**: POS integration gateways (REST API, stateless)

### Supplier Integrations
- **Flash** (58 product/transaction combinations): Vouchers, airtime, utilities, bill payment, cash-out PIN, gift vouchers, tokens
- **BlueLabelMobile**: Airtime and data bundle recharge via SOAP
- **BlueLabelBillPayment**: Bill payment services
- **BlueLabelUtilities**: Utilities payment
- **InstantMoney**: Cash transfers and withdrawals
- **PayLater**: Buy-Now-Pay-Later credit facility
- **SmartCall**: Prepaid services
- **Stellr**: Prepaid cards
- **WiCode**: Voucher redemption
- **GiftCard**: Gift card issuance and management
- **BMA**: Specialized payment services
- **EasyPay**: Bill payment gateway
- **TymeBank**: Banking services integration

### Transaction Types Supported
- **Purchase**: Standard product/service purchases
- **Withdraw**: Cash disbursements (InstantMoney, FlashCashOutPin)
- **Redeem**: Voucher redemption
- **Refund**: Transaction reversals and refunds
- **Reprint**: Receipt/voucher reprints
- **Catalog**: Product listing requests
- **Enquiry**: Balance checks, account queries
- **Cancel**: Transaction cancellations

### Message Types
- **Initial**: Pre-validation requests (meter check, account inquiry)
- **Authorize**: Primary transaction authorization
- **Confirm**: Two-phase commit confirmation (for SAF)
- **Reverse**: Transaction rollback/reversal
- **Netman**: Network management (handshakes, echo tests)

---

## Architectural Details

### Plugin Architecture Pattern

**Invocations.cs - Declarative Routing**:
```csharp
public override ModuleInvocation[] module_invocations => [
    // Infrastructure
    new ModuleInvocation<HsmFuturexModule>(),
    new ModuleInvocation<SupplierStatusModule>(),
    new ModuleInvocation<ReplicationModule>(),

    // Merchant → Supplier routing
    new ModuleInvocation<WebMerchantModule>(
        OutputTo<BlueLabelMobileModule>(),
        OutputTo<FlashModule>(),
        OutputTo<PayLaterModule>(),
        OutputTo<SmartCallModule>()
    ),

    // Supplier → Merchant response routing
    new ModuleInvocation<FlashModule>(
        OutputTo<OmniSocketModule>(),
        OutputTo<WebMerchantModule>()
    ),
];
```

**Benefits**:
- Zero-code supplier onboarding (add to configuration only)
- Compile-time routing validation
- Clear visualization of transaction flow

### Context Pattern (Universal Transaction Object)

**Key Properties**:
```csharp
public class Context {
    // Identity
    public Guid Id;
    public DateTimeOffset Timestamp;

    // Routing
    public EnumProductType ProductType;      // Flash1Voucher, Airtime, InstantMoney
    public EnumTransactType TransactType;    // Purchase, Withdraw, Refund
    public EnumMessageType MessageType;      // Authorize, Confirm, Reverse
    public EnumModuleId MerchantModuleId;
    public EnumSupplier SupplierModuleId;

    // State
    public EnumOutcome Outcome;              // Success, ConnectionError, InsufficientFunds
    public EnumTransactState State;          // Authorized, PendConfirm, Confirmed

    // Merchant Context
    public Guid? MerchantId;
    public string MerchantReference;
    public int Amount;  // Always in cents
    public bool CreditCheck;

    // Payloads (JSON serialized)
    public string AuthorizeRequest;
    public string SupplierRequest;
    public string SupplierResponse;
    public string MerchantResponse;

    // Database mapping
    public Transact Transact();  // Convert to/from DB entity
}
```

### Module Base Classes

**BaseMerchant<T>** (Merchant acquisition):
- `MessageReceiver(CommonMessageContainer)`: Receive messages from switch
- `SendToSupplierModule(Context, supplierId)`: Route to supplier
- `ConcurrentDictionary<Guid, PendingRequest>`: Async request/response matching
- Protocol-specific implementations in derived classes

**BaseSupplier<T>** (Supplier integration):
- `ProcessMessage(Context)`: Handle routed transactions
- `SendToMerchantModule(Context, merchantId)`: Return response to merchant
- Timer-driven operations:
  - `TokenTimerCallback()`: OAuth/JWT token refresh
  - `HandshakeTimerCallback()`: Health check API calls
  - `ProductTimerCallback()`: Catalog synchronization
  - `StatusTimerCallback()`: Update supplier availability in database
  - `TickTimerCallback()`: SAF queue processing
- External API client management
- Circuit breaker integration

**BaseOffice<T>** (Administrative services):
- `ReportTimerCallback()`: Scheduled report generation
- gRPC service hosting (Kestrel HTTP/2)
- Database access for configuration and operations

### Integration Architecture

**Office Service Integration**:
```
Supplier Module (e.g., Flash)
    ↓
1. Check if CreditCheck enabled for merchant
2. IF yes: Call OfficeClient.UpdateBalance(merchantId, -amount)  // Debit
3. Office validates merchant, checks funds, updates balance
4. IF insufficient: Return error, transaction rejected
5. IF success: Proceed to external API call
6. IF API fails: Call OfficeClient.UpdateBalance(merchantId, +amount)  // Credit back
```

**Database Integration**:
- Shared SQL Server with Dashboard
- Realtime writes: Transact, Message, SupplierStatus tables
- Dashboard reads: Transaction monitoring and reporting
- Replication table tracks changes for Active-Active sync

---

## Operational Characteristics

**Performance**:
- Throughput: 500+ TPS sustained
- Latency: < 2s merchant-to-supplier round-trip
- Connections: 100+ concurrent merchant connections

**Availability**:
- Target: 99.95% uptime
- HA: Active-Active dual nodes with automatic replication
- SAF: Zero transaction loss during supplier downtime
- Failover: Sub-second for healthy node

**Security**:
- HSM Integration: PCI-DSS compliant PIN operations (Futurex)
- Sensitive Data Masking: Automatic PAN/PIN/voucher PIN masking in logs
- Audit Trail: Complete request/response payloads stored
- gRPC Security: HTTP/2 with optional TLS

**Scalability**:
- Current: Vertical scaling (CPU/RAM on single server)
- Bottlenecks: Single-node processing, database connection limits
- Capacity: 1000+ TPS achievable with hardware upgrades

---

## Key Architecture Patterns

1. **Plugin Architecture**: Declarative module registration (Invocations.cs)
2. **Message-Driven**: Asynchronous, decoupled via CausalNexus message bus
3. **Context Pattern**: Universal transaction object across all modules
4. **Base Class Abstraction**: BaseMerchant/BaseSupplier for consistent development
5. **Timer-Driven Operations**: Automated health, token, catalog management
6. **Hot-Reload Configuration**: FileWatcher for zero-downtime updates
7. **Store-and-Forward**: Database-backed resilience with automatic retry
8. **Circuit Breakers**: Per-supplier failure isolation
9. **Active-Active HA**: Dual-node replication

---

## Technical Considerations

- **Proprietary Framework**: CausalNexus is custom-built, requires specialized knowledge
- **Windows Service**: Requires Windows Server environment and licensing
- **Configuration**: JSON file-based with manual editing
- **Monitoring**: File-based logging, database-backed status tracking
- **Port Management**: Fixed port assignments per module

---

## BMA Blockchain Integration

**Integration Touchpoint**: [Realtime Switch ↔ BMA Blockchain Settlement](BMA_PaymentSwitch_Integration_Analysis.md#2-realtime-switch--bma-blockchain-settlement)

### Realtime as BMA Supplier Module

Realtime Switch extends to blockchain settlement through a new **BMA Blockchain Supplier Module**:

**Integration Pattern**:
- BMA supplier module follows existing module architecture (FrameworkMessageProcessor)
- Transaction state machine unchanged (Authorize → Confirm → Complete → Reverse)
- Blockchain transaction hash stored as SupplierReference
- Smart KYC NFT validation during Authorize phase
- Balance sync between SQL database and blockchain wallets

**New Product Types**:
- Blockchain wallet transfers (merchant to consumer)
- Tokenized asset purchases (ERC20 airtime, ERC1400 fractional assets)
- Cross-border payments via OpenRUN network
- Smart contract-based distributions

**Business Value**:
- Instant settlement (seconds vs T+1/T+2)
- Lower transaction costs (minimal gas fees)
- Immutable audit trail on blockchain
- Regulatory compliance via Smart KYC NFTs
- Traditional payment UX with blockchain benefits

**Technical Integration**: [Full Details](BMA_PaymentSwitch_Integration_Analysis.md#a-new-supplier-module-bma-blockchain-supplier)

---

## Summary

Realtime Switch is a battle-tested central orchestration platform processing 500+ TPS across 100+ merchants and 14+ suppliers. Its plugin architecture enables rapid supplier onboarding, message-driven design provides fault isolation, and comprehensive resilience patterns (SAF, circuit breakers, HA) ensure zero transaction loss. Integration with Office Service provides real-time credit management, while HSM integration ensures PCI-DSS compliance.

**BMA Integration**: Extends to blockchain settlement via new supplier module, enabling instant settlement and tokenized assets while preserving existing transaction patterns.

**Strategic Value**: Demonstrates proven patterns for multi-channel acquisition, multi-provider routing, comprehensive resilience, and operational maturity at enterprise scale.

---

**Document Purpose**: Business and architectural reference for current system
**Audience**: Business stakeholders, solution architects, operations teams
**Scope**: Executive overview, next-gen patterns, functional capabilities, architecture


---

[← Platform Overview](../README.md#top) | [BMA Analysis](Block_Markets_Africa_Analysis.md) | [Collaboration Strategy](BMA_Collaboration_Analysis.md) | [↑ Top](#)

