# Prompt-Driven Data Engineering  
**Operate real data infrastructure using Cursor, uv, and MCP**

This project demonstrates a **modern data engineering workflow** where databases and infrastructure are managed using **natural language prompts inside Cursor**. Instead of switching between CLIs, SQL clients, and dashboards, you use **ChatOps** powered by **Model Context Protocol (MCP)**.

By the end of this project, you will:
- Provision PostgreSQL using prompts
- Manage schemas and users conversationally
- Load and analyze large datasets
- Introspect and visualize database structure
- Build dbt models with prompt-driven development
- Run data quality tests and fix issues conversationally
- Document data models with AI assistance
- Perform analytics directly from Cursor chat

---

## Tech Stack

| Tool | Role |
|---|---|
| **Cursor** | AI-native IDE & ChatOps interface |
| **uv** | Fast Python environment + MCP runner |
| **Docker MCP** | Infrastructure control via prompts |
| **Postgres MCP** | SQL execution & schema introspection |
| **dbt MCP** | Transformations, testing & documentation |
| **PostgreSQL** | Analytical data store |
| **dbt** | Data transformation & modeling framework |

---

## Why This Project Matters

Traditional data engineering workflows rely on:
- CLI-heavy Docker commands  
- Manual SQL execution  
- Separate tools for schema design, analytics, and debugging  

This project introduces a **prompt-native data engineering model**:
- Infrastructure as conversation  
- SQL as dialogue  
- Schema design via AI reasoning  
- Debugging with contextual awareness  

This is **ChatOps applied to Data Engineering**.

---

## Project Initialization (uv-first)

All MCP servers run inside a Python environment managed by `uv`.

### Initialize the Project

Open Cursor’s integrated terminal inside your project folder and run:

```bash
uv init
```

This creates:
- `pyproject.toml` – dependency & environment tracking  
- `.gitignore`  
- `README.md`  
- `main.py`  

---

## Install & Verify uv

Check if `uv` is already installed:

```bash
uv --version
```

If not installed:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Verify installation:

```bash
uv --version
```

> `uv` automatically handles Python environments and dependencies—no manual virtualenv setup required.

---

## Configure Cursor MCP Servers

MCP servers allow Cursor to **execute real Docker and PostgreSQL operations**.

### MCP Configuration

Add the following to Cursor’s **Tools & MCPs** configuration:

```json
{
  "mcpServers": {
    "docker": {
      "command": "uv",
      "args": ["run", "--with", "docker-mcp", "docker-mcp", "--access-mode=unrestricted"]
    },
    "postgres": {
      "command": "uv",
      "args": ["run", "--with", "postgres-mcp", "postgres-mcp", "--access-mode=unrestricted"],
      "env": {
        "DATABASE_URI": "postgresql://app:app@localhost:5432/demo"
      }
    },
    "dbt": {
      "command": "uv",
      "args": ["run", "--with", "dbt-postgres", "dbt-mcp"],
      "env": {
        "DBT_PROJECT_DIR": "./MCP_dbt"
      }
    }
  }
}
```

### Activate MCPs
1. Restart Cursor  
2. Open **Tools & MCPs**  
3. Ensure **docker**, **postgres**, and **dbt** show green indicators   

---

## Data Engineering via Prompts

From this point forward, **all operations happen in Cursor chat**.

---

### 1️Provision PostgreSQL (Infrastructure Prompt)

```text
Using the Docker MCP, create a PostgreSQL 16 container named pg-local
on port 5432 with database demo and user/password app
```

This prompt:
- Pulls the PostgreSQL image  
- Creates and runs a container  
- Initializes the database  

---

### Configure Users & Schemas

```text
Using the Postgres MCP, create an application user named app,
grant it permissions on the demo database,
and create a schema called app owned by this user
```

**Why this matters**
- Matches real production security practices  
- Separates admin and application access  
- Limits blast radius in case of compromise  

---

### Load Large-Scale Demo Data

The dataset simulates a production e-commerce system:
- 2,000 customers  
- 1,000 products  
- 10,000 orders  
- 30,000+ order items  

Load the data:

```bash
cat demo_data.sql | docker exec -i pg-local psql -U app -d demo
```

This mimics **bulk ingestion**, a core data engineering task.

---

### Validate Infrastructure State

```text
Using the Docker MCP, list all running containers.
Then using the Postgres MCP, list all tables in the demo database.
```

Confirms:
- Container health  
- Database connectivity  
- Schema correctness  

---

### Schema Introspection & Visualization

```text
Using the Postgres MCP, read the database schema and
sample 3 rows from each table.
Create a Mermaid ER diagram with example values.
Render in chat only.
```

Replaces:
- pgAdmin  
- ER diagram tools  
- Manual documentation  

---

### dbt Model Development & Testing

This project includes a **dbt project** (`MCP_dbt/`) with analytical models built using prompts.

