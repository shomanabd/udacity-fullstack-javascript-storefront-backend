# Storefront Backend Project

## Overview

This is a Backend API project built with Node.js, Express.js, and TypeScript to support an e-commerce storefront. It provides RESTful API endpoints for managing products, users, and orders with JWT-based authentication.

## Technologies Used

### Core Technologies

- **Node.js** - JavaScript runtime environment
- **Express.js** - Web application framework
- **TypeScript** - Typed superset of JavaScript
- **PostgreSQL** - Relational database management system
- **Docker** - Containerization for database

### Libraries and Dependencies

#### Production Dependencies

- `express` - Express web framework
- `pg` - PostgreSQL client for Node.js
- `body-parser` - HTTP request body parser
- `cors` - Cross-Origin Resource Sharing middleware
- `jsonwebtoken` - JWT token generation and verification
- `bcrypt` - Password hashing library
- `dotenv` - Environment variable management

#### Development Dependencies

- `typescript` - TypeScript compiler
- `ts-node` - TypeScript execution environment
- `tsc-watch` - TypeScript compiler with watch mode
- `jasmine` - Testing framework
- `jasmine-ts` - TypeScript support for Jasmine
- `supertest` - HTTP endpoint testing library
- `db-migrate` - Database migration tool
- `db-migrate-pg` - PostgreSQL driver for db-migrate

## Project Structure

```
storefront-backend/
├── src/                          # Source code (TypeScript)
│   ├── server.ts                 # Application entry point
│   ├── database.ts               # Database connection setup
│   ├── controllers/              # Request handlers
│   │   ├── userController.ts
│   │   ├── productController.ts
│   │   ├── orderController.ts
│   │   └── tests/                # Controller tests
│   │       ├── userController.spec.ts
│   │       ├── productController.spec.ts
│   │       └── orderController.spec.ts
│   ├── modules/                  # Database models
│   │   ├── user.ts
│   │   ├── product.ts
│   │   ├── order.ts
│   │   └── tests/                # Model tests
│   │       ├── user.spec.ts
│   │       ├── product.spec.ts
│   │       └── order.spec.ts
│   ├── routes/                   # Route definitions
│   │   ├── users.ts
│   │   ├── products.ts
│   │   └── orders.ts
│   └── middleware/               # Middleware
│       └── auth.ts               # JWT authentication
├── dist/                         # Compiled JavaScript output
├── migrations/                   # Database migration files
│   ├── 20251110164230-create-tables.js
│   ├── 20251204073429-insert-mock-data.js
│   └── sqls/                     # SQL migration files
│       ├── 20251110164230-create-tables-up.sql
│       ├── 20251110164230-create-tables-down.sql
│       ├── 20251204073429-insert-mock-data-up.sql
│       └── 20251204073429-insert-mock-data-down.sql
├── spec/                         # Jasmine configuration
│   └── support/
│       └── jasmine.json
├── docker-compose.yml            # Docker configuration for PostgreSQL
├── database.json                 # db-migrate configuration
├── tsconfig.json                 # TypeScript configuration
├── package.json                  # Project dependencies
├── .gitignore                    # Git ignore file
├── .env                          # Environment variables (not in Git)
├── README.md                     # This file
└── REQUIREMENTS.md               # API requirements documentation
```

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (version 14 or higher)
- **npm** or **yarn** (package manager)
- **PostgreSQL** (version 12 or higher) or **Docker** to run the database
- **Git** (for cloning the repository)

## Setup and Installation

### 1. Clone the Repository

```bash
git clone <repository-url>
cd storefront-backend
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Environment Variables Setup

Create a `.env` file in the root directory and add the following variables:

```env
# Database Configuration
POSTGRES_HOST=127.0.0.1
POSTGRES_PORT=5433
POSTGRES_DB=shopping
POSTGRES_USER=<your-username>
POSTGRES_PASSWORD=<your-password>

# Encryption Settings
BCRYPT_PASSWORD=<your-pepper-string>
SALT_ROUNDS=10

