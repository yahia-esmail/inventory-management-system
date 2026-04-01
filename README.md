# 📦 Inventory Management System

A fully browser-based Inventory Management System built with vanilla JavaScript, HTML, CSS, and Bootstrap 5. All data is persisted client-side via `localStorage` — no backend or build tools required.


---

## 📋 Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Architecture & Design Decisions](#-architecture--design-decisions)
- [Getting Started](#-getting-started)
- [Usage Guide](#-usage-guide)
- [Data Model](#-data-model)
- [Utility Reference](#-utility-reference)
- [Contributing](#-contributing)

---

## ✨ Features

| Module | Capabilities |
|---|---|
| **Dashboard** | Summary cards (total products, low stock, out-of-stock, inventory value), system overview, quick-action buttons |
| **Products** | Full CRUD, SKU uniqueness validation, category & supplier linking, stock status badges (In Stock / Low Stock / Out of Stock), search by name/SKU/category, filter by category & status, paginated table |
| **Categories** | Full CRUD with duplicate-name detection and description validation |
| **Suppliers** | Full CRUD with soft-delete (suppliers with pending orders are protected), search by name/contact/phone |
| **Purchase Orders** | Create multi-item orders linked to suppliers, auto-populate unit prices, receive or cancel orders (receiving increases stock automatically), status badges |
| **Stock Adjustments** | Increase or decrease product quantities with a mandatory reason, real-time stock hint, guards against over-subtracting |
| **Reports** | Low Stock report, Inventory Value breakdown (with % of total), full Summary overview of all modules |
| **Activity Log** | Timestamped log of every CRUD and stock action, filterable by module, paginated |
| **Dark / Light / Auto Theme** | Bootstrap color-mode toggle persistent across pages |
| **Responsive Layout** | Mobile-friendly sidebar that collapses to an off-canvas menu |

---

## 🛠 Tech Stack

| Technology | Version | Purpose |
|---|---|---|
| **HTML5** | — | Page structure & native `<dialog>` elements |
| **CSS3** | — | Custom properties (CSS variables), theme tokens, component styles |
| **Vanilla JavaScript (ES Modules)** | ES2020+ | All application logic, DOM manipulation, data layer |
| **Bootstrap** | 5.3.8 | Responsive grid, UI components (modals, toasts, offcanvas, badges) |
| **Bootstrap Icons** | 1.13.1 | Sidebar and table icons |
| **Font Awesome** | 6 (Kit) | Action icons throughout the UI |
| **Google Fonts** | Public Sans | Primary application font |
| **localStorage API** | — | Client-side data persistence (no backend) |
| **Intl API** | — | Currency formatting (`EGP`) and date localisation |

> All external libraries are loaded via **CDN** — no `npm install` or build step needed.

---

## 📁 Project Structure

```
inventory-management-system/
│
├── index.html              ← Dashboard
├── products.html           ← Products CRUD
├── categories.html         ← Categories CRUD
├── suppliers.html          ← Suppliers CRUD
├── orders.html             ← Purchase Orders
├── adjustments.html        ← Stock Adjustments
├── reports.html            ← Reports & Analytics
├── log.html                ← Activity Log
│
├── css/
│   └── style.css           ← All custom styles & CSS variable theme tokens
│
├── js/
│   ├── db.js               ← localStorage abstraction layer (shared data access)
│   ├── utils.js            ← Shared utilities: formatDate, formatCurrency, showToast, generateId, logAction
│   ├── navbar.js           ← Injects shared sidebar layout + highlights active link
│   ├── dashboard.js        ← Dashboard summary widget logic
│   ├── products.js         ← Products CRUD, search, filter, pagination
│   ├── categories.js       ← Categories CRUD
│   ├── suppliers.js        ← Suppliers CRUD, soft-delete, search
│   ├── orders.js           ← Purchase Orders creation, receive/cancel flow
│   ├── adjustments.js      ← Stock adjustment form & validation
│   ├── reports.js          ← Low stock, inventory value, and summary reports
│   └── log.js              ← Activity log renderer with filter & pagination
│
└── assets/
    ├── img/                ← Favicon images per page
    └── js/
        └── color-modes.js  ← Bootstrap dark/light/auto theme switcher
```

---

## 🏗 Architecture & Design Decisions

### Shared Layout Injection
Rather than duplicating the sidebar HTML across all pages, `navbar.js` dynamically renders the full layout (header + sidebar) and injects each page's content into a `#page-content-wrapper` element. Each page exports its main content inside a `#injected` div which is moved into the wrapper on `DOMContentLoaded`.

### Data Layer (`db.js`)
All `localStorage` read/write operations are centralised in `db.js`. Each entity (products, categories, suppliers, orders, adjustments, logs) has its own namespaced key. `db.js` also exposes a `seed()` method that populates realistic sample data on first load — it is only called when the relevant collection is empty, preventing data overwrites.

### Utilities (`utils.js`)
Shared helpers exported as a single default object:
- `generateNextId(array, prefix)` — deterministic, sequential IDs (e.g. `prod_001`)
- `formatDate(dateString)` — locale-aware date display (`en-GB`)
- `formatCurrency(amount)` — EGP formatting via `Intl.NumberFormat`
- `showToast(message, type)` — Bootstrap toast notifications (success / error)
- `confirmDelete(name)` — native `confirm()` guard before deletions
- `logAction(action, message)` — writes to the activity log automatically

### ES Modules
All JS files use `type="module"` for clean imports/exports and scoped variables with no global namespace pollution.

### Soft Deletes
Suppliers are never hard-deleted if they have associated products or pending orders. A `deleted: true` flag hides them from the UI while preserving data integrity across linked records.

---

## 🚀 Getting Started

### Prerequisites
- Any modern browser (Chrome, Firefox, Edge, Safari)
- A local static file server (required for ES Modules — `file://` won't work)

### Option 1 — VS Code Live Server (Recommended)
1. Install the [Live Server extension](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
2. Open the project folder in VS Code
3. Right-click `index.html` → **Open with Live Server**
4. The app opens at `http://127.0.0.1:5500`

### Option 2 — Python HTTP Server
```bash
# Python 3
python -m http.server 8080
```
Then visit `http://localhost:8080`

### Option 3 — Node.js `serve`
```bash
npx serve .
```
Then visit the URL shown in the terminal.

> **Note:** On first load, the app automatically seeds sample data (products, categories, suppliers) into `localStorage` so you can explore all features immediately.

---

## 📖 Usage Guide

### Dashboard
The home screen (`index.html`) shows live counts for products, low stock items, out-of-stock items, and total inventory value. Quick-action buttons navigate directly to the most common tasks.

### Managing Products
1. Navigate to **Products**
2. Click **Add Product** and fill in the name, SKU, price, category, supplier, quantity, and reorder level
3. Use the **search bar** to filter by name, SKU, or category
4. Use the **dropdowns** to filter by category or stock status
5. Click the **edit icon** to update product details (quantity is changed via Stock Adjustments only)
6. Click the **trash icon** to delete a product

### Managing Suppliers & Categories
- Both follow the same Add / Edit / Delete pattern via modal dialogs
- Suppliers with pending purchase orders **cannot be deleted**

### Purchase Orders
1. Navigate to **Purchase Orders** → **Create Purchase Order**
2. Select a supplier — the product dropdown auto-populates with that supplier's products
3. Add one or more line items with quantities; the total is calculated in real time
4. Submit the order (status: **Pending**)
5. Later, mark the order as **Received** (stock is automatically increased) or **Cancelled**

### Stock Adjustments
1. Navigate to **Stock Adjustments** → **Add Adjustment**
2. Select a product — a hint shows current stock and warns on low/zero stock
3. Choose **Increase** or **Decrease**, enter a quantity and reason
4. Submit — product quantity updates immediately and the action is logged

### Reports
Three report views are available via toggle buttons:
- **Low Stock** — all items at or below their reorder level
- **Inventory Value** — per-product value breakdown with percentage of total
- **Summary** — a full overview of inventory and operations statistics

### Activity Log
Every create, update, delete, and stock change is recorded automatically. Filter by module (Products, Categories, Suppliers, Orders, Stock) and page through entries chronologically (newest first).

### Theme Toggle
A floating button in the bottom-right corner switches between **Light**, **Dark**, and **Auto** (follows the OS preference).

---

## 🗄 Data Model

All entities are stored as JSON arrays in `localStorage`.

### Product
```json
{
  "id": "prod_001",
  "sku": "SKU-1001",
  "name": "Ergonomic Keyboard",
  "categoryId": "cat_001",
  "supplierId": "sup_001",
  "price": 1500,
  "quantity": 42,
  "reorderLevel": 10,
  "createdAt": "2026-01-15T10:00:00.000Z",
  "updatedAt": "2026-01-15T10:00:00.000Z"
}
```

### Category
```json
{
  "id": "cat_001",
  "name": "Electronics",
  "description": "Electronic devices and accessories",
  "createdAt": "2026-01-15T10:00:00.000Z"
}
```

### Supplier
```json
{
  "id": "sup_001",
  "name": "Tech Parts Inc.",
  "contact": "John Smith",
  "email": "john@techparts.com",
  "phone": "01100000000",
  "address": "123 Main St, Cairo",
  "createdAt": "2026-01-15T10:00:00.000Z",
  "deleted": false
}
```

### Purchase Order
```json
{
  "id": "po_001",
  "poNumber": "PO-4821",
  "supplierId": "sup_001",
  "status": "Pending",
  "items": [
    { "productId": "prod_001", "quantity": 10, "unitPrice": 1500 }
  ],
  "totalValue": 15000,
  "createdAt": "2026-01-15T10:00:00.000Z",
  "expectedDate": "2026-02-01",
  "receivedAt": null
}
```

### Stock Adjustment
```json
{
  "id": "adj_001",
  "productId": "prod_001",
  "type": "decrease",
  "quantity": 5,
  "reason": "Damaged in storage",
  "createdAt": "2026-01-15T10:00:00.000Z"
}
```

### Activity Log Entry
```json
{
  "id": "log_001",
  "action": "PRODUCT_ADDED",
  "message": "Product Ergonomic Keyboard (SKU: SKU-1001) has been added",
  "createdAt": "2026-01-15T10:00:00.000Z"
}
```

---

## 🔧 Utility Reference

### `ACTIONS` constants (`utils.js`)
| Constant | Triggered by |
|---|---|
| `PRODUCT_ADDED` | Adding a new product |
| `PRODUCT_UPDATED` | Editing a product |
| `PRODUCT_DELETED` | Deleting a product |
| `SUPPLIER_ADDED/UPDATED/DELETED` | Supplier CRUD |
| `CATEGORY_ADDED/UPDATED/DELETED` | Category CRUD |
| `ORDER_CREATED/RECEIVED/CANCELLED` | Purchase order lifecycle |
| `STOCK_ADJUSTED` | Stock adjustment or PO receive |

### Resetting All Data
Open the browser's **DevTools → Application → Local Storage**, select the origin, and click **Clear All**. Refresh the page to re-seed with sample data.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Make your changes and test across pages
4. Commit with a clear message: `git commit -m "feat: add export to CSV on reports page"`
5. Push and open a Pull Request



# inventory-management-system
