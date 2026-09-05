# Autonomous MFS & Banking Payment Gateway Architecture
### Complete Technical Specification, UI/UX Engine & Database Architecture Documentation

---

## Executive Summary
This document serves as the comprehensive, authoritative technical reference for the Autonomous Mobile Financial Services (MFS) and Banking Payment Gateway platform. 

The platform is designed to provide high-speed, automated payment processing for digital businesses, e-commerce stores, and software service providers in South Asia (primarily Bangladesh). By decoupling the resource-intensive SaaS merchant portal (Main Domain) from the ultra-fast, lightweight payment checkout execution engine (Subdomain), the system delivers sub-millisecond execution times, resilient Android-driven carrier SMS matching, and multi-theme dynamic checkout interfaces.

---

## Table of Contents
1. [Executive Architecture Overview](#1-executive-architecture-overview)
   - 1.1 Architectural Purpose & Topology
   - 1.2 Separation of Concerns: Main Domain vs. Subdomain
   - 1.3 High-Level System Architecture Diagram
2. [Complete Directory & File-by-File Catalog](#2-complete-directory--file-by-file-catalog)
   - 2.1 File Matrix (`Reference.zip` vs. `Reference-UI-architecture.zip`)
   - 2.2 Controllers Analysis (`app/Controllers/`)
   - 2.3 Models Analysis (`app/Models/`)
   - 2.4 Integration Libraries Analysis (`app/Libraries/`)
   - 2.5 Helpers Analysis (`app/Helpers/`)
   - 2.6 Core Framework & Configuration Manifests (`app/Config/` & `app/system/`)
   - 2.7 Storage, Runtime & Diagnostics (`writable/`)
3. [Database Architecture & Schema Deep Dive (`egpayio_pay.sql`)](#3-database-architecture--schema-deep-dive-egpayio_paysql)
   - 3.1 Entity Relationship Diagram (ERD)
   - 3.2 Exhaustive 34-Table Dictionary & Column Specifications
   - 3.3 Relational Keys, Foreign Keys, Indexes & Constraints
   - 3.4 Transactional Data Flow Across Tables
4. [Subdomain Checkout Engine & Lifecycle Flow](#4-subdomain-checkout-engine--lifecycle-flow)
   - 4.1 Step 1: Invoice Generation & API Initialization
   - 4.2 Step 2: Session Creation (`temp_transactions`)
   - 4.3 Step 3: Gateway Selection Presentation (`execute.php`)
   - 4.4 Step 4: Gateway Execution Presentation (`execute_payment.php`)
   - 4.5 Payment Mode Variations: Personal vs. Agent vs. Merchant
   - 4.6 Step 5: Android Device SMS Listener & Web Automation
   - 4.7 Step 6: TrxID Ingestion, Regex Normalization & Matching
   - 4.8 Step 7: Webhook Dispatch, Receipt Generation & Customer Redirects
5. [UI Architecture & View Theme Matrix](#5-ui-architecture--view-theme-matrix)
   - 5.1 Theme Comparison (`unique`, `uddok`, `nago`, `wall`)
   - 5.2 Deep Dive: `execute.php` (Gateway Selection View)
   - 5.3 Deep Dive: `execute_payment.php` (Selected Method & TrxID View)
   - 5.4 Deep Dive: `report.php` (Status Receipt & Countdown Redirect)
   - 5.5 Modular Gateway Partials (`methods/*.php`)
   - 5.6 Error Handling & 404 Fallback Redirection
6. [Design System & Public Assets Inventory](#6-design-system--public-assets-inventory)
   - 6.1 Graphic Assets & Iconography (`public/assets/img/`)
   - 6.2 Typography & Custom Bengali Fonts (`public/assets/css/`)
   - 6.3 Cascading Style Sheets (`public/assets/css/`)
   - 6.4 JavaScript & Toastr Notifications (`public/assets/js/`, `public/assets/toast/`)
7. [API Protocols, Webhooks & Device Communications](#7-api-protocols-webhooks--device-communications)
   - 7.1 Invoice Creation API Specification
   - 7.2 Transaction Verification API Specification
   - 7.3 Android Device SMS Push API Specification
   - 7.4 Outgoing Merchant Webhook Specification
8. [Developer Implementation & Customization Guide](#8-developer-implementation--customization-guide)
   - 8.1 Building a Custom Checkout Theme from Scratch
   - 8.2 Adding a New Payment Gateway Provider
   - 8.3 Environment Configuration, Deployment & Permissions
9. [Security, Fraud Mitigation & Production Hardening](#9-security-fraud-mitigation--production-hardening)
   - 9.1 Double-Spending & Replay Attack Prevention
   - 9.2 Carrier SMS Spoofing Protection
   - 9.3 Atomic Database Transactions
   - 9.4 Session TTL & Merchant Isolation
10. [Conclusion & Engineering Summary](#10-conclusion--engineering-summary)

---

## 1. Executive Architecture Overview

### 1.1 Architectural Purpose & Topology
Mobile Financial Services (MFS)—primarily **bKash, Nagad, Dutch-Bangla Bank Rocket, and UCB Upay**—power the overwhelming majority of online consumer payments in Bangladesh. However, traditional corporate merchant accounts impose heavy bureaucratic barriers:
1. Requirements for extensive trade licenses, TIN/BIN registrations, physical office inspections, and bank solvency certificates.
2. High upfront integration and security deposit fees.
3. Transaction processing surcharges between 1.5% and 2.5% per successful settlement.

To liberate small-to-medium businesses, SaaS startups, freelancers, and independent digital stores, this system provides an **Autonomous MFS Payment Gateway Engine**. The platform bridges consumer-facing payment requests with merchant personal, agent, or commercial MFS accounts through automated background SMS reconciliation.

The system is deployed using a **Decoupled Two-Tier Domain Model**:

```
+-----------------------------------------------------------------------------------------+
|                                MAIN DOMAIN (e.g., egpay.io)                             |
|                 Full Enterprise Web Application (Laravel / Primary Engine)              |
+-----------------------------------------------------------------------------------------+
|  * Merchant Registration, Authentication, Profile Management & KYC                      |
|  * Subscription Billing, Plans, Durations & Coupons (`plans`, `user_plans`)             |
|  * Brand Administration (`brands`: domains, brand keys, custom fees, metadata)          |
|  * Device Pairing (`devices`: Android SMS listener tokens & hardware identifiers)       |
|  * Gateway Account Setup (`user_payment_settings`: numbers, modes, credentials)         |
|  * Merchant Ledger, Wallet Balances, Add-Funds & Payout Withdrawals                     |
|  * Staff & Support Operator RBAC (`staffs`, `user_roles`, `admin_activity_logs`)        |
|  * Customer Support Desk (`tickets`, `ticket_messages`)                                 |
+-----------------------------------------------------------------------------------------+
                                             │
                                             │ REST API / Shared Database Engine
                                             ▼
+-----------------------------------------------------------------------------------------+
|                             SUBDOMAIN (e.g., pay.egpay.io)                              |
|                 Lightweight, High-Concurrency Engine (CodeIgniter 4)                    |
+-----------------------------------------------------------------------------------------+
|  * Customer Checkout Presentation Layer (Zero login overhead, stateless processing)     |
|  * Dynamic Multi-Theme UI Engine (`unique`, `uddok`, `nago`, `wall`)                    |
|  * Real-Time Merchant Customization (Brand Name, Logo, WhatsApp/Phone/Email)            |
|  * Dynamic Payment Method Filtering (bKash, Nagad, Rocket, Upay, Cards, Banks)          |
|  * Mode Dispatcher: Personal (Send Money) vs Agent (Cash Out) vs Merchant (Payment)     |
|  * TrxID Ingestion & Regex Matching against Carrier SMS Ingested from Android Phones    |
|  * Asynchronous HMAC Webhook Dispatch & Instant Customer Redirects                      |
+-----------------------------------------------------------------------------------------+
```

---

### 1.2 Separation of Concerns: Main Domain vs. Subdomain

| Architectural Attribute | Main Domain (`egpay.io` / `bdbetterpay.com`) | Subdomain (`pay.egpay.io` / `checkout.domain.com`) |
| :--- | :--- | :--- |
| **Framework & Stack** | Laravel 10+ / Heavy Enterprise MVC | CodeIgniter 4 Minimalist High-Speed MVC |
| **User Base** | Registered Merchants, System Administrators | End-Paying Customers & Android Listener Daemons |
| **Asset Payload** | Heavy admin bundles, charts, analytics, Datatables | Ultra-compressed, mobile-optimized assets (<100KB) |
| **Authentication Mode** | Stateful Session Cookies, MFA, Admin RBAC | Stateless Public Tokens, Session Hashes, Device API Keys |
| **Concurrency Demands** | Moderate RPS (Dashboard analysis, reporting) | High Burst RPS (Hundreds of concurrent checkout hits) |
| **Core Responsibilities** | Plan renewals, brand whitelisting, gateway configuration, payout processing, support tickets | Checkout view rendering, real-time TrxID matching, SMS parsing, webhook distribution |
| **Database Operations** | Comprehensive Read/Write across all 34 tables | Targeted Read/Write on `temp_transactions`, `module_data`, `user_payment_settings`, `brands`, `transactions` |

---

### 1.3 High-Level System Architecture Diagram

```
+---------------+              +--------------------+              +----------------------+
| End-Customer  |              | Subdomain Gateway  |              | Merchant Server      |
| Mobile/Desktop|              | (CodeIgniter 4)    |              | (e.g. WHMCS/WooComm) |
+---------------+              +--------------------+              +----------------------+
        │                                 │                                    │
        │ 1. Initiates Checkout           │                                    │
        │─────────────────────────────────────────────────────────────────────>│
        │                                 │                                    │
        │                                 │ 2. POST /api/create-charge         │
        │                                 │<───────────────────────────────────│
        │                                 │    (brand_key, amount, urls)       │
        │                                 │                                    │
        │                                 │ 3. Generates Hash & returns URL    │
        │                                 │───────────────────────────────────>│
        │                                 │                                    │
        │ 4. Redirects to /checkout/{id}  │                                    │
        │<────────────────────────────────│                                    │
        │                                 │                                    │
        │ 5. Selects Provider & Mode      │                                    │
        │    (e.g., bKash Personal)       │                                    │
        │    Receives Number & Guide      │                                    │
        │                                 │                                    │
        │ 6. Sends Money via bKash App    │                                    │
        │                                 │                                    │
        │                                 │    +-------------------------+     │
        │                                 │    | Merchant Android Phone  |     │
        │                                 │    | (Running Listener App)  |     │
        │                                 │    +-------------------------+     │
        │                                 │                 │                  │
        │                                 │ 7. SMS Received │                  │
        │                                 │    & Pushed via │                  │
        │                                 │    REST API     │                  │
        │                                 │<────────────────│                  │
        │                                 │ (Stored in      │                  │
        │                                 │  `module_data`) │                  │
        │ 8. Submits TrxID (AJAX)         │                 │                  │
        │────────────────────────────────>│                                    │
        │                                 │                                    │
        │                                 │ 9. Real-time Matching Algorithm    │
        │                                 │    (Verify TrxID & Amount)         │
        │                                 │                                    │
        │                                 │ 10. Asynchronous Webhook POST      │
        │                                 │───────────────────────────────────>│
        │                                 │     (Signature, TrxID, Status=PAID)│
        │                                 │                                    │
        │ 11. Success Report & Redirect   │                                    │
        │<────────────────────────────────│                                    │
```

---

## 2. Complete Directory & File-by-File Catalog

### 2.1 File Matrix (`Reference.zip` vs. `Reference-UI-architecture.zip`)

| File / Folder Path | In `Reference.zip` | In `Reference-UI-architecture.zip` | Scope & Description |
| :--- | :---: | :---: | :--- |
| `app/Controllers/Api.php` | Yes | Yes | Main checkout, method selection & TrxID verification controller |
| `app/Controllers/BaseController.php` | Yes | Yes | Base CI4 controller with database, session & request bindings |
| `app/Controllers/Callback.php` | Yes | Yes | Android device SMS push endpoint & automated IPN callbacks |
| `app/Controllers/File_manager.php` | Yes | Yes | File upload handler for merchant brand logos and receipts |
| `app/Models/Api.php` | Yes | Yes | Database query operations for transactions, brands & invoices |
| `app/Models/FileManagerModel.php` | No | Yes | Database tracking model for file uploads and media metadata |
| `app/Libraries/Bkashapi.php` | No | Yes | bKash Tokenized Merchant API driver (OAuth, Payment, Query) |
| `app/Libraries/Nagad_lib.php` | No | Yes | Nagad RSA Public/Private Key encrypted gateway driver |
| `app/Libraries/Paypal.php` | No | Yes | PayPal REST API integration driver |
| `app/Libraries/Smssender.php` | No | Yes | Outbound SMS gateway client (Nexmo, Twilio, BulkSMS) |
| `app/Libraries/Custom_encryption.php`| No | Yes | AES-256-CBC encryption for sensitive gateway credentials |
| `app/Libraries/Curl.php` | No | Yes | Standardized HTTP request client wrapper |
| `app/Helpers/common_helper.php` | No | Yes | Currency formatting (`৳`), hash generators, plan helpers |
| `app/Helpers/settings_helper.php` | No | Yes | Options table caching and configuration retrieval helpers |
| `app/Helpers/user_helper.php` | No | Yes | Merchant profile verification and subscription validity checks |
| `app/Helpers/files_helper.php` | No | Yes | File path resolution, size formatters, and MIME detection |
| `app/Viewsuddok/` | Yes | Yes | UddoktaPay-inspired clean structured checkout theme |
| `app/Viewsunique/` | Yes | Yes | Modern gradient fintech checkout theme |
| `app/Viewsnago/` | Yes | Yes | NagorikPay-inspired compact mobile-first theme |
| `app/Viewswall/` | Yes | Yes | Grid-style payment wall with MFS, Internet Banking & Cards |
| `app/Config/` | No | Yes | CodeIgniter 4 application configurations (DB, Routes, Security) |
| `app/system/` | No | Yes | Complete CodeIgniter 4 Framework Core Kernel |
| `public/assets/` | Yes | Yes | CSS stylesheets, Bengali fonts, MFS icons, Toastr, jQuery |
| `writable/` | No | Yes | Temporary runtime cache, sessions, and transaction execution logs |

---

### 2.2 Controllers Analysis (`app/Controllers/`)

#### 1. `app/Controllers/Api.php`
- **Class**: `App\Controllers\Api extends BaseController`
- **Responsibility**: Core checkout life cycle orchestrator.
- **Methods**:
  - `execute($hash)`:
    1. Looks up `temp_transactions` by `ids = $hash`.
    2. Validates session state (`status != 2`) and verifies the session is within its 15-minute Time-To-Live (TTL).
    3. Resolves merchant details from `brands` (`domain`, `brand_name`, `brand_logo`, `meta`, `fees`, `fees_type`).
    4. Queries `user_payment_settings` for active gateways (`status = 1` and `brand_id = $brand->id`).
    5. Reads active theme setting from `options.theme` (e.g. `unique`, `uddok`, `nago`, `wall`).
    6. Passes brand metadata, invoice amounts, calculated fees, and gateway list to `execute.php`.
  - `execute_payment($hash, $method)`:
    1. Triggered when the customer clicks a specific payment provider (e.g. `bkash`, `nagad`, `rocket`, `upay`).
    2. Retrieves gateway parameters for `$method` from `user_payment_settings.params`.
    3. Parses JSON to determine active mode tabs:
       - `personal` (Personal Number for Send Money)
       - `agent` (Agent Number for Cash Out)
       - `merchant` (Merchant Number / Counter for Payment)
    4. Renders `execute_payment.php` along with the matching partial `methods/{method}.php`.
  - `verify_transaction()`:
    1. Receives AJAX POST containing `ids` (session hash) and `trxid`.
    2. Performs sanitization (stripping whitespace, uppercase conversion).
    3. Queries `module_data` for unclaimed SMS records (`status = 0`) matching the merchant's `uid`.
    4. Regex parses SMS body for TrxID and Amount.
    5. If verified: atomically updates `module_data.status = 1`, `temp_transactions.status = 2`, records verified entry into `transactions`, and dispatches the background merchant webhook.
    6. Returns JSON: `{ status: "success", redirect_url: "..." }` or `{ status: "error", message: "..." }`.
  - `report($hash)`:
    1. Displays receipt view (`report.php`).
    2. Renders success or failure badge with auto-redirect timer.

#### 2. `app/Controllers/Callback.php`
- **Class**: `App\Controllers\Callback extends BaseController`
- **Responsibility**: External event ingestion and device communications.
- **Methods**:
  - `device_push()`:
    1. Ingests raw SMS forwarded by the merchant's Android phone.
    2. Validates `device_key` against `devices.device_key`.
    3. Records raw SMS payload into `module_data` table (`address`, `message`, `status = 0`).
  - `ipn_listener($gateway)`:
    1. Handles automated third-party IPN webhooks for merchants with formal bKash Tokenized or Nagad API accounts.

#### 3. `app/Controllers/BaseController.php`
- **Class**: `App\Controllers\BaseController extends Controller`
- **Responsibility**: Injects system-wide services:
  - Database instance (`$this->db`)
  - Session manager (`$this->session`)
  - Request/Response utilities (`$this->request`, `$this->response`)

#### 4. `app/Controllers/File_manager.php`
- **Class**: `App\Controllers\File_manager extends BaseController`
- **Responsibility**: Secures merchant brand logo uploads under `public/uploads/user/{hash}/`, enforcing MIME validation and dimension constraints.

---

### 2.3 Models Analysis (`app/Models/`)

#### 1. `app/Models/Api.php`
- **Class**: `App\Models\Api extends Model`
- Encapsulates low-level database operations:
  - `get_active_methods($brand_id)`: Fetches enabled gateways from `user_payment_settings`.
  - `find_unclaimed_sms($uid, $trxid)`: Runs optimized index query over `module_data`.
  - `complete_payment($temp_id, $module_id, $data)`: Wraps session update, module claim, and transaction insertion in a single ACID database transaction.

#### 2. `app/Models/FileManagerModel.php`
- **Class**: `App\Models\FileManagerModel extends Model`
- Manages records in `file_managers` table for media assets and brand logos.

---

### 2.4 Integration Libraries Analysis (`app/Libraries/`)

#### 1. `Bkashapi.php`
- Implements bKash Tokenized Checkout API v1.2:
  - `grantToken()`: Negotiates OAuth 2.0 access token using `app_key` and `app_secret`.
  - `createPayment()`: Initializes web tokenized payment session.
  - `executePayment()`: Captures payment after customer enters OTP and PIN.
  - `queryPayment()`: Queries transaction status directly from bKash servers.

#### 2. `Nagad_lib.php`
- Implements Nagad Public/Private Key Cryptographic Protocol:
  - Generates RSA-SHA256 signatures with merchant's private key.
  - Decrypts Nagad gateway responses with Nagad's public key.
  - Generates payment verification payloads.

#### 3. `Custom_encryption.php`
- Implements `AES-256-CBC` encryption with HMAC authentication to secure API secrets, merchant passwords, and sensitive keys stored in the database.

#### 4. `Smssender.php`
- Dispatches transactional outbound SMS notifications to merchants and customers via REST APIs.

#### 5. `Curl.php`
- Standardized HTTP/HTTPS client wrapper with configurable timeouts, headers, and SSL verification.

---

### 2.5 Helpers Analysis (`app/Helpers/`)

1. **`common_helper.php`**:
   - `currency_format($amount)`: Returns formatted currency strings (e.g. `৳ 1,500.00`).
   - `generate_hash($len)`: Generates cryptographic pseudo-random tokens.
   - `duration_type()`: Formats subscription plan durations.
2. **`settings_helper.php`**:
   - `site_config($key, $default)`: Cached getter for records in `options` table.
3. **`user_helper.php`**:
   - `is_plan_valid($uid)`: Verifies merchant subscription validity before allowing checkouts.
4. **`files_helper.php`**:
   - Helper routines for path resolution, file size formatting, and MIME validation.

---

## 3. Database Architecture & Schema Deep Dive (`egpayio_pay.sql`)

The database `egpayio_pay` contains **34 relational tables** with foreign key constraints, indexes, and optimized storage types.

### 3.1 Entity Relationship Diagram (ERD)

```
       +------------------------------------+
       |               users                |
       |------------------------------------|
       | id (PK)                            |
       | email, password, phone, balance    |
       +------------------------------------+
         │   │   │   │   │   │   │   │   │
         │   │   │   │   │   │   │   │   +-----------------------+
         │   │   │   │   │   │   │   +-------------+         │
         │   │   │   │   │   │   +--------+        │         │
         │   │   │   │   │   ▼            ▼        ▼         ▼
         │   │   │   │   │ +------------+ +------+ +-------+ +-----------------------+
         │   │   │   │   │ | user_plans | | kyc  | |invoice| | user_payment_settings |
         │   │   │   │   │ +------------+ +------+ +-------+ +-----------------------+
         │   │   │   │   ▼
         │   │   │   │ +-------------+
         │   │   │   │ |   brands    |
         │   │   │   │ +-------------+
         │   │   │   │   │         │
         │   │   ▼   ▼   ▼         │
         │   │ +---------------+   │
         │   │ |    devices    |   │
         │   │ +---------------+   │
         │   │   │                 │
         │   ▼   ▼                 ▼
         │ +-------------+   +-------------------+
         │ | module_data |   | temp_transactions |
         │ +-------------+   +-------------------+
         │                         │
         ▼                         ▼
   +-------------------+     +-------------------+
   | user_transactions |     |   transactions    |
   +-------------------+     +-------------------+
```

---

### 3.2 Exhaustive 34-Table Dictionary & Column Specifications

#### 1. `users`
The core merchant user entity.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT): Unique user identifier.
- `ids` (`varchar(50)`, UNIQUE): Public external UUID/hash for user masking.
- `email` (`varchar(100)`, UNIQUE): Login email address.
- `password` (`varchar(255)`): Bcrypt password hash.
- `first_name` (`text`, nullable): Merchant given name.
- `last_name` (`text`, nullable): Merchant family name.
- `phone` (`varchar(20)`, UNIQUE): Merchant primary contact mobile number.
- `balance` (`decimal(10,3)`, default `0.000`): Internal wallet balance in BDT.
- `more_information` (`text`, nullable): Extended user profile metadata.
- `avatar` (`varchar(255)`, nullable): Relative path to avatar image.
- `api_credentials` (`text`, nullable): User-level API tokens.
- `timezone` (`text`, nullable): User operational timezone.
- `ref_id` (`int(10)`): User ID of referring affiliate.
- `ref_key` (`text`, nullable): Unique referral key.
- `addons` (`text`, nullable): Enabled account extensions.
- `status` (`tinyint(1)`, default `1`): `1`=Active, `0`=Suspended/Inactive.
- `activation_key` (`varchar(50)`, nullable): Email activation token.
- `reset_key` (`varchar(50)`, nullable): Password recovery token.
- `created_at` (`datetime`): Account registration timestamp.
- `updated_at` (`datetime`, nullable): Last profile update timestamp.
- `deleted_at` (`datetime`, nullable): Soft-delete timestamp.

#### 2. `brands`
Stores merchant websites, e-commerce stores, or applications.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT): Unique brand identifier.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE): Owner merchant ID.
- `domain` (`text`, nullable): Whitelisted merchant domain (e.g. `hostreset.com`, `bdbetterpay.com`).
- `ip` (`text`, nullable): Whitelisted merchant server IP.
- `brand_name` (`varchar(30)`, nullable): Display name shown on checkout (e.g. "Hostreset").
- `brand_key` (`text`, nullable): 50-character secret key used to authenticate API requests.
- `brand_logo` (`text`, nullable): Relative path to brand logo image (e.g. `public/uploads/...`).
- `meta` (`text`, JSON, nullable): Contact information rendered in checkout header and footer:
  ```json
  {"mobile_number":"01710604946","whatsapp_number":"01710604946","support_mail":"info@hostreset.com"}
  ```
- `fees` (`decimal(10,3)`, default `0.000`): Additional processing fee passed to customer.
- `fees_type` (`tinyint(1)`, default `0`): `0` = Flat fee (e.g. 1.000 BDT); `1` = Percentage fee (e.g. 1.5%).
- `currency` (`varchar(20)`, nullable): Operating currency (e.g. `BDT`).
- `status` (`tinyint(1)`, default `0`): `1` = Active; `0` = Inactive.
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 3. `devices`
Android listener phone instances paired to merchant accounts.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT): Unique device identifier.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE): Owner merchant ID.
- `user_email` (`text`): Associated merchant login email.
- `device_name` (`text`): Hardware model label (e.g. "Realme 8", "Samsung S21").
- `device_key` (`text`): 40-character secret token used by Android app to authenticate SMS pushes.
- `device_ip` (`text`, nullable): Hardware device fingerprint or connected IP address.
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 4. `module_data`
Raw transactional SMS data pushed from merchant Android phones.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT): Unique record identifier.
- `tmp_id` (`text`, nullable): Optional temporary tracking token.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE): Owner merchant ID.
- `message` (`text`, nullable): Complete raw SMS body. Real samples from dump:
  - bKash: `You have received payment Tk 1,500.00 from 01329503432. Fee Tk 0.00. Balance Tk 1,506.40. TrxID CCE6L9KZ74 at 14/03/2025 20:27`
  - Nagad: `Money Received. Amount: Tk 11.00 Sender: 01716458399 Ref: N/A TxnID: 73QOF3OE Balance: Tk 2799.27 14/03/2025 16:48`
  - BRAC Bank: `TK 1,500.00 credited to A/C#10531**0001 on 14-03-25 @09:15 PM from OTHER BANK. Balance TK 4,047.24. BRAC Bank.`
- `address` (`text`, nullable): Originating carrier sender ID (`bKash`, `Nagad`, `BRAC-BANK`, `GP Bundle`).
- `status` (`tinyint(1)`, default `0`): `0` = Unclaimed / Pending; `1` = Claimed / Matched.
- `created_at`, `updated_at` (`datetime`, nullable).

#### 5. `user_payment_settings`
Merchant-configured payment gateway parameters per brand.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE): Owner merchant ID.
- `brand_id` (`int(10)`): Associated brand ID.
- `g_type` (`varchar(255)`, nullable): Provider identifier (`bkash`, `nagad`, `rocket`, `upay`, etc.).
- `t_type` (`varchar(255)`, nullable): Account type classification (`mobile`, `bank`, `crypto`).
- `status` (`int(2)`, default `0`): `1` = Active; `0` = Disabled.
- `params` (`text`, JSON): Configuration JSON payload:
  ```json
  {
    "status": "1",
    "brand_id": "19",
    "active_payments": {"personal": "1", "agent": "0", "merchant": "0", "payment": "0"},
    "personal_number": "01783632964",
    "payment_number": "",
    "agent_number": "",
    "sandbox": "0",
    "logs": "0",
    "username": "",
    "password": "",
    "app_key": "",
    "app_secret": ""
  }
  ```
- `created_at`, `deleted_at` (`text`, nullable).

#### 6. `temp_transactions`
In-flight active customer checkout sessions.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): 32-character MD5 hash representing checkout URL token (`/checkout/{ids}`).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE): Merchant ID.
- `brand_id` (`int(10) UNSIGNED`, FK -> `brands.id`): Associated brand.
- `params` (`text`, JSON): Return callbacks and customer metadata:
  ```json
  {
    "cus_name": "MD Leon Islam",
    "cus_email": "bdbosstv@gmail.com",
    "success_url": "https://panel.hostreset.com/viewinvoice.php?id=31",
    "cancel_url": "https://panel.hostreset.com/viewinvoice.php?id=31",
    "webhook_url": "https://panel.hostreset.com/modules/gateways/callback/bdbetterpayv1.php?api=KEY&invoice=31"
  }
  ```
