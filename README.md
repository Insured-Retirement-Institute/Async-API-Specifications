# IRI Financial Transaction Day‑2 Confirmation Events

## Overview

This repository contains the standardized AsyncAPI specification for Financial Transaction Day‑2 Confirmation Events.

The specification defines how Financial Transaction confirmations are published asynchronously through Advanced Event Mesh after downstream transaction processing has completed.

Supported transaction families include:

- One-Time Withdrawal (OTW)
- Systematic Program (Systematic Withdrawal / Systematic RMD)
- Dollar Cost Average (DCA)
- Auto Rebalance
- Index Performance Lock (IPL)
- Systematic Investment (SI)
- Allocation Instructions (AI)
- Fund Transfer (FT)

This specification provides:

- Standardized Day‑2 event notifications
- Event routing conventions
- Common confirmation payload structure
- Correlation and lifecycle tracking standards
- Advanced Event Mesh integration patterns
- Transaction-family-specific confirmation schemas

---

## Business Case

### Problem Statement

Financial transactions frequently require downstream policy administration processing after the original request is accepted.

Examples include:

- One-Time Withdrawals
- Systematic Programs
- Dollar Cost Average Arrangements
- Auto Rebalance Arrangements
- Index Performance Lock Arrangements
- Systematic Investment Arrangements
- Allocation Instruction Arrangements
- Fund Transfers

Because processing is not always completed during the initial API request, firms require a standardized method to receive final transaction outcomes.

Without a common Day‑2 confirmation model, firms must:

- Poll carrier systems continuously
- Implement carrier-specific reconciliation logic
- Build custom status tracking processes
- Manage inconsistent confirmation mechanisms

### Objectives

- Standardize Day‑2 confirmation events across Financial Transaction APIs.
- Support asynchronous processing patterns.
- Provide consistent event structures and routing patterns.
- Improve transaction reconciliation and auditability.
- Enable enterprise-scale event-driven architecture.
- Ensure reliable end-to-end correlation using requestId and correlationId.

### Key Features

- AsyncAPI 3.0.0 compliant specification.
- Event-driven Day‑2 confirmation notifications.
- Standardized event envelope shared across transaction families.
- Consistent routing through Advanced Event Mesh.
- Transaction-family-specific payload extensions.
- Support for lifecycle tracking and operational monitoring.
- Idempotent processing support through unique event identifiers.

---

## Supported Transaction Families

### 1. One-Time Withdrawal (OTW)

Provides Day‑2 confirmation notifications for:

- One-Time Partial Withdrawals
- Full Surrenders

#### Day‑2 Message

`Day2WithdrawalConfirmation`

#### Key Tracking Attributes

- requestId
- correlationId
- policyNumber
- transactionSubType
- associatedFirmId

---

### 2. Systematic Program

Provides Day‑2 confirmation notifications for:

- Systematic Program Setup
- Systematic Program Update
- Systematic Program Cancellation

#### Day‑2 Message

`Day2SystematicProgramConfirmation`

#### Key Tracking Attributes

- requestId
- correlationId
- policyNumber
- associatedFirmId
- externalArrangementId
- arrangementType

---

### 3. Arrangement Transactions

Provides Day‑2 confirmation notifications for:

- Dollar Cost Average (DCA)
- Auto Rebalance
- Index Performance Lock (IPL)
- Systematic Investment (SI)
- Allocation Instructions (AI)

#### Day‑2 Messages

- `Day2DcaConfirmation`
- `Day2AutoRebalanceConfirmation`
- `Day2IndexPerformanceLockConfirmation`
- `Day2SystematicInvestmentConfirmation`
- `Day2AllocationInstructionConfirmation`

#### Shared Day‑2 Schema

All arrangement transaction families use the shared message payload:

`Day2ArrangementConfirmation`

#### Key Tracking Attributes

- requestId
- correlationId
- policyNumber
- associatedFirmId
- transactionType

---

### 4. Fund Transfer

Provides Day‑2 confirmation notifications for:

- Fund-to-Fund Transfers

#### Day‑2 Message

`Day2FundTransferConfirmation`

#### Key Tracking Attributes

- requestId
- correlationId
- policyNumber
- associatedFirmId

---

## Event Routing Model

### Channel

```text
{root}/ift/v1/{eventType}/{status}/{transactionType}/{firm}
```

