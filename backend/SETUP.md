# Zynta Backend Setup

## Prerequisites
- Node.js installed
- PostgreSQL database

## Setup

1. Install dependencies:
```bash
cd backend
npm install
```

2. Copy `.env.example` to `.env` and fill in your own database URL and JWT secret:
```bash
cp .env.example .env
```
```
DATABASE_URL=postgresql://USER:PASSWORD@HOST:5432/DBNAME?sslmode=require
JWT_SECRET=replace_with_a_long_random_secret
```
Never commit your real `.env` — it's already excluded via `.gitignore`.

3. Create database schema:
```bash
psql $DATABASE_URL -f src/database/schema.sql
```

4. Start server:
```bash
npm run dev
```

## Roles
- **admin**: Manage sellers, categories
- **seller**: Manage products (requires admin approval)
- **customer**: Browse, cart, checkout, orders

## API Endpoints
- POST /api/auth/register
- POST /api/auth/login
- GET /api/auth/profile
- GET /api/products
- GET /api/categories
- GET/POST/PUT/DELETE /api/cart
- GET/POST /api/orders
- GET/POST/PUT/DELETE /api/addresses
- GET /api/admin/sellers (admin)
- PUT /api/admin/sellers/:id/approve|reject (admin)
- GET/POST/PUT/DELETE /api/seller/products (seller)