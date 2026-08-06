# Havano C# API Documentation (IIS Hosted)

This repository contains the complete API documentation for the **Havano C# API**, which acts as a bridge between Odoo and the Sage Evolution SDK.

## 🚀 Server Information
This API is hosted natively as a C# Application running under **Internet Information Services (IIS)** on a Windows Server. 

* **Host / Server IP**: `173.249.29.208`
* **Port**: `8156`
* **Base URL**: `http://173.249.29.208:8156/api`

---

## ⚙️ Configuration & Security

The API runs directly on the local network via IIS. Because the integration uses a **polling architecture** (Odoo requests data directly from the API), the API is completely open on the local network and relies on your Windows Firewall for security. 

**No JWT tokens or Webhook tokens are required.**

### Sage Evolution Database Configuration
The API connects to Sage Evolution using the settings defined in your `appsettings.json` file. Here are the default settings:

```json
"SageEvolution": {
    "Database": "Copy supreme job cards",
    "CommonDatabase": "SageCommon",
    "Server": "127.0.0.1,1022",
    "Username": "sa",
    "Password": "hT9mX4vQ2pL7dK3z",
    "SerialNumber": "DE12111066",
    "ActivationKey": "9824686",
    "DefaultWarehouseCode": "Mstr"
}
```

---

## 🔄 Sync (Polling Architecture)
*Odoo polls these endpoints periodically to fetch newly modified data from Sage.*

* `GET /api/Sync/changes` - Polls for any new, updated, or deleted records (Inventory, Customers, Suppliers, etc.)
* `POST /api/Sync/changes/{id}/acknowledge` - Acknowledges a change has been successfully synced to Odoo so it is cleared from the queue.
* `POST /api/Sync/changes/{id}/fail` - Marks a change as failed during Odoo sync so it can be retried later.

---

## 👥 Customers

* `GET /api/Customers` - Retrieves all customers
* `GET /api/Customers/{code}` - Retrieves a specific customer by Code
* `POST /api/Customers` - Creates a new customer
* `PUT /api/Customers` - Updates an existing customer

**Creation/Update Payload Example:**
```json
{
  "Code": "CUST001",
  "Name": "Acme Corp",
  "PhysicalAddress": { "Line1": "123 Main St", "City": "New York", "PostalCode": "10001" },
  "PostalAddress": { "Line1": "PO BOX 123", "City": "New York", "PostalCode": "10001" },
  "Telephone": "555-1234",
  "Email": "billing@acme.com",
  "TaxNumber": "TAX123456",
  "CreditLimit": 5000.0,
  "Active": true
}
```

---

## 🏭 Suppliers

* `GET /api/Suppliers` - Retrieves all suppliers
* `GET /api/Suppliers/{id}` - Retrieves a specific supplier
* `POST /api/Suppliers` - Creates a new supplier
* `PUT /api/Suppliers` - Updates an existing supplier

**Creation/Update Payload Example:**
```json
{
  "Code": "SUPP001",
  "Name": "Global Supplies Ltd",
  "Telephone": "555-9876",
  "Email": "orders@globalsupplies.com",
  "TaxNumber": "VAT987654",
  "Active": true
}
```

---

## 📦 Inventory (Products & Warehouses)

* `GET /api/Inventory` - Retrieves all inventory items
* `GET /api/Inventory/{id}` - Retrieves a specific item
* `POST /api/Inventory` - Creates a new inventory item
* `PUT /api/Inventory` - Updates an existing inventory item
* `GET /api/Inventory/warehouses` - Retrieves all warehouses
* `POST /api/Inventory/warehouse` - Assigns an item to a specific warehouse

**Creation/Update Payload Example:**
```json
{
  "Code": "ITEM100",
  "Description": "Zenon Headlight",
  "ItemGroup": "PARTS",
  "Active": true,
  "UnitSellingPrice": 150.00
}
```

---

## 🛒 Sales

* `GET /api/Sales` - Retrieves sales history
* `GET /api/Sales/orders` - Retrieves all Sales Orders
* `GET /api/Sales/orders/{id}` - Retrieves a specific Sales Order
* `GET /api/Sales/invoices` - Retrieves all Sales Invoices
* `POST /api/Sales/orders` - Creates a new Sales Order (or Quotation if `"IsQuotation": true`)
* `PUT /api/Sales/orders/{orderNo}` - Updates an existing Sales Order
* `POST /api/Sales/orders/{orderNumber}/invoice` - Processes a Sales Order into a Tax Invoice
* `POST /api/Sales/credit-notes` - Creates a Credit Note

**Create Sales Order Payload Example:**
```json
{
  "CustomerCode": "CUST001",
  "OrderDate": "2026-08-06T10:00:00Z",
  "OrderNumber": "SO-1025", 
  "AgentId": 1,
  "IsQuotation": false,
  "IsProcessed": false,
  "Lines": [
    {
      "ItemCode": "ITEM100",
      "WarehouseCode": "Mstr",
      "Quantity": 2,
      "UnitPrice": 150.00,
      "TaxTypeId": 1
    }
  ]
}
```

---

## 🚚 Purchases

* `GET /api/Purchase` - Retrieves purchase history
* `GET /api/Purchase/orders` - Retrieves all Purchase Orders
* `GET /api/Purchase/orders/{id}` - Retrieves a specific Purchase Order
* `GET /api/Purchase/invoices` - Retrieves all Purchase Invoices
* `POST /api/Purchase/orders` - Creates a new Purchase Order
* `PUT /api/Purchase/orders/{orderNo}` - Updates an existing Purchase Order
* `POST /api/Purchase/orders/grv` - Receives goods into the warehouse and creates a GRV
* `POST /api/Purchase/orders/{orderNumber}/invoice` - Finalizes a GRV into a Supplier Invoice
* `POST /api/Purchase/returns` - Creates a Return To Supplier (RTS) document

**Create Purchase Order Payload Example:**
```json
{
  "SupplierCode": "SUPP001",
  "OrderDate": "2026-08-06T10:00:00Z",
  "ExternalOrderNo": "PO-5050",
  "IsProcessed": false,
  "Lines": [
    {
      "ItemCode": "ITEM100",
      "WarehouseCode": "Mstr",
      "Quantity": 50,
      "UnitPrice": 95.00
    }
  ]
}
```

---

## 🕵️‍♂️ Agents (Users)

* `GET /api/Agents` - Retrieves a list of all Agents/Users from Sage.
* `POST /api/Agents` - Creates a new Agent.

---

## 🗄️ General Ledger & Accounts

* `POST /api/Accounts` - Creates a new GL Account
* `PUT /api/Accounts` - Updates an existing GL Account
* `POST /api/Journals` - Posts a new Journal entry to the GL
