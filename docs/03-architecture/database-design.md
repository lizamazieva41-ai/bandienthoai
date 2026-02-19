# Database Design Document

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  

---

## 1. Entity Relationship Diagram (ERD)

```mermaid
erDiagram
  CUSTOMER ||--o{ ORDER : places
  CUSTOMER ||--o{ WARRANTY_CASE : requests
  CUSTOMER ||--o{ ADDRESS : has

  ORDER ||--|{ ORDER_ITEM : contains
  ORDER ||--o{ PAYMENT : paid_by
  ORDER ||--o{ SHIPMENT : shipped_by
  ORDER ||--o{ RETURN_REQUEST : may_have

  PRODUCT ||--o{ PRODUCT_VARIANT : has
  PRODUCT ||--o{ PRODUCT_CATEGORY : belongs_to
  CATEGORY ||--o{ PRODUCT_CATEGORY : contains

  PRODUCT_VARIANT ||--o{ INVENTORY : stocked_as
  PRODUCT_VARIANT ||--o{ IMEI_SERIAL : identifies
  PRODUCT_VARIANT ||--o{ ORDER_ITEM : ordered_as

  WAREHOUSE ||--o{ INVENTORY : holds

  SHIPMENT ||--o{ SHIPMENT_EVENT : updates

  PROMOTION ||--o{ ORDER_PROMOTION : used_in
  ORDER ||--o{ ORDER_PROMOTION : applies

  SUPPLIER ||--o{ PURCHASE_ORDER : provides
  PURCHASE_ORDER ||--|{ PURCHASE_ITEM : includes
  PURCHASE_ITEM }o--|| PRODUCT_VARIANT : buys

  ORDER_ITEM }o--o| IMEI_SERIAL : assigned

  CUSTOMER {
    uuid id PK
    string phone UK
    string email UK
    string name
    string tier "regular|silver|gold|vip"
    boolean is_active
    datetime created_at
    datetime updated_at
  }

  ADDRESS {
    uuid id PK
    uuid customer_id FK
    string name
    string phone
    string province
    string district
    string ward
    string street
    boolean is_default
  }

  PRODUCT {
    uuid id PK
    string brand
    string model
    string slug UK
    text description
    jsonb specs
    string status "active|draft|archived"
    datetime created_at
    datetime updated_at
  }

  CATEGORY {
    uuid id PK
    uuid parent_id FK
    string name
    string slug UK
    int sort_order
  }

  PRODUCT_CATEGORY {
    uuid product_id FK
    uuid category_id FK
  }

  PRODUCT_VARIANT {
    uuid id PK
    uuid product_id FK
    string color
    string storage
    string sku UK
    bigint list_price
    bigint sale_price
    boolean track_imei
    string status "active|inactive"
    datetime sale_price_from
    datetime sale_price_to
  }

  IMEI_SERIAL {
    uuid id PK
    uuid variant_id FK
    uuid order_item_id FK
    string imei UK
    string status "in_stock|reserved|sold|returned|warranty"
    datetime created_at
  }

  WAREHOUSE {
    uuid id PK
    string name
    string address
    boolean is_active
  }

  INVENTORY {
    uuid id PK
    uuid warehouse_id FK
    uuid variant_id FK
    int on_hand
    int reserved
    int low_stock_threshold
    datetime updated_at
  }

  ORDER {
    uuid id PK
    string order_number UK
    uuid customer_id FK
    uuid shipping_address_id FK
    string status
    bigint subtotal
    bigint discount_amount
    bigint shipping_fee
    bigint total
    string payment_method
    string note
    string cancel_reason
    datetime confirmed_at
    datetime created_at
    datetime updated_at
  }

  ORDER_ITEM {
    uuid id PK
    uuid order_id FK
    uuid variant_id FK
    int qty
    bigint unit_price
    bigint total_price
  }

  PAYMENT {
    uuid id PK
    uuid order_id FK
    string provider "vnpay|momo|zalopay|cod|bank_transfer"
    string transaction_ref UK
    string provider_ref
    string status "pending|completed|failed|refunded"
    bigint amount
    jsonb metadata
    datetime paid_at
    datetime created_at
  }

  SHIPMENT {
    uuid id PK
    uuid order_id FK
    string carrier "ghn|ghtk"
    string tracking_code UK
    string status
    bigint cod_amount
    bigint shipping_fee
    boolean allow_open_package
    datetime created_at
    datetime updated_at
  }

  SHIPMENT_EVENT {
    uuid id PK
    uuid shipment_id FK
    string event_code
    string description
    string location
    datetime occurred_at
  }

  PROMOTION {
    uuid id PK
    string code UK
    string type "percent|fixed_amount"
    decimal value
    bigint min_order_value
    int max_uses
    int used_count
    int max_uses_per_customer
    datetime starts_at
    datetime ends_at
    boolean is_active
  }

  ORDER_PROMOTION {
    uuid order_id FK
    uuid promotion_id FK
    bigint discount_applied
  }

  RETURN_REQUEST {
    uuid id PK
    uuid order_id FK
    string reason
    string status "pending|approved|rejected|completed"
    text description
    jsonb evidence_urls
    string refund_method
    bigint refund_amount
    datetime created_at
    datetime updated_at
  }

  WARRANTY_CASE {
    uuid id PK
    uuid customer_id FK
    uuid variant_id FK
    string imei
    string issue
    string status "open|processing|resolved|closed"
    text description
    jsonb evidence_urls
    datetime created_at
    datetime updated_at
  }

  SUPPLIER {
    uuid id PK
    string name
    string phone
    string email
    string address
    boolean is_active
  }

  PURCHASE_ORDER {
    uuid id PK
    string po_number UK
    uuid supplier_id FK
    string status "draft|ordered|partial|received|cancelled"
    bigint total_amount
    datetime expected_at
    datetime created_at
  }

  PURCHASE_ITEM {
    uuid id PK
    uuid purchase_order_id FK
    uuid variant_id FK
    int qty_ordered
    int qty_received
    bigint unit_cost
  }
```

