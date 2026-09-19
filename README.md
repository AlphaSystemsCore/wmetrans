# M-Pesa + WhatsApp Order Automation

A backend project that models how Kenyan businesses could automate the workflow between customer orders, WhatsApp communication, M-Pesa payments, and order tracking.

The project focuses on the **backend workflow and state management** rather than building the external WhatsApp and M-Pesa integrations themselves.

## Problem

Many small businesses receive customer orders through channels such as WhatsApp or Instagram and receive payment through M-Pesa.

A typical workflow may involve:

1. Customer sends an order through WhatsApp.
2. Business creates or records the order.
3. Customer pays through M-Pesa.
4. Business manually checks the M-Pesa transaction.
5. Business confirms the payment.
6. Business updates the order.
7. Business communicates the order status back to the customer.

This creates repetitive manual work and introduces opportunities for mistakes, duplicate processing, and payment verification problems.

## Project Goal

This project explores how the workflow could be automated by placing a backend system between the communication and payment platforms.

The intended architecture is:

```text
Customer
   │
   │ WhatsApp
   ▼
Backend
   │
   ├── Orders
   ├── Customers
   ├── Payments
   ├── Order State
   └── Notifications
   │
   ├───────────────┐
   ▼               ▼
WhatsApp         M-Pesa
```

## Naive Implementation

The first version intentionally does **not** integrate with the real WhatsApp or M-Pesa APIs.

Instead, those external systems are simulated.

```text
Simulated WhatsApp
        │
        ▼
     Backend
        │
        ▼
    PostgreSQL
        │
        ▼
Simulated M-Pesa
        │
        ▼
 Payment Processing
        │
        ▼
   Order Updated
        │
        ▼
Simulated WhatsApp
```

The goal is to build the complete internal workflow before introducing external platform dependencies.

## What the System Should Demonstrate

The system should be able to:

* create customers
* create orders
* generate unique order numbers
* track order status
* associate payments with orders
* simulate M-Pesa payment notifications
* verify payments against orders
* prevent duplicate payment processing
* update order state after successful payment
* simulate customer notifications
* maintain a history of important order events

## Example Workflow

A customer places an order:

```text
Customer
   ↓
"2 burgers"
   ↓
Order created
   ↓
Order #ORD-1001
   ↓
Amount: KSh 800
   ↓
AWAITING_PAYMENT
```

The simulated payment system then sends:

```text
Transaction:
ABC123XYZ

Amount:
800

Phone:
2547XXXXXXXX
```

The backend verifies the payment:

```text
Payment valid
        ↓
Order #ORD-1001
        ↓
PAID
```

The system can then simulate sending a confirmation message to the customer.

## Order States

The initial order state machine is:

```text
CREATED
   ↓
AWAITING_PAYMENT
   ↓
PAID
   ↓
PROCESSING
   ↓
COMPLETED
```

Possible failure states include:

```text
AWAITING_PAYMENT
        ↓
     EXPIRED

PAID
 ↓
CANCELLED
```

The backend should prevent invalid state transitions.

## Important Constraints

The system should maintain several invariants:

* Every payment belongs to an existing order.
* A payment transaction reference cannot be processed more than once.
* A payment must correspond to the expected order/payment amount according to the business rules.
* An order cannot be marked as paid without a valid payment.
* Invalid order-state transitions must be rejected.

## Technology

Initial implementation:

* Python
* FastAPI
* PostgreSQL
* psycopg2
* HTTP/REST
* Docker
* Git/GitHub

The external WhatsApp and M-Pesa APIs are intentionally replaced with local simulators.

## Project Scope

This is a learning project and a **naive first implementation**.

It is not intended to be a production payment platform.

The purpose is to understand the backend architecture required to connect:

```text
Communication
      +
Orders
      +
Payments
      +
State Management
      +
Notifications
```

before dealing with the complexity of external platform APIs.

## Future Integration

A later version could replace the simulated components with real integrations:

```text
Simulated WhatsApp
        ↓
WhatsApp Business API

Simulated M-Pesa
        ↓
M-Pesa API
```

The internal order and payment architecture should remain largely independent of those external providers.

## Engineering Questions

The project is intentionally used to explore questions such as:

* How should order states be modeled?
* How should payments be associated with orders?
* How should duplicate payment callbacks be handled?
* What happens if a payment callback is delivered twice?
* What happens if the backend crashes after receiving a payment?
* How should transactions be made idempotent?
* How should failed payments be represented?
* What happens when payment and order updates occur concurrently?
* How should external webhook requests be authenticated?
* How should the system recover from partially completed workflows?

These questions are part of the project rather than problems to hide behind the implementation.
