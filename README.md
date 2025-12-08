# FastAPI JWT Authentication with E2E Testing - Module 13

## Overview
This is a **Module 13** FastAPI project that implements JWT-based authentication with front-end registration and login pages, Playwright E2E tests, and automated CI/CD deployment to Docker Hub.

## Key Features
- **JWT Authentication**: Secure token-based authentication with /register and /login endpoints
- **Front-End Pages**: Interactive HTML pages for user registration and login with client-side validation
- **User Management**: Registration, login, and user retrieval with secure password hashing (PBKDF2-SHA256)
- **Calculation Service**: Full BREAD operations (Create, Read, Update, Delete)
- **Calculation Types**: Add, Subtract, Multiply, Divide with automatic result computation
- **Data Validation**: Pydantic schemas with comprehensive validation (email format, password length, divide-by-zero)
- **Database Integration**: SQLAlchemy ORM with SQLite/PostgreSQL support
- **E2E Testing**: Playwright automated tests for registration and login flows
- **RESTful API**: FastAPI with automatic OpenAPI documentation
- **Comprehensive Testing**: 61 unit tests + Playwright E2E tests (all passing ✅)
- **CI/CD Pipeline**: GitHub Actions with automated testing and Docker Hub deployment
- **Docker Support**: Containerized deployment ready

## 🆕 Module 13 Additions
- JWT token generation and validation
- Client-side form validation for email format and password strength
- Playwright E2E tests covering positive and negative scenarios
- Front-end static pages for authentication
- Automated CI/CD pipeline with E2E testing

## Project Structure

```
app/
├── __init__.py              # App initialization
├── main.py                  # FastAPI application & routes
├── database.py              # Database configuration & session management
├── crud.py                  # Database operations (Create, Read, Update, Delete)
├── security.py              # Password hashing and verification
├── models.py                # Wrapper for models package
├── schemas.py               # Wrapper for schemas package
├── models/
│   ├── __init__.py
│   ├── user.py              # User database model
│   └── calculation.py       # Calculation database model
├── schemas/
│   ├── __init__.py
│   ├── user.py              # User Pydantic schemas (UserCreate, UserRead, UserLogin)
│   └── calculation.py       # Calculation Pydantic schemas (CalculationCreate, CalculationRead, CalculationUpdate, CalcType)
└── services/
    ├── __init__.py
    └── factory.py           # Calculation factory pattern implementation

tests/
├── __init__.py
├── conftest.py              # Pytest configuration & shared fixtures
├── test_security.py         # Password hashing/verification tests
├── test_schemas.py          # Pydantic schema validation tests
├── test_users_api.py        # User API endpoint tests
├── unit/
│   ├── test_calculation_factory.py   # Factory pattern unit tests
│   └── test_calculation_schema.py    # Calculation schema validation tests
└── integration/
    ├── test_calculation_db.py        # Calculation database integration tests
    └── test_users_calculations_api.py # Complete API endpoint integration tests (28 tests)

Root Files:
├── Dockerfile               # Docker configuration
├── docker-compose.yml       # Docker Compose setup for local development
├── requirements.txt         # Python dependencies
├── README.md               # This file
└── reflection.md           # Project reflection notes
```

## API Endpoints

### User Endpoints

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| POST | `/users/` | Register user (backward compatibility) | `UserCreate` | `UserRead` (201) |
| POST | `/users/register` | Register user (Module 12 spec) | `UserCreate` | `UserRead` (201) |
| POST | `/users/login` | Authenticate user | `UserLogin` | `UserRead` (200) |
| GET | `/users/{user_id}` | Get user details | - | `UserRead` (200) |

### Calculation Endpoints (BREAD)

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| POST | `/calculations/` | **B**rowse/Create calculation | `CalculationCreate` | `CalculationRead` (201) |
| GET | `/calculations/` | **R**ead all calculations | - | `List[CalculationRead]` (200) |
| GET | `/calculations/{calc_id}` | **R**ead specific calculation | - | `CalculationRead` (200) |
| PUT | `/calculations/{calc_id}` | **E**dit/Update calculation | `CalculationUpdate` | `CalculationRead` (200) |
| DELETE | `/calculations/{calc_id}` | **A**dd/Delete calculation | - | None (204) |

