## AlienSoft Product Management App (Vue 3 + Pinia + Tailwind)

Mini product management dashboard built for the AlienSoft Frontend Technical Test.  
The app uses the public DummyJSON API for authentication and product data.

### Tech stack

- **Vue 3** (Composition API, `<script setup>`)
- **Pinia** for state management (auth + products)
- **Vue Router** for navigation and route guards
- **Tailwind CSS** for styling (primary color `#000080`)
- **Axios** for HTTP calls

### Project structure (key folders)

- `src/api` – Axios client and API helpers for auth/products
- `src/stores` – Pinia stores (`auth`, `products`)
- `src/router` – Router with protected routes and guest-only login
- `src/views` – Main pages (`LoginView`, `ProductsView`, `ProductDetailView`, `AddProductView`)
- `src/components` – Shared UI (`AppHeader`, `ProductTable`, `LoadingSpinner`, `ErrorMessage`)

### Setup & development

```bash
npm install
npm run dev
```

The app will start on the default Vite port (e.g. `http://localhost:5173`).

### Authentication

- Uses `POST https://dummyjson.com/auth/login`
- On success, the app stores:
  - `accessToken` (as `auth.token` in `localStorage`)
  - `refreshToken` (optional)
  - basic user profile (name, username, email, avatar)
- Session is restored on refresh via the auth Pinia store.
- Example test user from DummyJSON:
  - Username: `emilys`
  - Password: `emilyspass`

### Features

- **Login page (`/login`)**

  - Centered form with username/password
  - Shows clear validation and API error messages
  - On success: saves auth state and redirects to `/products`
  - Route guard prevents logged-in users from revisiting `/login`

- **Products list (`/products`)**

  - Loads products via Pinia store (`GET /products?limit=0`)
  - Search by title, optional filters:
    - Category dropdown (from `GET /products/categories`)
    - Min/Max price
  - Table shows:
    - Circular thumbnail
    - Title + truncated description
    - Category, price, stock
  - Row click navigates to product details
  - “Add new product” button → `/products/new`

- **View product (`/products/:id`)**

  - Fetches a single product via store (`GET /products/{id}`) or uses cached list item
  - Shows large image, full description, category, price, stock, rating, discount
  - Back to products, Edit (inline quick edit), and Delete actions
  - Delete:
    - Confirms with user
    - Calls `DELETE /products/{id}`
    - Updates store and redirects to `/products`

- **Add product (`/products/new`)**
  - Form fields:
    - Title (required)
    - Description
    - Category (dropdown)
    - Price
    - Stock
    - Thumbnail URL
  - Basic validation and error messaging
  - On submit:
    - Calls `POST /products/add`
    - Adds created product to store
    - Redirects to product details (or list as fallback)

### Styling & UX

- Tailwind CSS with primary color `#000080` for:
  - Buttons, links, headers, and key accents
- Clean table with hover states, rounded corners, and subtle shadows
- Responsive layout for laptop and tablet widths
- Loading and error states:
  - Global `LoadingSpinner` for async operations
  - `ErrorMessage` component for API and form errors

### GitHub Pages deployment

1. Install deployment dependency:

```bash
npm install --save-dev gh-pages
```

2. Ensure `vite.config.js` has the correct `base` for your repo:

```js
// vite.config.js
export default defineConfig({
  plugins: [vue()],
  base: "/technical-frontend/", // change to '/<your-repo-name>/' if needed
});
```

3. Add deploy scripts to `package.json`:

```json
{
  "scripts": {
    "build": "vite build",
    "preview": "vite preview",
    "deploy": "gh-pages -d dist"
  }
}
```

4. Build and deploy:

```bash
npm run build
npm run deploy
```

GitHub Pages will serve the app from `https://<your-username>.github.io/<your-repo-name>/`.
