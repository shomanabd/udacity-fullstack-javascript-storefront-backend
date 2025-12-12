# Storefront Backend

This is a RESTful API for an online storefront built with Node.js, Express, PostgreSQL, and TypeScript. The API provides endpoints for managing users, products, and orders with JWT-based authentication.

## Table of Contents

- [Technologies](#technologies)
- [Getting Started](#getting-started)
- [Database Setup](#database-setup)
  - [Option 1: Using Docker (Recommended)](#option-1-using-docker-recommended)
  - [Option 2: Local PostgreSQL Installation](#option-2-local-postgresql-installation)
- [Environment Variables](#environment-variables)
- [Installation](#installation)
- [Running the Application](#running-the-application)
- [Database Migrations](#database-migrations)
- [Testing](#testing)
- [API Endpoints](#api-endpoints)
- [Ports](#ports)

## Technologies

This application uses the following libraries and tools:

- **Node.js** - JavaScript runtime environment
- **Express** - Web application framework
- **TypeScript** - Typed superset of JavaScript
- **PostgreSQL** - Relational database
- **Docker & Docker Compose** - Containerization (optional)
- **db-migrate** - Database migration management
- **dotenv** - Environment variable management
- **jsonwebtoken (JWT)** - Token-based authentication
- **bcrypt** - Password hashing
- **Jasmine** - Testing framework
- **CORS** - Cross-Origin Resource Sharing middleware

## Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:

- Node.js (v14 or higher)
- npm or yarn
- **Either:**
  - Docker and Docker Compose (recommended, easier setup)
  - OR PostgreSQL (v12 or higher) installed locally

## Database Setup

### Option 1: Using Docker (Recommended) 🐳

This is the easiest option - you don't need to install PostgreSQL on your machine!

#### 1. Install Docker

If Docker is not installed:
- **Windows/Mac**: Download [Docker Desktop](https://www.docker.com/products/docker-desktop)
- **Linux**: 
  ```bash
  sudo apt-get update
  sudo apt-get install docker.io docker-compose
  ```

#### 2. Create docker-compose.yml

If you don't have a `docker-compose.yml` file, create one in the project root:

```yaml
version: '3.8'

services:
  postgres:
    container_name: storefront-db
    image: postgres:14
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres123
      POSTGRES_DB: storefront
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./init-db.sql:/docker-entrypoint-initdb.d/init-db.sql

volumes:
  postgres-data:
```

#### 3. Create init-db.sql

Create an `init-db.sql` file in the project root to initialize both databases:

```sql
-- Create test database
CREATE DATABASE storefront_test;
```

#### 4. Start PostgreSQL Container

Run the database using Docker Compose:

```bash
# Start database in the background
docker-compose up -d

# Check that container is running
docker-compose ps
```

This will:
- ✅ Run PostgreSQL in a container
- ✅ Automatically create `storefront` database
- ✅ Automatically create `storefront_test` database
- ✅ Expose PostgreSQL on port 5432
- ✅ Persist data in a volume (data survives container restarts)

#### 5. Verify Docker Database

Check that the database is running:

```bash
# Access PostgreSQL inside the container
docker exec -it storefront-db psql -U postgres

# List all databases
\l

# You should see both 'storefront' and 'storefront_test'

# Exit psql
\q
```

#### 6. Managing Docker Database

Useful Docker commands:

```bash
# Stop the database
docker-compose down

# Start the database again
docker-compose up -d

# View logs
docker-compose logs -f

# Restart the database
docker-compose restart

# Delete database and all data (BE CAREFUL!)
docker-compose down -v

# Access database directly
docker exec -it storefront-db psql -U postgres -d storefront
```

### Option 2: Local PostgreSQL Installation

If you prefer to install PostgreSQL locally:

#### 1. Install PostgreSQL

Download and install PostgreSQL for your operating system.

#### 2. Start PostgreSQL Service

```bash
# Linux
sudo service postgresql start

# macOS
brew services start postgresql

# Windows - PostgreSQL service should start automatically
```

#### 3. Create Databases

Connect to PostgreSQL and create two databases:

```bash
# Connect to PostgreSQL
psql -U postgres

# Inside psql, run:
CREATE DATABASE storefront;
CREATE DATABASE storefront_test;

# Exit
\q
```

Or use these commands directly:

```bash
psql -U postgres -c "CREATE DATABASE storefront;"
psql -U postgres -c "CREATE DATABASE storefront_test;"
```

## Environment Variables

Create a `.env` file in the root directory of the project with the following variables:

### For Docker Setup:

```env
# PostgreSQL Database Configuration (Docker)
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=storefront
POSTGRES_TEST_DB=storefront_test
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123

# Application Environment
ENV=dev

# Application Port
PORT=3000

# Bcrypt Configuration
BCRYPT_PASSWORD=your-secret-bcrypt-password
SALT_ROUNDS=10

# JWT Configuration
TOKEN_SECRET=your-secret-token-key
```

### For Local PostgreSQL Setup:

```env
# PostgreSQL Database Configuration (Local)
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=storefront
POSTGRES_TEST_DB=storefront_test
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_postgres_password

# Application Environment
ENV=dev

# Application Port
PORT=3000

# Bcrypt Configuration
BCRYPT_PASSWORD=your-secret-bcrypt-password
SALT_ROUNDS=10

# JWT Configuration
TOKEN_SECRET=your-secret-token-key
```

**Important Notes:**
- Replace `your_postgres_password` with your actual PostgreSQL password (if using local installation)
- Replace `your-secret-bcrypt-password` with a secure random string
- Replace `your-secret-token-key` with a secure random string for JWT signing
- The `.env` file should be listed in `.gitignore` and never committed to version control
- When `ENV=test`, the application will use `POSTGRES_TEST_DB` instead of `POSTGRES_DB`

### Example .env.example File

Create this file in your repo (safe to commit):

```env
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=storefront
POSTGRES_TEST_DB=storefront_test
POSTGRES_USER=postgres
POSTGRES_PASSWORD=change_this_password

ENV=dev
PORT=3000

BCRYPT_PASSWORD=change_this_secret
SALT_ROUNDS=10

TOKEN_SECRET=change_this_token_secret
```

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/shomanabd/udacity-fullstack-javascript-storefront.git
cd udacity-fullstack-javascript-storefront
```

### 2. Install Dependencies

Using npm:

```bash
npm install
```

Or using yarn:

```bash
yarn install
```

## Running the Application

### 1. Ensure Database is Running

**If using Docker:**
```bash
docker-compose up -d
```

**If using local PostgreSQL:**
```bash
# Check PostgreSQL is running
sudo service postgresql status  # Linux
brew services list              # macOS
```

### 2. Run Database Migrations

Before starting the application for the first time, run the database migrations to create all necessary tables:

```bash
# Using npm script
npm run migrate:up

# Or using db-migrate directly
db-migrate up
```

This will create all the required tables (users, products, orders, etc.) in your database.

### 3. Build the Application

Compile TypeScript to JavaScript:

```bash
npm run build
```

### 4. Start the Server

#### Development Mode (with automatic restart on file changes):

```bash
npm run watch
```

#### Production Mode:

```bash
npm run start
```

The server will start and listen on the port specified in your `.env` file (default: 3000).

You should see a message like:
```
Server is starting on port 3000
```

## Database Migrations

This project uses `db-migrate` for database schema management.

### Database Configuration

The `database.json` file configures db-migrate for different environments:

```json
{
  "dev": {
    "driver": "pg",
    "host": {"ENV": "POSTGRES_HOST"},
    "port": {"ENV": "POSTGRES_PORT"},
    "database": {"ENV": "POSTGRES_DB"},
    "user": {"ENV": "POSTGRES_USER"},
    "password": {"ENV": "POSTGRES_PASSWORD"}
  },
  "test": {
    "driver": "pg",
    "host": {"ENV": "POSTGRES_HOST"},
    "port": {"ENV": "POSTGRES_PORT"},
    "database": {"ENV": "POSTGRES_TEST_DB"},
    "user": {"ENV": "POSTGRES_USER"},
    "password": {"ENV": "POSTGRES_PASSWORD"}
  }
}
```

### Available Migration Commands

```bash
# Run all pending migrations (creates tables)
db-migrate up

# Run migrations for test environment
db-migrate up -e test

# Rollback the last migration
db-migrate down

# Reset database (rollback all migrations)
db-migrate reset

# Create a new migration file
db-migrate create migration-name --sql-file
```

### Migration Files

Migration files are located in the `migrations/` directory. Each migration has:
- An `up` function to apply the migration (creates tables)
- A `down` function to rollback the migration (drops tables)

Example migration structure:

```
migrations/
├── 20230101000001-users-table.js
├── 20230101000002-products-table.js
├── 20230101000003-orders-table.js
└── 20230101000004-order-products-table.js
```

### Important Migration Notes

- Migrations must be run in order (foreign key dependencies)
- Always test migrations with both `up` and `down`
- The test database needs migrations too before running tests

## Testing

This project uses Jasmine for testing both the API endpoints and database models.

### Setup for Testing

1. **Ensure test database exists:**
   ```bash
   # If using Docker, it's already created
   
   # If using local PostgreSQL:
   psql -U postgres -c "CREATE DATABASE storefront_test;"
   ```

2. **Run migrations on test database:**
   ```bash
   db-migrate up -e test
   ```

3. **Set ENV to test in your .env:**
   ```env
   ENV=test
   ```
   Or the test script should do this automatically.

### Run All Tests

```bash
# Run all tests
npm run test

# Run tests with verbose output
npm run test -- --verbose
```

### Test Execution Flow

When you run tests:

1. ENV is set to `test`
2. Application connects to `storefront_test` database
3. Tests run against test database
4. Test data is created and cleaned up

### Test Structure

```
src/
├── handlers/
│   └── tests/
│       ├── users.spec.ts       # User endpoint tests
│       ├── products.spec.ts    # Product endpoint tests
│       └── orders.spec.ts      # Order endpoint tests
└── models/
    └── tests/
        ├── user.spec.ts        # User model tests
        ├── product.spec.ts     # Product model tests
        └── order.spec.ts       # Order model tests
```

### What Tests Should Cover

**Model Tests:**
- Create (INSERT)
- Read/Index (SELECT all)
- Show (SELECT by ID)
- Update (UPDATE)
- Delete (DELETE)

**Endpoint Tests:**
- Successful requests (200, 201)
- Authentication with JWT tokens
- Unauthorized access (401)
- Invalid requests (400, 404)
- Response data structure

### Testing Tips

```bash
# Reset test database if tests are failing
db-migrate reset -e test
db-migrate up -e test
npm run test

# Run specific test file
npm run test -- spec/models/user.spec.ts

# If using Docker, ensure container is running
docker-compose ps
```

### Common Test Issues

1. **Database connection errors**: Make sure Docker container or PostgreSQL is running
2. **Migration errors**: Run `db-migrate up -e test` before testing
3. **Port conflicts**: Ensure port 5432 is not used by another service
4. **Environment variables**: Verify `.env` has correct test database settings

## API Endpoints

For detailed API endpoint documentation, including routes, HTTP methods, request parameters, and response formats, please refer to [REQUIREMENTS.md](REQUIREMENTS.md).

### Quick Overview

The API provides the following main endpoint groups:

- **Users** - User registration, authentication, and management
- **Products** - Product CRUD operations
- **Orders** - Order creation and management

Authentication is required for most endpoints using JWT tokens passed in the Authorization header:

```
Authorization: Bearer <your-jwt-token>
```

### Testing Endpoints with curl

```bash
# Create a user
curl -X POST http://localhost:3000/users \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","password":"password123"}'

# Login
curl -X POST http://localhost:3000/users/authenticate \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","password":"password123"}'

# Get products (with token)
curl http://localhost:3000/products \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

## Ports

- **Backend Server**: Port `3000` (configurable via `PORT` in `.env`)
- **PostgreSQL Database**: Port `5432` (default PostgreSQL port)
  - When using Docker, this is mapped from container to host
  - Configurable via `POSTGRES_PORT` in `.env`

## Project Structure

```
├── src/
│   ├── handlers/          # Express route handlers
│   │   └── tests/         # Endpoint tests
│   ├── models/            # Database models
│   │   └── tests/         # Model tests
│   ├── middleware/        # Custom middleware (auth, etc.)
│   ├── database.ts        # Database connection
│   └── server.ts          # Express server setup
├── migrations/            # Database migration files
├── spec/                  # Jasmine configuration
│   └── support/
│       └── jasmine.json
├── .env                   # Environment variables (not in repo)
├── .env.example           # Example environment variables
├── .gitignore             # Git ignore file
├── database.json          # Database configuration for db-migrate
├── docker-compose.yml     # Docker Compose configuration
├── init-db.sql           # Database initialization script
├── package.json           # Project dependencies and scripts
├── tsconfig.json          # TypeScript configuration
├── README.md              # This file
└── REQUIREMENTS.md        # Project requirements and API documentation
```

## Scripts

Here are the main npm scripts available:

```bash
npm run build          # Compile TypeScript to JavaScript
npm run start          # Start the production server
npm run watch          # Start development server with auto-reload
npm run test           # Run all tests
npm run migrate:up     # Run database migrations (dev)
npm run migrate:down   # Rollback last migration (dev)
npm run migrate:test   # Run migrations on test database
npm run lint           # Lint the code (if configured)
npm run format         # Format the code (if configured)
```

## Complete Setup Example

Here's the complete flow from clone to running tests:

```bash
# 1. Clone repository
git clone https://github.com/shomanabd/udacity-fullstack-javascript-storefront.git
cd udacity-fullstack-javascript-storefront

# 2. Install dependencies
npm install

# 3. Create .env file (copy from .env.example and modify)
cp .env.example .env
# Edit .env with your values

# 4. Start Docker database (if using Docker)
docker-compose up -d

# 5. Run migrations
db-migrate up
db-migrate up -e test

# 6. Build the application
npm run build

# 7. Run tests
npm run test

# 8. Start the server
npm run start
```

## Troubleshooting

### Database Connection Issues

**Issue**: Cannot connect to database

**Solutions**:
1. If using Docker:
   ```bash
   docker-compose ps  # Check container is running
   docker-compose logs  # Check for errors
   docker-compose restart  # Restart if needed
   ```

2. If using local PostgreSQL:
   ```bash
   sudo service postgresql status  # Check if running
   sudo service postgresql start   # Start if stopped
   ```

3. Check `.env` credentials match your database
4. Verify port 5432 is not blocked by firewall

### Migration Errors

**Issue**: Migrations fail to run

**Solutions**:
```bash
# Reset migrations completely
db-migrate reset

# Check database exists
docker exec -it storefront-db psql -U postgres -l

# Run migrations again
db-migrate up
```

### Test Failures

**Issue**: Tests are failing

**Solutions**:
```bash
# 1. Ensure test database has migrations
db-migrate up -e test

# 2. Check ENV is set to test
echo $ENV  # Should be 'test'

# 3. Reset test database
db-migrate reset -e test
db-migrate up -e test

# 4. Run tests again
npm run test
```

### Docker Issues

**Issue**: Docker container won't start

**Solutions**:
```bash
# Check if port 5432 is already in use
lsof -i :5432  # macOS/Linux
netstat -ano | findstr :5432  # Windows

# Stop any existing PostgreSQL service
sudo service postgresql stop

# Remove old containers
docker-compose down -v
docker-compose up -d
```

### Port Already in Use

**Issue**: Port 3000 or 5432 already in use

**Solutions**:
```bash
# For port 3000
lsof -ti:3000 | xargs kill -9  # macOS/Linux

# For port 5432 (stop PostgreSQL or Docker)
sudo service postgresql stop  # If running locally
docker-compose down  # If running in Docker

# Or change port in .env
PORT=3001
POSTGRES_PORT=5433
```

## Security Notes

- Never commit the `.env` file to version control
- Always use strong, random values for `BCRYPT_PASSWORD` and `TOKEN_SECRET`
- User passwords are hashed using bcrypt before storage
- JWT tokens are required for protected routes
- Use HTTPS in production environments
- Regularly update dependencies for security patches



## Acknowledgments

- Udacity Full Stack JavaScript Developer Nanodegree Program
- Project starter code provided by Udacity
