# Citi_hackathon
GharTak is a cross-border remittance platform that optimizes FX costs, enables purpose-based payments, and builds verifiable financial histories. It uses provider optimization, HMAC-signed hash-chained receipts, transaction analysis, explainable financial profiling, and consent-controlled APIs to improve transparency and financial inclusion.


# GharTak

**Send smart. Send outcomes. Build credit.**

Built for the Citi x Drunix Hackathon · Domain: Cross-Border Remittances with Financial Inclusion

> Remittance shouldn't just move money. It should move it cheaply, move it with purpose, and turn it into a future.

---

## Table of Contents

1. [Overview](#overview)
2. [The Problem](#the-problem)
3. [Existing Solutions and the Gap](#existing-solutions-and-the-gap)
4. [Our Approach](#our-approach)
5. [Features](#features)
6. [User Journeys](#user-journeys)
7. [System Architecture](#system-architecture)
8. [Core Logic](#core-logic)
9. [Tech Stack](#tech-stack)
10. [Data Sources and Simulation Policy](#data-sources-and-simulation-policy)
11. [Challenges We Expect (and How We Handle Them)](#challenges-we-expect-and-how-we-handle-them)
12. [Regulatory and Privacy Considerations](#regulatory-and-privacy-considerations)
13. [Getting Started](#getting-started)
14. [Evaluation](#evaluation)
15. [Business Model](#business-model)
16. [Roadmap](#roadmap)
17. [Team](#team)

---

## Overview

GharTak is a remittance layer that covers the full loop, from what a transfer costs, to what it accomplishes, to what it builds for the family receiving it.

A migrant worker opens the app, sees how much they lost to hidden fees, compares providers on the amount that actually arrives, splits one transfer into goals (school fees, medicine, rent, savings) that are paid directly to each payee, and gives the family a verifiable record of every payment. With the family's consent, that record becomes an explainable income-stability signal a lender can read.

GharTak does not hold or move money. It compares, orchestrates, and verifies. Licensed partners execute the transfers.

## The Problem

Migrant workers send money home every month, and three things consistently go wrong.

**1. The cost is hidden.**
Workers lose roughly 5-8% on a typical transfer. Most of that loss isn't the visible fee. It sits in the exchange rate the provider offers compared with the mid-market rate. Rates, fees, and delivery speed vary by provider and by time of day, and nobody tells the sender which option is best right now.

**2. Senders have no control over how money is used.**
When someone sends money home for school fees or medical care, they have two options: send cash and trust it goes where it should, or phone the family for proof. Neither is reliable, and both can strain the relationship.

**3. Families stay financially invisible.**
A household can receive regular remittances for a decade and still have no credit history. Without a file, formal lenders can't assess them, so families fall back on moneylenders with far worse terms.

## Existing Solutions and the Gap

The market is not empty. Several kinds of products solve part of this problem.

| Category | Examples | What they do well | Where they stop |
|---|---|---|---|
| Digital remittance providers | Wise, Remitly, Western Union, Xoom | Fast, convenient transfers; some publish transparent pricing | Each promotes its own rates. None will tell a sender that a competitor is cheaper today. |
| Comparison sites | Monito, provider-comparison tools | Side-by-side fees and rates across providers | Ranking often centers on the visible fee, and there is no view of what happens after the money leaves. |
| Public price benchmarks | World Bank Remittance Prices Worldwide | Authoritative corridor-level cost data | It's a dataset for policymakers, not a tool a worker uses on payday. |
| Bank transfer channels | Bank wires, SWIFT-based transfers | Trusted and regulated | Often slower and costlier, with little transparency on the exchange margin. |
| Credit bureaus and alternative scoring | CIBIL and cash-flow-based underwriting models | Established credit infrastructure | Require existing credit or bank history. Remittance-only households are left out. |

*Please double-check the specific product names and claims above before the final submission, since providers change their offerings often.*

**The gap.** Comparison tools stop at fees. Remittance apps stop at sending. Nobody connects the cost of the transfer to its purpose, and nobody turns the transfer history into something a lender can use. GharTak is built around that missing connection.

## Our Approach

GharTak is a three-step flow with one hook.

**The hook: Loss Tracker.** "You lost ₹X to hidden fees this year." A 12-month replay shows the worker what they've already lost, which makes the case for the rest of the product.

**Step 1: Send Smart (Optimizer).**
Compare providers on the exact amount received. A hidden-margin X-ray shows the gap between each provider's offered rate and the mid-market rate. The app also suggests a good window to send.

**Step 2: Send Outcomes (Earmarked Remittance).**
Split one transfer into goals. Each portion is paid directly to its payee (the school, the clinic, the landlord), and a signed receipt appears in the family's WhatsApp in their own language.

**Step 3: Build Credit (Passport).**
With scoped, time-limited, revocable consent, the family's inflows and verified receipts become a plain-language income-stability signal that a lender can review.

## Features

### P0: Core demo
- Provider comparison by exact delivered amount
- Hidden-margin X-ray (offered rate vs. mid-market rate)
- Goal-based split with simulated direct payments to payees
- Signed, hash-chained receipts
- Family view over WhatsApp, in the local language
- Loss Tracker with 12-month replay

### P1: Strong additions
- Passport score with plain-language reasons
- Consent screen: scoped, time-limited, revocable
- "Best window to send" based on rate position and provider timing patterns
- Target-rate alerts

### P2: Polish
- Voice-note support in the local language
- Lender-side view
- Multiple corridors (for example UAE to India, Saudi Arabia to India)
- Shared control, so recipients can request a change to a goal

## User Journeys

**Ramesh, sender in Dubai**
1. Opens GharTak and sees that he lost ₹14,000 last year.
2. Enters ₹20,000. The X-ray shows his usual app costs ₹1,300 more than the best option.
3. Splits the amount across school fee, medicine, rent, and savings.
4. Confirms, then tracks each payment and its receipt.

**Sunita, recipient in India**
1. Receives a WhatsApp message in her language: "School fee paid. Receipt attached."
2. Replies to request a change, for example "medicine needed more this month."
3. Months later, sees a consent request from a lender and chooses to approve or refuse it.

## System Architecture

```
  Sender Web App (React)          Family WhatsApp Bot
              \                         /
               \                       /
          +------ API Gateway (FastAPI) ------+
          |                  |                |
   Optimizer Service   Earmark Service   Passport Service
   (quotes, X-ray,     (splits, payee    (consent, features,
    timing)             calls, receipts)  score, reasons)
          |                  |                |
   Redis (rate cache,   Mock Payee APIs   Mock AA-style
   pub/sub, WebSockets) (school, clinic,  consent service
          |              landlord)              |
          +-------------- PostgreSQL -----------+
```

**Components**

- **Optimizer service** pulls mid-market rates, applies each provider's fee and markup, and computes the delivered amount and hidden margin.
- **Earmark service** turns a transfer into allocations, calls mock payee APIs, and issues signed receipts.
- **Passport service** builds features from consented data, produces the score and reasons, and enforces consent scope and expiry.
- **Mock services** simulate providers, an Account Aggregator-style consent flow, and payees. All are clearly labeled as simulations.
- **Realtime layer** uses Redis and WebSockets to push live rate and status updates to the UI.

### Core data model

| Table | Key fields |
|---|---|
| `users` | id, role, language |
| `recipients` | id, sender_id, relationship |
| `providers` | id, name, fee_model, markup_pattern |
| `quotes` | provider_id, corridor, mid_rate, offered_rate, fee, timestamp |
| `transfers` | id, sender_id, amount, corridor, provider_id, delivered_amount |
| `allocations` | transfer_id, goal, share, payee_id, status |
| `payees` | id, type, name, public_key |
| `receipts` | id, allocation_id, payload, signature, prev_hash, hash |
| `consents` | id, subject, requester, scope, expires_at, revoked_at |
| `passport_scores` | recipient_id, score, reasons_json, computed_at |

## Core Logic

### Hidden margin and delivered amount

```
delivered_INR     = (send_amount − fee) × offered_rate
hidden_margin_%   = (mid_rate − offered_rate) / mid_rate × 100
total_cost_%      = (send_amount × mid_rate − delivered_INR)
                    / (send_amount × mid_rate) × 100
```

Providers are ranked by `delivered_INR` and nothing else.

### Best-window guidance

We do not predict exchange rates. Instead, we show:

- **Rate position:** where today's rate sits within its 30-day range.
- **Volatility:** when volatility is low, timing matters little, and we say so.
- **Provider timing patterns:** markups by weekday and hour, learned from quote history.
- **Target-rate alerts:** a notification when the user's chosen rate is reached.

### Signed, hash-chained receipts

- The payee signs each receipt payload with HMAC.
- `hash_i = SHA-256(receipt_i || hash_(i−1))`, so any edit to history breaks the chain.
- The family view shows a "verified" tick only when both the signature and the chain check out.

### Passport score

- **Features:** months of history, regularity (variation in monthly inflow), average inflow, sender consistency, share of inflow backed by verified receipts, and recent trend.
- **Model:** a transparent points-based scorecard first, with optional logistic regression later. Per-feature reasons are always shown.
- **An honest note:** we have no real default labels, so the output is an **income-stability signal, not a credit score**. The product says this plainly, and the lender makes the decision.

## Tech Stack

| Layer | Choice |
|---|---|
| Frontend | React, Vite, Tailwind, Recharts |
| Backend | FastAPI (Python) |
| Database | PostgreSQL |
| Cache and realtime | Redis, WebSockets |
| WhatsApp | Meta Cloud API test number or Twilio sandbox |
| Rates and cost data | A free FX API and the World Bank Remittance Prices dataset |
| ML | scikit-learn or XGBoost, SHAP for reasons |
| Vernacular AI | AI4Bharat IndicTrans2 or an LLM API, Whisper for voice |
| Receipts | HMAC signatures and SHA-256 hash chain |
| Deploy | Docker Compose, then Render or Railway |

## Data Sources and Simulation Policy

We want judges and users to always know what is real and what is not.

- **Real:** live mid-market exchange rates and World Bank remittance cost data.
- **Simulated (and labeled on screen):** provider quotes, payee responses, family bank inflows, and lender data.

Every simulated element in the UI carries a visible label.

## Challenges We Expect (and How We Handle Them)

| Challenge | Why it matters | Our approach |
|---|---|---|
| We can't move real money | Moving funds requires licenses we don't have | Simulate the rails with mock services, label them, and describe the licensed-partner path for production |
| Provider quotes are simulated | Numbers could look invented | Ground them in real mid-market rates and World Bank cost data |
| Exchange-rate prediction is unreliable | Overpromising would damage trust | No prediction claims. We show rate position, volatility, provider patterns, and alerts |
| Fake receipts or a gamed Passport | The credit signal is only as good as its inputs | Payee-signed receipts, regularity checks, and use as one signal rather than underwriting |
| It may feel controlling to the family | Earmarking can read as distrust | Shared control, a flexible cash bucket, and change requests from the recipient |
| Scope is too large for a hackathon | Three people, limited time | Strict P0/P1/P2 priorities and a web fallback if WhatsApp breaks |
| Regulatory questions | Remittance and lending are both regulated | No custody, consent-based data, lender decides, legal validation as a defined next step |
| Comparison tools already exist | We'd look like one more of them | Differentiate on outcomes and credit, not comparison |
| Sensitive financial data | Trust and legal exposure | Data minimization, synthetic demo data, deletion on revocation |
| Referral bias | Revenue could distort rankings | Rank by delivered amount only and disclose the revenue model |
| Payee adoption | Schools, clinics, and landlords must accept direct payments | Start with a small set of simulated payees for the demo, and treat onboarding at scale as future work |
| Language and literacy | The family view must actually be usable | Local-language messages, simple wording, and voice notes as a stretch goal |

## Regulatory and Privacy Considerations

These are design principles, not legal advice. Current rules should be verified before being quoted in any pitch.

- **No custody of funds.** GharTak compares and orchestrates. In production, licensed authorized-dealer banks or payment partners would execute transfers.
- **Consent-based data sharing.** An Account Aggregator-style flow: scoped, purpose-limited, time-bound, and revocable.
- **Data minimization.** Collect the minimum, use synthetic data in the demo, and delete data on revocation, following the principles of the Digital Personal Data Protection Act.
- **Lending stays with the lender.** The Passport is decision support only.
- **Transparent ranking.** Rank by delivered amount only, and disclose any referral revenue.

## Getting Started

> Replace the placeholders below with your actual repo details once the code is in place.

```bash


**Environment variables** you'll likely need:

- FX rates API key
- WhatsApp credentials (Meta Cloud API or Twilio sandbox)
- Database and Redis connection strings
- HMAC secret for receipt signing

**Demo data.** A seed script loads synthetic senders, recipients, quote history, and 12 months of inflows so the Loss Tracker and Passport work out of the box.

## Evaluation

We'll measure and report the following, including the cases where the tool doesn't help.

- Average delivered-amount improvement versus the worker's default provider (in simulation)
- Share of transfers where a cheaper option existed
- Percentage of allocations with a verified receipt
- Passport reason coverage (every score has plain-language reasons)
- Time from consent revocation to access removal

## Business Model

- Disclosed referral fees when a transfer completes through a partner provider
- API subscriptions for banks and fintechs that want transparent comparison and outcome-based remittances
- Lender referral fees for Passport-based leads

## Roadmap

- More corridors and providers
- Live provider integrations through licensed partners
- Real Account Aggregator integration for the Passport
- Payee onboarding for schools, hospitals, and landlords at scale
- Savings and insurance goals for families


