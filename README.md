# SkyPay Payment Gateway — Complete API & Integration Documentation

<div align="center">

![SkyPay Banner](https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/rainbow.png)

**Automated Multi-Channel MFS Payment Automation Infrastructure for Bangladesh**  
*Accept automated **bKash**, **Nagad**, **Rocket**, and **Upay** payments with zero manual transaction matching.*

[![SkyPay Version](https://img.shields.io/badge/SkyPay%20API-v2.0%20Production-2563eb?style=for-the-badge&logo=powershell&logoColor=white)](https://pay.skypaybd.top/)
[![License](https://img.shields.io/badge/License-Proprietary-0f172a?style=for-the-badge)](https://pay.skypaybd.top/)
[![Last Updated](https://img.shields.io/badge/Updated-12%20September%202026-10b981?style=for-the-badge&logo=clock&logoColor=white)](https://pay.skypaybd.top/)
[![Status](https://img.shields.io/badge/Gateway%20Status-Operational-16a34a?style=for-the-badge&logo=statuspage&logoColor=white)](https://pay.skypaybd.top/)

</div>

---

## 🛠️ Technology Stack & Architecture Badges

The SkyPay platform, its developer SDKs, hosted checkout views, and merchant automation bridge are built using modern industry-standard technologies:

<div align="center">

| Category | Technologies & Tools |
|---|---|
| **Core Backend & Gateway Engine** | [![PHP](https://img.shields.io/badge/PHP%208.x-777BB4?style=for-the-badge&logo=php&logoColor=white)](https://php.net) [![CodeIgniter 4](https://img.shields.io/badge/CodeIgniter%204-EF4223?style=for-the-badge&logo=codeigniter&logoColor=white)](https://codeigniter.com) [![REST API](https://img.shields.io/badge/REST%20API-005571?style=for-the-badge&logo=fastapi&logoColor=white)](https://restfulapi.net) |
| **Frontend & Template Engine** | [![Blade Template Views](https://img.shields.io/badge/Blade%20Views-F05340?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com/docs/blade) [![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://w3.org/html) [![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)](https://w3.org/Style/CSS) [![JavaScript ES6+](https://img.shields.io/badge/JavaScript%20ES6+-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript) |
| **Data Interchange & Network** | [![JSON](https://img.shields.io/badge/JSON-000000?style=for-the-badge&logo=json&logoColor=white)](https://json.org) [![cURL](https://img.shields.io/badge/cURL-073551?style=for-the-badge&logo=curl&logoColor=white)](https://curl.se) [![HTTPS / SSL](https://img.shields.io/badge/SSL%20%2F%20TLS-22c55e?style=for-the-badge&logo=letsencrypt&logoColor=white)](https://letsencrypt.org) |
| **Hardware & SMS Synchronization** | [![Android Device Bridge](https://img.shields.io/badge/Android%20SMS%20Bridge-3DDC84?style=for-the-badge&logo=android&logoColor=white)](https://developer.android.com) [![GSM MFS Network](https://img.shields.io/badge/MFS%20Telco%20Bridge-0284c7?style=for-the-badge&logo=cellular&logoColor=white)](https://pay.skypaybd.top) |
| **E-Commerce & Client Ecosystem** | [![WooCommerce](https://img.shields.io/badge/WooCommerce-96588A?style=for-the-badge&logo=woocommerce&logoColor=white)](https://woocommerce.com) [![WordPress](https://img.shields.io/badge/WordPress-21759B?style=for-the-badge&logo=wordpress&logoColor=white)](https://wordpress.org) [![WHMCS](https://img.shields.io/badge/WHMCS-535353?style=for-the-badge&logo=serverfault&logoColor=white)](https://whmcs.com) [![Telegram Bot](https://img.shields.io/badge/Telegram%20Bot%20API-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://core.telegram.org/bots/api) [![Sketchware](https://img.shields.io/badge/Sketchware%20SWB-EA4335?style=for-the-badge&logo=googleplay&logoColor=white)](https://pay.skypaybd.top) |

</div>

---

## 📋 Table of Contents

- [1. Executive Overview](#1-executive-overview)
- [2. Architectural Philosophy: Hosted Gateway vs Headless API v2](#2-architectural-philosophy-hosted-gateway-vs-headless-api-v2)
- [3. Authentication & Security Headers](#3-authentication--security-headers)
- [4. API 1: Hosted Checkout Gateway (v1 — Web Redirect Flow)](#4-api-1-hosted-checkout-gateway-v1--web-redirect-flow)
  - [4.1 Architecture & Workflow Diagram](#41-architecture--workflow-diagram)
  - [4.2 Create Payment URL Endpoint](#42-create-payment-url-endpoint)
  - [4.3 Return Callback & Query Parameters](#43-return-callback--query-parameters)
  - [4.4 Verify Hosted Payment Endpoint](#44-verify-hosted-payment-endpoint)
- [5. API 2: Headless Payment API (v2 — Zero Redirect / In-App / Bot)](#5-api-2-headless-payment-api-v2--zero-redirect--in-app--bot)
  - [5.1 3-Step Headless Architecture](#51-3-step-headless-architecture)
  - [5.2 Step 1: Initiate Payment Session](#52-step-1-initiate-payment-session)
  - [5.3 Step 2: In-App / Telegram Bot Wallet Presentation](#53-step-2-in-app--telegram-bot-wallet-presentation)
  - [5.4 Step 3: Verify Transaction via Device SMS Sync](#54-step-3-verify-transaction-via-device-sms-sync)
  - [5.5 Headless Integration Best Practices & Rules](#55-headless-integration-best-practices--rules)
- [6. Architectural Comparison Matrix](#6-architectural-comparison-matrix)
- [7. Merchant Mobile App & Android SMS Synchronization Engine](#7-merchant-mobile-app--android-sms-synchronization-engine)
- [8. Ready-Made CMS Plugins & Module Downloads](#8-ready-made-cms-plugins--module-downloads)
- [9. Error Codes, Status Dictionary & Troubleshooting](#9-error-codes-status-dictionary--troubleshooting)
- [10. Production Security & Deployment Checklist](#10-production-security--deployment-checklist)

---

## 1. Executive Overview

**SkyPay BD** is a high-availability payment automation gateway engineered specifically for businesses, online stores, digital agencies, and independent creators operating in Bangladesh. 

Accepting Mobile Financial Services (MFS) like **bKash**, **Nagad**, **Rocket**, and **Upay** traditionally involves either expensive aggregator contracts with high transaction fees, or cumbersome manual verification where merchants manually check SMS logs on personal phones before fulfilling customer orders.

SkyPay bridges this gap through an **intelligent Android hardware notification bridge** combined with a high-throughput cloud API engine. Incoming SMS notifications on the merchant's physical Android smartphone are encrypted and streamed in real-time to the SkyPay backend. When a customer inputs their Transaction ID (TrxID), SkyPay instantly validates the amount, sender/receiver wallet, and payment method against device SMS logs within **5 to 20 seconds**.

### Supported Payment Channels & Modes

| Channel | Personal (Send Money) | Agent (Cash In) | Merchant (Payment) |
|---|:---:|:---:|:---:|
| **bKash** | ✅ Yes | ✅ Yes | ✅ Yes |
| **Nagad** | ✅ Yes | ✅ Yes | ❌ Under Review |
| **Rocket** | ✅ Yes | ✅ Yes | ❌ Under Review |
| **Upay** | ✅ Yes | ❌ No | ❌ Under Review |

---

## 2. Architectural Philosophy: Hosted Gateway vs Headless API v2

SkyPay delivers two distinct integration paradigms to serve diverse technical stacks and checkout experiences:

### Model A: Hosted Checkout Gateway (`v1`)
- **How it works:** The merchant server requests a checkout session; SkyPay returns a unique `payment_url`. The merchant temporarily redirects the customer’s browser to `https://pay.skypaybd.top/checkout/order/...`.
- **Customer Experience:** The customer lands on SkyPay’s hosted UI, picks their preferred wallet (bKash/Nagad/Rocket/Upay), views the merchant number, sends money from their own mobile app, enters their TrxID, and clicks verify.
- **Return Callback:** On verification, SkyPay redirects the user back to the merchant’s `success_url` or `cancel_url` with GET parameters (`transactionId`, `paymentMethod`, `paymentAmount`, `status`).
- **Ideal For:** Traditional websites, WordPress, WooCommerce stores, WHMCS hosting portals, Shopify custom apps, and CMS carts where merchants want a turnkey UI without writing frontend code.

### Model B: Headless Payment API (`v2` — Zero Redirect)
- **How it works:** The merchant backend communicates directly with SkyPay’s API endpoints using JSON. No customer ever visits SkyPay’s website.
- **Customer Experience:** 100% white-label and native. The customer remains entirely inside your Telegram Bot, Discord Bot, mobile application (Flutter, React Native, Swift, Kotlin), or modern Single-Page Web App (React, Next.js, Vue).
- **Workflow:** 
  1. Your bot/app calls `/api/v2/payment/create` with customer name and amount.
  2. SkyPay responds with the active merchant phone numbers for bKash, Nagad, Rocket, and Upay.
  3. Your bot/app presents these numbers to the user with tailored instructions.
  4. The user sends money, inputs their SMS Transaction ID (TrxID) directly into your bot chat or mobile input field.
  5. Your backend posts the TrxID to `/api/v2/payment/verify`. SkyPay validates against synced SMS records and returns instant success.
- **Ideal For:** Telegram Bots, mobile apps, SaaS micro-billing, custom React/Vue checkouts, embedded desktop software.

---

## 3. Authentication & Security Headers

Every request sent to SkyPay APIs must include authentication headers. Authentication keys are generated inside your **SkyPay Merchant Dashboard** under the **Brands** management tab.

### Required HTTP Headers

| Header | Value | Requirement | Description |
|---|---|:---:|---|
| `BRAND-KEY` | `your_active_brand_key` | **Mandatory** | Unique merchant identifier generated in your SkyPay Brand dashboard. Identifies which merchant account and Android device will process the transaction. |
| `Content-Type` | `application/json` | **Mandatory** | Standard payload format. `application/x-www-form-urlencoded` is also supported for legacy cURL scripts. |
| `Accept` | `application/json` | **Recommended** | Informs the gateway to return JSON error and success payloads. |

> 🔒 **Security Notice:** Never expose your `BRAND-KEY` in public client-side JavaScript, mobile app client bundles, or frontend repositories. All SkyPay API requests must originate strictly from your secure backend server, cloud function, or Telegram bot server.

---

## 4. API 1: Hosted Checkout Gateway (v1 — Web Redirect Flow)

### 4.1 Architecture & Workflow Diagram

```
+---------------+              +---------------+              +-----------------+
| Customer      |              | Merchant      |              | SkyPay Cloud    |
| Browser       |              | Backend       |              | Gateway         |
+---------------+              +---------------+              +-----------------+
        |                              |                               |
        |  1. Place Order on Store     |                               |
        |----------------------------->|                               |
        |                              |  2. POST /api/payment/create  |
        |                              |------------------------------>|
        |                              |  3. Return { payment_url }    |
        |                              |<------------------------------|
        |  4. HTTP 302 Redirect to payment_url                         |
        |------------------------------------------------------------->|
        |                                                              |
        |  5. Customer selects MFS, sends money & inputs TrxID         |
        |  6. SkyPay matches SMS with Merchant Android Phone           |
        |                                                              |
        |  7. Redirect to Merchant success_url?transactionId=XYZ...    |
        |<-------------------------------------------------------------|
        |                                                              |
        |  8. Query Success Page       |                               |
        |----------------------------->|  9. POST /api/payment/verify  |
        |                              |------------------------------>|
        |                              | 10. Return { status: COMPLETED }
        |                              |<------------------------------|
        | 11. Display Order Confirmed  |                               |
        |<-----------------------------|                               |
```

---

### 4.2 Create Payment URL Endpoint

Creates a pending checkout session and returns a secure payment link where the customer completes the transaction.

- **URL:** `https://pay.skypaybd.top/api/payment/create`
- **Method:** `POST`
- **Authentication:** `BRAND-KEY` headers

#### Request Headers

```http
POST /api/payment/create HTTP/1.1
Host: pay.skypaybd.top
BRAND-KEY: b8c34f5982e0a2938472bf6d
Content-Type: application/json
```

#### Request Parameters

| Parameter | Type | Required | Description | Example |
|---|---|:---:|---|---|
| `cus_name` | `String` | **Yes** | Full name of the customer | `"John Doe"` |
| `cus_email` | `String` | **Yes** | Customer email address for order notifications | `"customer@example.com"` |
| `amount` | `Numeric` | **Yes** | Total payable amount in BDT (natural numbers or 2 decimals) | `500` or `500.00` |
| `success_url` | `String (URL)` | **Yes** | Fully qualified URL where customer is redirected upon successful payment | `"https://mystore.com/checkout/success"` |
| `cancel_url` | `String (URL)` | **Yes** | Fully qualified URL where customer is redirected if they cancel | `"https://mystore.com/checkout/cancel"` |
| `metadata` | `Object / JSON` | **No** | Arbitrary merchant data (order ID, customer phone, cart tokens) | `{"order_id": 10452, "user_id": 982}` |

#### Concise Request Payload

```json
{
  "cus_name": "Rahim Khan",
  "cus_email": "rahim@gmail.com",
  "amount": "750.00",
  "success_url": "https://mystore.com/payment/success",
  "cancel_url": "https://mystore.com/payment/cancel",
  "metadata": {
    "order_id": "ORD-98214",
    "phone": "017XXXXXXXX"
  }
}
```

#### Success Response (`200 OK`)

```json
{
  "status": true,
  "message": "Payment URL generated successfully.",
  "payment_url": "https://pay.skypaybd.top/checkout/order/a8c2f10b7d9e4a3c"
}
```

#### Error Response (`400 / 401`)

```json
{
  "status": false,
  "message": "Invalid BRAND-KEY or inactive merchant account."
}
```

---

### 4.3 Return Callback & Query Parameters

Upon completing the checkout on SkyPay, the customer’s browser is automatically redirected to your designated `success_url` (or `cancel_url`) with the following URL query string parameters attached:

```
https://mystore.com/payment/success?transactionId=BLA38KDK2M&paymentMethod=bkash&paymentAmount=750.00&paymentFee=0.00&status=completed
```

#### Callback Parameters Breakdown

| Query Parameter | Type | Sample Value | Explanation |
|---|---|---|---|
| `transactionId` | `String` | `BLA38KDK2M` | Unique SMS Transaction ID (TrxID) verified from the user's MFS payment. |
| `paymentMethod` | `String` | `bkash` | Channel utilized: `bkash`, `nagad`, `rocket`, or `upay`. |
| `paymentAmount` | `Numeric` | `750.00` | The exact verified amount received in BDT. |
| `paymentFee` | `Numeric` | `0.00` | Gateway fee deducted (if applicable). |
| `status` | `String` | `completed` | Outcome status: `completed`, `pending`, or `failed`. |

> ⚠️ **Best Practice Warning:** Never mark an order fulfilled solely based on GET parameters received in the customer browser URL, as query parameters can be spoofed. Always execute a server-side call to `/api/payment/verify` to confirm that the transaction exists in SkyPay database.

---

### 4.4 Verify Hosted Payment Endpoint

Validates the transaction status on the SkyPay server using the `transaction_id`.

- **URL:** `https://pay.skypaybd.top/api/payment/verify`
- **Method:** `POST`
- **Authentication:** `BRAND-KEY: your_brand_key`

#### Request Payload

```json
{
  "transaction_id": "BLA38KDK2M"
}
```

#### Success Response (`200 OK`)

```json
{
  "status": "COMPLETED",
  "cus_name": "Rahim Khan",
  "cus_email": "rahim@gmail.com",
  "amount": "750.00",
  "transaction_id": "BLA38KDK2M",
  "payment_method": "bkash",
  "metadata": {
    "order_id": "ORD-98214",
    "phone": "017XXXXXXXX"
  }
}
```

#### Verification Status Dictionary

| Status Value | Meaning | Action Recommended |
|---|---|---|
| `COMPLETED` | Transaction matched incoming SMS, amount verified, funds confirmed. | Fulfill order, deliver digital goods, or credit wallet balance. |
| `PENDING` | Customer initiated order but SMS is not yet matched or verified. | Do not fulfill; advise customer to wait or check TrxID. |
| `ERROR` | Transaction ID invalid, already claimed, or amount mismatched. | Prompt customer to enter valid TrxID or contact support. |

---

## 5. API 2: Headless Payment API (v2 — Zero Redirect / In-App / Bot)

### 5.1 3-Step Headless Architecture

The Headless API v2 gives you complete autonomy over the user interface. It is purposefully engineered for:
- 🤖 **Telegram Bots & Discord Bots** (Chatbot shopping & subscription billing)
- 📱 **Native Mobile Apps** (Flutter, React Native, Swift iOS, Kotlin Android)
- ⚡ **Single-Page Applications** (Next.js, React, Vue, Svelte custom checkouts)
- 🖥️ **Desktop Client Applications** (Electron, C#, Python apps)

```
[Customer inside Telegram Bot / Mobile App]
                  │
                  ▼
  [1. User requests: "Deposit 500 BDT"]
                  │
  [2. Your Bot calls: POST /api/v2/payment/create]
                  │
  [3. SkyPay returns Active Merchant Numbers]
      - bKash: 01761844968 (Personal/Payment)
      - Nagad: 01761844968 (Personal)
                  │
  [4. Bot displays wallet details to user in chat]
      "Please Send Money 500 BDT to bKash: 01761844968
       Then enter your SMS TrxID here:"
                  │
  [5. User sends money via bKash App & enters TrxID: 'BLA38KDK2M']
                  │
  [6. Your Bot calls: POST /api/v2/payment/verify]
                  │
  [7. SkyPay matches incoming Android SMS in real-time (5-20s)]
                  │
  [8. Success: Bot immediately upgrades user account!]
```

---

### 5.2 Step 1: Initiate Payment Session

Initializes a pending payment session and retrieves real-time merchant wallet numbers active on your connected Android devices.

- **URL:** `https://pay.skypaybd.top/api/v2/payment/create`
- **Method:** `POST`
- **Headers:** `BRAND-KEY: your_brand_key`, `Content-Type: application/json`

#### Request Parameters

| Parameter | Type | Required | Description | Example |
|---|---|:---:|---|---|
| `cus_name` | `String` | **Yes** | Name of the customer | `"Siyam Ahmed"` |
| `amount` | `Numeric` | **Yes** | Amount in BDT to deposit or pay (must be > 0) | `500` |
| `meta_data` | `Object / JSON` | **No** | Custom metadata payload | `{"telegram_user_id": 12345678, "product_id": "VIP_SUB_01"}` |

#### Concise Request Payload

```json
{
  "cus_name": "Siyam Ahmed",
  "amount": 500,
  "meta_data": {
    "telegram_user_id": 12345678,
    "product_id": "VIP_SUB_01"
  }
}
```

#### Success Response (`200 OK`)

```json
{
  "status": true,
  "id": "a1b2c3d4e5f6g7h8",
  "brand": {
    "name": "My Tech Store",
    "mobile": "017XXXXXXXX",
    "whatsapp": "017XXXXXXXX",
    "email": "support@mytechstore.com"
  },
  "methods": [
    {
      "name": "bkash",
      "active_payments": {
        "personal": true,
        "agent": false,
        "payment": true
      },
      "personal": "01761844968",
      "agent": "",
      "payment": "01761844968"
    },
    {
      "name": "nagad",
      "active_payments": {
        "personal": true,
        "agent": false
      },
      "personal": "01761844968",
      "agent": ""
    },
    {
      "name": "rocket",
      "active_payments": {
        "personal": true,
        "agent": false
      },
      "personal": "017257649946",
      "agent": ""
    },
    {
      "name": "upay",
      "active_payments": {
        "personal": true,
        "agent": false
      },
      "personal": "017284684646",
      "agent": ""
    }
  ]
}
```

#### Key Fields in the Response

- `id`: The unique payment session identifier. **You must store this ID** in your database or session cache to pass into the verify endpoint in Step 3.
- `methods`: Dynamic array of gateways. Each gateway specifies which payment types are currently active (`personal` for Send Money, `agent` for Cash Out, `payment` for Merchant Pay). If a number is empty or its active flag is `false`, do not present that option to the user.

---

### 5.3 Step 2: In-App / Telegram Bot Wallet Presentation

When your backend receives the response from `/create`, you present the details to the customer within your interface.

#### Example Telegram Bot Message Format
```
💳 Order #VIP_SUB_01 | Payable: 500 BDT

Please send the exact amount to any of our official wallets:
• bKash (Personal): 01761844968 (Send Money)
• Nagad (Personal): 01761844968 (Send Money)
• Rocket (Personal): 017257649946 (Send Money)

After completing the payment in your MFS app, please reply with your Transaction ID (TrxID):
```

---

### 5.4 Step 3: Verify Transaction via Device SMS Sync

When the customer submits their TrxID into your bot or custom checkout screen, your backend sends a verification request to match incoming device SMS notifications.

- **URL:** `https://pay.skypaybd.top/api/v2/payment/verify`
- **Method:** `POST`
- **Headers:** `BRAND-KEY: your_brand_key`, `Content-Type: application/json`

#### Request Parameters

| Parameter | Type | Required | Description | Example |
|---|---|:---:|---|---|
| `id` | `String` | **Yes** | The payment session `id` returned by `/create` | `"a1b2c3d4e5f6g7h8"` |
| `method` | `String` | **Yes** | Channel name in **lowercase**: `bkash`, `nagad`, `rocket`, or `upay` | `"bkash"` |
| `transaction_id` | `String` | **Yes** | The SMS Transaction ID (TrxID) submitted by customer | `"BLA38KDK2M"` |

#### Concise Request Payload

```json
{
  "id": "a1b2c3d4e5f6g7h8",
  "method": "bkash",
  "transaction_id": "BLA38KDK2M"
}
```

#### Success Response (`200 OK`)

```json
{
  "status": true,
  "amount": "500.00",
  "cus_name": "Siyam Ahmed",
  "id": "a1b2c3d4e5f6g7h8"
}
```

#### Error Responses (`400 / 404`)

| HTTP Code | Error Message | Cause / Solution |
|---|---|---|
| `400` | `Invalid transaction ID or transaction already used.` | The TrxID does not match any incoming SMS, or has already been claimed by another order. |
| `404` | `Payment session not found or expired.` | The session `id` does not exist or expired. Re-initiate `/create`. |
| `400` | `This payment session has already been completed.` | Prevent double fulfillment; session already marked verified. |
| `400` | `Unsupported payment method supplied.` | Supplied method is not `bkash`, `nagad`, `rocket`, or `upay`. |
| `401` | `Invalid or inactive BRAND-KEY provided.` | Check your Merchant Dashboard Brand Key. |

---

### 5.5 Headless Integration Best Practices & Rules

1. **⏱️ Android SMS Bridge Sync Latency (5 to 20 Seconds):**
   When a customer completes payment in their bKash or Nagad app, the telco sends an SMS to the merchant's physical Android SIM. The SkyPay Android Sync App intercepts this SMS and relays it to the database. This entire bridge takes between **5 to 20 seconds**. If a customer inputs their TrxID instantaneously, the SMS may still be in transit.
   * **Rule:** Do not immediately reject verification permanently on the first attempt. If verification returns false, prompt the user: *"SMS matching in progress. Please retry in 10 seconds."* Implement a grace period of 2–3 minutes with retry buttons.

2. **🔒 Idempotency & Replay Attack Protection:**
   Once an SMS `transaction_id` matches and completes an order, it is marked as `status = 1 (claimed)` in the central database. **No single TrxID can ever be reused or claimed twice.** Subsequent attempts will fail immediately with `Invalid transaction ID or transaction already used.`

3. **🔡 Strict Lowercase Method Names:**
   Always pass the `method` string in strict lowercase. E.g.:
   - ✅ `"bkash"`, `"nagad"`, `"rocket"`, `"upay"`
   - ❌ `"Bkash"`, `"BKASH"`, `"NagadPay"`

4. **💰 Amount Matching:**
   SkyPay verifies that the amount received in the device SMS matches the amount specified during `/create`. If a user pays 400 BDT for a 500 BDT order, the verification will fail.

---

## 6. Architectural Comparison Matrix

| Dimension | Hosted Gateway (`v1`) | Headless API (`v2`) |
|---|---|---|
| **Checkout Flow** | External web redirect | 100% In-App / In-Bot (Zero Redirect) |
| **User Interface** | Pre-built SkyPay hosted page | Custom built by merchant |
| **Telegram / Bot Compatibility** | Requires opening external browser link | Native chat interaction |
| **Session Tracking** | Return callback URL query string | Session `id` returned via JSON |
| **Authentication Required** | `BRAND-KEY` | `BRAND-KEY` |
| **Wallet Numbers** | Rendered automatically on gateway | Fetched dynamically via `/create` payload |
| **Customer Data Needed** | `cus_name`, `cus_email`, `amount` | `cus_name`, `amount` |
| **Development Effort** | 15 Minutes (Plug & Play) | 1–2 Hours (Custom UI & Logic) |
| **Best Used For** | WordPress, WooCommerce, WHMCS, SMM | Telegram Bots, Discord Bots, Mobile Apps |

---

## 7. Merchant Mobile App & Android SMS Synchronization Engine

SkyPay’s automated zero-fee payment infrastructure is powered by our official **SkyPay Merchant Sync Android Application**.

### How the Synchronization Bridge Operates

```
[Customer Pays from bKash App]
              │
              ▼ (Telco GSM SMS Network)
[Merchant Android Smartphone]
   └── SkyPay Merchant Sync APK (Background Service)
              │
              ▼ (Encrypted TLS API Stream)
[SkyPay Cloud Transaction Matcher]
              │
              ▼ (Real-Time Database Match)
[Merchant Backend Verification API]
```

### Application Specifications & Setup

- **Download Link:** `/public/assets/downloads/SkyPay.apk`
- **OS Requirement:** Android 7.0 (Nougat) or higher.
- **Hardware Requirement:** Any entry-level Android device equipped with your active merchant SIM cards (bKash, Nagad, Rocket, Upay).
- **Setup Steps:**
  1. Download and install `SkyPay.apk` on your merchant device.
  2. Grant **SMS Notification Listener** and **Battery Optimization Exemption** permissions so the app stays active 24/7.
  3. Open the app and input your unique **BRAND-KEY** from your SkyPay Dashboard.
  4. Tap **Connect Device**. The device status will turn **Active (Green)** on your merchant dashboard.
  5. The bridge is now fully operational! Every incoming cash-in or send-money SMS will instantly sync to your SkyPay account.

---

## 8. Ready-Made CMS Plugins & Module Downloads

For merchants utilizing popular content management, e-commerce, and billing platforms, SkyPay provides pre-built, production-tested modules that require zero custom coding:

### 1. WordPress & WooCommerce Plugin
- **Download:** `/public/assets/downloads/WP.zip`
- **Compatibility:** WordPress 5.8+ · WooCommerce 6.0+ · PHP 7.4 to 8.2
- **Features:** 
  - Adds SkyPay as a native checkout gateway in WooCommerce settings.
  - Automatic order status update to `Processing` or `Completed` upon successful payment.
  - Automatic cancellation callback handling for abandoned carts.
- **Installation:** Upload via WordPress Admin Dashboard &rarr; *Plugins* &rarr; *Add New* &rarr; *Upload Plugin*, enter Brand Key in WooCommerce Payment Settings.

### 2. WHMCS Web Hosting Billing Module
- **Download:** `/public/assets/downloads/WHMCS.zip`
- **Compatibility:** WHMCS 7.x & 8.x
- **Features:**
  - Automatically marks invoices as `Paid` upon verification.
  - Instant service provisioning for cPanel, VPS, and domain registrations.
- **Installation:** Extract to `modules/gateways/` in your WHMCS root directory and activate in *Payment Gateways*.

### 3. SMM Panel Auto-Balance Module
- **Download:** `/public/assets/downloads/SMM.zip`
- **Compatibility:** SmartPanel, PerfectPanel, and standard SMM script architectures.
- **Features:**
  - Automated user balance crediting 24/7 without manual admin approval.
  - Fraud prevention and duplicate TrxID rejection.

### 4. Sketchware Android SWB Module
- **Download:** `/public/assets/downloads/SWB.zip`
- **Features:** Complete block project for visual mobile app developers to accept in-app payments.

---

## 9. Error Codes, Status Dictionary & Troubleshooting

### Common HTTP Status Codes

| Code | Status | Meaning & Resolution |
|---|---|---|
| `200` | `OK` | Request succeeded. Check `"status": true` in JSON response. |
| `400` | `Bad Request` | Missing required parameters, invalid amount, or mismatched TrxID. |
| `401` | `Unauthorized` | Missing or invalid `BRAND-KEY` header. |
| `403` | `Forbidden` | No active Android SMS device connected. Ensure `SkyPay.apk` is running. |
| `404` | `Not Found` | Payment session ID expired or does not exist. |
| `405` | `Method Not Allowed` | Only `POST` requests are accepted. |
| `500` | `Internal Error` | SkyPay server error. Contact SkyPay support. |

### Top 4 Integration Pitfalls & Solutions

1. **"No active SMS sync device found for this account" (`403 Forbidden`):**
   * *Cause:* Your Android smartphone running `SkyPay.apk` has disconnected, lost internet access, or the battery saver closed the app.
   * *Fix:* Open the SkyPay app on the phone, ensure background execution is allowed, and confirm the connection indicator shows green.

2. **Customer Paid but Verification Returns "Invalid transaction ID":**
   * *Cause:* Customer submitted their TrxID before the SMS arrived from the telecom operator.
   * *Fix:* Implement a 10-second retry delay in your bot/app.

3. **Wrong Payment Method Supplied:**
   * *Cause:* Sending `"Bkash"` instead of lowercase `"bkash"`.
   * *Fix:* Normalize all gateway names using `.toLowerCase()` before posting.

4. **Spoofed Callbacks on Hosted Gateway:**
   * *Cause:* Relying only on browser redirect parameters without verification.
   * *Fix:* Always perform a backend POST to `/api/payment/verify` before granting credits.

---

## 10. Production Security & Deployment Checklist

Before launching your integration into live production, ensure your setup meets these security criteria:

- [ ] **Environment Variables:** `BRAND-KEY` are securely stored in server `.env` files and never checked into Git.
- [ ] **Server-Side Only:** No client-side JavaScript or mobile app frontends make direct calls to SkyPay API.
- [ ] **HTTPS Enforced:** All merchant callback and webhook endpoints use valid TLS/SSL certificates (`https://`).
- [ ] **Retry Grace Period:** Bot or checkout UI provides a 10–20 second retry window for SMS synchronization.
- [ ] **Idempotency Guard:** Database checks that an order has not already been fulfilled before granting balance.
- [ ] **Android Device Care:** Merchant synchronization device is plugged into power, connected to high-speed WiFi, and has battery optimization disabled.

---

<div align="center">

**SkyPay BD — Automated Payment Infrastructure**  
*Built for High-Growth Bangladeshi Merchants, Telegram Bots & Platforms*

[Merchant Portal](https://pay.skypaybd.top) • [Support Telegram](https://t.me/skypaybd) • [Official Website](https://pay.skypaybd.top)

</div>