# JWT Settings
TOKEN_SECRET=<your-jwt-secret-key>
```

**Note:** Make sure to use secure and strong values for sensitive variables like `BCRYPT_PASSWORD` and `TOKEN_SECRET`.

### 4. Database Setup

#### Option 1: Using Docker (Recommended)

```bash
# Start PostgreSQL in Docker
docker-compose up -d
```

This will start PostgreSQL on port **5433** (mapped from container port 5432).

#### Option 2: Local PostgreSQL

If you have PostgreSQL installed locally, ensure it's running on port **5433** or update the port in your `.env` file.

### 5. Run Database Migrations

```bash
# Run all migrations
db-migrate up
```

This will automatically create the following tables:

- `categories` - Product categories
- `products` - Products
- `users` - Users
- `orders` - Orders
- `order_products` - Order products (junction table)

### 6. Compile TypeScript

```bash
npm run tsc
```

This will compile TypeScript files from `src/` directory to `dist/` directory.

### 7. Run the Application

#### Production Mode

```bash
npm start
```

The server will run on **http://localhost:3000**

#### Development Mode (with auto-reload)

```bash
npm run watch
```

This will watch for changes in TypeScript files and automatically recompile and restart the server.

## Ports

- **Backend API**: Port `3000`
- **PostgreSQL Database**: Port `5433`

## Database Schema

### Tables

#### `categories` Table

- `id` - Primary key (SERIAL PRIMARY KEY)
- `name` - Category name (VARCHAR(100) UNIQUE NOT NULL)

#### `products` Table

- `id` - Primary key (SERIAL PRIMARY KEY)
- `name` - Product name (VARCHAR(100) NOT NULL)
- `price` - Product price (NUMERIC(10, 2) NOT NULL)
- `category_id` - Foreign key to categories (INTEGER REFERENCES categories(id))

#### `users` Table

- `id` - Primary key (SERIAL PRIMARY KEY)
- `first_name` - User's first name (VARCHAR(100) NOT NULL)
- `last_name` - User's last name (VARCHAR(100) NOT NULL)
- `password_digest` - Hashed password (VARCHAR(256) NOT NULL)

#### `orders` Table

- `id` - Primary key (SERIAL PRIMARY KEY)
- `user_id` - Foreign key to users (INTEGER REFERENCES users(id))
- `status` - Order status (order_status ENUM: 'active' or 'complete')

#### `order_products` Table

- `id` - Primary key (SERIAL PRIMARY KEY)
- `order_id` - Foreign key to orders (INTEGER REFERENCES orders(id))
- `product_id` - Foreign key to products (INTEGER REFERENCES products(id))
- `quantity` - Product quantity (INTEGER NOT NULL)
- Unique constraint on (`order_id`, `product_id`)

## API Endpoints

### Products

- `GET /products` - Get list of all products
- `GET /products/:id` - Get a specific product by ID
- `POST /products` - Create a new product **[token required]**
- `GET /products/top-five-popular` - Get top 5 popular products [optional]
- `GET /products/category/:category_id` - Get products by category [optional]

### Users

- `GET /users` - Get list of all users **[token required]**
- `GET /users/:id` - Get a specific user by ID **[token required]**
- `POST /users` - Create a new user (returns JWT token)

### Orders

- `GET /orders/current/:user_id` - Get current active order for user **[token required]**
- `GET /orders/completed/:user_id` - Get completed orders for user [optional] **[token required]**
- `POST /orders` - Create a new order **[token required]**
- `POST /orders/:id/products` - Add products to an order **[token required]**
- `PUT /orders/:id` - Update order status **[token required]**

For complete details about endpoints and data shapes, refer to `REQUIREMENTS.md`.

## Authentication

The project uses **JWT (JSON Web Tokens)** for authentication:

1. When creating a new user via `POST /users`, a JWT token is returned in the response.
2. To access protected routes, send the token in the header:
   ```
   Authorization: Bearer <your-token>
   ```
3. The `verifyAuthToken` middleware validates the token before allowing access.

## Available Scripts

```bash
# Install dependencies
npm install

# Compile TypeScript
npm run tsc

# Run the application (after compilation)
npm start

# Run in development mode (with auto-reload)
npm run watch

# Run tests
npm test

# Create a new migration
npm run migrate:create <migration-name>

# Run migrations
db-migrate up

# Rollback migrations
db-migrate down
```

## Testing

The project includes comprehensive tests:

### Controller Tests

- `src/controllers/tests/userController.spec.ts`
- `src/controllers/tests/productController.spec.ts`
- `src/controllers/tests/orderController.spec.ts`

### Model Tests

- `src/modules/tests/user.spec.ts`
- `src/modules/tests/product.spec.ts`
- `src/modules/tests/order.spec.ts`

To run all tests:

```bash
npm test
```

## Security Features

- **Password Hashing**: Passwords are encrypted using `bcrypt` with salt and pepper
- **JWT Tokens**: Used for authentication and authorization
- **Environment Variables**: All sensitive information is stored in `.env` file (excluded from Git)
- **CORS**: Enabled to allow requests from different origins

## Features

- ✅ Full RESTful API with CRUD operations
- ✅ JWT-based authentication
- ✅ Password encryption using bcrypt
- ✅ Relational database with Foreign Keys
- ✅ Database migrations management
- ✅ Comprehensive testing (Unit Tests & Integration Tests)
- ✅ TypeScript with full type support
- ✅ CORS enabled
- ✅ Proper error handling

## Contributing

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the ISC License - see the `LICENSE.txt` file for details.

## Support

For help or to report issues, please open an issue in the repository.

---

**Note**: Make sure to read `REQUIREMENTS.md` for complete details about API requirements and data shapes.