---

## 2. Data Dictionary

### 2.1 Bảng CUSTOMER

| Column | Type | Constraints | Mô tả |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | Định danh duy nhất |
| phone | VARCHAR(15) | UNIQUE, NOT NULL | Số điện thoại (định dạng VN) |
| email | VARCHAR(255) | UNIQUE | Email đăng nhập |
| name | VARCHAR(255) | NOT NULL | Họ tên đầy đủ |
| tier | VARCHAR(20) | DEFAULT 'regular' | Hạng thành viên |
| is_active | BOOLEAN | DEFAULT true | Tài khoản có hoạt động không |
| created_at | TIMESTAMPTZ | DEFAULT NOW() | Thời gian tạo (UTC) |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() | Cập nhật cuối (UTC) |

### 2.2 Bảng PRODUCT_VARIANT

| Column | Type | Constraints | Mô tả |
|---|---|---|---|
| id | UUID | PK | Định danh |
| product_id | UUID | FK → PRODUCT, NOT NULL | Sản phẩm cha |
| color | VARCHAR(50) | NOT NULL | Màu sắc (vd: "Đen", "Titanium") |
| storage | VARCHAR(20) | NOT NULL | Dung lượng (vd: "128GB", "256GB") |
| sku | VARCHAR(100) | UNIQUE, NOT NULL | Mã SKU |
| list_price | BIGINT | NOT NULL, CHECK ≥ 0 | Giá niêm yết (VNĐ, integer) |
| sale_price | BIGINT | CHECK ≥ 0 | Giá sale (NULL nếu không sale) |
| track_imei | BOOLEAN | DEFAULT false | Có theo dõi IMEI không |
| sale_price_from | TIMESTAMPTZ | | Thời gian bắt đầu sale |
| sale_price_to | TIMESTAMPTZ | | Thời gian kết thúc sale |

### 2.3 Bảng INVENTORY

| Column | Type | Constraints | Mô tả |
|---|---|---|---|
| id | UUID | PK | Định danh |
| warehouse_id | UUID | FK → WAREHOUSE, NOT NULL | Kho |
| variant_id | UUID | FK → PRODUCT_VARIANT, NOT NULL | Biến thể sản phẩm |
| on_hand | INT | NOT NULL, DEFAULT 0, CHECK ≥ 0 | Tồn kho thực |
| reserved | INT | NOT NULL, DEFAULT 0, CHECK ≥ 0 | Đã đặt giữ |
| low_stock_threshold | INT | DEFAULT 5 | Ngưỡng cảnh báo tồn thấp |

**Constraint:** `UNIQUE(warehouse_id, variant_id)`  
**Computed:** `available = on_hand - reserved` (tính lúc query)

### 2.4 Bảng ORDER

| Column | Type | Constraints | Mô tả |
|---|---|---|---|
| status | VARCHAR(30) | NOT NULL | Trạng thái: new/confirmed/packing/shipping/delivered/completed/cancelled/return_requested/returned |
| subtotal | BIGINT | NOT NULL | Tổng tiền hàng trước giảm |
| discount_amount | BIGINT | DEFAULT 0 | Số tiền giảm |
| shipping_fee | BIGINT | DEFAULT 0 | Phí vận chuyển |
| total | BIGINT | NOT NULL | Tổng tiền phải trả |

---

## 3. Indexing Strategy

