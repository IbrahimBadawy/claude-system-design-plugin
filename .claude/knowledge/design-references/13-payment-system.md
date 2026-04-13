# Design Reference: Payment System

## Requirements
- Process payments reliably (exactly-once)
- Integrate with Payment Service Providers (Stripe, PayPal)
- Full audit trail
- Reconciliation with external systems
- Handle refunds, chargebacks

## Architecture
```
Client -> API Gateway -> Payment Service -> PSP (Stripe/PayPal)
                              |                    |
                        Payment DB           Webhook callback
                        (ledger)                   |
                              <--- Update status ---
```

## Critical Patterns

### Exactly-Once Processing
```
Exactly-once = At-least-once (retry) + At-most-once (idempotency)
```
- **Idempotency Key**: Every payment request has a unique UUID
- PSP deduplicates: same key = same result (no double charge)
- Store idempotency key in DB before calling PSP

### Double-Entry Bookkeeping
Every transaction has a debit AND credit entry:
```
| entry_id | transaction_id | account   | type   | amount |
|----------|---------------|-----------|--------|--------|
| 1        | txn_001       | user_wallet | DEBIT  | -100   |
| 2        | txn_001       | merchant    | CREDIT | +100   |
```
Sum of all entries for a transaction = 0 (always balanced)

### State Machine
```
CREATED -> PROCESSING -> COMPLETED
                      -> FAILED
                      -> REFUND_PENDING -> REFUNDED
```
- Only valid transitions allowed (enforced in code)
- Every state change is logged with timestamp

## Payment Flow
1. Client submits payment with idempotency key
2. Payment Service validates, creates record (status: CREATED)
3. Payment Service calls PSP with idempotency key
4. PSP processes and returns result (or webhook later)
5. Update status to COMPLETED or FAILED
6. Return result to client

## Retry Rules
- **Retry ON**: Network timeout, 5xx from PSP, connection reset
- **DO NOT retry ON**: 4xx (insufficient funds, invalid card) - these are permanent failures
- Exponential backoff: 1s, 2s, 4s, 8s, max 3 retries

## Reconciliation
- **Daily batch job**: Compare internal ledger with PSP settlement files
- **Match on**: transaction_id, amount, currency, status
- **Handle mismatches**: Flag for manual review, auto-resolve timing differences
- **Tools**: Flink for real-time recon, Hadoop for batch

## Database Schema
```sql
CREATE TABLE payments (
    id UUID PRIMARY KEY,
    idempotency_key UUID UNIQUE NOT NULL,
    user_id UUID NOT NULL,
    amount DECIMAL(19,4) NOT NULL,
    currency VARCHAR(3) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'CREATED',
    psp_reference VARCHAR(255),
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);

CREATE TABLE ledger_entries (
    id UUID PRIMARY KEY,
    payment_id UUID REFERENCES payments(id),
    account_id UUID NOT NULL,
    entry_type VARCHAR(6) NOT NULL, -- DEBIT or CREDIT
    amount DECIMAL(19,4) NOT NULL,
    created_at TIMESTAMP NOT NULL
);
```

## Security
- PCI DSS: Never store full card numbers. Use PSP tokenization
- Encrypt sensitive fields at rest
- Audit log every payment action
- Rate limit payment endpoints aggressively
- Fraud detection: velocity checks, amount thresholds, geo-anomaly