- `meta` (`text`, nullable): Extra merchant tracking data.
- `amount` (`decimal(10,3)`, default `0.000`): Payable invoice total.
- `currency` (`varchar(10)`, default `'BDT'`).
- `request` (`varchar(10)`, default `'GET'`): Protocol method (`GET` or `POST`).
- `status` (`tinyint(1)`, default `0`): `0`=Pending, `1`=Initiated, `2`=Success, `3`=Cancel.
- `transaction_id` (`varchar(20)`, nullable): Network TrxID written upon verification.
- `created_at`, `updated_at` (`datetime`, nullable).

#### 7. `transactions`
Permanent verified financial ledger.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): Associated session hash from `temp_transactions.ids`.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `brand_id` (`int(10)`, nullable): Associated brand ID.
- `message` (`text`, nullable): Verbatim carrier SMS snippet proving payment.
- `type` (`varchar(10)`, nullable): Provider used (`nagad`, `bkash`, `rocket`, `upay`).
- `amount` (`decimal(10,3)`, default `0.000`): Verified amount received.
- `currency` (`varchar(10)`): Operating currency (`BDT`).
- `status` (`tinyint(1)`, default `0`): `2` = Success.
- `transaction_id` (`varchar(20)`): Verified network TrxID.
- `created_at`, `updated_at` (`datetime`, nullable).

