# security-concept
This project give a detailed picture of how authentication and authorization works.


# Spring Boot Keycloak Authentication & Authorization Demo

This project demonstrates how to integrate [Keycloak](https://www.keycloak.org/) for authentication and authorization in a Spring Boot 3.x application. It provides a secure RESTful API with CRUD operations on user-specific data and role-based access control.

---

## Table of Contents
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Technology Stack](#technology-stack)
- [Setup Keycloak](#setup-keycloak)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [Testing the Endpoints](#testing-the-endpoints)
- [Authentication & Authorization Flow](#authentication--authorization-flow)
- [Notes](#notes)
- [License](#license)

---

## Overview

This Spring Boot application integrates Keycloak as an identity and access management solution. It manages user details with role-based permissions:

- Users with the **user** role can read user information.
- Users with the **admin** role can create, update, and delete user information.

The app uses:

- Spring Security with Keycloak adapter for OAuth2/OpenID Connect authentication.
- Spring Data JPA with H2 in-memory database for quick data persistence.
- Role-based authorization enforced via annotations and Spring Security configuration.

---

## Prerequisites

- Java 17+
- Maven 3+
- Keycloak server (v24.0.5 referenced)
- Internet access to download dependencies

---

## Project Structure

.
├── src
│ ├── main
│ │ ├── java/com/example/keycloakdemo
│ │ │ ├── config/SecurityConfig.java
│ │ │ ├── controller/UserDetailsController.java
│ │ │ ├── entity/UserDetail.java
│ │ │ ├── repository/UserDetailRepository.java
│ │ │ ├── service/UserDetailService.java
│ │ │ └── KeycloakDemoApplication.java
│ │ └── resources/application.yml
│ └── test/java/com/example/keycloakdemo/KeycloakDemoApplicationTests.java
└── pom.xml



---

## Technology Stack

- Spring Boot 3.2.7
- Spring Security
- Keycloak Spring Boot Starter 24.0.5
- Spring Data JPA
- H2 Database (in-memory)
- Lombok
- Maven

---

## Setup Keycloak

1. **Download and Start Keycloak (Development Mode):**
  bin/kc.sh start-dev

2. **Create Realm:**

- Login to http://localhost:8080/admin (create admin user if prompted).
- Create a new realm named `SpringBootRealm`.

3. **Create Client:**

- Client ID: `spring-boot-app`
- Client type: Public (Client authentication OFF)
- Standard flow enabled.
- Root URL: `http://localhost:8081`
- Valid redirect URIs: `http://localhost:8081/*`
- Web origins: `http://localhost:8081`
- Save the client.

4. **Create Roles:**

- Realm roles: `user` and `admin`.

5. **Create Users and Assign Roles:**

- Add users (e.g., `testuser`, `testadmin`).
- Set passwords for users.
- Assign `user` role to `testuser`.
- Assign `user` and `admin` roles to `testadmin`.

---

## Configuration

### `src/main/resources/application.yml`

- Configured for Keycloak connection (auth-server-url, realm, resource/client ID).
- In-memory H2 datasource enabled with console access.
- Security settings for role-based access.
- Debug logging enabled for Keycloak and Spring Security.

---

## Running the Application

1. **Build the project:**

mvn clean install


2. **Run the application:**
mvn spring-boot:run


3. **Access Points:**

- Application runs on: `http://localhost:8081`
- H2 Console: `http://localhost:8081/h2-console` (JDBC URL pre-filled)
- Keycloak login redirects from secured endpoints.

---

## Testing the Endpoints

### 1. Public Endpoint (No Authentication)
- URL: `http://localhost:8081/public`
- Method: GET
- Response: `This is a public endpoint, accessible to everyone!`

### 2. Protected Endpoints (Require Authentication)

- Accessing `http://localhost:8081/api/users` (GET) redirects to Keycloak login.

### 3. Get Current User Info
- URL: `http://localhost:8081/api/users/me`
- GET
- Returns Keycloak user info including username, email, roles.

### 4. Create User Detail (admin role required)
- Login as `testadmin`
- POST `http://localhost:8081/api/users`
- JSON body:

{
"username": "john.doe",
"email": "john.doe@example.com",
"firstName": "John",
"lastName": "Doe"
}




### 5. Read User Details (user role)
- GET `http://localhost:8081/api/users` or `/api/users/{id}`

### 6. Update User Detail (admin role)
- PUT `http://localhost:8081/api/users/{id}`
- Provide updated JSON body.

### 7. Delete User Detail (admin role)
- DELETE `http://localhost:8081/api/users/{id}`

---

## Authentication & Authorization Flow

- Unauthenticated requests to secured endpoints redirect to Keycloak login.
- Upon successful login, the app exchanges authorization code for tokens.
- Spring Security gets the authenticated user and roles from Keycloak token.
- Access is granted or denied based on user roles (`user` or `admin`) defined in Keycloak and mapped in the app.
- Roles are used in endpoint method annotations (`@PreAuthorize`).

---

## Notes

- This project is for educational/demo purposes using an in-memory DB and public client.
- For production:
- Use HTTPS and secure clients (confidential with secrets).
- Persist user and client data.
- Secure H2 console or disable it.
- Consider advanced Keycloak configurations.
- Detailed logs are enabled for tracing requests and debugging.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

*Happy coding with Spring Boot and Keycloak!*



