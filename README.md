# Project Plan for Stock Trading Application

## Objective

Build a full-stack stock trading application using Next.js with the new app router, PostgreSQL for database management, Prisma as the ORM, Tailwind CSS and Shad-cn for UI components, following the MVC model and modern architecture principles.

1. Project Structure
    Organize the project using Next.js with the app router. The structure will follow the MVC pattern, separating concerns between Models, Views, and Controllers:

    ```
        stock-trading-app/
        │
        ├── prisma/                     # Prisma schema and migrations
        │   ├── schema.prisma           # Prisma schema defining database structure
        │   ├── migrations/             # Database migrations
        │
        ├── public/                     # Static files (images, etc.)
        │
        ├── src/                        
        │   ├── app/                    # Next.js app directory (handles routing and rendering)
        │   │   ├── api/                # API routes for server-side logic (Controllers)
        │   │   │   ├── auth/           # Authentication APIs
        │   │   │   ├── stocks/         # APIs for handling stock data
        │   │   │   ├── trades/         # APIs for managing trades
        │   │   │   └── user/           # APIs for user-related operations
        │   │   │
        │   │   ├── auth/               # Pages related to authentication (Views)
        │   │   │   ├── login.tsx       # Login page
        │   │   │   ├── signup.tsx      # Signup page
        │   │   │   └── reset-password.tsx  # Password reset page
        │   │   │
        │   │   ├── dashboard/          # User dashboard pages (Views)
        │   │   │   ├── index.tsx       # Main dashboard page
        │   │   │   ├── portfolio.tsx   # Portfolio page
        │   │   │   └── history.tsx     # Trade history page
        │   │   │
        │   │   ├── layout.tsx          # Application layout
        │   │   └── page.tsx            # Landing page (Home view)
        │   │
        │   ├── components/             # Reusable UI components (Views)
        │   │   ├── Navbar.tsx          # Navigation bar
        │   │   ├── Footer.tsx          # Footer
        │   │   └── StockCard.tsx       # Stock item component
        │   │
        │   ├── styles/                 # Tailwind CSS and global styles (Views)
        │   │   └── globals.css         # Global stylesheet
        │   │
        │   ├── models/                 # Models (Database and business logic)
        │   │   ├── User.ts             # User model
        │   │   ├── Stock.ts            # Stock model
        │   │   ├── Trade.ts            # Trade model
        │   │   └── Portfolio.ts        # Portfolio model
        │   │
        │   ├── controllers/            # Controllers (Handle API requests and responses)
        │   │   ├── AuthController.ts   # Authentication controller
        │   │   ├── StockController.ts  # Stock management controller
        │   │   ├── TradeController.ts  # Trade execution controller
        │   │   └── UserController.ts   # User management controller
        │   │
        │   ├── services/               # Service layer (Business logic and external API integration)
        │   │   ├── AuthService.ts      # Service for authentication logic
        │   │   ├── StockService.ts     # Service for interacting with stock data APIs
        │   │   ├── TradeService.ts     # Service for handling trades and transactions
        │   │   └── UserService.ts      # Service for user-related operations
        │   │
        │   ├── lib/                    # Utility functions, hooks, and helpers
        │   │   ├── jwt.ts              # JWT handling utilities
        │   │   ├── api.ts              # API helper functions
        │   │   ├── hooks/              # Custom React hooks
        │   │   └── validators.ts       # Input validation logic
        │   │
        │   ├── context/                # React context for global state
        │   │   └── AuthContext.tsx     # Context for authentication state
        │   │
        │   ├── middleware/             # Middleware for request handling and security
        │   │   └── authMiddleware.ts   # Middleware for protecting routes
        │   │
        │   ├── types/                  # TypeScript types and interfaces
        │   │   ├── index.ts            # Global types
        │   │   └── api.ts              # API-specific types
        │   │
        ├── .env                        # Environment variables
        ├── next.config.js              # Next.js configuration
        ├── tailwind.config.js          # Tailwind CSS configuration
        ├── tsconfig.json               # TypeScript configuration
        ├── package.json                # Dependencies and scripts
        ├── yarn.lock                   # Yarn lockfile
        └── README.md                   # Project documentation
    ```

2. Data Flow
    1. User Authentication and Authorization:

        - Frontend: User interacts with login/signup forms. The form data is sent to /api/auth API endpoints.
        - Backend (Controller): AuthController receives requests, AuthService handles logic (e.g., password hashing, token generation), and User model updates the database.
        - Response: JWT token is issued and stored client-side (cookies or local storage) to manage sessions.

    2. Fetching Stock Data:

        - Frontend: User dashboard requests stock data via the /api/stocks endpoint.
        - Backend (Controller): StockController interacts with StockService, which fetches data from third-party APIs or database. Data is processed, then sent back to the client.
        - Service Layer: StockService handles all external API interactions and business logic before passing data to the controller.

    3. Placing Trades:

        - Frontend: Users place orders via the UI, triggering requests to /api/trades.
        - Backend (Controller): TradeController uses TradeService to validate the trade, check the user’s balance, and update the database accordingly using the Trade and Portfolio models.
        - Service Layer: TradeService ensures all business rules are met (e.g., sufficient funds, market status) before finalizing the trade.

    4. User Dashboard:

        - Frontend: Displays user portfolio, transaction history, and stock analytics.
        - Backend (Controller): UserController retrieves user data via UserService, aggregates relevant information (portfolio value, stock performance), and sends it to the frontend.

    5. Notifications and Real-Time Data:

        - Frontend: Notifications and updates are displayed on the dashboard in real-time.
        - Backend (Controller): WebSockets or SSE (handled in StockService) are used to push live updates to the client (e.g., stock price changes).