#### 8. `invoice`
Merchant direct programmatic and manual invoices.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): Public invoice hash token.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `customer_name` (`text`): Payer full name.
- `customer_number` (`text`, nullable): Payer phone number.
- `customer_amount` (`text`): Bill amount.
- `customer_email` (`text`): Payer email.
- `customer_address` (`text`): Payer billing address.
- `customer_description` (`text`, nullable): Invoice line items/description.
- `status` (`int(11)`): Invoice lifecycle status.
- `pay_status` (`int(4)`): `0` = Unpaid; `1` = Paid.
- `brand_id` (`text`, nullable): Associated brand ID.
- `transaction_id` (`text`, nullable): Linked verified TrxID.
- `extras` (`text`, nullable), `created_at`, `deleted_at` (`text`, nullable).

#### 9. `payments`
Global system directory of available payment methods.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `type` (`varchar(255)`, nullable): Gateway slug (`bkash`, `nagad`, `rocket`, `upay`, `cellfin`, `ibl`, `bbrac`, `dbbl`, `sonali`, `binance`, etc.).
- `name` (`varchar(225)`): Provider display name (e.g. "Bkash", "Nagad").
- `sort` (`int(3)`, nullable): UI display order.
- `status` (`varchar(10)`, default `'0'`): `1` = Enabled; `0` = Disabled.
- `params` (`text`, JSON): Default icon metadata:
  ```json
  {"type":"bkash","option":{"logo":"public/uploads/admin/.../1720001734_bed271b1089aa12b9887.png"},"name":"Bkash","status":"1"}
  ```