## Data Models

### User Model
```python
id: int (primary key)
username: str (unique, 3-50 chars)
email: str (unique, valid email format)
password_hash: str (PBKDF2-SHA256 hashed)
created_at: datetime (server default)
calculations: List[Calculation] (relationship, cascade delete)
```

### Calculation Model
```python
id: int (primary key)
a: float (first operand)
b: float (second operand)
type: str (Add, Sub, Multiply, or Divide)
user_id: int (optional, foreign key to User)
user: User (relationship back to User)
result: float (computed on-the-fly in schema)
```

## Schemas

### User Schemas
- **UserCreate**: `{username, email, password}` - For registration
- **UserRead**: `{id, username, email, created_at}` - Response model
- **UserLogin**: `{username, password}` - For login

### Calculation Schemas
- **CalculationCreate**: `{a, b, type}` - For creating calculations
- **CalculationRead**: `{id, a, b, type, user_id, result}` - Response model with computed result
- **CalculationUpdate**: `{a?, b?, type?}` - For partial updates (all fields optional)
- **CalcType**: Enum with values `Add`, `Sub`, `Multiply`, `Divide`

## Getting Started

### Prerequisites
- Python 3.11+
- pip package manager
- Docker (optional)

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Tejen1710/Module-12.git
   cd module-12
   ```

2. **Create and activate virtual environment:**
   ```bash
   # Windows
   python -m venv venv
   .\venv\Scripts\activate

   # macOS/Linux
   python -m venv venv
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

### Running the Application

**Option 1: Direct with Uvicorn**
```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

**Option 2: Using Docker**
```bash
docker build -t fastapi-calculator .
docker run -p 8000:8000 fastapi-calculator
```

**Option 3: Using Docker Compose**
```bash
docker-compose up
```

Once running, access the API documentation at: **http://127.0.0.1:8000/docs**

### Running Tests

**Run all tests:**
```bash
pytest
```

**Run with verbose output:**
```bash
pytest -v
```

**Run only unit tests:**
```bash
pytest tests/unit/ -v
```

**Run only integration tests:**
```bash
pytest tests/integration/ -v
```

**Run specific test file:**
```bash
pytest tests/integration/test_users_calculations_api.py -v
```

**Run with coverage report:**
```bash
pytest --cov=app
```

### Database Configuration

**For SQLite (default, development):**
```bash
# Automatically uses ./test.db or ./test_db.db
pytest
```

**For PostgreSQL (testing):**
```bash
# PowerShell
$env:DATABASE_URL = "postgresql://user:password@localhost:5432/calculator_test"
pytest

# Linux/macOS
export DATABASE_URL="postgresql://user:password@localhost:5432/calculator_test"
pytest
```

## Usage Examples

### Register a New User
```bash
curl -X POST "http://127.0.0.1:8000/users/register" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "johndoe",
    "email": "john@example.com",
    "password": "securepass123"
  }'
```

**Response (201):**
```json
{
  "id": 1,
  "username": "johndoe",
  "email": "john@example.com",
  "created_at": "2025-12-01T10:30:00"
}
```

### Login
```bash
curl -X POST "http://127.0.0.1:8000/users/login" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "johndoe",
    "password": "securepass123"
  }'
```

### Create a Calculation
```bash
curl -X POST "http://127.0.0.1:8000/calculations/" \
  -H "Content-Type: application/json" \
  -d '{
    "a": 15,
    "b": 3,
    "type": "Multiply"
  }'
```

**Response (201):**
```json
{
  "id": 1,
  "a": 15.0,
  "b": 3.0,
  "type": "Multiply",
  "user_id": null,
  "result": 45.0
}
```

### Get All Calculations
```bash
curl -X GET "http://127.0.0.1:8000/calculations/"
```

### Update a Calculation
```bash
curl -X PUT "http://127.0.0.1:8000/calculations/1" \
  -H "Content-Type: application/json" \
  -d '{"type": "Divide"}'