3. Detailed API Structure

    1. Authentication APIs (/api/auth):
        - POST /login:
            - Input: { email, password }
            - Output: { token, user } or error.
            - Controller Logic: Authenticates user, generates JWT.

        - POST /signup:
            - Input: { name, email, password }
            - Output: { token, user } or error.
            - Controller Logic: Creates a new user, hashes the password, generates JWT.

        - POST /logout:
            - Input: token
            - Output: { message }
            - Controller Logic: Invalidates the JWT, logs out user.

    2. Stock Data APIs (/api/stocks):
        - GET /list:
            - Output: [{ symbol, name, price, ... }]
            - Controller Logic: Fetches list of available stocks from third-party API.
        - GET /detail/:symbol:
            - Output: { symbol, name, price, history, ... }
            - Controller Logic: Fetches detailed data for a specific stock.
        - GET /trending:
            - Output: [{ symbol, name, price, ... }]
            - Controller Logic: Fetches trending stocks based on volume, price change.

    3. Trade Management APIs (/api/trades):
        - POST /buy:
            - Input: { symbol, quantity, price }
            - Output: { tradeId, status, portfolio }
            - Controller Logic: Executes a buy order, updates portfolio.
        - POST /sell:
            - Input: { symbol, quantity, price }
            - Output: { tradeId, status, portfolio }
            - Controller Logic: Executes a sell order, updates portfolio.
        - GET /history:
            - Output: [{ tradeId, symbol, type, date, status, ... }]
            - Controller Logic: Returns user’s trade history.

    4. User Management APIs (/api/user):

        - GET /profile:
            - Output: { name, email, balance, portfolio }
            - Controller Logic: Retrieves the user's profile information, including their current balance and portfolio details.

        - PATCH /profile:
            - Input: { name, email, password, newPassword }
            - Output: { message, user }
            - Controller Logic: Allows the user to update their profile details. If a password update is requested, the old password is validated before the new one is set.

        - GET /portfolio:
            - Output: { stocks: [{ symbol, quantity, currentPrice, ... }] }
            - Controller Logic: Retrieves the user's current portfolio, listing all owned stocks with their quantities and current market values.

        - GET /balance:
            - Output: { balance }
            - Controller Logic: Returns the user's current account balance.

    5. Notification APIs (/api/notifications):
        - GET /list:
            - Output: [{ id, message, type, read, timestamp }]
            - Controller Logic: Fetches a list of notifications for the user, such as trade confirmations, market alerts, and account updates.

        - PATCH /read/:id:
            - Input: { read: boolean }
            - Output: { message }
            - Controller Logic: Marks a specific notification as read or unread.

4. MVC Directory Structure Breakdown

    1. Model Layer (src/models):
        - User.ts: Defines the User model, including fields like id, name, email, password, balance, and relationships to Portfolio.
        - Stock.ts: Defines the Stock model, representing individual stocks, including fields like symbol, name, price, marketCap.
        - Trade.ts: Represents trades made by users, including fields like id, symbol, quantity, price, type (buy/sell), and references to the User and Stock.
        - Portfolio.ts: Represents a user's portfolio, detailing the stocks owned and their quantities.

    2. View Layer (src/app):
        - app/pages: Contains pages like index.tsx (home), auth/login.tsx, dashboard/index.tsx, dashboard/portfolio.tsx.
        - app/components: Contains reusable components like Navbar.tsx, Footer.tsx, StockCard.tsx.

    3. Controller Layer (src/controllers):
        - AuthController.ts: Handles authentication logic, routes like login, signup, and logout.
        - StockController.ts: Manages stock-related API endpoints, fetching data from external APIs and responding to client requests.
        - TradeController.ts: Handles trade execution, buy/sell logic, and interaction with the Trade and Portfolio models.
        - UserController.ts: Manages user data, profile updates, and retrieves portfolio and balance information.

    4. Service Layer (src/services):
        - AuthService.ts: Contains business logic for user authentication, including password hashing and token management.
        - StockService.ts: Handles interactions with external stock data APIs, processes real-time data, and manages stock caching.
        - TradeService.ts: Manages trade logic, including validation, execution, and updating the user’s portfolio.
        - UserService.ts: Handles user-related operations such as profile updates, balance management, and portfolio aggregation.

    5. Utility Functions (src/lib):
        - jwt.ts: Utilities for JWT token generation, verification, and decoding.
        - api.ts: Helper functions for making API requests, error handling.
        - hooks/: Custom React hooks for managing state, side effects, and context.
        - validators.ts: Functions for validating user inputs, like email format, password strength.

    6. Middleware (src/middleware):
        - authMiddleware.ts: Ensures that API requests are authenticated, checks for valid JWT tokens, and restricts access to protected routes.
    7. Context Management (src/context):
        - AuthContext.tsx: Manages global authentication state, provides context to components about the current user’s authentication status.
    8. Global Types and Interfaces (src/types):
        - index.ts: General TypeScript types and interfaces used across the application.
        - api.ts: Specific types and interfaces related to API requests and responses.