#### 10. `plans`
Platform subscription pricing plans.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`varchar(50)`): Public plan identifier hash.
- `name` (`varchar(50)`): Plan title (e.g. "Free 7 Days", "30 Days", "6 Months").
- `description` (`text`): Feature bullet points.
- `brand` (`int(10)`): Maximum connected brands/domains allowed.
- `device` (`int(10)`): Maximum allowed Android sync devices.
- `transaction` (`int(10)`): Maximum monthly transactions (`-1` = Unlimited).
- `price` (`decimal(10,3)`, default `0.000`): Regular price.
- `final_price` (`decimal(10,3)`, default `0.000`): Discounted final price.
- `duration` (`int(3)`): Duration units.
- `duration_type` (`int(3)`): `1` = Day; `2` = Month; `3` = Year.
- `sort` (`int(10)`): Display sort order.
- `status` (`int(2)`, default `1`): `1` = Active; `0` = Disabled.
- `created_at`, `deleted_at` (`datetime`, nullable).

#### 11. `user_plans`
Merchant active plan subscriptions.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `plan_id` (`int(10)`): Links to `plans.id`.
- `price` (`decimal(10,3)`): Paid subscription amount.
- `brand` (`int(10)`), `device` (`int(10)`), `transaction` (`int(10)`): Granted plan limits.
- `key` (`varchar(100)`): Subscription serial/license key.
- `expire` (`datetime`, nullable): Expiration date/time.
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 12. `options`
Key-value configuration store for platform-wide settings.
- `id` (`int(9)`, PK, AUTO_INCREMENT).
- `name` (`text`, nullable): Setting identifier.
- `value` (`text`, nullable): Value string.
- Key configurations in database dump:
  - `theme`: Active view theme (`custom`, `unique`, `uddok`, `nago`, `wall`).
  - `site_form`: `V4`.
  - `site_paymentform`: `V2`.
  - `site_title`, `site_name`, `site_logo`, `site_icon`.
  - `currency_symbol`: `৳`.
  - `currency_code`: `BDT`.
  - `embed_head_javascript`: Embedded live chat widgets (WhatsApp / Telegram).