```

### Delete a Calculation
```bash
curl -X DELETE "http://127.0.0.1:8000/calculations/1"
```

## Test Coverage

### Test Summary
- **Total Tests**: 57 ✅
- **User Tests**: 11 (registration, login, retrieval)
- **Calculation Tests**: 16 (BREAD operations)
- **Workflow Tests**: 1 (integrated user + calculation flow)
- **Unit Tests**: 29 (factory, schemas, security)

### Key Test Categories

**User Authentication Tests:**
- Successful registration
- Duplicate username/email validation
- Email format validation
- Password strength validation
- Login with correct/incorrect credentials
- User not found scenarios

**Calculation BREAD Tests:**
- Create calculations (all operation types: Add, Sub, Multiply, Divide)
- Divide-by-zero validation
- Invalid operation type rejection
- Read all calculations (empty and populated)
- Read specific calculation
- Partial updates (update individual fields)
- Delete calculations

**Integration Tests:**
- Full workflow (register → login → create calculations)
- Multiple users with multiple calculations
- Database persistence
- Relationship integrity (user-calculation)

## CRUD Operations Details

### CREATE
- **Endpoint**: `POST /users/register`, `POST /calculations/`
- **Validation**: Email format, password strength, divide-by-zero check
- **Response**: 201 Created with full resource

### READ
- **Endpoints**: `GET /users/{id}`, `GET /calculations/`, `GET /calculations/{id}`
- **Response**: 200 OK with resource data
- **Error**: 404 Not Found for missing resources

### UPDATE
- **Endpoint**: `PUT /calculations/{id}`
- **Features**: Partial updates, field-level flexibility
- **Response**: 200 OK with updated resource
- **Error**: 404 Not Found for missing resource

### DELETE
- **Endpoint**: `DELETE /calculations/{id}`
- **Response**: 204 No Content
- **Error**: 404 Not Found for missing resource

## Security Features
- **Password Hashing**: PBKDF2-SHA256 algorithm
- **Input Validation**: Pydantic schemas with type hints
- **Error Handling**: Appropriate HTTP status codes
- **Database Constraints**: Unique constraints on username and email
- **Relationships**: CASCADE delete for data integrity
- **Dependency Injection**: FastAPI's dependency system for database sessions

## Docker Deployment

### Build Image
```bash
docker build -t fastapi-calculator:latest .
```

### Run Container
```bash
docker run -d \
  -p 8000:8000 \
  --name calculator-app \
  fastapi-calculator:latest
```

### Using Docker Compose
```bash
docker-compose up -d
```

### Check Logs
```bash
docker logs calculator-app
```

### Stop Container
```bash
docker stop calculator-app
```

## Environment Variables
- `DATABASE_URL`: Database connection string (default: `sqlite:///./test.db`)
- `PORT`: Server port (default: `8000`)
- `HOST`: Server host (default: `0.0.0.0`)

## Module 13: JWT Authentication & E2E Testing

### JWT Authentication Endpoints

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| POST | `/register` | Register user (JWT) | `{email, password}` | `{access_token, token_type}` (200) |
| POST | `/login` | Login user (JWT) | `{email, password}` | `{access_token, token_type}` (200) |

**Features:**
- Auto-generates username from email (e.g., `john@example.com` → `john`)
- Returns JWT token upon successful registration/login
- Token expires in 60 minutes (configurable)
- Uses HS256 algorithm for JWT signing

### Front-End Pages

**Access the front-end:**
- Registration: http://127.0.0.1:8000/static/register.html
- Login: http://127.0.0.1:8000/static/login.html

**Client-Side Validation:**
- Email format validation (must contain @)
- Password length validation (min 8 characters)
- Password confirmation matching
- Real-time error messages
- JWT token stored in localStorage

### Running the Front-End

1. **Start the server:**
   ```bash
   uvicorn app.main:app --reload
   ```

2. **Open in browser:**
   - Register: http://localhost:8000/static/register.html
   - Login: http://localhost:8000/static/login.html