### 3.1 Primary Indexes (tự động từ PK)
Tất cả bảng có PRIMARY KEY UUID, PostgreSQL tự tạo B-tree index.

### 3.2 Additional Indexes

```sql
-- Product/Search
CREATE INDEX idx_product_status ON product(status);
CREATE INDEX idx_product_brand ON product(brand);
CREATE INDEX idx_product_slug ON product(slug);  -- UNIQUE
CREATE INDEX idx_variant_product_id ON product_variant(product_id);
CREATE INDEX idx_variant_sku ON product_variant(sku);  -- UNIQUE

-- Inventory
CREATE UNIQUE INDEX idx_inventory_warehouse_variant ON inventory(warehouse_id, variant_id);
CREATE INDEX idx_inventory_variant ON inventory(variant_id);

-- IMEI
CREATE UNIQUE INDEX idx_imei ON imei_serial(imei);
CREATE INDEX idx_imei_variant ON imei_serial(variant_id);
CREATE INDEX idx_imei_status ON imei_serial(status);

-- Order
CREATE INDEX idx_order_customer ON "order"(customer_id);
CREATE INDEX idx_order_status ON "order"(status);
CREATE INDEX idx_order_created ON "order"(created_at DESC);
CREATE INDEX idx_order_number ON "order"(order_number);  -- UNIQUE

-- Payment
CREATE INDEX idx_payment_order ON payment(order_id);
CREATE UNIQUE INDEX idx_payment_ref ON payment(transaction_ref);

-- Shipment
CREATE INDEX idx_shipment_order ON shipment(order_id);
CREATE UNIQUE INDEX idx_shipment_tracking ON shipment(tracking_code);

-- Promotion
CREATE UNIQUE INDEX idx_promotion_code ON promotion(code);
CREATE INDEX idx_promotion_active ON promotion(is_active, starts_at, ends_at);

-- Full-text search (PostgreSQL tsvector)
CREATE INDEX idx_product_fts ON product USING gin(to_tsvector('simple', coalesce(brand,'') || ' ' || coalesce(model,'')));
```

### 3.3 Partial Indexes

```sql
-- Chỉ index active products
CREATE INDEX idx_product_active ON product(status) WHERE status = 'active';

-- Chỉ index available promotions
CREATE INDEX idx_promo_valid ON promotion(ends_at) WHERE is_active = true;

-- IMEI còn trong kho
CREATE INDEX idx_imei_in_stock ON imei_serial(variant_id) WHERE status = 'in_stock';
```

---

## 4. Migration Strategy

### 4.1 Tool
Sử dụng **Prisma Migrate** (nếu Node.js) hoặc **golang-migrate** (nếu Go) để quản lý database migrations.

### 4.2 Migration Principles
1. **Forward-only migrations** – không dùng down migrations trong production
2. **Expand-Contract pattern** cho breaking changes:
   - Expand: Thêm cột mới (nullable)
   - Migrate: Backfill dữ liệu cũ
   - Contract: Remove cột cũ (release tiếp theo)
3. **Zero-downtime migrations:** Thêm cột mới phải nullable hoặc có DEFAULT
4. Mỗi migration có timestamp prefix: `20260219_001_create_customer_table.sql`

### 4.3 Data Migration Plan (từ hệ thống cũ)

| Bước | Mô tả | Thời gian |
|---|---|---|
| 1 | Export dữ liệu sản phẩm từ POS/spreadsheet | 1–2 ngày |
| 2 | Transform & validate (SKU, price, category) | 1–2 ngày |
| 3 | Import vào staging, kiểm tra | 1 ngày |
| 4 | Import tồn kho ban đầu | 1 ngày |
| 5 | Migration khách hàng (nếu có) | 1–2 ngày |
| 6 | Final import production trước Go-live | 4–8 giờ |

---

## 5. Backup & Retention

| Loại backup | Tần suất | Retention | Tool |
|---|---|---|---|
| Full backup | Hàng ngày (2:00 AM) | 30 ngày | pg_dump + S3 |
| WAL archiving | Liên tục | 7 ngày | pg_basebackup / pgBackRest |
| Point-in-time recovery | – | 7 ngày | WAL streaming |
| Pre-deployment snapshot | Trước mỗi deploy | 30 ngày | Manual pg_dump |

---

## 6. Database Configuration (Production)

```ini
# postgresql.conf recommendations
max_connections = 200
shared_buffers = 2GB              # 25% RAM
effective_cache_size = 6GB        # 75% RAM
work_mem = 64MB
maintenance_work_mem = 512MB
wal_level = replica
max_wal_senders = 3
checkpoint_completion_target = 0.9
random_page_cost = 1.1            # SSD storage
effective_io_concurrency = 200    # SSD storage
log_min_duration_statement = 1000 # Log slow queries > 1s
```