#### 13. `staffs`
Administrative user accounts.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`varchar(50)`, UNIQUE): Public staff identifier.
- `role_id` (`int(50)`, nullable): Associated role ID.
- `email` (`varchar(100)`, UNIQUE): Staff login email.
- `first_name`, `last_name` (`text`, nullable): Staff name.
- `balance` (`decimal(10,3)`): Staff account balance.
- `more_information` (`text`, nullable): Staff notes.
- `avatar` (`varchar(255)`, nullable): Profile image path.
- `password` (`varchar(255)`): Password hash.
- `status` (`tinyint(1)`, default `1`): Staff account state.
- `activation_key`, `reset_key` (`varchar(50)`, nullable).
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 14. `user_roles`
Role-Based Access Control (RBAC) permission definitions.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `name` (`varchar(50)`): Role name (`Admin`, `Owner`, `Support Operator`).
- `permissions` (`text`, JSON, nullable): Detailed permission matrix:
  ```json
  {"dashboard_statistics":"on","user_access_user":"on","domain_access_domain":"on","device_access_device":"on",...}
  ```
- `created_at` (`datetime`, nullable).

#### 15. `activity_logs`
Merchant action and session audit trail.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `ip` (`varchar(50)`): IP address of merchant.
- `activity` (`text`, nullable): Action description (e.g. `Signin`).
- `created_at` (`datetime`, nullable).

#### 16. `admin_activity_logs`
Administrator activity and security audit trail.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `staffs.id` ON DELETE CASCADE).
- `ip` (`varchar(50)`): IP address of staff member.
- `activity` (`text`, nullable): Action description (e.g. `Login`, `Bulk user delete`).
- `created_at`, `deleted_at` (`datetime`, nullable).

#### 17. `affiliates`
Referral commission ledger.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE): Earning affiliate.
- `ref_id` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE): Referred user.
- `amount` (`decimal(10,3)`, default `0.000`): Commission earned in BDT.
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 18. `bank_transaction_logs`
Manual bank deposit verification requests.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): Hash token.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `brand_id` (`int(10)`, nullable): Associated brand ID.
- `files` (`text`, nullable): Uploaded bank slip screenshot path.
- `type` (`varchar(10)`, nullable): Bank type identifier.
- `status` (`tinyint(1)`, default `0`): `0`=Pending, `1`=Initiated, `2`=Success, `3`=Cancel.
- `created_at`, `updated_at` (`datetime`, nullable).

#### 19. `blogs`
Platform marketing articles and guides.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`, nullable), `thumbnail` (`text`, nullable), `uri` (`text`, nullable).
- `title` (`varchar(255)`, nullable), `description` (`text`, nullable).
- `status` (`int(1)`, default `1`): `1` = Published; `0` = Draft.
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 20. `coupons`
Discount promo codes for subscription plans.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `code` (`varchar(191)`): Coupon code string (e.g. `DISCOUNT20`).
- `type` (`tinyint(4)`): `1` = Fixed amount; `2` = Percentage.
- `price` (`double`): Discount value.
- `times` (`varchar(191)`, nullable): Max total usage count.
- `used` (`int(191) UNSIGNED`, default `0`): Current times redeemed.
- `param` (`text`): Target plan IDs.
- `description` (`text`, nullable).
- `status` (`tinyint(4)`, default `1`).
- `start_date`, `end_date` (`date`).

#### 21. `user_coupons`
Redemption tracking linking coupons to merchants.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `coupon_id` (`int(11)`, nullable), `plan_id` (`int(11)`, nullable).
- `price` (`decimal(10,3)`), `discount` (`decimal(10,3)`).
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 22. `faqs`
Frequently Asked Questions for landing page.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `question` (`text`, nullable): Question text.
- `answer` (`longtext`, nullable): Answer rich HTML content.
- `sort` (`int(11)`, nullable): Display sort order.
- `status` (`int(1)`, default `1`): Visibility state.
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 23. `file_managers`
Upload directory catalog.
- `id` (`int(9)`, PK, AUTO_INCREMENT).
- `uid` (`int(11)`, nullable): Uploader user ID (`0` for admin).
- `file_name` (`varchar(255)`): Stored filename.
- `file_url` (`varchar(255)`): Relative path on server.
- `file_type` (`varchar(100)`): MIME type (e.g. `image/png`, `image/jpeg`).
- `file_size` (`int(11)`): File size in bytes.
- `created_at` (`datetime`).

#### 24. `kyc`
Merchant identity verification records.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): Public verification token.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `params` (`text`, nullable): Submitted documents (NID front/back, trade license).
- `status` (`int(2)`, default `0`): `0` = Pending, `1` = Approved, `2` = Rejected.
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 25. `migrations`
Database schema migration history.
- `id` (`bigint(20) UNSIGNED`, PK, AUTO_INCREMENT).
- `version` (`varchar(255)`), `class` (`varchar(255)`), `group` (`varchar(255)`).
- `namespace` (`varchar(255)`), `time` (`int(11)`), `batch` (`int(11) UNSIGNED`).

#### 26. `notifications`
Merchant in-app notification inbox.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `message` (`text`): Notification message text.
- `is_read` (`tinyint(1)`, default `0`): `1` = Read; `0` = Unread.
- `admin_status` (`tinyint(1)`, default `1`), `is_admin_read` (`tinyint(1)`, default `0`).
- `created_at` (`datetime`, nullable).

#### 27. `queue`
Asynchronous job processing queue.
- `id` (`int(9)`, PK, AUTO_INCREMENT).
- `task_type` (`text`, nullable): Job handler class.
- `task_data` (`text`, nullable): Serialized payload.
- `status` (`varchar(16)`): Execution state (`pending`, `processing`, `failed`).
- `created_at` (`datetime`).

#### 28. `templates`
Email and SMS notification message templates.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(11)`, nullable), `template_key` (`varchar(120)`, nullable).
- `email_from` (`varchar(191)`), `name` (`varchar(191)`), `subject` (`varchar(191)`).
- `template` (`text`, nullable): Email body with placeholders.
- `sms_body` (`text`, nullable): SMS text with placeholders.
- `mail_status`, `sms_status` (`tinyint(1)`, default `0`).
- `created_at`, `updated_at`, `deleted_at` (`timestamp`, nullable).

#### 29. `tickets`
Customer and merchant support tickets.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): Public ticket token.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `description` (`text`), `subject` (`text`).
- `status` (`varchar(10)`, default `'pending'`).
- `is_user_read`, `is_admin_read` (`tinyint(1)`, default `0`).
- `created_at`, `updated_at`, `deleted_at` (`datetime`, nullable).

