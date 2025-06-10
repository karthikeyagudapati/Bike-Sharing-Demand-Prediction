# EduGuide AI: Code Starter Templates Outline

This document outlines the proposed code starter templates for both the frontend and backend of the EduGuide AI platform.

## I. Frontend (React.js + Next.js + Tailwind CSS)

**A. Folder Structure (Next.js App Router convention):**

```
eduguide-ai-frontend/
├── app/                            # App Router directory
│   ├── (auth)/                     # Route group for authentication pages
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── signup/
│   │       └── page.tsx
│   ├── (dashboard)/                # Route group for protected dashboard pages
│   │   ├── layout.tsx              # Layout for dashboard section
│   │   ├── student-dashboard/
│   │   │   └── page.tsx
│   │   ├── colleges/
│   │   │   ├── page.tsx            # College listing/discovery
│   │   │   └── [id]/               # Dynamic route for single college detail
│   │   │       └── page.tsx
│   │   ├── applications/
│   │   │   └── page.tsx
│   │   ├── loans/
│   │   │   └── page.tsx            # Loan comparison
│   │   └── profile/
│   │       └── page.tsx
│   ├── api/                        # API routes (Next.js backend for frontend)
│   │   └── auth/
│   │       └── [...nextauth]/      # NextAuth.js dynamic route handler
│   │           └── route.ts
│   ├── favicon.ico
│   ├── globals.css                 # Global styles (Tailwind base, custom global styles)
│   ├── layout.tsx                  # Root layout for the entire application
│   └── page.tsx                    # Homepage/Landing page
├── components/
│   ├── ui/                         # Reusable UI components (Atomic design, e.g., Shadcn/ui style)
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── input.tsx
│   │   ├── dialog.tsx
│   │   ├── dropdown-menu.tsx
│   │   └── ...                     # Other generic UI elements
│   ├── common/                     # Common application-wide components
│   │   ├── Navbar.tsx
│   │   ├── Footer.tsx
│   │   └── Sidebar.tsx             # For dashboard layout
│   └── features/                   # Components specific to a particular feature or page
│       ├── student-dashboard/      # Components for the student dashboard
│       │   └── ProfileSummaryCard.tsx
│       ├── colleges/               # Components for college features
│       │   ├── CollegeCard.tsx
│       │   └── CollegeFilters.tsx
│       ├── loans/                  # Components for loan features
│       │   └── LoanOptionCard.tsx
│       └── applications/
│           └── ApplicationStatusBadge.tsx
├── contexts/                       # React Context API for global state management
│   ├── AuthContext.tsx             # Manages authentication state, user details
│   └── ThemeContext.tsx            # Manages theme (light/dark mode)
├── hooks/                          # Custom React hooks for reusable logic
│   ├── useUserDetails.ts           # Fetches/manages detailed user profile data
│   ├── useFormValidation.ts        # Generic form validation hook
│   └── useDebounce.ts              # Debouncing hook
├── lib/                            # Utility functions, API clients, constants
│   ├── api.ts                      # Client for interacting with the FastAPI backend
│   ├── utils.ts                    # General utility functions (formatting, etc.)
│   ├── validators.ts               # Zod schemas for form and data validation
│   └── constants.ts                # Application-wide constants
├── public/                         # Static assets (images, fonts, etc.)
│   ├── images/
│   │   └── logo.png
│   └── fonts/
├── .env.local                      # Local environment variables (API keys, backend URL)
├── .eslintrc.json                  # ESLint configuration
├── .gitignore
├── next.config.mjs                 # Next.js configuration
├── package.json                    # Project dependencies and scripts
├── postcss.config.js               # PostCSS configuration (for Tailwind CSS)
├── tailwind.config.ts              # Tailwind CSS configuration
└── tsconfig.json                   # TypeScript configuration
```

**B. Key Files & Descriptions:**

