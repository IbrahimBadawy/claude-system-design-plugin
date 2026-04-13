# Example: /design payment-system

This is an example of what the plugin produces when designing a payment system.

## Input
```
/design payment-system
```

## Expected Output Structure

### Step 1: Clarifying Questions
- What payment methods? (Credit card, bank transfer, wallets)
- What scale? (transactions/day, peak during events)
- Multi-currency support needed?
- Which PSPs to integrate? (Stripe, PayPal, local providers)
- Refund/chargeback requirements?
- Compliance requirements? (PCI DSS, regional regulations)

### Step 2: Requirements
**Functional**: Process payments, handle refunds, transaction history, webhook handling, reconciliation
**Non-Functional**: 99.99% availability, p99 < 500ms, exactly-once processing, zero money loss

### Step 3: Estimation
```
DAU: 1M users, 5% make payment/day = 50K payments/day
QPS: 50K / 86400 = ~0.6 QPS avg, ~2 QPS peak
Storage: 50K * 1KB = 50MB/day, 18GB/year
```

### Step 4: API Design
```
POST /api/v1/payments          (create payment, idempotency-key required)
GET  /api/v1/payments/:id      (get payment status)
POST /api/v1/payments/:id/refund  (initiate refund)
POST /api/v1/webhooks/stripe   (PSP callback)
GET  /api/v1/payments          (list with filters, cursor pagination)
```

### Step 5: Architecture
```
Client -> API Gateway -> Payment Service -> PSP (Stripe)
                              |                    |
                        Payment DB           Webhook callback
                        (PostgreSQL)               |
                              <- Update status ----
```

### Step 6: Deep Dive
- **Idempotency**: UUID key per payment, PSP deduplicates
- **Double-entry bookkeeping**: Every txn has debit + credit = 0
- **State machine**: CREATED -> PROCESSING -> COMPLETED/FAILED/REFUNDED
- **Reconciliation**: Daily batch comparing internal ledger with PSP

### Step 7: Wrap Up
- Retry ON network errors, NOT ON business errors (insufficient funds)
- PCI DSS: Never store card numbers, use PSP tokenization
- Monitoring: Track payment success rate, latency, reconciliation mismatches

## What Makes This Good
1. Referenced the Payment System design reference automatically
2. Idempotency was enforced (rule: "every write must answer: what if sent twice?")
3. Security considered from the start (rule: security-by-default)
4. Failure scenarios addressed (rule: think-about-failure)
5. Scaling plan included even for low QPS (rule: design for 10x, build for 1x)
