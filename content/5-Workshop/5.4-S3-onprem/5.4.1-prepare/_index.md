---
title: "FastAPI Application Structure"
date: "2024-01-01"
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## FastAPI Application Structure

In this section, you will understand the FastAPI application structure and how it's organized using clean layered architecture.

### Application Architecture

The FastAPI application follows a **layered architecture** pattern:

```
┌─────────────────────────────────────────┐
│         API Layer (routers/)            │  ← HTTP endpoints, request/response
├─────────────────────────────────────────┤
│      Service Layer (services/)          │  ← Business logic, orchestration
├─────────────────────────────────────────┤
│   Repository Layer (repositories/)      │  ← Data access, DynamoDB operations
├─────────────────────────────────────────┤
│        Models (models/)                 │  ← Data structures, validation
└─────────────────────────────────────────┘
```

### Main Application Entry Point

```python
# backend/app/main.py
from fastapi import FastAPI
from app.api.routers.auth import router as auth_router
from app.api.routers.products import router as products_router
from app.api.routers.orders import router as orders_router
from app.core.logging import configure_logging
from app.api.middleware import add_correlation_middleware

def create_app() -> FastAPI:
    configure_logging()
    app = FastAPI(title="Products & Orders API", version="1.0.0")
    
    add_correlation_middleware(app)
    
    @app.get("/health", tags=["health"])
    async def health() -> dict:
        return {"status": "ok"}
    
    app.include_router(auth_router, prefix="/auth", tags=["auth"])
    app.include_router(products_router, prefix="/products", tags=["products"])
    app.include_router(orders_router, prefix="/orders", tags=["orders"])
    
    return app

app = create_app()
```

### Lambda Handler

```python
# backend/app/lambda_handler.py
from mangum import Mangum
from app.main import app

# Expose Lambda handler for API Gateway HTTP API
handler = Mangum(app)
```

**How Mangum Works:**
- Converts API Gateway HTTP API events to ASGI requests
- Routes requests to FastAPI application
- Converts FastAPI responses back to API Gateway format

### Configuration Management

```python
# backend/app/core/config.py
from pydantic_settings import BaseSettings
from pydantic import Field
import os

class Settings(BaseSettings):
    aws_region: str = Field(..., alias="AWS_REGION")
    products_table: str = Field(..., alias="PRODUCTS_TABLE")
    orders_table: str = Field(..., alias="ORDERS_TABLE")
    jwt_secret: str = Field(..., alias="JWT_SECRET")
    log_level: str = Field("INFO", alias="LOG_LEVEL")

    class Config:
        env_file = os.getenv("ENV_FILE", ".env")
        case_sensitive = True

settings = Settings()  # will raise if required env missing
```

**Environment Variables:**
- `AWS_REGION`: AWS region (e.g., `us-east-1`)
- `PRODUCTS_TABLE`: DynamoDB products table name
- `ORDERS_TABLE`: DynamoDB orders table name
- `JWT_SECRET`: JWT secret from Secrets Manager
- `LOG_LEVEL`: Logging level (default: INFO)

### API Routers

#### Auth Router

```python
# backend/app/api/routers/auth.py
from fastapi import APIRouter, Depends, HTTPException, status
from pydantic import BaseModel, EmailStr
from app.api.deps import get_auth_service
from app.services.auth_service import AuthService

router = APIRouter()

class RegisterRequest(BaseModel):
    email: EmailStr
    password: str

class LoginRequest(BaseModel):
    email: EmailStr
    password: str

class TokenResponse(BaseModel):
    access_token: str
    token_type: str = "bearer"

@router.post("/register", status_code=status.HTTP_201_CREATED)
async def register(payload: RegisterRequest, 
                   auth_service: AuthService = Depends(get_auth_service)) -> dict:
    await auth_service.register_user(email=payload.email, password=payload.password)
    return {"message": "registered"}

@router.post("/login", response_model=TokenResponse)
async def login(payload: LoginRequest, 
                auth_service: AuthService = Depends(get_auth_service)) -> TokenResponse:
    token = await auth_service.login(email=payload.email, password=payload.password)
    return TokenResponse(access_token=token)
```

**Endpoints:**
- `POST /auth/register` - Register new user
- `POST /auth/login` - Login and get JWT token

#### Products Router

```python
# backend/app/api/routers/products.py
from fastapi import APIRouter, Depends, HTTPException, status, Query
from typing import Optional, List
from app.api.deps import require_admin, get_product_service
from app.services.product_service import ProductService
from app.models.product import ProductCreate, ProductUpdate, ProductOut

router = APIRouter()

@router.post("", response_model=ProductOut, status_code=status.HTTP_201_CREATED, 
             dependencies=[Depends(require_admin)])
async def create_product(payload: ProductCreate, 
                         service: ProductService = Depends(get_product_service)) -> ProductOut:
    return await service.create_product(payload)

@router.get("", response_model=List[ProductOut])
async def list_products(category: Optional[str] = Query(default=None), 
                        service: ProductService = Depends(get_product_service)) -> List[ProductOut]:
    return await service.list_products(category=category)

@router.get("/{product_id}", response_model=ProductOut)
async def get_product(product_id: str, 
                      service: ProductService = Depends(get_product_service)) -> ProductOut:
    product = await service.get_product(product_id)
    if not product:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Product not found")
    return product

@router.put("/{product_id}", response_model=ProductOut, dependencies=[Depends(require_admin)])
async def update_product(product_id: str, payload: ProductUpdate, 
                         service: ProductService = Depends(get_product_service)) -> ProductOut:
    return await service.update_product(product_id, payload)

@router.delete("/{product_id}", status_code=status.HTTP_204_NO_CONTENT, 
               dependencies=[Depends(require_admin)])
async def delete_product(product_id: str, 
                         service: ProductService = Depends(get_product_service)) -> None:
    await service.delete_product(product_id)
```

