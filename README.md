# 🌐 SkyPay BD — Technical Asset Documentation & Architecture Guide

<div align="center">

<p align="center">
  <strong>Comprehensive Documentation of Frontend Landing Page & User Dashboard Assets</strong>
</p>

<!-- Technology Badges & Official Links -->
<p align="center">
  <a href="https://developer.mozilla.org/en-US/docs/Web/CSS" target="_blank">
    <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS3 Official Docs" />
  </a>
  <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript" target="_blank">
    <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript Official Docs" />
  </a>
  <a href="https://codeigniter.com/" target="_blank">
    <img src="https://img.shields.io/badge/CodeIgniter_4-EF4223?style=for-the-badge&logo=codeigniter&logoColor=white" alt="CodeIgniter Official Website" />
  </a>
  <a href="https://laravel.com/docs/blade" target="_blank">
    <img src="https://img.shields.io/badge/Blade_Engine-FF2D20?style=for-the-badge&logo=laravel&logoColor=white" alt="Blade Documentation" />
  </a>
  <a href="https://getbootstrap.com/" target="_blank">
    <img src="https://img.shields.io/badge/Bootstrap_5-7952B3?style=for-the-badge&logo=bootstrap&logoColor=white" alt="Bootstrap Official" />
  </a>
  <a href="https://jquery.com/" target="_blank">
    <img src="https://img.shields.io/badge/jQuery-0769AD?style=for-the-badge&logo=jquery&logoColor=white" alt="jQuery Official" />
  </a>
</p>

---

</div>

## 📌 Executive Overview