#### 30. `ticket_messages`
Support ticket conversation messages.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`), `ticket_id` (`int(10) UNSIGNED`).
- `message` (`text`), `author` (`varchar(20)`).
- `support` (`tinyint(4)`): `1` = Support staff; `0` = Merchant client.
- `created_at`, `updated_at` (`datetime`, nullable).

#### 31. `user_notifier`
Outbound notification delivery logs.
- `id` (`int(11) UNSIGNED`, PK, AUTO_INCREMENT).
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `message` (`text`, nullable), `response` (`text`, nullable).
- `type` (`tinyint(4)`, default `1`): `0` = Mail; `1` = SMS.
- `medium` (`varchar(50)`): Destination mobile number or email.
- `status` (`tinyint(4)`, nullable), `charge` (`float`, nullable).
- `created_at` (`datetime`), `deleted_at` (`text`, nullable).

#### 32. `user_payouts`
Merchant wallet withdrawal requests.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): Withdrawal tracking token.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `g_type` (`varchar(30)`, nullable): Destination gateway (`bkash`, `bank`).
- `params` (`text`, nullable): Destination account details.
- `amount` (`decimal(10,3)`), `charge` (`decimal(10,3)`), `net_amount` (`decimal(10,3)`).
- `status` (`tinyint(1)`, default `0`): `0`=Pending, `1`=Processing, `2`=Disbursed, `3`=Rejected.
- `created_at`, `updated_at` (`datetime`, nullable).

#### 33. `user_transactions`
Merchant internal financial ledger for platform charges.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `ids` (`text`): Hash token.
- `uid` (`int(10) UNSIGNED`, FK -> `users.id` ON DELETE CASCADE).
- `information` (`text`, nullable): Action description (e.g. Plan purchase).
- `type` (`varchar(20)`, nullable): Transaction type (`plan`, `addon`, `deposit`).
- `amount` (`decimal(10,3)`), `currency` (`varchar(10)`).
- `status` (`tinyint(1)`, default `0`): `2` = Success.
- `transaction_id` (`varchar(20)`): Internal tracking reference.
- `created_at`, `updated_at` (`datetime`, nullable).

#### 34. `addons`
Optional marketplace modules for platform expansion.
- `id` (`int(10) UNSIGNED`, PK, AUTO_INCREMENT).
- `name` (`varchar(50)`, nullable), `unique_identifier` (`varchar(50)`, nullable).
- `version` (`varchar(50)`, nullable), `price` (`decimal(10,3)`, default `0.000`).
- `status` (`int(2)`, default `1`), `image` (`text`).

---

## 4. Subdomain Checkout Engine & Lifecycle Flow

### 4.1 Step 1: Invoice Generation & API Initialization
The checkout journey begins when a merchant store calls the Subdomain REST API:

```http
POST /api/create-charge HTTP/1.1
Host: pay.egpay.io
Content-Type: application/json

{
  "brand_key": "fXB5o17G3eregnsoB266sxelC5qBZGysKo4emeNP9bAEknZf04",
  "amount": 777.00,
  "currency": "BDT",
  "cus_name": "Rahim Ahmed",
  "cus_email": "rahim@example.com",
  "cus_phone": "01710604946",
  "success_url": "https://mystore.com/order/success?id=101",
  "cancel_url": "https://mystore.com/order/cancel?id=101",
  "webhook_url": "https://mystore.com/api/payment-webhook"
}
```

The gateway engine:
1. Queries `brands` by `brand_key`.
2. Verifies the caller's domain and IP against whitelisted parameters.
3. Calculates gateway surcharges based on `brands.fees` and `brands.fees_type`.

---

### 4.2 Step 2: Session Creation (`temp_transactions`)
Upon validation, the engine creates an active checkout session:
```php
$session_hash = md5(uniqid(rand(), true));
```
A row is inserted into `temp_transactions`:
- `ids = $session_hash`
- `amount = 777.000`
- `status = 0` (Pending)
- `params` = JSON containing callback URLs and customer information.

The API returns:
```json
{
  "status": "success",
  "message": "Payment session created",
  "payment_url": "https://pay.egpay.io/checkout/3f9f443c1c0462807157280984cbb0c5"
}
```

---

### 4.3 Step 3: Gateway Selection Presentation (`execute.php`)
When the customer opens `https://pay.egpay.io/checkout/{hash}`:
1. `app/Controllers/Api.php::execute($hash)` handles the request.
2. The controller checks `user_payment_settings` for enabled methods under the brand:
   - bKash enabled -> renders bKash card with `bkash.png`.
   - Nagad enabled -> renders Nagad card with `nagad.png`.
   - Rocket enabled -> renders Rocket card with `rocket.png`.
   - Upay enabled -> renders Upay card with `upay.png`.
3. If no gateways are enabled, a user-friendly alert informs the customer to contact support.
4. Renders brand logo (`brands.brand_logo`) and brand name (`brands.brand_name`).
5. Displays support contact actions (WhatsApp, Phone Call, Email) from `brands.meta`.

---

### 4.4 Step 4: Gateway Execution Presentation (`execute_payment.php`)
When the customer selects a provider (e.g. bKash):
1. The route `/checkout/{hash}/bkash` is opened.
2. `app/Controllers/Api.php::execute_payment($hash, 'bkash')` executes.
3. The controller parses `user_payment_settings.params.active_payments` to display active mode tabs:
   - **Personal**: Displays receiver number with a 1-click clipboard copy button and Send Money instructions.
   - **Agent**: Displays agent number with Cash Out instructions.
   - **Merchant**: Displays merchant counter number with Payment instructions.
4. An input field prompts: `"Enter Transaction ID (TrxID)"`.
5. A live countdown timer enforces session validity (15 minutes).

---

### 4.5 Payment Mode Variations: Personal vs. Agent vs. Merchant

| Dimension | Personal Account ("Send Money") | Agent Account ("Cash Out") | Merchant Account ("Make Payment") |
| :--- | :--- | :--- | :--- |
| **MFS Menu Action** | 01. Send Money | 02. Cash Out | 03. Payment / Merchant Pay |
| **Target Number** | Personal SIM mobile number | Agent SIM mobile number | Merchant Till / Counter Number |
| **Reference Field** | Customer enters Invoice ID | Optional / Counter Number | Mandatory Reference Code |
| **Sender Fee** | Standard sender fee (if any) | Standard Cash-out fee (~1.85%) | 0% charge for customer |
| **Target Merchant** | Freelancers, Micro-merchants | High-volume OTC agents | Registered corporate businesses |
| **Carrier SMS Trigger** | *"You have received payment Tk..."* | *"Cash In received Tk..."* | *"Payment received Tk..."* |

---

### 4.6 Step 5: Android Device SMS Listener & Web Automation
Personal and agent MFS accounts do not have official server webhooks. The system solves this through an **Android SMS Gateway Listener**:
1. The merchant installs a specialized Android application on the phone containing the receiving SIM cards.
2. When money arrives, the phone receives an SMS from `bKash`, `Nagad`, `16216`, etc.
3. A background `BroadcastReceiver` intercepts the SMS immediately and POSTs it to `https://pay.egpay.io/api/callback`:
   ```json
   {
     "device_key": "hFKo7yB5Fg27PKLxT5hmi9xi1Wx3Zp6ZqPEm5zEh",
     "sender": "bKash",
     "message": "You have received payment Tk 777.00 from 01710604946. Fee Tk 0.00. Balance Tk 4,905.55. TrxID CCH9NTW41P at 18/03/2025 05:40"
   }
   ```
4. The subdomain server validates `device_key` and saves the raw text into `module_data` with `status = 0`.

---

### 4.7 Step 6: TrxID Ingestion, Regex Normalization & Matching
When the customer enters `CCH9NTW41P` and clicks **Verify Payment**:
1. An AJAX POST request is submitted to `Api::verify_transaction()`.
2. The server sanitizes the input (removes spaces, uppercase normalization).
3. The server queries `module_data` where `uid = $tempTrx->uid` and `status = 0`.
4. Regex extracts the TrxID and Amount from the message text:
   ```php
   // TrxID extraction pattern
   preg_match('/(?:TrxID|TxnID)[:\s]+([A-Z0-9]+)/i', $sms_text, $trx_match);
   
   // Amount extraction pattern
   preg_match('/(?:Tk|Amount)[:\s]+([0-9,]+(?:\.[0-9]{2})?)/i', $sms_text, $amt_match);
   ```
5. **Validation Rules**:
   - `strtoupper($extracted_trx) === strtoupper($input_trx)`
   - `$extracted_amount >= $session_amount`