*   **`app/layout.tsx`**: The root layout for the application. It includes the basic HTML shell (`<html>`, `<body>`), and globally shared UI like a top-level ThemeProvider or AuthContext provider.
*   **`app/page.tsx`**: The main landing page of the EduGuide AI platform, accessible to all visitors.
*   **`app/(auth)/...`**: Route group for authentication pages like login and signup. These pages will use a simpler layout, distinct from the main dashboard.
*   **`app/(dashboard)/layout.tsx`**: A shared layout for all pages within the user dashboard (e.g., student-dashboard, colleges, profile). This typically includes the main navigation (Navbar, Sidebar) and ensures a consistent structure for logged-in users.
*   **`app/(dashboard)/student-dashboard/page.tsx`**: The main page for the Student Dashboard, displaying summaries and quick actions.
*   **`app/(dashboard)/colleges/page.tsx`**: Page for discovering and shortlisting colleges, including search and filter functionalities.
*   **`app/(dashboard)/colleges/[id]/page.tsx`**: Dynamic route page to display detailed information about a specific college.
*   **`app/(dashboard)/loans/page.tsx`**: Page for comparing education loan options.
*   **`app/api/auth/[...nextauth]/route.ts`**: Handles authentication logic using NextAuth.js (e.g., sign in, sign up, session management).
*   **`components/ui/`**: Contains generic, highly reusable UI components like Button, Card, Input, Dialog. These are often styled using Tailwind CSS and could be sourced from a library like Shadcn/UI or built custom.
*   **`components/common/Navbar.tsx`**: The main navigation bar component, typically shown at the top of the page.
*   **`components/common/Sidebar.tsx`**: A sidebar component, often used in dashboard layouts for secondary navigation.
*   **`components/features/*`**: These are components that are more complex and tied to specific features of the application, e.g., `CollegeCard.tsx` for displaying college information or `LoanOptionCard.tsx` for loan details.
*   **`contexts/AuthContext.tsx`**: Manages global authentication state, such as the current user, tokens, and login/logout functions.
*   **`hooks/useUserDetails.ts`**: A custom hook to encapsulate logic for fetching and managing user-specific details.
*   **`lib/api.ts`**: Contains functions for making API calls to the backend (FastAPI). This might use `fetch` or a library like `axios`.
*   **`lib/validators.ts`**: Defines validation schemas using a library like Zod, used for validating form inputs and API responses.
*   **`tailwind.config.ts`**: Configuration file for Tailwind CSS, defining theme, plugins, and content paths.
*   **`next.config.mjs`**: Configuration file for Next.js, allowing customization of its behavior (e.g., redirects, environment variables).

## II. Backend (FastAPI + PostgreSQL + SQLModel/SQLAlchemy)

**A. Folder Structure:**

```
eduguide-ai-backend/
├── app/
│   ├── __init__.py
│   ├── main.py                     # FastAPI application instance and router includes
│   ├── core/                       # Core application settings and configurations
│   │   ├── __init__.py
│   │   └── config.py               # Pydantic model for settings (from .env)
│   ├── db/                         # Database interaction layer
│   │   ├── __init__.py
│   │   ├── database.py             # Database engine, session creation, get_db dependency
│   │   └── models.py               # SQLModel/SQLAlchemy ORM models
│   ├── schemas/                    # Pydantic schemas for API request/response validation & serialization
│   │   ├── __init__.py
│   │   ├── student.py              # Schemas for Student entity (Create, Read, Update)
│   │   ├── university.py           # Schemas for University entity
│   │   ├── program.py              # Schemas for Program entity
│   │   ├── loan.py                 # Schemas for Loan entity
│   │   ├── application.py          # Schemas for Application entity
│   │   ├── reminder.py             # Schemas for Reminder entity
│   │   └── token.py                # Schemas for JWT tokens (Token, TokenData)
│   ├── crud/                       # CRUD (Create, Read, Update, Delete) database operations
│   │   ├── __init__.py
│   │   ├── crud_student.py
│   │   ├── crud_university.py
│   │   ├── crud_program.py
│   │   └── base.py                 # Optional: Base CRUD class for common operations
│   ├── api/                        # API routers and endpoints
│   │   ├── __init__.py
│   │   ├── deps.py                 # API dependencies (e.g., get_current_user, get_db_session)
│   │   └── v1/                     # API version 1
│   │       ├── __init__.py
│   │       ├── endpoints/          # Individual endpoint files for each resource
│   │       │   ├── __init__.py
│   │       │   ├── auth.py         # Authentication (login, register, password recovery)
│   │       │   ├── students.py     # Student related endpoints
│   │       │   ├── universities.py # University related endpoints
│   │       │   ├── programs.py     # Program related endpoints
│   │       │   ├── loans.py        # Loan related endpoints
│   │       │   └── applications.py # Application related endpoints
│   │       └── api.py              # Main API router including all v1 endpoint routers
│   ├── services/                   # Business logic, external API integrations
│   │   ├── __init__.py
│   │   ├── email_service.py        # Service for sending emails (e.g., notifications, password reset)
│   │   ├── gpt_service.py          # Service for interacting with GPT-4o or other AI models
│   │   └── recommendation_service.py # Service for generating recommendations
│   └── security/                   # Authentication, authorization, password hashing, JWT handling
│       ├── __init__.py
│       └── security.py             # Password hashing, JWT creation/decoding functions
├── tests/                          # Unit and integration tests
│   ├── __init__.py
│   ├── conftest.py                 # Pytest fixtures (e.g., test client, db session)
│   ├── crud/
│   │   └── test_crud_student.py
│   └── api/
│       └── v1/
│           ├── test_auth.py
│           └── test_students.py
├── .env                            # Environment variables (DB_URL, JWT_SECRET, etc.)
├── .env.example                    # Example environment file
├── .gitignore
├── alembic/                        # Alembic database migration scripts (if using SQLAlchemy)
├── alembic.ini                     # Alembic configuration file
├── poetry.lock                     # For Poetry package manager
├── pyproject.toml                  # Project metadata and dependencies (Poetry)
└── README.md                       # Project overview and setup instructions
```

