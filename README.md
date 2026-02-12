# Internal-Wallet-Service

📘 Wallet Service – Internal Credit Ledger System
1. Project Overview

This project implements a closed-loop internal wallet service designed for high-traffic applications such as gaming platforms or loyalty rewards systems.

The wallet system manages application-specific credits (e.g., Gold Coins, Reward Points) that:

Exist only within the application

Cannot be transferred externally

Must maintain strict data integrity

Must never allow negative balances

Must not lose transactions under heavy concurrency

The system guarantees:

ACID-compliant transaction handling

Concurrency-safe balance updates

Idempotent request handling

Full auditability using a double-entry ledger

2. Technology Stack
Backend

Java 21

Spring Boot 3

Spring Data JPA

Hibernate ORM

Database

MySQL 8+

3. Why This Technology?
Java + Spring Boot

Industry-standard framework for scalable backend systems

Strong transaction management support

Excellent support for REST APIs

Mature and production-ready ecosystem

MySQL

ACID-compliant relational database

Supports row-level locking

Reliable under concurrent write operations

Widely used in financial systems

JPA + Hibernate

Simplifies database interactions

Supports pessimistic locking

Provides declarative transaction management

4. How to Spin Up the Database and Run the Seed Script
Step 1: Prerequisites

Ensure the following are installed:

Java 21

Maven

MySQL Server (8+)

Step 2: Create Database

Login to MySQL and execute:

CREATE DATABASE wallet_db;

Step 3: Configure Application Properties

Open:

src/main/resources/application.properties


Add:

spring.datasource.url=jdbc:mysql://localhost:3306/wallet_db
spring.datasource.username=root
spring.datasource.password=your_password

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect


Replace your_password with your MySQL password.

Step 4: Run the Application

From the project root directory:

mvn clean install
mvn spring-boot:run


If successful, you will see:

Tomcat started on port 8080
Started WalletServiceApplication


Application will run at:

http://localhost:8080

Step 5: Run Seed Script

A seed.sql file is provided in the project root.

To execute:

mysql -u root -p wallet_db < seed.sql


Or inside MySQL:

USE wallet_db;
SOURCE path_to_seed.sql;


The seed script inserts:

Asset Types (e.g., Gold Coins)

System/Treasury account

Two sample users

Wallets with initial balances

5. API Endpoints

Base URL:

http://localhost:8080/api/wallets

5.1 Wallet Top-up

POST /topup

{
  "userId": 1,
  "assetTypeId": 1,
  "amount": 100,
  "idempotencyKey": "txn-1001"
}

5.2 Bonus / Incentive

POST /bonus

{
  "userId": 1,
  "assetTypeId": 1,
  "amount": 50,
  "idempotencyKey": "txn-1002"
}

5.3 Spend / Purchase

POST /spend

{
  "userId": 1,
  "assetTypeId": 1,
  "amount": 30,
  "idempotencyKey": "txn-1003"
}

5.4 Check Balance

GET

/api/wallets/{userId}/{assetId}/balance


Example:

/api/wallets/1/1/balance


Response:

{
  "balance": 120
}

6. Concurrency Handling Strategy

Concurrency control is critical in wallet systems.

The system ensures safe balance updates using the following mechanisms:

6.1 ACID Transactions

All transaction logic is wrapped inside:

@Transactional


This ensures:

Atomicity

Consistency

Isolation

Durability

If any operation fails, the entire transaction is rolled back.

6.2 Row-Level Locking (Pessimistic Locking)

Wallet records are fetched using:

@Lock(LockModeType.PESSIMISTIC_WRITE)


This ensures:

Only one transaction can modify a wallet row at a time

Prevents race conditions

Prevents lost updates

Prevents double spending

6.3 Idempotency Handling

Each transaction request must contain a unique:

idempotencyKey


A unique database constraint ensures:

Duplicate requests are rejected

Retry requests do not double process

Safe integration with external payment systems

This protects against:

Network retries

Duplicate API calls

Payment gateway timeouts

6.4 Double-Entry Ledger Architecture

Instead of directly modifying balances:

Each transaction creates:

One debit entry

One credit entry

Ledger entries are permanently stored

Wallet balance reflects ledger state

Benefits:

Full audit trail

Traceability

Reconciliation support

Financial integrity

7. Data Integrity Guarantees

The system guarantees:

No negative balances

No lost transactions

No duplicate processing

Full transactional consistency

Audit-friendly ledger system

Safe under concurrent access

8. Design Principles Followed

Constructor-based dependency injection

Clean layered architecture

Controller

Service

Repository

Entity

RESTful API design

Database-level constraints

Production-grade transaction management

9. Future Enhancements

Potential improvements include:

Deadlock retry mechanism

Redis caching layer

Docker containerization

Cloud deployment (AWS/GCP/Azure)

Monitoring & logging improvements

Horizontal scaling support

10. Conclusion

This wallet service demonstrates:

Strong concurrency control

ACID-compliant transaction management

Idempotent transaction processing

Double-entry ledger architecture

Production-ready backend design

The system is suitable for high-traffic internal credit platforms where data integrity is critical.
