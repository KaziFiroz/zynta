# 🛍️ Zynta — Multi-Role E-Commerce Platform

A full-stack e-commerce marketplace with three distinct roles — **Admin**, **Seller**, and **Customer** — built with a **React (Vite)** frontend and a **Node.js/Express + PostgreSQL** backend.

> Built as a collaborative group project to practice a real-world multi-role marketplace: seller onboarding/approval, product catalog, cart, checkout, and order management, all behind JWT-secured REST APIs.

---

## ✨ Features

- **Three roles, three experiences**
  - **Customer** — browse products, manage cart, checkout, view order history, leave reviews, manage addresses
  - **Seller** — manage own product catalog, view and update orders for their products (requires admin approval before selling)
  - **Admin** — approve/reject sellers, manage categories
- **JWT-based authentication** with role-aware middleware on every protected route
- **Seller approval workflow** — sellers must be approved by an admin before they can list products
- **PostgreSQL** relational schema (users, seller profiles, categories, products, cart, orders, reviews, addresses)
- **Vite + React** SPA with context-based auth state and an Axios API layer

---

## 🛠️ Tech Stack

| Layer     | Technology |
|-----------|------------|
| Backend   | Node.js, Express, PostgreSQL (`pg`), JWT, bcryptjs, express-validator |
| Frontend  | React 18, Vite, Axios, React Context (auth state) |
| Database  | PostgreSQL (works with any provider — Supabase, Neon, Railway, or local) |
| Deployment| Frontend configured for Vercel (`vercel.json`) |

---

## 🏗️ Architecture

```
Browser (React + Vite SPA)
        │  Axios, JWT sent as `x-auth-token` header
        ▼
Express REST API
   ├── /api/auth        register, login, profile
   ├── /api/products     public catalog + reviews
   ├── /api/categories   public list, admin create
   ├── /api/cart          authenticated cart CRUD
   ├── /api/orders        create/list/view orders
   ├── /api/addresses     authenticated address CRUD
   ├── /api/seller        approved-seller-only product & order management
   ├── /api/admin         admin-only seller approval, category management
   └── auth/adminAuth middleware validate JWT + role on every protected route
        │
        ▼
   PostgreSQL (users, seller_profiles, categories, products, cart, orders, reviews, addresses)
```

---

## 📁 Project Structure

```
zynta/
├── backend/
│   ├── src/
│   │   ├── config/db.js          # PostgreSQL pool
│   │   ├── controllers/          # Business logic per resource
│   │   ├── middleware/           # auth.js, adminAuth.js
│   │   ├── routes/               # Express routers
│   │   ├── database/schema.sql   # Full Postgres schema
│   │   └── seed.js               # Seeds ~200 demo products
│   ├── .env.example
│   └── SETUP.md
│
├── frontend/
│   ├── src/
│   │   ├── components/           # Navbar, Toast
│   │   ├── context/AuthContext.jsx
│   │   ├── pages/                 # Home, Products, Cart, Orders, Login, Register,
│   │   │                          # Profile, AdminDashboard, SellerDashboard, ProductDetail
│   │   └── services/api.js        # Axios instance + typed API calls
│   ├── .env.example
│   └── vercel.json
```

---

## 🔐 Security & Configuration (read this before running)

Both the database connection string and the JWT signing secret are read from **environment variables** — nothing sensitive lives in source control.

| Variable      | Used for                              | Where |
|---------------|-----------------------------------------|-------|
| `DATABASE_URL`| PostgreSQL connection string            | backend |
| `DB_SSL`      | Enable SSL for providers that require it | backend |
| `JWT_SECRET`  | Signing key for JWTs                     | backend |
| `SEED_KEY`    | Guards the one-time `/api/seed` endpoint | backend |
| `PORT`        | Backend port (default 5000)              | backend |
| `VITE_API_URL`| Frontend's API base URL                  | frontend |

Templates are at `backend/.env.example` and `frontend/.env.example` — copy them to `.env`, fill in your own values, and never commit the copies (`.env` is already excluded via `.gitignore`).

**Note on the seed endpoint:** `/api/seed` is protected by a shared `SEED_KEY` you set yourself — set a real value before deploying anywhere public, or remove the route entirely once seeding is done.

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ and npm
- A PostgreSQL database (a free [Supabase](https://supabase.com) or [Neon](https://neon.tech) project works well)

### 1. Clone and configure
```bash
git clone https://github.com/<your-username>/zynta.git
cd zynta

cp backend/.env.example backend/.env       # fill in your own DATABASE_URL and JWT_SECRET
cp frontend/.env.example frontend/.env     # default works for local dev as-is
```

### 2. Set up the database
```bash
cd backend
npm install
psql "$DATABASE_URL" -f src/database/schema.sql
```

### 3. Run the backend
```bash
npm run dev
```
Runs on **http://localhost:5000**.

### 4. (Optional) Seed demo products
```bash
curl -X POST http://localhost:5000/api/seed -H "Content-Type: application/json" -d '{"key":"<your SEED_KEY>"}'
```

### 5. Run the frontend
```bash
cd ../frontend
npm install
npm run dev
```
Runs on **http://localhost:5173** and talks to the backend automatically via `VITE_API_URL`.

More detail is in [`backend/SETUP.md`](./backend/SETUP.md).

---

## 📡 API Overview

| Method | Endpoint                         | Access                | Description |
|--------|-----------------------------------|------------------------|--------------|
| POST   | `/api/auth/register`              | Public                 | Create an account |
| POST   | `/api/auth/login`                 | Public                 | Log in, returns a JWT |
| GET    | `/api/auth/profile`               | Authenticated          | Current user profile |
| GET    | `/api/products` / `/:id`          | Public                 | Browse catalog |
| GET/POST | `/api/products/:id/reviews`     | Public / Authenticated | Read / add reviews |
| GET    | `/api/categories`                 | Public                 | List categories |
| POST   | `/api/categories`                 | Admin                  | Create category |
| GET/POST/PUT/DELETE | `/api/cart`, `/api/cart/:itemId` | Authenticated | Cart management |
| POST/GET | `/api/orders`, `/api/orders/:id`| Authenticated          | Create / view orders |
| GET/POST/PUT/DELETE | `/api/addresses`, `/:id` | Authenticated | Address book |
| GET/POST/PUT/DELETE | `/api/seller/products`, `/:id` | Approved seller | Manage own products |
| GET/PATCH | `/api/seller/orders`, `/:itemId` | Approved seller | View/update own order items |
| GET/PUT | `/api/admin/sellers`, `/:id/approve\|reject` | Admin | Seller approval workflow |

---

## 🧪 Notes on This Version

This is a cleaned-up snapshot prepared for sharing publicly:
- Real database credentials were never committed and have been removed from the working copy in favor of `.env.example` templates.
- Git history was reset to a single initial commit for this public copy.

## 🗺️ Possible Next Steps

- Add automated tests for controllers and middleware
- Add pagination/search/filtering to the product catalog
- Wire up a real payment provider for checkout (`/api/orders/verify` currently expects a manual verification step)
- Containerize with Docker Compose for one-command local setup

---

## 📝 License

Built for educational purposes as a group coursework/portfolio project.

## 🙋 Author

**Your Name** — built collaboratively as a group project. Feel free to reach out via [LinkedIn](#) or [email](#).
