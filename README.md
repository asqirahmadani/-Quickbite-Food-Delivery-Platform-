# 🍔 QuickBite Food Delivery Platform

A comprehensive microservices-based food delivery platform with an API Gateway implementing business process orchestration. This gateway uses the **Orchestrator Pattern** to manage complex business workflows, coordinating calls to multiple microservices to complete end-to-end processes.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Bun](https://img.shields.io/badge/Bun-1.0+-000000?logo=bun)](https://bun.sh)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue?logo=typescript)](https://www.typescriptlang.org/)

## 📋 Table of Contents

- [Architecture Overview](#-architecture-overview)
- [Technologies](#-technologies)
- [Microservices](#-microservices)
- [Key Features](#-key-features)
- [Orchestration Workflows](#-orchestration-workflows)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Running the Application](#-running-the-application)
- [API Documentation](#-api-documentation)
- [Testing](#-testing)
- [Security](#-security)
- [Contributing](#-contributing)

## 🏗️ Architecture Overview

```
┌─────────────┐
│   Client    │
└──────┬──────┘
       │
┌──────▼───────────────────┐
│   API Gateway            │
│   (Orchestrator)         │
└──────┬───────────────────┘
       │
       ├─────────┬─────────┬──────────┬──────────┬──────────┐
       │         │         │          │          │          │
   ┌───▼───┐ ┌──▼──┐  ┌───▼────┐ ┌──▼─────┐ ┌──▼─────┐ ┌──▼────┐
   │ User  │ │Rest │  │ Order  │ │Payment │ │Delivery│ │Review │
   │Service│ │aurant│ │Service │ │Service │ │Service │ │Service│
   └───────┘ └─────┘  └────────┘ └────────┘ └────────┘ └───────┘
       │         │         │          │          │          │
   ┌───▼─────────▼─────────▼──────────▼──────────▼──────────▼───┐
   │                 PostgreSQL + RabbitMQ                      │
   └────────────────────────────────────────────────────────────┘
```

## 🛠 Technologies

### Core Stack
- **[Bun](https://bun.sh)** - High-performance JavaScript runtime and package manager
- **[Elysia](https://elysiajs.com)** - Fast and type-safe web framework for Bun
- **[TypeScript](https://www.typescriptlang.org/)** - Type-safe JavaScript development

### Database & ORM
- **PostgreSQL** - Primary relational database
- **Drizzle/TypeORM** - Database ORM and migrations

### Message Queue
- **RabbitMQ** - Message broker for async service communication
- **AMQP** - Advanced Message Queueing Protocol

### Payment Integration
- **Stripe** - Payment gateway
  - Payment Intents API
  - Webhooks for payment status updates
  - Refund and dispute handling
- **Payment Gateway SDK** - Custom payment abstraction layer

### Notifications
- **Nodemailer** - Email delivery service
- **SMTP** - Email protocol integration
- **Email Templates** - HTML/Text email templating
- **Push Notifications** - Web app notifications

### Security & Authentication
- **JWT** - JSON Web Tokens for stateless authentication
- **bcrypt** - Password hashing and validation
- **CORS** - Cross-Origin Resource Sharing
- **Helmet** - Security headers middleware
- **Rate Limiting** - API abuse prevention

### Testing & Documentation
- **Bun Test** - Unit and integration testing
- **Swagger** - API documentation and testing interface
- **@elysiajs/swagger** - Elysia Swagger integration

## 🎯 Microservices

The platform consists of **8 microservices**:

| Service | Port | Description |
|---------|------|-------------|
| **API Gateway** | 3000 | Main orchestrator for business processes |
| **User Service** | 3001 | User management and authentication |
| **Restaurant Service** | 3002 | Restaurant and menu management |
| **Order Service** | 3003 | Order processing and lifecycle |
| **Payment Service** | 3004 | Payment processing via Stripe |
| **Delivery Service** | 3005 | Delivery assignment and tracking |
| **Review Service** | 3006 | Reviews and ratings |
| **Notification Service** | 3007 | Email and push notifications |

## ✨ Key Features

### Orchestration Pattern
- ✅ **Transaction Management** - Unique transaction ID for every orchestrated process
- ✅ **Automatic Compensation** - Rollback on failures to maintain consistency
- ✅ **Retry Mechanism** - Configurable retry with exponential backoff
- ✅ **Distributed Tracing** - Request/response correlation across services
- ✅ **Circuit Breaker** - Graceful degradation on persistent failures

### Business Capabilities
- 👥 User registration and authentication
- 🍽️ Restaurant and menu management
- 🛒 Order placement and tracking
- 💳 Secure payment processing
- 🚚 Delivery assignment and tracking
- ⭐ Review and rating system
- 📧 Email and push notifications

## 🔄 Orchestration Workflows

### 1. Order Creation Flow
```
POST /api/orders
├── Step 1: Create order (PENDING_PAYMENT) → order-service
├── Step 2: Generate payment intent → payment-service
├── Step 3: Send payment notification → notification-service
└── Compensation: Cancel order if any step fails
```

### 2. Order Acceptance Flow
```
POST /api/orders/{id}/accept
├── Step 1: Update order status → order-service
├── Step 2: Create delivery assignment → delivery-service
├── Step 3: Notify customer → notification-service
├── Step 4: Notify restaurant → notification-service
└── Compensation: Reset order status if any step fails
```

### 3. Review Submission Flow
```
POST /api/reviews
├── Step 1: Create review → review-service
├── Step 2: Update restaurant rating → restaurant-service
├── Step 3: Update driver rating → user-service
└── Compensation: Delete review if rating updates fail
```

## 📦 Prerequisites

Before you begin, ensure you have the following installed:

- **[Bun](https://bun.sh)** v1.0 or higher
- **PostgreSQL** v14 or higher
- **RabbitMQ** v3.9 or higher
- **Node.js** v18+ (for some microservices)

### Required Services Running

All 7 microservices must be running on their respective ports before starting the gateway:
- User Service (port 3001)
- Restaurant Service (port 3002)
- Order Service (port 3003)
- Payment Service (port 3004)
- Delivery Service (port 3005)
- Review Service (port 3006)
- Notification Service (port 3007)

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/asqirahmadani/-Quickbite-Food-Delivery-Platform-.git
cd -Quickbite-Food-Delivery-Platform-
```

### 2. Install Gateway Dependencies

```bash
cd quickbite-gateway
bun install
```

### 3. Install All Microservices

```bash
# Install each microservice
cd ../user-service && bun install
cd ../restaurant-service && bun install
cd ../order-service && bun install
cd ../payment-service && bun install
cd ../delivery-service && bun install
cd ../review-service && bun install
cd ../notification-service && bun install
```

### 4. Setup Databases

```bash
# Run PostgreSQL migrations for each service
cd user-service && bun run migrate
cd ../restaurant-service && bun run migrate
cd ../order-service && bun run migrate
# ... repeat for other services
```

## ⚙️ Configuration

### Environment Variables

Create `.env` file in the gateway root:

```env
# Gateway Configuration
PORT=3000
NODE_ENV=development

# Service URLs
USER_SERVICE_URL=http://localhost:3001
RESTAURANT_SERVICE_URL=http://localhost:3002
ORDER_SERVICE_URL=http://localhost:3003
PAYMENT_SERVICE_URL=http://localhost:3004
DELIVERY_SERVICE_URL=http://localhost:3005
REVIEW_SERVICE_URL=http://localhost:3006
NOTIFICATION_SERVICE_URL=http://localhost:3007

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/quickbite

# Redis
REDIS_URL=redis://localhost:6379

# RabbitMQ
RABBITMQ_URL=amqp://localhost:5672

# Orchestration Settings
ORCHESTRATION_TIMEOUT_MS=30000
MAX_RETRY_ATTEMPTS=3
COMPENSATION_ENABLED=true

# JWT
JWT_SECRET=your-super-secret-jwt-key
JWT_EXPIRATION=24h
JWT_REFRESH_EXPIRATION=7d

# Stripe
STRIPE_SECRET_KEY=sk_test_your_stripe_key
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
```

Copy for each microservice:
```bash
cp .env.example .env
# Edit each .env file accordingly
```

## 🎯 Running the Application

### Development Mode

Start all services in development mode:

```bash
# Terminal 1: Start Gateway
cd quickbite-gateway
bun run dev

# Terminal 2: Start User Service
cd user-service
bun run dev

# Terminal 3-8: Start other services
# Repeat for each microservice
```

### Production Mode

```bash
# Start Gateway
cd quickbite-gateway
bun run start

# Start each microservice
cd user-service && bun run start
# Repeat for other services
```

### Using Docker (Recommended)

```bash
# Start all services with Docker Compose
docker-compose up -d

# Check logs
docker-compose logs -f

# Stop all services
docker-compose down
```

## 📚 API Documentation

### Access Swagger UI

Once the gateway is running, access the interactive API documentation:

- **Swagger UI**: http://localhost:3000/docs
- **OpenAPI JSON**: http://localhost:3000/docs/json

### Quick Examples

#### Create Order

```bash
curl -X POST http://localhost:3000/api/orders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "restaurantId": "uuid-here",
    "items": [
      {"menuItemId": "uuid-here", "quantity": 2}
    ],
    "deliveryAddress": "123 Main St, City"
  }'
```

#### Submit Review

```bash
curl -X POST http://localhost:3000/api/reviews \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "orderId": "uuid-here",
    "restaurantId": "uuid-here",
    "restaurantRating": 5,
    "foodQuality": 4,
    "deliveryTime": 5,
    "restaurantComment": "Excellent food!"
  }'
```

### Health Check

```bash
curl http://localhost:3000/health
```

Response:
```json
{
  "status": "healthy",
  "timestamp": "2025-10-20T00:00:00.000Z",
  "services": {
    "user": "healthy",
    "restaurant": "healthy",
    "order": "healthy",
    "payment": "healthy",
    "delivery": "healthy",
    "review": "healthy",
    "notification": "healthy"
  },
  "orchestration": {
    "compensationEnabled": true,
    "maxRetryAttempts": 3,
    "timeoutMs": 30000
  }
}
```

## 🧪 Testing

### Run All Tests

```bash
# Run gateway tests
cd quickbite-gateway
bun test

# Run service tests
cd user-service && bun test
cd ../restaurant-service && bun test
# ... etc
```

### Run Specific Test Suite

```bash
bun test orchestration
bun test integration
bun test unit
```

### Test Coverage

```bash
bun test --coverage
```

## 🔐 Security

### Authentication
- JWT-based authentication with refresh tokens
- Role-based access control (RBAC)
- Password hashing with bcrypt

### API Security
- Rate limiting per user/IP
- Request validation with TypeScript schemas
- CORS configuration
- Secure headers (CSP, X-Frame-Options, etc.)

### Payment Security
- PCI-compliant payment processing via Stripe
- Payment data encryption
- Webhook signature verification

### Audit & Logging
- Structured logging with Pino
- Audit trails for sensitive operations
- Transaction-level tracing

## 🔍 Monitoring & Observability

### Transaction Tracking

Every orchestrated request returns a `transactionId` for tracking:

```json
{
  "transactionId": "txn_abc123def456",
  "status": "success",
  "data": { ... }
}
```

### Metrics Endpoint

Access metrics at: http://localhost:3000/metrics

### Error Handling

- **4xx errors**: Client errors (validation, authentication)
- **5xx errors**: Server errors (service failures, timeouts)
- **Compensation**: Automatic rollback on orchestration failures
- **Idempotency**: Safe retry mechanisms for critical operations

## 📁 Project Structure

```
services/
├── quickbite-gateway/          # API Gateway (Orchestrator)
│   ├── src/
│   │   ├── orchestrators/      # Orchestration workflows
│   │   ├── routes/             # API routes
│   │   ├── middleware/         # Auth, validation, etc.
│   │   ├── utils/              # Helpers
│   │   └── index.ts            # Main entry point
│   └── package.json
├── user-service/               # User management
├── restaurant-service/         # Restaurant & menu
├── order-service/              # Order processing
├── payment-service/            # Payment via Stripe
├── delivery-service/           # Delivery tracking
├── review-service/             # Reviews & ratings
└── notification-service/       # Email & push notifications
```

## 🚀 Performance

- **Connection pooling** for downstream services
- **Configurable timeouts** and retry policies
- **Async/non-blocking** architecture
- **Efficient error propagation**
- **Message queuing** with RabbitMQ

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Add tests for new functionality
4. Commit your changes (`git commit -m 'Add amazing feature'`)
5. Push to the branch (`git push origin feature/amazing-feature`)
6. Open a Pull Request

Please ensure:
- All tests pass
- Code follows TypeScript best practices
- Documentation is updated
- Commit messages are descriptive

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👤 Author

**Asqi Rahmadani**
- GitHub: [@asqirahmadani](https://github.com/asqirahmadani)

## 📞 Support

For issues, questions, or contributions:
- Create an [Issue](https://github.com/asqirahmadani/-Quickbite-Food-Delivery-Platform-/issues)
- Submit a [Pull Request](https://github.com/asqirahmadani/-Quickbite-Food-Delivery-Platform-/pulls)

## 🙏 Acknowledgments

- Built with [Bun](https://bun.sh) and [Elysia](https://elysiajs.com)
- Payment processing by [Stripe](https://stripe.com)
- Inspired by modern microservices architecture patterns

---

⭐ If you find this project helpful, please consider giving it a star!
