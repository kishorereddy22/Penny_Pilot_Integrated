# PennyPilot — Integrated Flask + MySQL Application

PennyPilot is a personal budget and expense tracker. The browser interface is served by Flask and communicates with the same Flask backend using JSON APIs. Authentication uses password hashing and JWT; application data is stored in MySQL.

## Included features
- Account signup and login
- JWT-protected API requests
- Income and expense transactions (add, list, edit, delete)
- User-specific categories loaded from MySQL
- Monthly dashboard totals and category-wise spending chart
- Monthly category budgets stored in MySQL
- Existing backend modules for savings goals, reports and spending insights

## Requirements
- Python 3.10+
- MySQL Server 8+
- A modern browser

## 1. Prepare the database
In MySQL Workbench, open `sql/schema.sql` and execute it. It creates the `pennypilot` database and the required tables without dropping existing data.

## 2. Configure the environment
In this folder, copy `.env.example` to `.env` and enter your own local MySQL password and a strong JWT secret:

```env
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=pennypilot
JWT_SECRET_KEY=replace_with_a_long_random_secret
```

Do not upload or share `.env`.

## 3. Install Python packages (PowerShell)
Run these commands from the PennyPilot folder:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

## 4. Run PennyPilot

```powershell
python app.py
```

Open `http://127.0.0.1:5000/`.

Check the database connection at `http://127.0.0.1:5000/api/health`.

## How the frontend/backend connection works
- The frontend is in `static/index.html` and `static/style.css`.
- The frontend calls Flask endpoints on the same origin; no Firebase SDK, Firebase configuration, or Firestore is used.
- Signup calls `POST /api/auth/signup`, then logs in through `POST /api/auth/login`.
- Login returns a JWT. The browser stores the token and sends `Authorization: Bearer <token>` with protected requests.
- Transactions and categories are read/written through Flask APIs and stored in MySQL.
- The frontend never supplies a trusted user ID for transaction ownership; protected backend routes obtain the user ID from the JWT.

## Important API endpoints
| Purpose | Endpoint |
|---|---|
| Health check | `GET /api/health` |
| Signup | `POST /api/auth/signup` |
| Login | `POST /api/auth/login` |
| Categories | `GET /api/categories/<user_id>` |
| Transactions | `GET/POST /api/transactions` |
| Update transaction | `PUT /api/transactions/<transaction_id>` |
| Delete transaction | `DELETE /api/transactions/<transaction_id>` |
| Dashboard summary | `GET /api/dashboard/<user_id>` |
| Budgets | `GET /api/budgets/<user_id>` and `POST /api/budgets` |
| Budget progress | `GET /api/budget-progress/<user_id>?month=<month>&year=<year>` |

## Notes
- New accounts receive starter income and expense categories.
- The dashboard summary cards calculate the current month's totals from transactions loaded from the API.
- The budget card lets the user create a monthly budget for an expense category. The backend currently prevents duplicate budgets for the same category and month; use a different category or month if one already exists.
- The forgot-password control is intentionally hidden because this backend does not currently provide a password-reset email workflow.
- Existing backend savings-goal, monthly-report, and spending-insights routes remain included and can be connected to additional frontend screens later.
