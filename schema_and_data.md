## Setup users (reference table)

```sql
CREATE TABLE users (
  user_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  email text NOT NULL UNIQUE,
  encrypted_password text NOT NULL ,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (user_id)
);

SELECT create_reference_table('users');
```

## Setup stores (distributed table)

```sql
CREATE TABLE stores (
  store_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  user_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  name text NOT NULL,
  category text NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (store_id)
);

SELECT create_distributed_table('stores', 'store_id');
```

## Setup products (distributed table)

```sql
CREATE TABLE products (
  store_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  product_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  name text NOT NULL,
  description text NOT NULL,
  product_details jsonb NOT NULL,
  price numeric(20,2) NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (store_id, product_id),
  FOREIGN KEY (store_id) REFERENCES stores (store_id)
);

SELECT create_distributed_table('products', 'store_id');
```

## Setup orders (distributed table)

```sql
CREATE TABLE orders (
  store_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  order_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  status text NOT NULL,
  total_amount numeric(20,2) NOT NULL,
  shipping_address text NOT NULL,
  billing_address text NOT NULL,
  shipping_info jsonb NOT NULL,
  ordered_at timestamptz,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (store_id, order_id),
  FOREIGN KEY (store_id) REFERENCES stores (store_id)
);

SELECT create_distributed_table('orders', 'store_id');
```

## Setup line items (distributed table)

```sql
CREATE TABLE line_items (
  store_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  line_item_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  order_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  product_id uuid NOT NULL DEFAULT uuid_generate_v4(),
  quantity integer NOT NULL,
  line_amount numeric(20,2) NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (store_id, line_item_id)
);

SELECT create_distributed_table('line_items', 'store_id');
```

## Load data

```sql
SELECT load_tutorial_data();
```