6. Upon a successful match:
   - `module_data.status` is updated to `1` (Claimed).
   - `temp_transactions.status` is updated to `2` (Success).
   - A verified record is inserted into `transactions`.

---

### 4.8 Step 7: Webhook Dispatch, Receipt Generation & Customer Redirects
1. **Merchant Webhook**:
   The server dispatches an asynchronous HTTP POST request to the merchant's `webhook_url`:
   ```json
   {
     "status": "COMPLETED",
     "transaction_id": "CCH9NTW41P",
     "amount": 777.00,
     "currency": "BDT",
     "brand_id": 19,
     "customer_email": "bdbosstv@gmail.com",
     "signature": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
   }
   ```
2. **Frontend Redirection**:
   The browser receives:
   ```json
   {
     "status": "success",
     "message": "Payment verified successfully!",
     "redirect_url": "https://pay.egpay.io/report/3f9f443c1c0462807157280984cbb0c5"
   }
   ```
3. **Receipt View (`report.php`)**:
   - Displays `public/assets/img/success.png`.
   - Shows paid amount, verified TrxID, and timestamp.
   - An automatic 5-second countdown redirects the customer to the merchant's `success_url`.

---

## 5. UI Architecture & View Theme Matrix

### 5.1 Theme Comparison (`unique`, `uddok`, `nago`, `wall`)

```
app/Views/
├── Viewsunique/   # Modern Fintech UI with vibrant gradients and micro-animations
├── Viewsuddok/    # Clean, structured card layout inspired by UddoktaPay
├── Viewsnago/     # Compact mobile-first layout inspired by NagorikPay
└── Viewswall/     # Comprehensive multi-column payment wall (MFS, Banks, Cards)
```

| Theme Dimension | `Viewsuddok` | `Viewsunique` | `Viewsnago` | `Viewswall` |
| :--- | :--- | :--- | :--- | :--- |
| **Visual Style** | Bordered white container, high contrast | Gradient background, rounded cards, modern fintech look | Compact layout optimized for smartphones | Multi-column grid accommodating 10+ payment methods |
| **Header Layout** | Centered brand logo with amount badge | Top-left brand badge with animated glow | Compact navbar with brand logo | Left sidebar with brand summary |
| **Tab Navigation** | Horizontal pill buttons | Smooth sliding indicator tabs | Radio button switcher | Accordion / Grid categories |
| **TrxID Field** | Prominent input with bold verify CTA | Floating label input with subtle shadow | Flat input with icon prefix | Compact in-line verify field |

---

### 5.2 Deep Dive: `execute.php` (Gateway Selection View)
Key components rendered in `execute.php`:
1. **Dynamic Brand Display**:
   ```php
   <img src="<?= base_url($brand->brand_logo) ?>" alt="<?= esc($brand->brand_name) ?>" class="brand-logo">
   <h2 class="brand-title"><?= esc($brand->brand_name) ?></h2>
   ```
2. **Order Summary**: Displays payable amount (`৳ 777.00`), customer name, customer email, and reference.
3. **Dynamic Payment Grid**: Loops through active methods from `user_payment_settings`:
   - Only displays providers that are configured and enabled (`status = 1`).
   - Surcharges (`brands.fees`) are automatically calculated and added to the total.
4. **Support Contacts Widget**:
   ```php
   <?php $meta = json_decode($brand->meta, true); ?>
   <a href="https://wa.me/<?= $meta['whatsapp_number'] ?>" target="_blank"><img src="<?= base_url('public/assets/img/whatsapp.png') ?>"> WhatsApp</a>
   <a href="tel:<?= $meta['mobile_number'] ?>"><img src="<?= base_url('public/assets/img/phone-call.png') ?>"> Call</a>
   <a href="mailto:<?= $meta['support_mail'] ?>"><img src="<?= base_url('public/assets/img/email.png') ?>"> Email</a>
   ```

---

### 5.3 Deep Dive: `execute_payment.php` (Selected Method & TrxID View)
1. **Header Provider Banner**: Highlights provider branding (bKash `#D12053`, Nagad `#F7941D`, Rocket `#8C3494`).
2. **Payment Mode Switcher**:
   Dynamically renders mode tabs (`Personal`, `Agent`, `Merchant`) based on `user_payment_settings.params.active_payments`.
3. **Target Number & 1-Click Copy**:
   ```javascript
   function copyNumber() {
       var text = document.getElementById("targetNumber").innerText;
       navigator.clipboard.writeText(text);
       toastr.success("Number copied to clipboard!");
   }
   ```
4. **Step-by-Step Instructions**: Rendered in clear Bengali text using `banglabold.ttf`.
5. **TrxID Verification Form**:
   ```javascript
   $('#verifyBtn').click(function(e) {
       e.preventDefault();
       var trxid = $('#trxid').val().trim();
       if (!trxid) {
           toastr.error("Please enter a valid Transaction ID");
           return;
       }
       $('#verifyBtn').prop('disabled', true).text('Verifying...');
       $.post("<?= base_url('api/verify') ?>", { ids: "<?= $tempTrx->ids ?>", trxid: trxid }, function(res) {
           if (res.status === 'success') {
               toastr.success(res.message);
               setTimeout(function() { window.location.href = res.redirect_url; }, 1000);
           } else {
               toastr.error(res.message);
               $('#verifyBtn').prop('disabled', false).text('Verify Payment');
           }
       }, 'json');
   });
   ```

---

### 5.4 Deep Dive: `report.php` (Status Receipt & Countdown Redirect)
- **Success State**: Displays `public/assets/img/success.png`, paid amount, verified TrxID, and an automated 5-second countdown redirecting to `temp_transactions.params.success_url`.
- **Failed State**: Displays `public/assets/img/failed.png`, failure explanation, and return buttons.

---

### 5.5 Modular Gateway Partials (`methods/*.php`)
Inside `methods/` across view folders:
- `bkash.php`: bKash-specific USSD dial code (`*247#`) and App flowchart.
- `nagad.php`: Nagad-specific USSD dial code (`*167#`) and App flowchart.
- `rocket.php`: Dutch-Bangla Rocket USSD dial code (`*322#`) and App instructions.
- `upay.php`: UCB Upay USSD dial code (`*268#`) and App instructions.
- `cellfin.php`: Islami Bank Cellfin transfer guide.
- `bank.php`: Manual bank deposit instructions with account number and branch routing.
- `binance.php`: Cryptocurrency USDT TRC20/BEP20 address and QR code display.

---

### 5.6 Error Handling & 404 Fallback Redirection
In `app/Viewsuddok/errors/404.php`:
```php
<?php 
header("Location: https://skypaybd.top/"); 
exit(); 
?>
```
If an invalid URL, expired invoice hash, or direct directory traversal is attempted, the user is redirected back to the main service portal.

---

## 6. Design System & Public Assets Inventory

### 6.1 Graphic Assets & Iconography (`public/assets/img/`)

| Filename | Format | Dimensions | Purpose & Placement |
| :--- | :---: | :---: | :--- |
| `bkash.png` | PNG | 512x512 | bKash gateway selector card and payment modal header. |
| `nagad.png` | PNG | 512x512 | Nagad gateway selector card and payment modal header. |
| `rocket.png` | PNG | 512x512 | Dutch-Bangla Rocket gateway selector tile. |
| `upay.png` | PNG | 512x512 | UCB Upay gateway selector tile. |
| `Goldenpay.jpg` | JPEG | 1200x600 | Fallback header banner when brand has no custom logo uploaded. |
| `success.png` | PNG | 256x256 | High-resolution checkmark icon for `report.php` success state. |
| `failed.png` | PNG | 256x256 | High-resolution warning icon for `report.php` failure state. |
| `whatsapp.png` | PNG | 64x64 | Floating action icon connecting customers to merchant WhatsApp. |
| `phone-call.png`| PNG | 64x64 | Floating action icon opening phone dialer with merchant number. |
| `email.png` | PNG | 64x64 | Floating action icon opening email client with merchant address. |
| `consent.svg` | SVG | Vector | Security verification badge displayed next to TrxID form. |
| `download.jpeg` | JPEG | 64x64 | Icon for "Download Receipt" action button. |