This document provides an exhaustive inventory and architectural analysis of all stylesheets (CSS), JavaScript (JS) libraries, icon fonts, and layout structures powering the **[SkyPay](https://skypaybd.top/)** platform.

The system is built on a modular PHP architecture (typically **CodeIgniter 4** / modular MVC pattern). It employs two distinct UI ecosystems:
1. **Frontend Landing Page:** Built with a modern responsive landing page template utilizing Bootstrap 5, AOS animations, Swiper sliders, and GLightbox.
2. **User Dashboard:** Built using the **`blithe`** admin dashboard theme integrated with jQuery UI, Select2, TinyMCE, jQuery File Upload, and JQVMap.

---

## 🎨 1. Stylesheets & CSS Assets

### 🔹 A. Landing Page Stylesheets (`https://skypaybd.top/`)

| # | File Name | Directory Path (Server / cPanel) | Direct URL | Description & Purpose |
|---|---|---|---|---|
| 1 | `all.min.css` (FontAwesome) | CDN (`cdnjs.cloudflare.com`) | [all.min.css](https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css) | **Font Awesome 5.15.4**: Comprehensive vector icon toolkit for social, navigation, and UI symbols. |
| 2 | `aos.css` | `public/assets/frontend/vendor/aos/` | [aos.css](https://skypaybd.top/public/assets/frontend/vendor/aos/aos.css) | **Animate On Scroll (AOS)**: CSS animations triggered when elements enter viewport on scroll. |
| 3 | `bootstrap.min.css` | `public/assets/frontend/vendor/bootstrap/css/` | [bootstrap.min.css](https://skypaybd.top/public/assets/frontend/vendor/bootstrap/css/bootstrap.min.css) | **Bootstrap Framework**: Core responsive grid system, layout containers, and typography. |
| 4 | `bootstrap-icons.css` | `public/assets/frontend/vendor/bootstrap-icons/` | [bootstrap-icons.css](https://skypaybd.top/public/assets/frontend/vendor/bootstrap-icons/bootstrap-icons.css) | **Bootstrap Icons**: Lightweight SVG font icons for buttons, lists, and feature highlights. |
| 5 | `glightbox.min.css` | `public/assets/frontend/vendor/glightbox/css/` | [glightbox.min.css](https://skypaybd.top/public/assets/frontend/vendor/glightbox/css/glightbox.min.css) | **GLightbox**: Lightbox popup modal styling for responsive media and video preview. |
| 6 | `remixicon.css` | `public/assets/frontend/vendor/remixicon/` | [remixicon.css](https://skypaybd.top/public/assets/frontend/vendor/remixicon/remixicon.css) | **Remix Icon**: Open-source neutral-style system symbol set for UI actions and fintech icons. |
| 7 | `swiper-bundle.min.css` | `public/assets/frontend/vendor/swiper/` | [swiper-bundle.min.css](https://skypaybd.top/public/assets/frontend/vendor/swiper/swiper-bundle.min.css) | **Swiper Carousel**: Hardware-accelerated touch slider and carousel layout styles. |
| 8 | `style.css` | `public/assets/frontend/css/` | [style.css](https://skypaybd.top/public/assets/frontend/css/style.css) | **Frontend Custom Theme**: Primary custom stylesheet for branding, hero sections, pricing cards, and footer. |

---

### 🔹 B. User Dashboard Stylesheets (`https://skypaybd.top/user/dashboard`)

| # | File Name | Directory Path (Server / cPanel) | Direct URL | Description & Purpose |
|---|---|---|---|---|
| 1 | `app.min.css` | `public/assets/blithe/css/` | [app.min.css](https://skypaybd.top/public/assets/blithe/css/app.min.css) | **Blithe Core CSS**: Main administrative dashboard framework CSS, navigation, top bar, and layout grid. |
| 2 | `style.css` | `public/assets/blithe/css/` | [style.css](https://skypaybd.top/public/assets/blithe/css/style.css) | **Blithe Theme Styles**: Custom dark/light theme properties, sidebar colors, and admin layouts. |
| 3 | `jquery.toast.css` | `public/assets/js/jquery-toast/css/` | [jquery.toast.css](https://skypaybd.top/public/assets/js/jquery-toast/css/jquery.toast.css) | **jQuery Toast**: Toast notification styling for asynchronous status and alerts. |
| 4 | `select2.min.css` | `public/assets/js/select2/css/` | [select2.min.css](https://skypaybd.top/public/assets/js/select2/css/select2.min.css) | **Select2 CSS**: Enhances standard `<select>` inputs with search, tags, and custom styling. |
| 5 | `components.css` | `public/assets/blithe/css/` | [components.css](https://skypaybd.top/public/assets/blithe/css/components.css) | **UI Components**: Cards, statistics widgets, badges, tabs, and tables for data presentation. |
| 6 | `jqvmap.min.css` | `public/assets/blithe/css/` | [jqvmap.min.css](https://skypaybd.top/public/assets/blithe/css/jqvmap.min.css) | **JQVMap**: Vector map stylesheets used for geographic transaction analysis and heatmaps. |

---

## ⚙️ 2. JavaScript (JS) Assets

### 🔹 A. Landing Page Scripts

| # | Script Name | Directory Path (Server / cPanel) | Direct URL | Description & Purpose |
|---|---|---|---|---|
| 1 | `purecounter_vanilla.js` | `public/assets/frontend/vendor/purecounter/` | [purecounter_vanilla.js](https://skypaybd.top/public/assets/frontend/vendor/purecounter/purecounter_vanilla.js) | **PureCounter**: Animates numbers counting up when scrolled into view (transactions, users, stats). |
| 2 | `aos.js` | `public/assets/frontend/vendor/aos/` | [aos.js](https://skypaybd.top/public/assets/frontend/vendor/aos/aos.js) | **AOS Script**: JavaScript trigger for scroll-based entrance animations. |
| 3 | `bootstrap.bundle.min.js`| `public/assets/frontend/vendor/bootstrap/js/` | [bootstrap.bundle.min.js](https://skypaybd.top/public/assets/frontend/vendor/bootstrap/js/bootstrap.bundle.min.js) | **Bootstrap 5 Bundle**: Handles dropdowns, mobile navigation toggles, modals, and tooltips. |
| 4 | `glightbox.min.js` | `public/assets/frontend/vendor/glightbox/js/` | [glightbox.min.js](https://skypaybd.top/public/assets/frontend/vendor/glightbox/js/glightbox.min.js) | **GLightbox JS**: Modal lightbox controller for responsive gallery and video playback. |
| 5 | `isotope.pkgd.min.js` | `public/assets/frontend/vendor/isotope-layout/` | [isotope.pkgd.min.js](https://skypaybd.top/public/assets/frontend/vendor/isotope-layout/isotope.pkgd.min.js) | **Isotope Layout**: Intelligent grid layout and dynamic category filtering. |
| 6 | `swiper-bundle.min.js` | `public/assets/frontend/vendor/swiper/` | [swiper-bundle.min.js](https://skypaybd.top/public/assets/frontend/vendor/swiper/swiper-bundle.min.js) | **Swiper Slider**: Powers client review sliders, logo carousels, and responsive testimonials. |
| 7 | `validate.js` | `public/assets/frontend/vendor/php-email-form/`| [validate.js](https://skypaybd.top/public/assets/frontend/vendor/php-email-form/validate.js) | **PHP Email Form**: Asynchronous client-side form validation and AJAX handler for contact inquiries. |
| 8 | `main.js` | `public/assets/frontend/js/` | [main.js](https://skypaybd.top/public/assets/frontend/js/main.js) | **Landing Page Main Controller**: Initializes all vendor plugins, sticky header, and back-to-top button. |

---

### 🔹 B. User Dashboard Scripts

| # | Script Name | Location / Path | Direct URL | Description & Purpose |
|---|---|---|---|---|
| 1 | `app.min.js` (In `<head>`) | `public/assets/blithe/js/` | [app.min.js](https://skypaybd.top/public/assets/blithe/js/app.min.js) | **Blithe Core Bundle**: Bundles core framework JavaScript dependencies (jQuery & core layout helpers). |
| 2 | `scripts.js` | `public/assets/blithe/js/` | [scripts.js](https://skypaybd.top/public/assets/blithe/js/scripts.js) | **Dashboard UI Logic**: Handles sidebar toggle, dark mode, submenus, and panel collapses. |
| 3 | `notify.min.js` | `public/assets/js/` | [notify.min.js](https://skypaybd.top/public/assets/js/notify.min.js) | **Notify.js**: Notification banner and popup feedback mechanism. |
| 4 | `tinymce.min.js` | `public/assets/js/tinymce/` | [tinymce.min.js](https://skypaybd.top/public/assets/js/tinymce/tinymce.min.js) | **TinyMCE WYSIWYG**: Rich HTML text editor for invoices, emails, and ticket descriptions. |
| 5 | `jquery.toast.js` | `public/assets/js/jquery-toast/js/` | [jquery.toast.js](https://skypaybd.top/public/assets/js/jquery-toast/js/jquery.toast.js) | **jQuery Toast**: Toast popups displaying success, warning, or error messages after actions. |
| 6 | `process2.js` | `public/assets/js/` | [process2.js](https://skypaybd.top/public/assets/js/process2.js) | **Async Action Engine**: Custom platform JavaScript handling automated payments, verification, and API polling. |
| 7 | `general.js` | `public/assets/js/` | [general.js](https://skypaybd.top/public/assets/js/general.js) | **Global Utilities**: Common helper functions, number formatters, date parsers, and AJAX helpers. |
| 8 | `select2.full.min.js` | `public/assets/js/select2/js/` | [select2.full.min.js](https://skypaybd.top/public/assets/js/select2/js/select2.full.min.js) | **Select2 Full Engine**: Interactive dropdown select controller with search capabilities. |
| 9 | `admin.js` | `public/assets/js/` | [admin.js](https://skypaybd.top/public/assets/js/admin.js) | **Admin / User Dashboard Logic**: Core dashboard operations, table actions, and form submissions. |
| 10 | `jquery.ui.widget.js` | `public/assets/js/jquery-upload/js/vendor/` | [jquery.ui.widget.js](https://skypaybd.top/public/assets/js/jquery-upload/js/vendor/jquery.ui.widget.js) | **jQuery UI Widget Factory**: Foundational component for the jQuery file upload mechanism. |
| 11 | `jquery.iframe-transport.js` | `public/assets/js/jquery-upload/js/` | [jquery.iframe-transport.js](https://skypaybd.top/public/assets/js/jquery-upload/js/jquery.iframe-transport.js) | **Iframe Transport**: Handles cross-domain and fallback file uploads without page reloads. |
| 12 | `jquery.fileupload.js` | `public/assets/js/jquery-upload/js/` | [jquery.fileupload.js](https://skypaybd.top/public/assets/js/jquery-upload/js/jquery.fileupload.js) | **jQuery File Upload**: Multi-file upload plugin with progress bar support for payment proofs/documents. |
| 13 | `jquery-ui.min.js` | `public/assets/js/` | [jquery-ui.min.js](https://skypaybd.top/public/assets/js/jquery-ui.min.js) | **jQuery UI**: Powers draggable modals, datepickers, accordions, and animations. |
| 14 | `blithe.js` | `public/assets/js/` | [blithe.js](https://skypaybd.top/public/assets/js/blithe.js) | **Blithe Custom Driver**: Glue code that connects dashboard forms with backend API endpoints. |
| 15 | `gtag.js` (External) | Google Tag Manager | [gtag.js](https://www.googletagmanager.com/gtag/js?id=G-Y94EHHD0L5) | **Google Analytics 4 (`G-Y94EHHD0L5`)**: User event tracking, page view analytics, and conversions. |
| 16 | `MyAliceWebChat` | Third-party service | Inline Script | **MyAlice Live Chat Integration**: WhatsApp and Telegram embedded live chat widget. |

---

## 🔤 3. Icon & Typography Fonts Used

The application relies on modern vector web fonts and SVG icon sets rather than legacy static images:

1. **Font Awesome 5 (`fas`, `far`, `fab`)**:
   - Location: `https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css`
   - Assets: Webfonts loaded via CSS (`fa-solid-900.woff2`, `fa-brands-400.woff2`, etc.).
2. **Bootstrap Icons (`bi`)**:
   - Location: `public/assets/frontend/vendor/bootstrap-icons/bootstrap-icons.css`
   - Assets: Font files located in `public/assets/frontend/vendor/bootstrap-icons/fonts/`.
3. **Remixicon (`ri`)**:
   - Location: `public/assets/frontend/vendor/remixicon/remixicon.css`
   - Assets: Modern lightweight vector icons used throughout landing page action buttons.

---

## 📁 4. cPanel & CodeIgniter 4 Directory Architecture

If you are navigating via **cPanel File Manager**, here is where each file lives and how the template is structured:

```text
public_html/
│
├── app/                                # Backend Application Logic
│   ├── Config/                         # App configurations & routes
│   ├── Modules/                        # Modular architecture (e.g. User, Admin, Payment)
│   │   └── User/
│   │       ├── Controllers/            # Handles requests
│   │       └── Views/                  # Blade-like view files (content only)
│   └── Views/                          # Global Views & Master Layouts
│       ├── layouts/                    # Master templates containing <head>, header, footer
│       │   ├── master.php              # Or default.php (contains dashboard <head> & <scripts>)
│       │   └── frontend.php            # Master template for landing page
│       └── themes/
│           └── blithe/                 # Blithe theme header, sidebar, footer partials
│
└── public/                             # Public Web Root (Where browser loads assets from)
    ├── uploads/                        # User and Admin uploaded media & logos
    │   └── admin/.../
    └── assets/                         # Static Assets Directory
        ├── frontend/                   # Landing Page Assets
        │   ├── css/                    # style.css
        │   ├── js/                     # main.js
        │   └── vendor/                 # aos, bootstrap, glightbox, swiper, isotope
        ├── blithe/                     # Dashboard Theme Assets
        │   ├── css/                    # app.min.css, style.css, components.css, jqvmap.min.css
        │   └── js/                     # app.min.js, scripts.js
        └── js/                         # Common Plugins & Dashboard Controllers
            ├── notify.min.js
            ├── general.js
            ├── process2.js
            ├── admin.js
            ├── blithe.js
            ├── jquery-ui.min.js
            ├── select2/
            ├── tinymce/
            ├── jquery-toast/
            └── jquery-upload/
```

### 💡 Why view files don't show `<link>` or `<script>` tags:
In CodeIgniter 4 and modular PHP architectures, view files inside `app/Modules/User/Views/` only contain the specific page's HTML body. They inherit from a parent layout using:
```php
<?= $this->extend('layouts/master') ?>
<?= $this->section('content') ?>
    <!-- Your page content goes here -->
<?= $this->endSection() ?>
```
All the `<link rel="stylesheet">` tags are defined once in the master layout's `header.php` or `master.php`, and `<script>` tags are loaded in `footer.php`.

---

## 📄 Summary & Maintenance Notes

- **To modify landing page design:** Edit `public/assets/frontend/css/style.css`.
- **To modify dashboard look and feel:** Edit `public/assets/blithe/css/style.css` and `components.css`.
- **To add new scripts globally:** Add `<script>` tags into the master layout file in `app/Views/layouts/` or `app/Views/themes/blithe/`.