**Endpoints:**
- `GET /products` - List all products (optional category filter)
- `GET /products/{product_id}` - Get single product
- `POST /products` - Create product (admin only)
- `PUT /products/{product_id}` - Update product (admin only)
- `DELETE /products/{product_id}` - Delete product (admin only)

### Dependency Injection

```python
# backend/app/api/deps.py
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPAuthorizationCredentials, HTTPBearer
from typing import Optional
from app.core.security import decode_jwt
from app.core.config import settings
from app.models.user import CurrentUser, Role
from app.services.product_service import ProductService
from app.repositories.ddb_products import DynamoProductsRepository

security = HTTPBearer(auto_error=False)

def get_products_repo() -> DynamoProductsRepository:
    return DynamoProductsRepository(settings.aws_region, settings.products_table)

def get_product_service(repo: DynamoProductsRepository = Depends(get_products_repo)) -> ProductService:
    return ProductService(repo)

def get_current_user(creds: Optional[HTTPAuthorizationCredentials] = Depends(security)) -> CurrentUser:
    if creds is None:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Missing credentials")
    payload = decode_jwt(creds.credentials)
    sub = payload.get("sub")
    role = payload.get("role", Role.user.value)
    if not sub:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid token")
    return CurrentUser(user_id=sub, role=Role(role))

def require_admin(role: Role = Depends(get_current_user_role)) -> None:
    if role != Role.admin:
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN, detail="Admin only")
```

### JWT Security Implementation

```python
# backend/app/core/security.py
from datetime import datetime, timedelta, timezone
from typing import Dict, Any
from jose import jwt
from passlib.context import CryptContext
from app.core.config import settings

ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 60 * 6  # 6 hours

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def create_access_token(subject: str, role: str) -> str:
    expire = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode: Dict[str, Any] = {"sub": subject, "role": role, "exp": expire}
    return jwt.encode(to_encode, settings.jwt_secret, algorithm=ALGORITHM)

def decode_jwt(token: str) -> Dict[str, Any]:
    return jwt.decode(token, settings.jwt_secret, algorithms=[ALGORITHM])

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)
```

**Security Features:**
- JWT tokens with 6-hour expiration
- Password hashing using bcrypt
- Role-based access control (admin/user)
- Token validation on every protected endpoint

### Correlation ID Middleware

```python
# backend/app/api/middleware.py
import uuid
import logging
from starlette.middleware.base import BaseHTTPMiddleware
from starlette.requests import Request

logger = logging.getLogger(__name__)

class CorrelationMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        correlation_id = request.headers.get("X-Correlation-ID", str(uuid.uuid4()))
        
        logger.info(f"Request started", extra={
            "correlation_id": correlation_id,
            "method": request.method,
            "path": request.url.path
        })
        
        response = await call_next(request)
        response.headers["X-Correlation-ID"] = correlation_id
        
        logger.info(f"Request completed", extra={
            "correlation_id": correlation_id,
            "status_code": response.status_code
        })
        
        return response

def add_correlation_middleware(app):
    app.add_middleware(CorrelationMiddleware)
```

**Purpose:**
- Track requests across logs using correlation IDs
- Useful for debugging and tracing requests in distributed systems

### Dockerfile

```dockerfile
# backend/Dockerfile
# Lambda base image for Python 3.10
FROM public.ecr.aws/lambda/python:3.10

# Copy and install requirements
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt -t "${LAMBDA_TASK_ROOT}"

# Copy application code
COPY app/ ${LAMBDA_TASK_ROOT}/app/

# Set the handler
CMD ["app.lambda_handler.handler"]
```

**Key Points:**
- Uses official AWS Lambda Python 3.10 base image
- Installs dependencies to `${LAMBDA_TASK_ROOT}`
- Copies application code
- Sets handler to `app.lambda_handler.handler`

### Local Development

To run locally:

```bash
# Install dependencies
make install

# Run with uvicorn
make run-local
# or
uvicorn backend.app.main:app --reload
```

Access Swagger documentation at: `http://localhost:8000/docs`

![FastAPI Swagger](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.1-FastAPI-Application-Structure/fastapi-swagger.png)

{{% notice info %}}
The layered architecture (API → Service → Repository) makes the code testable, maintainable, and follows separation of concerns principles. Each layer has a single responsibility.
{{% /notice %}}