---

### 6.2 Typography & Custom Bengali Fonts (`public/assets/css/`)
1. **`banglabold.ttf`**:
   - Custom embedded TrueType font.
   - Purpose: Renders Bengali instructions cleanly across all mobile browsers and older Android WebViews without font clipping or broken conjunct characters.
   - Applied via CSS:
     ```css
     @font-face {
         font-family: 'BanglaBold';
         src: url('banglabold.ttf') format('truetype');
     }
     .instruction-step { font-family: 'BanglaBold', sans-serif; }
     ```
2. **`Aileron-Bold.otf`**:
   - Neo-grotesque modern sans-serif font.
   - Purpose: Renders high-legibility numbers, currency figures (`৳ 777.00`), timer digits, and TrxID codes.

---

### 6.3 Cascading Style Sheets (`public/assets/css/`)
- **`style.css`**: Default stylesheet containing base utility classes, grid systems, and responsive layout rules.
- **`style2.css`**: UddoktaPay-inspired layout rules (card shadows, pill tabs).
- **`style4.css`**: High-contrast minimal layout for low-light environments.
- **`style5.css`**: Modern gradient layout rules.
- **`stylev2.css`**: Optimized V2 layout rules with mobile-first CSS media queries.
- **`404.css`**: Minimalist error page styling.

---

### 6.4 JavaScript & Toastr Notifications
- **`public/assets/js/jquery.js`**: jQuery 3.6+ framework for DOM manipulation and asynchronous AJAX form posting.
- **`public/assets/toast/toastr.min.js` & `toastr.min.css`**: Non-blocking toast notification library. Provides real-time feedback without disruptive alert popups.

---

## 7. API Protocols, Webhooks & Device Communications

### 7.1 Invoice Creation API Specification
- **Endpoint**: `POST /api/create-charge`
- **Headers**: `Content-Type: application/json`
- **Request Body**:
  ```json
  {
    "brand_key": "fXB5o17G3eregnsoB266sxelC5qBZGysKo4emeNP9bAEknZf04",
    "amount": 500.00,
    "currency": "BDT",
    "cus_name": "Rahim Ahmed",
    "cus_email": "rahim@example.com",
    "cus_phone": "01700000000",
    "success_url": "https://mystore.com/order/success",
    "cancel_url": "https://mystore.com/order/cancel",
    "webhook_url": "https://mystore.com/api/payment-callback"
  }
  ```
- **Response**:
  ```json
  {
    "status": "success",
    "payment_url": "https://pay.egpay.io/checkout/800378da44a0f67761f894fb7854f2a1",
    "invoice_id": "800378da44a0f67761f894fb7854f2a1"
  }
  ```

---

### 7.2 Transaction Verification API Specification
- **Endpoint**: `POST /api/verify`
- **Request Body (Form Data)**:
  ```
  ids: 800378da44a0f67761f894fb7854f2a1
  trxid: CCE6L9KZ74
  ```
- **Success Response**:
  ```json
  {
    "status": "success",
    "message": "Payment verified successfully!",
    "redirect_url": "https://pay.egpay.io/report/800378da44a0f67761f894fb7854f2a1"
  }
  ```
- **Failure Response**:
  ```json
  {
    "status": "error",
    "message": "Transaction ID not recognized or amount mismatch. Please verify and retry."
  }
  ```

---

### 7.3 Android Device SMS Push API Specification
- **Endpoint**: `POST /api/callback`
- **Headers**: `Content-Type: application/json`
- **Request Body**:
  ```json
  {
    "device_key": "hFKo7yB5Fg27PKLxT5hmi9xi1Wx3Zp6ZqPEm5zEh",
    "sender": "bKash",
    "message": "You have received payment Tk 500.00 from 01700000000. Fee Tk 0.00. Balance Tk 5,500.00. TrxID CCE6L9KZ74 at 18/03/2025 14:00"
  }
  ```
- **Response**:
  ```json
  {
    "status": "success",
    "message": "SMS ingested successfully"
  }
  ```

---

### 7.4 Outgoing Merchant Webhook Specification
- **Endpoint**: Configured in merchant's `webhook_url`
- **Method**: `POST`
- **Payload**:
  ```json
  {
    "status": "COMPLETED",
    "transaction_id": "CCE6L9KZ74",
    "amount": 500.00,
    "currency": "BDT",
    "brand_id": 19,
    "customer_email": "rahim@example.com",
    "timestamp": 1742300000,
    "signature": "c8f5f6e85d9a8c7b4e3f2a1d0e9c8b7a6f5e4d3c2b1a0f9e8d7c6b5a4f3e2d1"
  }
  ```

---

## 8. Developer Implementation & Customization Guide

### 8.1 Building a Custom Checkout Theme from Scratch
To implement a custom checkout theme (e.g. `Viewsmodern`):
1. Create a directory `app/Views/Viewsmodern/`.
2. Place the three core view files inside:
   - `execute.php`
   - `execute_payment.php`
   - `report.php`
3. Create the `methods/` subdirectory with provider templates:
   - `methods/bkash.php`, `methods/nagad.php`, `methods/rocket.php`, `methods/upay.php`.
4. Update the active theme key in the `options` table:
   ```sql
   UPDATE options SET value = 'modern' WHERE name = 'theme';
   ```
5. The view loader in `app/Controllers/Api.php` automatically resolves templates from `app/Views/Viewsmodern/`.

---

### 8.2 Adding a New Payment Gateway Provider (e.g., Tap / MCash)
1. Insert a new record into `payments`:
   ```sql
   INSERT INTO payments (type, name, sort, status, params) 
   VALUES ('tap', 'Tap Mobile Banking', 9, '1', '{"type":"tap","option":{"logo":"public/assets/img/tap.png"}}');
   ```
2. Create `methods/tap.php` in each theme folder with USSD instructions (`*259#`).
3. Add the provider icon `tap.png` to `public/assets/img/`.
4. Merchants can now activate `tap` under their brand payment settings!

---

### 8.3 Environment Configuration, Deployment & Permissions
1. Set database credentials in `.env` or `app/Config/Database.php`:
   ```ini
   database.default.hostname = localhost
   database.default.database = egpayio_pay
   database.default.username = db_user
   database.default.password = db_password
   database.default.DBDriver = MySQLi
   ```
2. Configure base URL in `app/Config/App.php`:
   ```php
   public string $baseURL = 'https://pay.yourdomain.com/';
   ```
3. Set writable permissions:
   ```bash
   chmod -R 775 writable/
   ```

---

## 9. Security, Fraud Mitigation & Production Hardening

1. **Anti-Replay Protection**:
   Incoming SMS records in `module_data` have binary status: `0` = pending, `1` = claimed. Once matched, status is immediately updated to `1` within a database transaction, making replay attacks impossible.
2. **Exact Amount Enforcement**:
   The verification engine checks that the amount received in the carrier SMS is greater than or equal to the session amount (`$cleanAmount >= $tempTrx->amount`), preventing partial payment bypasses.
3. **Session Expiration (Time-To-Live)**:
   Checkout sessions expire after 15 minutes. Expired sessions reject verification attempts.
4. **Merchant Scoping (`uid`)**:
   Verification queries strictly filter by `uid = $tempTrx->uid`. An SMS received on Merchant A's phone can never verify an invoice belonging to Merchant B.
5. **HMAC Webhook Signatures**:
   Outgoing webhooks include a SHA-256 HMAC signature calculated with the merchant's `brand_key`, allowing merchants to verify authentic gateway callbacks.

---

## 10. Conclusion & Engineering Summary

This architecture provides a complete, robust, and autonomous payment gateway engine. By isolating the checkout presentation layer on a dedicated CodeIgniter 4 subdomain while maintaining core user and brand management on the primary Laravel application, the platform achieves:
- Sub-millisecond checkout response times.
- Zero-commission personal/agent account automation.
- Resilient Android-to-server SMS matching.
- Multi-theme customizable checkout interfaces.

This technical documentation serves as the complete engineering reference for maintaining, customizing, and scaling the payment gateway ecosystem.