#### Run dbt Models

```text
Using the DBT MCP, run the customer_analytics model 
and summarize the results concisely in a table.
```

The `customer_analytics` model creates a **customer 360 view** with:
- Lifetime value calculations
- Order frequency metrics
- Customer segmentation (inactive, occasional, regular, VIP)
- Favorite product categories
- Customer lifecycle metrics

#### Run Data Quality Tests

```text
Using the DBT MCP, run all tests for the customer_analytics model
and show me the results
```

Tests validate:
- Not null constraints
- Unique constraints
- Accepted values for segments
- Data quality rules

#### Fix Failing Tests with AI Assistance

```text
Fix the failing test(s) in my customer_analytics model. Update the model
file to resolve the issue and explain what you changed.
```

Cursor will:
- Identify the root cause
- Update the SQL model
- Re-run tests to confirm fixes
- Explain the changes made

#### Document Models with AI

```text
Update my existing models/schema.yml file to add detailed documentation.
Keep the tests I already added, but enhance the descriptions for the
customer_analytics model and its columns.
```

Creates production-ready documentation that:
- Explains business logic
- Documents data lineage
- Clarifies column purposes
- Helps other team members understand the model

---

## Example Analytics Prompts

```text
Show the top 10 customers by lifetime value
```

```text
Find products with high order volume but low inventory
```

```text
Which countries generate the highest revenue per customer?
```

```text
Explain which indexes would improve order lookup performance
```

Cursor will:
- Generate SQL  
- Execute queries  
- Explain results  
- Suggest optimizations  

---

## What This Project Demonstrates

- Prompt-driven infrastructure provisioning  
- AI-assisted schema and role management  
- Conversational SQL analytics  
- Production-style database security  
- dbt model development via prompts
- AI-assisted data quality testing
- Automated test fixing with explanations
- Documentation generation for data models
- ChatOps for data engineering workflows  

---

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

### 7. Run dbt Model

```
Using the DBT MCP, run the customer_analytics model 
and summarize the results concisely in a table.
```

### 8. Test dbt Model

```
Using the DBT MCP, run all tests for the customer_analytics model
and show me the results
```

### 9. Fix Failing Tests

```
Fix the failing test(s) in my customer_analytics model. Update the model
file to resolve the issue and explain what you changed.
```

### 10. Document dbt Models

```
Update my existing models/schema.yml file to add detailed documentation.
Keep the tests I already added, but enhance the descriptions for the
customer_analytics model and its columns (customer_id, email,
lifetime_value, customer_segment, total_orders).

Make the descriptions clear and helpful for other data team members.
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

- **dbt MCP**: Data transformation & modeling
  - Running dbt models
  - Executing data quality tests
  - Building analytical models
  - Generating documentation
  - Managing data transformations

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

## dbt Project Structure

The project includes a dbt project located in `MCP_dbt/`:

```
MCP_dbt/
├── models/
│   └── example/
│       ├── customer_analytics.sql    # Customer 360 analytics model
│       ├── my_first_dbt_model.sql    # Example model
│       ├── my_second_dbt_model.sql   # Example model
│       └── schema.yml                 # Model documentation & tests
├── dbt_project.yml                    # dbt project configuration
└── profiles.yml                        # Database connection settings
```

### Key dbt Models

#### customer_analytics

A comprehensive customer analytics model that provides:
- **Lifetime Value**: Total revenue per customer
- **Order Metrics**: Total orders, average order value, order frequency
- **Segmentation**: Automatic customer segmentation (inactive, occasional, regular, VIP)
- **Product Preferences**: Favorite product category per customer
- **Lifecycle Metrics**: Days since registration, customer lifespan

**Tests Applied:**
- `not_null` on critical columns
- `unique` on customer_id
- `accepted_values` on customer_segment

**Recent Improvements:**
- Fixed NULL handling for customers with no orders (lifetime_value defaults to 0)
- Enhanced documentation with detailed column descriptions
- Production-ready schema documentation

### dbt Documentation Commands

You can build and view dbt documentation for this project from the terminal:

```bash
# From the project root
uv run dbt --project-dir MCP_dbt docs generate
uv run dbt --project-dir MCP_dbt docs serve --port 8080
```

Then open `http://localhost:8080` in your browser to explore:
- Model documentation (including `customer_analytics`)
- Column-level descriptions
- Tests and lineage

## Development

### Python Version

This project uses Python 3.14 (specified in `.python-version`).

### Dependencies

See `pyproject.toml` for project dependencies. The project uses:
- `docker-mcp` for Docker operations
- `postgres-mcp` for PostgreSQL operations
- `dbt-postgres` for dbt transformations

### dbt Configuration

The dbt project connects to the `demo` database using the `app` user. Connection details are configured in `MCP_dbt/profiles.yml`.



