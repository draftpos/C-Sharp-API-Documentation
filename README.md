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
    "DefaultWarehouseCode": "MS"
}
```

---

## 👥 Customers

### 1. Create or Update Customer
`POST /api/Customers`
`PUT /api/Customers`

Creates a new customer or updates an existing customer in Sage Evolution.

**Payload Example:**
```json
{
  "Code": "CUST001",
  "Name": "Acme Corp",
  "Description": "Acme Corporation HQ",
  "PhysicalAddress": {
    "Line1": "123 Main St",
    "Line2": "Suite 500",
    "City": "New York",
    "PostalCode": "10001"
  },
  "PostalAddress": {
    "Line1": "PO BOX 123",
    "City": "New York",
    "PostalCode": "10001"
  },
  "Telephone": "555-1234",
  "Email": "billing@acme.com",
  "TaxNumber": "TAX123456",
  "CreditLimit": 5000.0,
  "Active": true
}
```

---

## 🏭 Suppliers

### 1. Create or Update Supplier
`POST /api/Suppliers`
`PUT /api/Suppliers`

**Payload Example:**
```json
{
  "Code": "SUPP001",
  "Name": "Global Supplies Ltd",
  "Description": "Hardware Vendor",
  "Telephone": "555-9876",
  "Email": "orders@globalsupplies.com",
  "TaxNumber": "VAT987654",
  "Active": true
}
```

---

## 📦 Inventory (Products)

### 1. Create or Update Product
`POST /api/Inventory`
`PUT /api/Inventory`

Creates a new inventory item in Sage, automatically linking it to the default warehouse (`MS`) and assigning it to the required item groups.

**Payload Example:**
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

### 1. Create Sales Order or Quotation
`POST /api/Sales/orders`

Creates a Sales Order or a Quotation in Sage. 
* To create a Quotation, set `"IsQuotation": true`. 
* To instantly process the order into an Invoice, set `"IsProcessed": true`.

**Payload Example:**
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
      "WarehouseCode": "MS",
      "Quantity": 2,
      "UnitPrice": 150.00,
      "TaxTypeId": 1
    }
  ]
}
```

### 2. Process Sales Order into Invoice
`POST /api/Sales/orders/{orderNumber}/invoice`

Converts a pre-existing Sales Order in Sage into a finalized Tax Invoice.

**Payload Example:**
```json
{
  "OrderNumber": "SO-1025",
  "InvoiceNo": "INV-1025"
}
```

---

## 🚚 Purchases

### 1. Create Purchase Order
`POST /api/Purchase/orders`

Creates a Purchase Order in Sage Evolution.

**Payload Example:**
```json
{
  "SupplierCode": "SUPP001",
  "OrderDate": "2026-08-06T10:00:00Z",
  "ExternalOrderNo": "PO-5050",
  "IsProcessed": false,
  "Lines": [
    {
      "ItemCode": "ITEM100",
      "WarehouseCode": "MS",
      "Quantity": 50,
      "UnitPrice": 95.00
    }
  ]
}
```

### 2. Process Purchase Order into GRV (Receive Goods)
`POST /api/Purchase/orders/grv`

Receives the goods in Sage, moving the stock into the warehouse and archiving the Purchase Order into a GRV.

**Payload Example:**
```json
{
  "OrderNumber": "PO-5050",
  "ExternalOrderNo": "PO-5050",
  "Lines": [
    {
      "ItemCode": "ITEM100",
      "WarehouseCode": "MS",
      "QuantityToProcess": 50
    }
  ]
}
```

### 3. Create Supplier Invoice (Bill)
`POST /api/Purchase/orders/{orderNumber}/invoice`

Finalizes the GRV into a Supplier Invoice in Sage.

**Payload Example:**
```json
{
  "OrderNumber": "PO-5050",
  "SupplierInvoiceNo": "BILL-998877"
}
```

---

## 🕵️‍♂️ Agents (Users)

### 1. Get All Agents
`GET /api/Agents`

Retrieves a list of all Agents/Users from Sage.

**Response Example:**
```json
[
  {
    "id": 1,
    "name": "Admin",
    "active": true
  },
  {
    "id": 2,
    "name": "John Doe",
    "active": true
  }
]
```