**B. Key Files & Descriptions:**

*   **`app/main.py`**: The entry point of the FastAPI application. It initializes the FastAPI app, mounts sub-routers (e.g., `api_router_v1`), and can include global middleware.
*   **`app/core/config.py`**: Defines a Pydantic `Settings` class to load and validate environment variables (e.g., database URL, JWT secret key, API keys for external services).
*   **`app/db/database.py`**: Contains logic for setting up the database connection (SQLAlchemy engine) and session management. It typically provides a `get_db` dependency for use in API endpoints.
*   **`app/db/models.py`**: Defines the ORM models using SQLModel or SQLAlchemy, corresponding to the tables in your database schema (e.g., `Student`, `University`, `Program`).
    *   *Example `Student` model (SQLModel):*
        ```python
        from typing import Optional, List
        from sqlmodel import Field, SQLModel, Relationship # type: ignore

        # Forward declaration for Score if it's in another file or defined later
        class Score(SQLModel, table=True): # Dummy for example
            score_id: Optional[int] = Field(default=None, primary_key=True)
            student_id: Optional[int] = Field(default=None, foreign_key="student.student_id")
            exam_name: str
            student: Optional["Student"] = Relationship(back_populates="scores")


        class StudentBase(SQLModel):
            first_name: str
            last_name: str
            email: str = Field(unique=True, index=True)
            # ... other fields from schema ...

        class Student(StudentBase, table=True):
            student_id: Optional[int] = Field(default=None, primary_key=True)
            password_hash: str

            scores: List["Score"] = Relationship(back_populates="student")
            # applications: List["Application"] = Relationship(back_populates="student")
            # reminders: List["Reminder"] = Relationship(back_populates="student")
        ```
*   **`app/schemas/*.py`**: Contains Pydantic models used for request body validation, response serialization, and data transfer objects (DTOs). For example, `StudentCreate` for creating a student, `StudentRead` for returning student data.
    *   *Example `StudentCreate` schema (Pydantic):*
        ```python
        from pydantic import BaseModel, EmailStr

        class StudentCreate(BaseModel):
            first_name: str
            last_name: str
            email: EmailStr
            password: str
            # ... other fields required for student creation ...

        class StudentRead(BaseModel):
            student_id: int
            first_name: str
            last_name: str
            email: EmailStr
            # ... other fields to be returned ...
            class Config:
                orm_mode = True # For compatibility with ORM models
        ```
*   **`app/crud/crud_*.py`**: Contains functions that encapsulate the direct database interaction logic (Create, Read, Update, Delete operations) for each model. For instance, `crud_student.py` would have functions like `get_student(db: Session, student_id: int)`, `create_student(db: Session, student: StudentCreate)`.
*   **`app/api/v1/endpoints/*.py`**: These files define the actual API routes using FastAPI's `APIRouter`. Each file typically corresponds to a specific resource (e.g., `students.py` for student-related endpoints, `auth.py` for authentication).
    *   *Example endpoint in `students.py`*:
        ```python
        from fastapi import APIRouter, Depends, HTTPException
        from sqlmodel import Session # type: ignore
        from app.db.database import get_db
        from app.schemas.student import StudentCreate, StudentRead
        from app.crud import crud_student
        from app.api import deps # For dependencies like get_current_active_user

        router = APIRouter()

        @router.post("/", response_model=StudentRead)
        def create_student_endpoint(
            student_in: StudentCreate,
            db: Session = Depends(get_db)
            # current_user: models.User = Depends(deps.get_current_active_user) # If endpoint needs auth
        ):
            db_student = crud_student.get_student_by_email(db, email=student_in.email)
            if db_student:
                raise HTTPException(status_code=400, detail="Email already registered")
            return crud_student.create_student(db=db, student=student_in)
        ```
*   **`app/api/deps.py`**: Defines common dependencies for API endpoints, such as `get_db` for database sessions, or `get_current_active_user` for protected routes.
*   **`app/security/security.py`**: Includes utility functions for security-related tasks, such as password hashing (e.g., using passlib), creating and verifying JWT tokens.
*   **`app/services/gpt_service.py`**: Contains logic for interacting with external AI services like OpenAI's GPT models. This abstracts the API calls and data transformation.
*   **`app/services/email_service.py`**: Handles the sending of emails for notifications, password resets, etc., possibly using a library like `fastapi-mail`.
*   **`alembic/`**: If using SQLAlchemy and Alembic for database migrations, this directory will store migration scripts.
*   **`pyproject.toml` / `poetry.lock`**: (If using Poetry) Manages project dependencies, scripts, and metadata. Alternatively, a `requirements.txt` file would be used with pip.
*   **`tests/`**: Contains all unit and integration tests for the backend application, typically mirroring the structure of the `app` directory.

This outline provides a comprehensive starting point for developers to understand the project structure, key components, and begin development on the EduGuide AI platform.
