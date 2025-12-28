# MCP Data Series

A project demonstrating Model Context Protocol (MCP) integration with Docker and PostgreSQL for database management and optimization.

## Overview

This project showcases the use of MCP tools to:
- Manage Docker containers (PostgreSQL)
- Interact with PostgreSQL databases
- Perform database audits and optimizations
- Analyze query performance and indexes

## Prerequisites

- Docker Desktop installed and running
- Python 3.11 or higher
- MCP server configured for Docker and PostgreSQL

## Project Structure

```
.
├── README.md           # This file
├── main.py            # Main application entry point
├── pyproject.toml     # Project configuration
├── demo_data.sql      # Database sample data
└── .python-version    # Python version specification (3.14)
```

## Database Setup

### PostgreSQL Container

The project uses a PostgreSQL 16 container named `pg-local` with the following configuration:

- **Container Name**: `pg-local`
- **Port**: 5432 (host) → 5432 (container)
- **User**: `app`
- **Password**: `app`
- **Database**: `demo`
- **Schema**: `app`

### Database Schema

The `app` schema contains the following tables:

#### Tables

1. **customers**
   - `customer_id` (PK, integer)
   - `email` (varchar)
   - `registration_date` (date)
   - `country` (varchar)
   - `tier` (varchar)

2. **orders**
   - `order_id` (PK, integer)
   - `customer_id` (FK → customers.customer_id)
   - `order_date` (date)
   - `status` (varchar)
   - `total_amount` (numeric)
   - `region` (varchar)

3. **order_items**
   - `item_id` (PK, integer)
   - `order_id` (FK → orders.order_id)
   - `product_id` (FK → products.product_id)
   - `quantity` (integer)
   - `price` (numeric)

4. **products**
   - `product_id` (PK, integer)
   - `name` (varchar)
   - `category` (varchar)
   - `price` (numeric)
   - `stock_quantity` (integer)

### Relationships

- Customers → Orders (one-to-many)
- Orders → Order Items (one-to-many)
- Products → Order Items (one-to-many)

## Database Optimizations

The following indexes have been created to optimize query performance:

1. **idx_orders_customer_id** - Index on `orders.customer_id` (foreign key)
2. **idx_order_items_order_id** - Index on `order_items.order_id` (foreign key)
3. **idx_order_items_product_id** - Index on `order_items.product_id` (foreign key)

### Performance Metrics

- **Cache Hit Rate**: 95.51%
- **Row Counts**:
  - Customers: 2,000
  - Orders: 10,000
  - Order Items: 30,000
  - Products: 1,000

## Prompts Used

This section documents the prompts/commands used to set up and configure the database:

### 1. Create PostgreSQL Container

```
Using the Docker MCP, create a PostgreSQL 16 container named
pg-local on port 5432 with user/password 'app' and database 'demo'
```

### 2. Configure Database User and Schema

```
Using the Postgres MCP, configure the database user named 'app' with 
password 'app', grant it all permissions on the demo database, and 
create a schema called 'app' owned by this user
```

### 3. List Containers and Tables

```
Using the Docker MCP, list all running containers. Then using the
Postgres MCP, list all tables in the demo database.
```

### 4. Generate Database Schema Diagram

```
Using the Postgres MCP, read the database schema and 
sample 3 rows from each table. Create a Mermaid diagram
including  the example values. Render the diagram in
the chat only, no files. Use a high-contrast style.
```

### 5. Database Audit

```
Using PostgreSQL MCP, audit my database (app schema: customers, orders, order_items, products).

Run these checks:
1. EXPLAIN ANALYZE on a join query between orders and customers - show execution time and scan types
2. Missing indexes - check which tables lack indexes on foreign keys
3. Cache hit rate - query pg_stat_database

Present findings in a table format with columns: Issue | Impact | Priority

Then provide:
- Top 3 optimization recommendations with exact SQL to implement

Keep responses concise but include key metrics (execution times, cache hit %, row counts).
```

### 6. Create Recommended Indexes

```
Using the Postgres MCP, create ALL the indexes you recommended in your audit. Execute each CREATE INDEX statement and confirm when all indexes are created.
```

## Usage

### Running the Application

```bash
python main.py
```

### Connecting to the Database

```bash
psql -h localhost -p 5432 -U app -d demo
```

Password: `app`

### Using MCP Tools

This project demonstrates the use of MCP tools for:

- **Docker MCP**: Container management
  - Creating and managing PostgreSQL containers
  - Listing running containers
  - Viewing container logs

- **PostgreSQL MCP**: Database operations
  - Executing SQL queries
  - Analyzing query performance (EXPLAIN ANALYZE)
  - Database health checks
  - Index recommendations
  - Schema inspection

## Database Audit Results

A comprehensive audit was performed on the database, identifying:

### Issues Found

| Issue | Impact | Priority |
|-------|--------|----------|
| Missing index on orders.customer_id | Sequential scan on 10,000 orders | HIGH |
| Missing index on order_items.order_id | Slow joins with 30,000 order_items | HIGH |
| Missing index on order_items.product_id | Sequential scans on product lookups | MEDIUM |
| High planning time (18.5ms) | Planning exceeds execution time | MEDIUM |
| Cache hit rate 95.51% | Good but can improve | LOW |

### Optimizations Applied

All recommended indexes have been created to improve query performance.

## Development

### Python Version

This project uses Python 3.14 (specified in `.python-version`).

### Dependencies

See `pyproject.toml` for project dependencies. Currently, the project has no external dependencies.

## License

[Add your license here]

## Contributing

[Add contribution guidelines here]