### Topic Segments

| Segment | Description |
|----------|----------|
| root | Environment topic prefix |
| ift | In-force transactions namespace |
| v1 | Contract major version |
| eventType | Day‑2 confirmation event type |
| status | Processing outcome |
| transactionType | Transaction classification |
| firm | Associated firm identifier |

### Supported Event Types

- DAY2_CONFIRM
- day2confirm

**Note:** One-Time Withdrawal uses the legacy value `day2confirm` while later Financial Transaction APIs use `DAY2_CONFIRM`.

### Supported Status Values

- SUCCESS
- SUCCESS_WITH_INFO
- FAILURE

### Supported Transaction Types

- one-time-partials
- full-surrenders
- SUBMIT_SYSTEMATIC_PROGRAM
- UPDATE_SYSTEMATIC_PROGRAM
- CANCEL_SYSTEMATIC_PROGRAM
- SUBMIT_DOLLAR_COST_AVERAGE
- UPDATE_DOLLAR_COST_AVERAGE
- CANCEL_DOLLAR_COST_AVERAGE
- SUBMIT_AUTO_REBALANCE
- UPDATE_AUTO_REBALANCE
- CANCEL_AUTO_REBALANCE
- SUBMIT_INDEX_PERFORMANCE_LOCK
- UPDATE_INDEX_PERFORMANCE_LOCK
- CANCEL_INDEX_PERFORMANCE_LOCK
- SUBMIT_SYSTEMATIC_INVESTMENT
- UPDATE_SYSTEMATIC_INVESTMENT
- CANCEL_SYSTEMATIC_INVESTMENT
- SUBMIT_ALLOCATION_INSTRUCTIONS
- UPDATE_ALLOCATION_INSTRUCTIONS
- CANCEL_ALLOCATION_INSTRUCTIONS
- FUND_TRANSFERS

### Example Topic

```text
prod/ift/v1/DAY2_CONFIRM/SUCCESS/SUBMIT_SYSTEMATIC_PROGRAM/FIRM123
```

---

## Event Lifecycle

### Day‑1 Processing

The originating Financial Transaction API accepts a request and returns:

- requestId
- correlationId

### Day‑2 Processing

Once downstream processing completes:

- Carrier validation is completed
- Business processing is completed
- Final outcome is determined
- A Day‑2 confirmation event is published

### Correlation Model

Consumers should use:

- requestId
- correlationId

to correlate Day‑2 confirmations with the originating Day‑1 transaction request.

For duplicate detection and idempotent processing, consumers should use:

- eventId

---

## Common Day‑2 Confirmation Envelope

All Day‑2 confirmation events inherit from the same base envelope.

### Required Attributes

- eventType
- eventId
- eventTimestamp
- associatedFirmId
- requestId
- correlationId
- policyNumber
- status
- nsccParticipantId
- npn
- transExeDate
- transExeTime

### Optional Attributes

- message
- effectiveDate

### Envelope Schema

| Field | Required | Description |
|----------|----------|----------|
| eventType | Yes | Type of Day‑2 event published to the event mesh |
| eventId | Yes | Globally unique event identifier |
| eventTimestamp | Yes | Timestamp when the event was published |
| associatedFirmId | Yes | Firm identifier associated with the transaction |
| requestId | Yes | Original Day‑1 request identifier |
| correlationId | Yes | Client-supplied correlation identifier |
| policyNumber | Yes | Policy number associated with the transaction |
| status | Yes | Final processing outcome |
| message | No | Human-readable explanation of the outcome |
| effectiveDate | No | Effective date of the transaction |
| npn | Yes | National Producer Number |
| nsccParticipantId | Yes | NSCC participant identifier |
| transExeDate | Yes | Processing execution date |
| transExeTime | Yes | Processing execution time |

---

## Transaction-Specific Schemas

### Day2WithdrawalConfirmation

Confirmation for one-time partial withdrawals, RMD withdrawals, and full surrenders.

#### Required Attributes

- transactionType

#### Optional Attributes

- transactionSubType

#### Schema Attributes

| Field | Required | Description |
|----------|----------|----------|
| transactionType | Yes | High-level classification of the withdrawal transaction |
| transactionSubType | No | Subtype of the withdrawal transaction when applicable |

#### Supported transactionType Values

