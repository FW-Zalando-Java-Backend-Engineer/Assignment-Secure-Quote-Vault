# 💡 Assignment: Secure Quote Vault

## 🧠 Background

In modern web applications, it's essential to **secure data access** and **differentiate users by roles**. Whether you're building a banking app, a school system, or a dashboard, you need secure authentication and role-based authorization.

In this assignment, you will build a **Secure Quote Vault** — a Spring Boot application that allows users to **sign up**, **sign in**, and **access random quotes** from an **external quote API**. Only authenticated users will be allowed to retrieve a quote, and the system will support multiple roles like `USER`, `ADMIN`, `MODERATOR`, and `SUPER_ADMIN`.

You'll learn how to:

* Secure your API using **JWT tokens**
* Use **MongoDB Atlas** for storing user data
* Build a **cleanly structured Spring Boot app**
* Use **RestTemplate** to fetch data from an external API
* **Dockerize** the full stack using multistage builds and Docker Compose
* Externalize configs using a `.env` file

This is a complete **end-to-end full-stack backend project**. Take your time and follow every instruction carefully.

---

## 📦 Requirements

You must build a secure application that:

* Allows user **registration** and **login**
* Uses **JWT tokens** to protect endpoints
* Provides a `GET /api/quotes/random` endpoint that:

  * Fetches a **random quote** from the external API: [https://dummyjson.com/quotes/random](https://dummyjson.com/quotes/random)
  * Returns the quote to the user
* Uses **MongoDB Atlas** to persist users
* Differentiates users by role (`USER`, `ADMIN`, `MODERATOR`, `SUPER_ADMIN`)
* Is fully **Dockerized** with Dockerfile and Docker Compose
* Loads configurations via `.env`

---

## 🧭 Step-by-Step Guide

### ✅ Step 0: Spring Initializr

Go to [https://start.spring.io](https://start.spring.io)

**Project Settings:**

* Group: `org.example`
* Artifact: `secure-quote-vault`
* Name: `SecureQuoteVault`
* Language: Java
* Java Version: 17

**Dependencies:**

* Spring Web
* Spring Security
* Spring Data MongoDB
* Lombok
* Spring Boot DevTools
* Validation

Click **Generate**, unzip, and open the project in IntelliJ.

---

### 📁 Step 1: Folder Structure

Inside `src/main/java/org/example/secure_quote_vault`, create the following package structure:

```
- alias
- config
- controller
- dto
- mapper
- model
- repository
- security
- service
```

### 📂 What Goes Where?

#### `alias`

* `SecurityUser.java`: extends `org.springframework.security.core.userdetails.User`

  * Used by Spring Security for authentication logic.

#### `config`

* JWT token filter
* Security configuration
* Bean config for RestTemplate

#### `controller`

* AuthController → `/api/auth/signup`, `/api/auth/login`
* QuoteController → `/api/quotes/random` (authenticated users only)

#### `dto`

* SignupRequest
* LoginRequest
* JwtResponse
* QuoteResponse (for returning quotes)

#### `mapper`

* DTO ↔ Model mapping helpers (optional but recommended)

#### `model`

* `User` model → MongoDB document
* `Role` enum → USER, ADMIN, MODERATOR, SUPER\_ADMIN
* `Quote` model → stores quote text, author, etc.

#### `repository`

* `UserRepository` extends `MongoRepository`

#### `security`

* `JwtUtils` → for generating and validating JWT
* `JwtFilter` → for checking JWT in requests
* `UserDetailsServiceImpl` → loads users from the DB

#### `service`

* `AuthService` → registration and login logic
* `QuoteService` → fetches a random quote from [https://dummyjson.com/quotes/random](https://dummyjson.com/quotes/random)

---

### ⚙️ Step 2: `application.properties`

```properties
spring.application.name=SecureQuoteVault
server.port=${PORT:8585}

spring.data.mongodb.uri=${MONGODB_URI}
spring.data.mongodb.database=${MONGODB_DATABASE}

jwt.secret=${JWT_SECRET}

quote.api.url=${QUOTE_API_URL}
```

---

### 🔐 Step 3: `.env` File

Create a `.env` file in the root:

```env
PORT=8585
MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/quotes?retryWrites=true&w=majority
MONGODB_DATABASE=quotes
JWT_SECRET=yourSuperSecretKeyHere123!
QUOTE_API_URL=https://dummyjson.com/quotes/random
```

> Don't forget to add `.env` to your `.gitignore`.

---

### 🐳 Step 4: Dockerfile (Multistage Build)

In the root of your project, create a file named `Dockerfile`:

```Dockerfile
# Stage 1 - Build
FROM maven:3.9.4-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

# Stage 2 - Run
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8585
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### 🧩 Step 5: docker-compose.yml

Also in the root, create `docker-compose.yml`:

```yaml
services:
  quotevault:
    build: .
    ports:
      - "${PORT}:8585"
    env_file:
      - .env
```

---

## 🔍 Reminders

* You **must protect** the `/api/quotes/random` endpoint so only authenticated users can access it.
* Use `RestTemplate` in your `QuoteService` to call the API.
* Use **Bean Validation** annotations like `@NotBlank` for DTOs.
* The password must be stored **encrypted** using `BCryptPasswordEncoder`.
* Add full inline comments and JavaDocs in your code — you’re not just coding, you’re documenting.

---

## 🧪 Submission

* Push your code to GitHub (exclude `.env`)
* Submit your repository link
* Deadline: **6:00 PM**

---


