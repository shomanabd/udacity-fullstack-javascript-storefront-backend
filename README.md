# Storefront Backend

This is a RESTful API for an online storefront built with Node.js, Express, PostgreSQL, and TypeScript.

## Technologies

- **Node.js** - JavaScript runtime
- **Express** - Web framework
- **TypeScript** - Programming language
- **PostgreSQL** - Database
- **db-migrate** - Database migrations
- **dotenv** - Environment variables
- **jsonwebtoken** - Authentication
- **bcrypt** - Password hashing
- **Jasmine** - Testing framework

## Getting Started

### Prerequisites

- Node.js (v14 or higher)
- PostgreSQL (v12 or higher)
- npm or yarn

## Database Setup

### 1. Install PostgreSQL

Download and install PostgreSQL for your system from [postgresql.org](https://www.postgresql.org/download/)

### 2. Create Databases

```bash
# Connect to PostgreSQL
psql -U postgres

# Create development database
CREATE DATABASE storefront;

# Create test database
CREATE DATABASE storefront_test;

# Exit
\q
```

## Environment Variables

Create a `.env` file in the root directory:

```env
POSTGRES_HOST=127.0.0.1
POSTGRES_PORT=5432
POSTGRES_DB=storefront
POSTGRES_TEST_DB=storefront_test
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password

ENV=dev
PORT=3000

BCRYPT_PASSWORD=your-secret-password
SALT_ROUNDS=10
TOKEN_SECRET=your-jwt-secret
```

**Important:**
- Replace `your_password` with your PostgreSQL password
- Replace `your-secret-password` and `your-jwt-secret` with secure random strings
- Add `.env` to `.gitignore` (never commit this file)

## Installation

### 1. Clone Repository

```bash
git clone https://github.com/shomanabd/udacity-fullstack-javascript-storefront.git
cd udacity-fullstack-javascript-storefront
```

### 2. Install Dependencies

```bash
npm install
```

## Running the Application

### 1. Run Migrations

Create all database tables:

```bash
db-migrate up
```

For test database:

```bash
db-migrate up -e test
```

### 2. Build Application

```bash
npm run build
```

### 3. Start Server

Development mode:
```bash
npm run watch
```

Production mode:
```bash
npm start
```

Server will run on `http://localhost:3000`

## Database Migrations

### Commands

```bash
# Run all migrations
db-migrate up

# Run migrations for test environment
db-migrate up -e test

# Rollback last migration
db-migrate down

# Reset database (rollback all)
db-migrate reset
```

### Migration Files

Migrations are in the `migrations/` folder. Each file has:
- `up` function - creates tables
- `down` function - drops tables

## Testing

### Setup Test Database

Before running tests:

```bash
# Create test database (if not done already)
psql -U postgres -c "CREATE DATABASE storefront_test;"

# Run migrations on test database
db-migrate up -e test
```

### Run Tests

```bash
npm run test
```

This runs all Jasmine tests for:
- Database models
- API endpoints
- Authentication

## Ports

- **Backend Server**: `3000` (from `.env` PORT variable)
- **PostgreSQL Database**: `5432` (default PostgreSQL port)

## Scripts

```bash
npm run build      # Compile TypeScript
npm run start      # Start production server
npm run watch      # Start dev server with auto-reload
npm run test       # Run all tests
npm run prettier   # Format code (if configured)
npm run lint       # Lint code (if configured)
```

## Project Structure

```
├── migrations/         # Database migrations
├── spec/              # Jasmine test configuration
│   └── support/
├── src/
│   ├── handlers/      # Route handlers
│   ├── models/        # Database models
│   ├── middleware/    # Express middleware
│   ├── database.ts    # Database connection
│   └── server.ts      # Express app setup
├── .env               # Environment variables (not in repo)
├── .gitignore
├── database.json      # db-migrate configuration
├── package.json
├── tsconfig.json
├── README.md
└── REQUIREMENTS.md    # API documentation
```

## API Documentation

See [REQUIREMENTS.md](REQUIREMENTS.md) for:
- API endpoints
- Request/response formats
- Database schema
- Authentication requirements

## Troubleshooting

### Cannot connect to database

```bash
# Check PostgreSQL is running
sudo service postgresql status

# Start PostgreSQL
sudo service postgresql start

# Verify databases exist
psql -U postgres -l
```

### Migration errors

```bash
# Reset and retry
db-migrate reset
db-migrate up
```

### Test failures

```bash
# Ensure test database has migrations
db-migrate up -e test

# Reset test database
db-migrate reset -e test
db-migrate up -e test

# Run tests again
npm run test
```

### Port in use

```bash
# Kill process on port 3000
lsof -ti:3000 | xargs kill -9
```

## Security

- `.env` file is in `.gitignore`
- Passwords are hashed with bcrypt
- JWT tokens protect routes
- Never commit sensitive data



## Acknowledgments

Udacity Full Stack JavaScript Developer Nanodegree
