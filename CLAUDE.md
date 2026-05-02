# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Week 8 final project for a JavaScript bootcamp — a Node.js CLI e-commerce system that integrates with the HexSchool LiveJS API (`https://livejs-api.hexschool.io`). The app covers products, cart, and order management for both customer and admin flows.

## Commands

```bash
npm install          # Install dependencies
npm start            # Run the CLI app (node app.js)
npm test             # Run all 75 Jest tests sequentially (--runInBand)
```

To run a single test file or group: Jest has no built-in named groups here — all tests live in `test.js`. Use `.only` or filter by description string:
```bash
npx jest --testNamePattern="Test 2"   # Run only the Utils test group
```

## Environment Setup

Requires a `.env` file (not committed) with:
```
API_PATH=<your-hexschool-api-path>
API_KEY=<your-admin-token>
```

Both values come from the HexSchool LiveJS platform account. Tests make real HTTP calls to the API — no mocking.

## Architecture

Layered, service-oriented design:

```
app.js                      ← Entry point, orchestrates menu/flow
services/
  productService.js         ← Product listing, category filtering, display
  cartService.js            ← Cart CRUD, quantity validation, display
  orderService.js           ← Order creation, validation, admin management
api.js                      ← All Axios HTTP calls (customer + admin endpoints)
utils.js                    ← Pure helper functions: validation, formatting, extraction
config.js                   ← Loads .env, exports API_PATH / BASE_URL / ADMIN_TOKEN
test.js                     ← 75 Jest tests across 5 groups (real API)
```

**Data flow:** `app.js` → service functions → `api.js` (HTTP) → HexSchool API. `utils.js` functions are called by services to validate and format data.

**Async pattern:** All API calls and service functions use `async/await` with `try/catch`.

**Utility functions in `utils.js`:** `validateOrderUser`, `validateCartQuantity`, `formatCurrency`, `formatDate`, `getDaysAgo`, `getAllCategories`, `getDiscountRate` — these are pure functions tested independently.

## Key Constraints

- Node.js >= 18 required.
- Tests require a valid `.env` with real API credentials; they will fail without a live API connection.
- Jest timeout is set to 30 seconds (in `test.js`) to accommodate API latency.
- No linting or TypeScript configured — plain ES2022+ Node.js.