- one-time-partials
- full-surrenders

#### Supported transactionSubType Values

- specified-withdrawal
- surrender-free
- rider-free
- interest-only
- rmd

---

### Day2SystematicProgramConfirmation

Confirmation for systematic withdrawal and systematic RMD arrangements.

#### Required Attributes

- transactionType
- arrangementType
- externalArrangementId

#### Schema Attributes

| Field | Required | Description |
|----------|----------|----------|
| transactionType | Yes | Systematic program transaction classification |
| arrangementType | Yes | Type of arrangement established by the transaction |
| externalArrangementId | Yes | Arrangement identifier supplied by the external system |

#### Supported transactionType Values

- SUBMIT_SYSTEMATIC_PROGRAM
- UPDATE_SYSTEMATIC_PROGRAM
- CANCEL_SYSTEMATIC_PROGRAM

#### Supported arrangementType Values

- PAYMENT
- WITHDRAWAL
- LOAN_REPAYMENT
- PAYOUT
- REQUIRED_MINIMUM_DISTRIBUTION

---

### Day2ArrangementConfirmation

Confirmation for arrangement-based transactions.

This shared confirmation schema is used by multiple arrangement transaction families but maintains a common event structure and routing model.

- Dollar Cost Average (DCA)
- Auto Rebalance
- Index Performance Lock (IPL)
- Systematic Investment (SI)
- Allocation Instructions (AI)

#### Required Attributes

- transactionType

#### Schema Attributes

| Field | Required | Description |
|----------|----------|----------|
| transactionType | Yes | Arrangement transaction classification |

#### Supported transactionType Values

##### Dollar Cost Average

- SUBMIT_DOLLAR_COST_AVERAGE
- UPDATE_DOLLAR_COST_AVERAGE
- CANCEL_DOLLAR_COST_AVERAGE

##### Auto Rebalance

- SUBMIT_AUTO_REBALANCE
- UPDATE_AUTO_REBALANCE
- CANCEL_AUTO_REBALANCE

##### Index Performance Lock

- SUBMIT_INDEX_PERFORMANCE_LOCK
- UPDATE_INDEX_PERFORMANCE_LOCK
- CANCEL_INDEX_PERFORMANCE_LOCK

##### Systematic Investment

- SUBMIT_SYSTEMATIC_INVESTMENT
- UPDATE_SYSTEMATIC_INVESTMENT
- CANCEL_SYSTEMATIC_INVESTMENT

##### Allocation Instructions

- SUBMIT_ALLOCATION_INSTRUCTIONS
- UPDATE_ALLOCATION_INSTRUCTIONS
- CANCEL_ALLOCATION_INSTRUCTIONS

---

### Day2FundTransferConfirmation

Confirmation for fund-to-fund and segment-based value transfers.

#### Required Attributes

- transactionType

#### Schema Attributes

| Field | Required | Description |
|----------|----------|----------|
| transactionType | Yes | Fund transfer transaction classification |

#### Supported transactionType Values

- FUND_TRANSFERS

---

## Event Outcomes

Supported processing outcomes:

### SUCCESS

Transaction completed successfully.

### SUCCESS_WITH_INFO

Transaction completed successfully but additional informational details are available.

### FAILURE

Transaction processing failed.

Additional details may be provided in the message attribute.


---

## Advanced Event Mesh

Day‑2 confirmations are delivered through Advanced Event Mesh.

Consumers subscribe to topic patterns and receive notifications after processing completes.

The event mesh serves as the enterprise distribution mechanism for Day‑2 confirmation events.

---

## AsyncAPI Specification

The AsyncAPI specification includes:

- Channel definitions
- Event routing patterns
- Message definitions
- Payload schemas
- Topic parameters
- Message examples
- Reusable schema components
- Day‑1 source specification references

The specification conforms to AsyncAPI 3.0.0.

---

## How to Contribute

- Fork the repository and submit pull requests.
- Report issues via the repository Issues section.
- Participate in IRI working groups and design reviews.

---

## Business Owners

- **Carrier Business Owner:** digitalfirst@brighthousefinancial.com
- **Distributor Business Owner:** [contact]
- **Solution Provider Business Owner:** [contact]

---

## Versioning

- Follow semantic versioning for specification updates.
- Maintain backward compatibility whenever possible.
- Document changes through changelogs and release notes.