3. **Test the workflow:**
   - Register a new user with email and password
   - Check browser console or localStorage for JWT token
   - Login with the same credentials
   - Verify token is stored

### Playwright E2E Tests

**Install Playwright browsers:**
```bash
python -m playwright install chromium
```

**Run E2E tests:**
```bash
# Run all E2E tests
pytest tests/e2e/ -v

# Run specific E2E test file
pytest tests/e2e/test_register_e2e.py -v
pytest tests/e2e/test_login_e2e.py -v

# Run with headed browser (see the tests run)
pytest tests/e2e/ -v --headed

# Run E2E tests with server already running
# (Start server in one terminal, run tests in another)
uvicorn app.main:app --reload
pytest tests/e2e/ -v
```

**E2E Test Coverage:**

**Positive Tests:**
- ✅ Register with valid data (email format, password length ≥8)
- ✅ Login with correct credentials
- ✅ JWT token stored in localStorage
- ✅ Success messages displayed
- ✅ Complete workflow: register → login

**Negative Tests:**
- ✅ Register with short password → front-end error
- ✅ Register with mismatched passwords → front-end error
- ✅ Register with invalid email → front-end error
- ✅ Register with duplicate email → server 400 error
- ✅ Login with wrong password → server 401 error
- ✅ Login with non-existent user → server 401 error
- ✅ Login with invalid email format → front-end error

### CI/CD Pipeline

**GitHub Actions Workflow:**
1. Run unit tests (pytest)
2. Run integration tests
3. Install Playwright browsers
4. Start FastAPI server in background
5. Run Playwright E2E tests
6. Build Docker image (if all tests pass)
7. Push to Docker Hub (if all tests pass)

**View workflow:** `.github/workflows/ci.yml`

**Docker Hub Repository:** https://hub.docker.com/r/[your-username]/module13-calculator

### Testing Workflow

**Complete test suite:**
```bash
# 1. Run unit and integration tests
pytest tests/unit tests/integration tests/test_*.py -v

# 2. Start server for E2E tests
uvicorn app.main:app --reload &

# 3. Run E2E tests
pytest tests/e2e/ -v

# 4. Run all tests (E2E will auto-start server)
pytest -v
```

## Dependencies

Key packages:
- **fastapi**: Web framework
- **uvicorn**: ASGI server
- **sqlalchemy**: ORM
- **pydantic**: Data validation
- **passlib**: Password hashing with PBKDF2-SHA256
- **python-jose**: JWT token generation and validation
- **pytest**: Testing framework
- **httpx**: HTTP client for testing
- **playwright**: Browser automation for E2E tests

See `requirements.txt` for complete list.

## Backward Compatibility

This module maintains backward compatibility with Module 11:
- Old `/users/` endpoint still works (calls same `create_user` function as `/users/register`)
- All existing tests pass
- Database schema unchanged
- CRUD operation signatures compatible

## Validation Rules

### User Registration
- **Username**: 3-50 characters, unique
- **Email**: Valid email format, unique
- **Password**: Minimum 8 characters

### Calculation Creation
- **a, b**: Float values (positive or negative)
- **type**: Must be one of: `Add`, `Sub`, `Multiply`, `Divide`
- **Division**: `b` cannot be zero when type is `Divide`

## Error Handling

Common HTTP Status Codes:
- `200 OK`: Successful GET/PUT/POST
- `201 Created`: Successful resource creation
- `204 No Content`: Successful DELETE
- `400 Bad Request`: Validation error (duplicate username/email)
- `401 Unauthorized`: Invalid login credentials
- `404 Not Found`: Resource not found
- `422 Unprocessable Entity`: Invalid request data format

## Contributing

Feel free to fork this project and submit pull requests for any improvements.

## License

This project is licensed under the MIT License - see LICENSE file for details.

## Author

**Tejen Thakkar**
- GitHub: [@Tejen1710](https://github.com/Tejen1710)
- Repository: [Module-12](https://github.com/Tejen1710/Module-12)

## References

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
- [Pydantic Documentation](https://docs.pydantic.dev/)
- [PBKDF2 Password Hashing](https://passlib.readthedocs.io/)