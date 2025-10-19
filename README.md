# Wallet Management System API  Wallet Management System API wallet

This document provides technical documentation for the Wallet Management System, a RESTful API for core fintech operations.

-----

## 1\. Overview

The Wallet Management System is a backend service that provides fundamental digital wallet functionality. It allows for the creation of users with associated wallets, balance management, fund transfers, and transaction history tracking. This project focuses on clean, testable, and robust backend logic.

-----

## 2\. Core Features

* **User Registration**: Creates a new user and automatically provisions a digital wallet with a zero balance.
* **View Wallet Balance**: Allows a user to check the current balance of their wallet.
* **Deposit & Withdraw**: Enables adding funds to and removing funds from a wallet.
* **Fund Transfer**: Facilitates the transfer of funds between two existing wallets.
* **Transaction History**: Provides a paginated list of all financial transactions for a given wallet.

-----

## 3\. API Endpoints

### User Management

#### `POST /api/users`

Creates a new user and their associated wallet.

* **Request Body**:

  ```json
  {
    "name": "John Doe",
    "email": "john.doe@example.com"
  }
  ```

* **Success Response** (`201 Created`):

  ```json
  {
    "id": 1,
    "name": "John Doe",
    "email": "john.doe@example.com",
    "walletId": 101
  }
  ```

* **Error Response** (`400 Bad Request`):

  ```json
  {
    "status": 400,
    "error": "Bad Request",
    "message": "Validation error: name must not be blank"
  }
  ```

### Wallet Operations

| Method | Endpoint                                   | Description                               |
| :----- | :----------------------------------------- | :---------------------------------------- |
| `GET`  | `/api/wallets/{walletId}/balance`          | Retrieves the current balance of a wallet. |
| `POST` | `/api/wallets/{walletId}/deposit`          | Adds funds to a wallet.                   |
| `POST` | `/api/wallets/{walletId}/withdraw`         | Withdraws funds from a wallet.            |

* **Deposit/Withdraw Request Body**:

  ```json
  {
    "amount": 50.00
  }
  ```

* **Balance Success Response** (`200 OK`):

  ```json
  {
    "walletId": 101,
    "balance": 150.00,
    "currency": "PHP"
  }
  ```

### Transactions

#### `POST /api/transactions/transfer`

Transfers a specified amount from one wallet to another.

* **Request Body**:

  ```json
  {
    "fromWalletId": 101,
    "toWalletId": 102,
    "amount": 25.50
  }
  ```

* **Success Response** (`200 OK`):

  ```json
  {
    "transactionId": 5003,
    "fromWalletId": 101,
    "toWalletId": 102,
    "amount": 25.50,
    "timestamp": "2025-10-20T14:48:00.123Z"
  }
  ```

#### `GET /api/wallets/{walletId}/history`

Retrieves a paginated list of transactions for a specific wallet.

* **Query Parameters**:

    * `page` (optional, default: 0): The page number to retrieve.
    * `size` (optional, default: 10): The number of transactions per page.

* **Success Response** (`200 OK`):

  ```json
  {
    "content": [
      {
        "transactionId": 5003,
        "type": "TRANSFER_DEBIT",
        "amount": 25.50,
        "timestamp": "2025-10-20T14:48:00.123Z"
      },
      {
        "transactionId": 5002,
        "type": "DEPOSIT",
        "amount": 100.00,
        "timestamp": "2025-10-20T11:30:00.000Z"
      }
    ],
    "pageable": {
      "pageNumber": 0,
      "pageSize": 10
    },
    "totalPages": 1,
    "totalElements": 2
  }
  ```

-----

## 4\. Data Models

### User

Represents a registered user in the system.

| Field | Type   | Description               |
| :---- | :----- | :------------------------ |
| `id`  | Long   | Unique identifier for the user. |
| `name`| String | Full name of the user.    |
| `email`| String | Unique email of the user. |

### Wallet

Represents a user's digital wallet, holding their funds. There is a **one-to-one** relationship between a `User` and a `Wallet`.

| Field   | Type        | Description                     |
| :------ | :---------- | :------------------------------ |
| `id`    | Long        | Unique identifier for the wallet. |
| `balance`| BigDecimal | The current amount of money held. |
| `user`  | User        | The user who owns the wallet.     |

### Transaction

Records every financial action that occurs in the system. There is a **one-to-many** relationship between a `Wallet` and its `Transactions`.

| Field     | Type          | Description                                    |
| :-------- | :------------ | :--------------------------------------------- |
| `id`      | Long          | Unique identifier for the transaction.         |
| `wallet`  | Wallet        | The wallet involved in the transaction.        |
| `type`    | Enum          | The type of transaction (e.g., `DEPOSIT`, `WITHDRAW`, `TRANSFER`). |
| `amount`  | BigDecimal    | The value of the transaction.                  |
| `timestamp`| LocalDateTime | When the transaction occurred.                 |

-----

## 5\. Technology Stack

* **Framework**: Spring Boot (Web + JPA)
* **Database**: H2 (In-Memory for development)
* **Language**: Java 17+
* **Build Tool**: Maven
* **Core Concepts**:
    * RESTful API Design
    * Data Transfer Objects (DTOs) with Validation (`@Valid`)
    * JPA for Object-Relational Mapping (ORM)
    * Centralized Exception Handling
    * Lombok for boilerplate code reduction
    * JUnit 5 for testing

-----

## 6\. Getting Started

### Prerequisites

* Java Development Kit (JDK) 17 
* Apache Maven

### Running the Application

1.  **Clone the repository**:

    ```bash
    git clone <repository-url>
    cd wallet-management-system
    ```

2.  **Build and run the application using Maven**:

    ```bash
    mvn spring-boot:run
    ```

3.  The API will be available at `http://localhost:8080`.

