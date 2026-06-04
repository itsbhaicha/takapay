# Taka Pay - Commercial Automated Payment Gateway Platform

<p align="center">
  <img src="https://i.postimg.cc/rpH8d33H/takapay.png" alt="Taka Pay Banner" width="100%" />
</p>

<p align="center">
  <strong>An enterprise-grade, high-performance payment orchestration platform designed for local digital wallets in Bangladesh.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Stars%20Goal-1%2C000%20to%20Open%20Source-6f42c1?style=for-the-badge&logo=github" alt="Stars Goal" />
  <img src="https://img.shields.io/badge/Framework-CodeIgniter%204.x-F27420?style=for-the-badge&logo=codeigniter" alt="CodeIgniter 4" />
  <img src="https://img.shields.io/badge/License-Proprietary-red?style=for-the-badge" alt="Proprietary License" />
</p>

<p align="center">
  🎯 <strong>Milestone Alert:</strong> We're Going Open Source at 1,000 Stars! Star this repository, share it with your developer community, and help us make open-source automated fintech accessible to everyone in Bangladesh.
</p>

---

## 📌 Platform Overview

**Taka Pay** is a next-generation, self-hosted commercial automated payment gateway utility engineered on top of the robust **CodeIgniter 4 MVC framework**. Optimized for scaling digital platforms, online shops, and enterprise SaaS systems, Taka Pay bridges the gap between manual mobile financial service (MFS) accounts and automated billing. 

By leveraging low-latency Android metadata and notification-based automation overlays, Taka Pay automates incoming transaction verifications for **bKash, Nagad, and Rocket** merchant and personal configurations in real-time, matching the operational capability of modern local payment aggregators.

---

## 🖥️ Platform Architecture & GUI Modules

Taka Pay is split into four decoupled user interfaces and services to streamline payment workflows:

### 1. Merchant Control Panel (Tailwind CSS)
A beautiful, responsive administrative interface built specifically for merchants to manage operations without visual clutter.
*   **Performance Dashboards:** Live tracking of daily volume, success rates, and average settlement times.
*   **API Management:** Self-service production and sandbox credential generation.
*   **Transaction Ledgers:** Comprehensive filtering options by Transaction ID, sender number, payment method, and operational status.
*   **Webhook Logs:** Real-time delivery tracking with response code captures to inspect failed endpoint pings.

### 2. Super Admin Control Center
A control tower designed for server administrators to coordinate global platform behavior.
*   **Routing Protocols:** Automatically distribute incoming traffic between secondary or fallback merchant wallets during peak times.
*   **Fee Adjustments:** Configure customizable percentage-based or flat rates for individual merchants.
*   **System Analytics:** Monitor live transaction queues, background workers, and overall database transaction logs.

### 3. Mobile-Responsive Checkout UI
A universal, distraction-free transaction screen optimized for mobile screens and low-bandwidth connections.
*   **Dynamic Instructions:** Tailored payment walk-throughs for Cash-Out, Send Money, or Merchant Pay modes based on the wallet types.
*   **Countdown Timers:** Expiration safeguards to avoid outdated, orphaned payment attempts.
*   **Live AJAX Validation:** Immediate verification feedback as soon as a user clicks the "Verify" button.

### 4. Tasker & Android SMS Relay Interface
An integrated listener workflow designed to parse incoming payment confirmation SMS strings into transaction objects.
*   **Pattern Matching:** Advanced regular expressions configured for major telecom providers and MFS formats.
*   **Instant Sync:** Secure metadata delivery via authorized POST payloads directly to the gateway core.
*   **Fallback Handling:** Support for manual reconciliation requests if network delays interrupt background push tasks.

---

## 📂 CodeIgniter 4 Directory Map

The platform is structured using the standard CodeIgniter 4 framework layout to maintain optimal performance, secure routing, and clean separation of concerns:

```text
taka-pay/
├── app/
│   ├── Config/
│   │   ├── App.php                  # Global application configuration settings
│   │   ├── Database.php             # Database credentials and transaction connection pooling
│   │   ├── Filters.php              # Auth filters & API security middleware
│   │   └── Routes.php               # Unified routing table for Checkout and Admin endpoints
│   ├── Controllers/
│   │   ├── Admin/                   # System-wide super administrative controller logic
│   │   ├── Api/
│   │   │   └── V1/
│   │   │       ├── Callback.php     # Handlers for incoming Android SMS push events
│   │   │       └── Checkout.php     # Endpoint processing engine for checkout creation
│   │   ├── Merchant/                # Merchant portal account control logic
│   │   └── Home.php                 # Core entry point and gateway redirection handler
│   ├── Models/
│   │   ├── MerchantModel.php        # Manage account balances, limits, and profiles
│   │   ├── TransactionModel.php     # Handle status updates, amounts, and transaction IDs
│   │   └── WebhookModel.php         # Logging delivery responses and dispatch payloads
│   └── Views/
│       ├── admin/                   # Blade-like view components for core administrators
│       ├── checkout/                # Responsive payment templates and timer interfaces
│       └── merchant/                # Tailwind CSS dashboard layouts for business owners
├── public/
│   ├── assets/
│   │   ├── css/                     # Compiled Tailwind utility styling files
│   │   ├── js/                      # Frontend validation logic and active timers
│   │   └── images/                  # Core branding materials and payment channel icons
│   └── index.php                    # System entry point
├── writable/                        # Session buffers, runtime logs, and cache targets
├── .env.example                     # Environment configuration base template
└── spark                            # CodeIgniter CLI commands panel
```

---

## 🔌 Merchant API Documentation

Integrate Taka Pay into your billing or checkout workflow using standard JSON REST requests.

### 1. Create Checkout Session
Initiate a transaction session. Securely generate a dynamic checkout URL to redirect your customers.

**Endpoint:** `POST /api/v1/checkout/create`

#### Request Payload
```json
{
  "api_key": "tp_live_9f82a177b83d4411802931a1",
  "amount": 1500.00,
  "currency": "BDT",
  "order_id": "ORDER-99281A",
  "customer_name": "Rahat Ahmed",
  "customer_email": "rahat@domain.com",
  "customer_phone": "01700000000",
  "success_url": "https://merchant.com/checkout/success",
  "fail_url": "https://merchant.com/checkout/failed",
  "cancel_url": "https://merchant.com/checkout/cancelled"
}
```

#### Response Payload
```json
{
  "status": "success",
  "message": "Checkout session successfully created.",
  "payment_id": "TXN_SESSION_83921008271",
  "checkout_url": "https://gateway.takapay.com/checkout/pay/TXN_SESSION_83921008271",
  "amount": 1500.00,
  "created_at": "2026-06-04 06:06:00"
}
```

---

### 2. Secure Webhook Signature Verification
Once a user confirms their transaction, Taka Pay transmits a cryptographically signed POST payload directly to your registered server endpoint. 

#### Webhook Request Payload
```json
{
  "event": "transaction.completed",
  "payment_id": "TXN_SESSION_83921008271",
  "order_id": "ORDER-99281A",
  "amount": 1500.00,
  "charge_applied": 15.00,
  "net_settled": 1485.00,
  "payment_method": "bkash",
  "sender_number": "017XXXXXXXX",
  "transaction_id": "ALJ8HDH762",
  "status": "COMPLETED",
  "timestamp": "2026-06-04 06:07:15"
}
```

#### Security Implementation Schema
To ensure integrity and prevent replay attacks, every payload includes a custom security header generated using your secret merchant configuration:

```text
Header Key: X-TakaPay-Signature
Algorithm: HMAC-SHA256
Signature Logic: HashHMAC("sha256", raw_json_payload, merchant_api_secret)
```

**Verification Steps for Integration:**
1. Intercept the incoming raw string response payload *before* any framework-level JSON parsing occurs.
2. Calculate the local payload signature using the raw body payload combined with your merchant private key.
3. Use a time-constant string comparison function to verify that your calculated signature matches the value inside the `X-TakaPay-Signature` header.

---

## 📜 Intellectual Property & Licensing Disclaimer

**Copyright © 2026 Taka Pay Team. All Rights Reserved.**

This software platform is developed and maintained under a strict proprietary license. Any unauthorized reproduction, distribution, reverse engineering, decompilation, translation, or alteration of code structures, visual designs, database schemas, or automation workflows constitutes copyright infringement and will be prosecuted under applicable digital asset protection laws.

* **Usage Limitations:** Access to system files and implementation templates is granted solely to registered enterprise operators under active agreement contracts.
* **Code Modification:** Custom layout styling modification is allowed solely within the parameters of designated user configurations, provided copyright ownership markers remain completely intact within dynamic views.
* **Open Source Transition:** As stated, core components of this system are scheduled to transition into public licensing modes upon reaching the specified milestone target of 1,000 GitHub Stars.

---

<p align="center">
  <sub>Taka Pay is designed to support development, research, and payment integration tasks for merchants in Bangladesh.</sub>
</p>
