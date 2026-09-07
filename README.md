# SkyPay BD — Headless Payment API `v2`

> **SkyPay BD Headless Payment API v2** allows merchants to accept automated **bKash**, **Nagad**, **Rocket**, and **Upay** payments directly within third-party environments such as Telegram Bots, mobile apps, and custom web checkouts — *without redirecting customers to an external gateway page.*

---

## 📋 Table of Contents

- [Architecture Overview](#architecture-overview)
- [Authentication](#authentication)
- [1. Initiate Payment](#1-initiate-payment)
  - [Request Headers](#request-headers)
  - [Request Parameters](#request-parameters)
  - [Sample Request Payload](#sample-request-payload)
  - [Success Response](#success-response-200-ok)
  - [Error Responses](#error-responses)
- [2. Verify Payment](#2-verify-payment)
  - [Request Headers](#request-headers-1)
  - [Request Parameters](#request-parameters-1)
  - [Sample Request Payload](#sample-request-payload-1)
  - [Success Response](#success-response-200-ok-1)
  - [Error Responses](#error-responses-1)
- [cURL Implementation Examples](#curl-implementation-examples)
- [Implementation Best Practices](#implementation-best-practices)

---

## 🏗️ Architecture Overview

The payment flow follows a simple **3-step architecture**:

1. **Initiate Payment** (`/create`) — Merchant backend sends customer details and payable amount to receive active payment wallets *(personal, agent, payment numbers)*.
2. **Collect Payment** — The Telegram Bot / App displays wallet details to the customer.
3. **Verify Transaction** (`/verify`) — Customer inputs the SMS Transaction ID *(TrxID)* into the bot. Merchant backend posts this ID to SkyPay to match incoming device SMS notifications and confirm payment in real-time.

---

## 🔐 Authentication

Every API request requires the merchant's **secret brand key** passed as a custom HTTP header.

| Header | Description | Required |
|---|---|---|
| `BRAND-KEY` | Active unique brand key generated from SkyPay Merchant Panel | ✅ Yes |
| `Content-Type` | `application/json` (or `application/x-www-form-urlencoded`) | ✅ Yes |

---

## 1. Initiate Payment

Creates a **pending payment session** and retrieves merchant wallet details.

| Property | Value |
|---|---|
| **Endpoint** | `https://pay.skypaybd.top/api/v2/payment/create` |
| **Method** | `POST` |
| **Access** | Public / Merchant Backend |

### Request Headers

```http
POST /api/v2/payment/create HTTP/1.1
Host: pay.skypaybd.top
BRAND-KEY: your_brand_key_here
Content-Type: application/json
```

### Request Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `cus_name` | `String` | ✅ Yes | Name of the customer *(e.g., "Rahim Ahmed")* |
| `amount` | `Numeric` | ✅ Yes | Amount in BDT to deposit/pay *(must be greater than 0)* |
| `meta_data` | `Object / JSON` | ❌ No | Optional metadata payload *(e.g., `{"user_id": 10293, "chat_id": 987654}`)* |

### Sample Request Payload

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

### Success Response `200 OK`

```json
{
  "status": true,
  "id": "a1b2c3d4e5f6g7h8",
  "brand": {
    "name": "My Store",
    "mobile": "017XXXXXXXX",
    "whatsapp": "017XXXXXXXX",
    "email": "support@mystore.com"
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

### Error Responses

<details>
<summary><strong>401 Unauthorized</strong> — Missing <code>BRAND-KEY</code> Header</summary>

```json
{
  "status": false,
  "message": "BRAND-KEY header is required."
}
```

</details>

<details>
<summary><strong>401 Unauthorized</strong> — Invalid <code>BRAND-KEY</code></summary>

```json
{
  "status": false,
  "message": "Invalid or inactive BRAND-KEY provided."
}
```

</details>

<details>
<summary><strong>403 Forbidden</strong> — No Active SMS Device</summary>

```json
{
  "status": false,
  "message": "No active SMS sync device found for this account."
}
```

</details>

<details>
<summary><strong>400 Bad Request</strong> — Invalid Amount or Name</summary>

```json
{
  "status": false,
  "message": "Valid cus_name and numeric amount are required."
}
```

</details>

<details>
<summary><strong>405 Method Not Allowed</strong> — Invalid HTTP Method</summary>

```json
{
  "status": false,
  "message": "Method not allowed. Only POST requests are accepted."
}
```

</details>

---

## 2. Verify Payment

Validates customer transaction ID against **SMS records** synced via the merchant's connected Android device.

| Property | Value |
|---|---|
| **Endpoint** | `https://pay.skypaybd.top/api/v2/payment/verify` |
| **Method** | `POST` |
| **Access** | Public / Merchant Backend |

### Request Headers

```http
POST /api/v2/payment/verify HTTP/1.1
Host: pay.skypaybd.top
BRAND-KEY: your_brand_key_here
Content-Type: application/json
```

### Request Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | `String` | ✅ Yes | The session `id` returned by the `/create` endpoint |
| `method` | `String` | ✅ Yes | Payment gateway channel: `bkash`, `nagad`, `rocket`, or `upay` |
| `transaction_id` | `String` | ✅ Yes | The transaction ID received by the user from their payment app/SMS |

### Sample Request Payload

```json
{
  "id": "a1b2c3d4e5f6g7h8",
  "method": "bkash",
  "transaction_id": "BLA38KDK2M"
}
```

### Success Response `200 OK`

```json
{
  "status": true,
  "amount": "500.00",
  "cus_name": "Siyam Ahmed",
  "id": "a1b2c3d4e5f6g7h8"
}
```

### Error Responses

<details>
<summary><strong>400 Bad Request</strong> — Transaction Not Found or Amount Mismatched</summary>

```json
{
  "status": false,
  "message": "Invalid transaction ID or transaction already used."
}
```

</details>

<details>
<summary><strong>404 Not Found</strong> — Payment Session Not Found</summary>

```json
{
  "status": false,
  "message": "Payment session not found or expired."
}
```

</details>

<details>
<summary><strong>400 Bad Request</strong> — Session Already Completed</summary>

```json
{
  "status": false,
  "message": "This payment session has already been completed."
}
```

</details>

<details>
<summary><strong>400 Bad Request</strong> — Unsupported Gateway</summary>

```json
{
  "status": false,
  "message": "Unsupported payment method supplied."
}
```

</details>

---

## 🧪 cURL Implementation Examples

### 1. Initiate Request

```bash
curl -X POST https://pay.skypaybd.top/api/v2/payment/create \
  -H "BRAND-KEY: b8c34f5982e0a2938472bf6d" \
  -H "Content-Type: application/json" \
  -d '{
    "cus_name": "Rahim Khan",
    "amount": 350,
    "meta_data": {"user_id": 9012}
  }'
```

### 2. Verify Request

```bash
curl -X POST https://pay.skypaybd.top/api/v2/payment/verify \
  -H "BRAND-KEY: b8c34f5982e0a2938472bf6d" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "a1b2c3d4e5f6g7h8",
    "method": "nagad",
    "transaction_id": "71C2K4J0"
  }'
```

---

## ✅ Implementation Best Practices

> Follow these guidelines to ensure reliable and secure integration.

- **⏱️ Polling or Retry Interval** — Do not immediately reject verification if the user pays instantly. The Android device notification bridge requires **5 to 20 seconds** to sync incoming SMS records to the database. Allow users to submit or re-verify within a reasonable grace period.

- **🔒 Idempotency & Replay Prevention** — Once a `transaction_id` matches and updates `module_data.status = 1`, it **cannot be claimed a second time**. Any subsequent verification attempt for that same ID will trigger an error response.

- **🔡 Clean Method Names** — Ensure your bot or application sends **lowercase** values for `method`: `"bkash"`, `"nagad"`, `"rocket"`, or `"upay"`.

---

<div align="center">

*Powered by **SkyPay BD** — Headless Payment Infrastructure for Bangladesh*

</div>
