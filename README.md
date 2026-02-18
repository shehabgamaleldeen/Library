# 📚 Library Management System

A full-stack web application for managing a library's book catalog and member borrowing workflows. Built with React, Node.js, Express.js, and MySQL, featuring role-based access control for Admins and Members.

---

## 🚀 Features

### 👤 Member
- Register and log in with hashed password authentication (bcrypt)
- Browse and search the full book catalog (by title, author, ISBN, category, or rack number)
- View detailed book information including cover image
- Submit borrowing requests for available books
- Track personal borrowed books dashboard

### 🛡️ Admin
- Approve or reject member borrowing requests
- Add new books with cover image upload
- Edit existing book details (automatically replaces old cover image)
- Delete books (automatically removes associated image from server)
- Manage and review all user accounts

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 18, React Router v6, Bootstrap 5, Axios |
| **Backend** | Node.js, Express.js, MVC Architecture |
| **Database** | MySQL (relational schema with JOINs) |
| **Auth** | Token-based authentication, bcrypt password hashing |
| **File Uploads** | Multer (image storage & deletion) |
| **Validation** | express-validator (all endpoints) |
| **Middleware** | Custom role-based route guards (Admin / Member) |

---

## 🏗️ Architecture

```
Library/
├── client/               # React frontend
│   └── src/
│       ├── Pages/        # Auth, Home, Book Info, Borrowed Books, Admin Manage
│       ├── Components/   # BookCard, BorrowingCard, AccountCard, etc.
│       ├── Shared/       # Reusable layout components
│       └── Router.js     # Route definitions with protected routes
│
└── server/               # Express backend
    ├── routes/           # Auth, Books, BorrowingRequest, AdminBorrowingRequest, Users
    ├── Database/         # MySQL connection + query modules per entity
    └── MiddleWare/       # Admin guard, Authorize guard, Multer upload config
```

---

## 🔌 API Endpoints

### Auth
| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/auth/signUp` | Register a new member |
| `POST` | `/auth/login` | Login and receive auth token |

### Books
| Method | Endpoint | Access | Description |
|---|---|---|---|
| `GET` | `/books` | Public | List all books (supports `?search=` query) |
| `GET` | `/books/:id` | Public | Get single book details |
| `POST` | `/books` | Admin | Add a new book with image upload |
| `PUT` | `/books/:id` | Admin | Update book details and cover image |
| `DELETE` | `/books/:id` | Admin | Delete a book and its image |

### Borrowing Requests
| Method | Endpoint | Access | Description |
|---|---|---|---|
| `POST` | `/borrowing_request` | Member | Submit a borrow request |
| `GET` | `/borrowing_request/:id` | Member | Get user's approved borrowed books |
| `GET` | `/admin_borrowing_request` | Admin | View all pending requests |
| `PUT` | `/admin_borrowing_request/accept` | Admin | Approve a borrow request |
| `DELETE` | `/admin_borrowing_request/reject` | Admin | Reject a borrow request |

### User Accounts
| Method | Endpoint | Access | Description |
|---|---|---|---|
| `GET` | `/users_accounts` | Admin | List all registered members |

---

## ⚙️ Getting Started

### Prerequisites
- Node.js v16+
- MySQL Server

### 1. Clone the repository
```bash
git clone https://github.com/shehabgamaleldeen/Library.git
cd Library
```

### 2. Set up the database
- Create a MySQL database and import the schema from `/database`
- Update the connection config in `server/Database/connection.js`

### 3. Start the backend
```bash
cd server
npm install
npm start
# Server runs on http://localhost:4000
```

### 4. Start the frontend
```bash
cd client
npm install
npm start
# App runs on http://localhost:3000
```

---

## 🔐 Role System

| Role | `role_id` | Capabilities |
|---|---|---|
| Admin | `1` | Full access — manage books, approve/reject requests, view all users |
| Member | `2` | Browse catalog, submit borrow requests, view own borrowed books |
| Pending | `3` | Registered but awaiting admin account approval |

> New registrations are assigned `role_id: 3` by default. An admin must manually promote the account to `role_id: 2` before the member can log in.

---

## 📁 Key Implementation Details

- **Image Management**: Multer handles book cover uploads; when a book is updated or deleted, the old image file is automatically removed from the server using `fs.unlinkSync`.
- **Query Abstraction**: All database interactions are encapsulated in dedicated query modules per entity (`queries.books.js`, `queries.user.js`, `queries.borrowingRequest.js`), keeping routes clean and maintainable.
- **Route Protection**: Two custom Express middleware functions (`Admin.js`, `authorize.js`) validate the request token against the database and check the user's `role_id` before allowing access to protected routes.
- **Input Validation**: Every POST and PUT endpoint uses `express-validator` to validate and sanitize request body fields before any database operation.
