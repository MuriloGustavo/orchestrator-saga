# Microservices Architecture: Orchestrated Saga Pattern

Orchestrated Saga Pattern implementation using Java 17, Spring Boot 3, Apache Kafka, PostgreSQL, and MongoDB to handle distributed transactions in a microservices architecture.

---

## 🚀 Technologies

- Java 17
- Spring Boot 3
- Apache Kafka
- API REST
- PostgreSQL
- MongoDB
- Docker
- Docker Compose
- Redpanda Console

---

## 🧩 Proposed Architecture

The image below represents the proposed architecture.

![Orchestrated Saga](docs/images/orchestrated-saga-architecture.png)

In our architecture, we will have 5 services:

- Order-Service: microservice responsible only for generating an initial order and receiving a notification. Here we will have REST endpoints to start the process and retrieve event data. The database used will be MongoDB.
- Orchestrator-Service: microservice responsible for orchestrating the entire Saga execution flow. It will know which microservice was executed and in what state, and to which microservice the next one will be sent. This microservice will also save the process of events. This service does not have a database.
- Product-Validation-Service: microservice responsible for validating whether the product entered in the order exists and is valid. This microservice will store the validation of a product for the ID of an order. The database used will be PostgreSQL.
- Payment-Service: microservice responsible for making a payment based on the unit values ​​and quantities entered in the order. This microservice will store the payment information of an order. The database used will be PostgreSQL.
- Inventory-Service: microservice responsible for performing the stock reduction of products in an order. This microservice will store the product reduction information for an order ID. The database used will be PostgreSQL.

All services in the architecture will be started through the docker-compose.yml file.

---

## ⚙️ Project Execution

To run the applications, you will need to have the following installed:

- Docker
- Java 17
- Gradle 7.6 or higher

Simply run the command in the repository's root directory:

```bash
docker-compose up --build -d
```

---

## 🔌 Accessing the Application

To access the application and create an order, open:

```bash
http://localhost:3000/swagger-ui.html
```

---

### 🧭 Service Ports

The services are available on the following ports:

- Order Service: 3000
- Orchestrator Service: 8080
- Product Validation Service: 8090
- Payment Service: 8091
- Inventory Service: 8092
- Apache Kafka: 9092
- Redpanda Console: 8081
- PostgreSQL (Product DB): 5432
- PostgreSQL (Payment DB): 5433
- PostgreSQL (Inventory DB): 5434
- MongoDB (Order DB): 27017

---

### 📬 Kafka Topics Monitoring (Redpanda Console)

To access the Redpanda Console and monitor topics or publish events, open:

```bash
http://localhost:8081
```
---

## 📁 API Data

To interact with the saga flow, it is important to understand the available products and the expected request payload.

### 🛍️ Available Products

The following products are pre-registered in the system:

- COMIC_BOOKS (4 units in stock)
- BOOKS (2 units in stock)
- MOVIES (5 units in stock)
- MUSIC (9 units in stock)

### ▶️ Start Saga (Create Order)

**Endpoint:**

```bash
POST http://localhost:3000/api/order
```

**Request Body:**
```json
{
  "products": [
    {
      "product": {
        "code": "COMIC_BOOKS",
        "unitValue": 15.50
      },
      "quantity": 3
    },
    {
      "product": {
        "code": "BOOKS",
        "unitValue": 9.90
      },
      "quantity": 1
    }
  ]
}
```

**Response:**

```json
{
  "id": "64429e987a8b646915b3735f",
  "products": [
    {
      "product": {
        "code": "COMIC_BOOKS",
        "unitValue": 15.5
      },
      "quantity": 3
    },
    {
      "product": {
        "code": "BOOKS",
        "unitValue": 9.9
      },
      "quantity": 1
    }
  ],
  "createdAt": "2023-04-21T14:32:56.335943085",
  "transactionId": "1682087576536_99d2ca6c-f074-41a6-92e0-21700148b519"
}
```

### 🔍 Retrieve Saga Status

You can retrieve the saga execution details using either the orderId or the transactionId:

**Endpoint:**

```bash
GET http://localhost:3000/api/event?orderId=64429e987a8b646915b3735f

GET http://localhost:3000/api/event?transactionId=1682087576536_99d2ca6c-f074-41a6-92e0-21700148b519
```

**Response:**

```json
{
  "id": "64429e9a7a8b646915b37360",
  "transactionId": "1682087576536_99d2ca6c-f074-41a6-92e0-21700148b519",
  "orderId": "64429e987a8b646915b3735f",
  "payload": {
    "id": "64429e987a8b646915b3735f",
    "products": [
      {
        "product": {
          "code": "COMIC_BOOKS",
          "unitValue": 15.5
        },
        "quantity": 3
      },
      {
        "product": {
          "code": "BOOKS",
          "unitValue": 9.9
        },
        "quantity": 1
      }
    ],
    "totalAmount": 56.40,
    "totalItems": 4,
    "createdAt": "2023-04-21T14:32:56.335943085",
    "transactionId": "1682087576536_99d2ca6c-f074-41a6-92e0-21700148b519"
  },
  "source": "ORCHESTRATOR",
  "status": "SUCCESS",
  "eventHistory": [
    {
      "source": "ORCHESTRATOR",
      "status": "SUCCESS",
      "message": "Saga started!",
      "createdAt": "2023-04-21T14:32:56.78770516"
    },
    {
      "source": "PRODUCT_VALIDATION_SERVICE",
      "status": "SUCCESS",
      "message": "Products validated successfully!",
      "createdAt": "2023-04-21T14:32:57.169378616"
    },
    {
      "source": "PAYMENT_SERVICE",
      "status": "SUCCESS",
      "message": "Payment processed successfully!",
      "createdAt": "2023-04-21T14:32:57.617624655"
    },
    {
      "source": "INVENTORY_SERVICE",
      "status": "SUCCESS",
      "message": "Inventory updated successfully!",
      "createdAt": "2023-04-21T14:32:58.139176809"
    },
    {
      "source": "ORCHESTRATOR",
      "status": "SUCCESS",
      "message": "Saga completed successfully!",
      "createdAt": "2023-04-21T14:32:58.248630293"
    }
  ],
  "createdAt": "2023-04-21T14:32:58.28"
}
```

---

## 👤 Author

Developed by **Murilo Gustavo**

- 💼 Backend Developer (Java & Spring)
- 🌎 Microservices | Event-Driven Architecture | Kafka

🔗 LinkedIn: https://www.linkedin.com/in/murillogustavo 
🔗 GitHub: https://github.com/MuriloGustavo