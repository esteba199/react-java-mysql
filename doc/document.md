Multi-Tier Web Application Deployment with Docker
Table of Contents

Introduction

Architecture Description

Service Breakdown

Networking Design

Deployment Guide

System Requirements

Deployment Procedure

Exposed Interfaces

Application Server Setup

Core Components

Configuration Files

Backend Dockerfile

Web Server and Reverse Proxy Setup

Request Routing

Reverse Proxy Behavior

Security Configuration

Testing and Verification

Functional Validation

Inter-Service Communication Tests

Log Inspection

Basic Performance Evaluation

Load and Stress Testing (Grafana k6)

Administration and Maintenance

Service Control

Scalability Considerations

Maintenance Guidelines

Final Remarks

1. Introduction

This project focuses on the deployment of a containerized multi-tier web application using Docker and Docker Compose. The primary goal is to design and implement a secure, modular, and reproducible deployment architecture that follows industry-standard practices for web application hosting.

The solution is inspired by the official Docker reference project react-java-mysql, which has been customized and extended to fully comply with the project requirements. These adaptations include the integration of a reverse proxy, strict service isolation, security improvements, and comprehensive technical documentation.

Technologies used

Docker

Docker Compose

Nginx (Reverse Proxy)

React (Frontend)

Spring Boot / Java (Backend – Application Server)

MariaDB (Database)

2. Architecture Description

The application is built using a three-tier architecture complemented by a dedicated web server acting as a reverse proxy. Each architectural layer is deployed in a separate container to ensure clear separation of responsibilities and improved security.

Service Breakdown

Web Server (Nginx)

Serves as the single public access point.

Forwards frontend requests (/) to the React service.

Forwards API requests (/api) to the backend service.

Frontend Service

React-based client interface.

Runs in its own isolated container.

Does not have direct access to the database.

Backend Service (Application Server)

Spring Boot application developed in Java.

Processes business logic and handles database operations.

Operates behind the reverse proxy.

Database Service

MariaDB relational database.

Persists application data using Docker volumes.

Fully isolated from external access.

Networking Design

Two separate Docker networks are defined to control communication paths:

react-spring: enables communication between the web server, frontend, and backend services.

spring-mysql: restricts communication exclusively between the backend and the database.

This network segmentation strengthens security and enforces proper service isolation.

3. Deployment Guide
System Requirements

Docker installed

Docker Compose available

Docker Desktop (recommended environment)

Deployment Procedure

First, clone the repository:

git clone <repository_url>


To build and start the application stack using the Compose configuration file:

docker compose up --build


To stop and remove the running containers:

docker compose down

Exposed Interfaces

Only port 80 is published to the host system through the Nginx reverse proxy.
The frontend, backend, and database services remain inaccessible from outside the Docker environment.

4. Application Server Setup

The backend is implemented as a Spring Boot application.

Core Components

Controllers: manage incoming HTTP requests and responses.

Services: implement business logic.

Repositories: handle persistence and database access.

Configuration Files

application.properties: defines server behavior and database connectivity.

Sensitive values such as credentials are injected through environment variables.

Backend Dockerfile

A multi-stage Docker build is used:

Build stage: compiles the application using Maven.

Runtime stage: runs the compiled application using a lightweight JRE image.

This strategy minimizes image size and enhances runtime security.

5. Web Server and Reverse Proxy Setup

Nginx is configured as a reverse proxy and acts as the only component exposed to external traffic.

Request Routing

/ → Frontend service (React)

/api → Backend service (Spring Boot)

Reverse Proxy Behavior

Requests are forwarded internally using Docker service names, allowing seamless inter-container communication without exposing internal ports. This setup demonstrates effective coordination between the web server and the application server.

6. Security Configuration

Several fundamental security practices have been applied:

Exposure limited to a single port (80).

Database service isolated from the host system.

Database credentials managed via Docker secrets.

Use of environment variables for sensitive configuration.

Separation of services through isolated Docker networks.

Explicit image versioning to control updates.

These measures collectively reduce the attack surface and improve overall system security.

7. Testing and Verification
Functional Validation

Frontend accessibility verified at http://localhost.

Backend API reachable via http://localhost/api.

Inter-Service Communication Tests

Confirmed frontend-to-backend communication through Nginx.

Verified backend-to-database connectivity using container logs.

Log Inspection

Service behavior was validated using Docker logs:

docker compose logs frontend
docker compose logs backend
docker compose logs db

Basic Performance Evaluation

A preliminary performance check was performed by monitoring container resource usage:

docker compose stats

Load and Stress Testing (Grafana k6)

Load and performance tests were conducted using Grafana k6 to analyze application behavior under concurrent HTTP requests.

The tests were executed using a containerized k6 instance, ensuring consistency and isolation from the host system. The load tests target the backend API via the Nginx reverse proxy, accurately simulating real user traffic.

The test was launched using:

docker compose run --rm k6


The test script generates multiple HTTP requests against the /api endpoint and validates both response codes and response times.

The k6 container operates within the same Docker network as the web server, enabling service discovery through Docker’s internal DNS resolution.

8. Administration and Maintenance
Service Control

Start services: docker compose up

Stop services: docker compose down

Rebuild images: docker compose up --build

Scalability Considerations

The backend service can be horizontally scaled using Docker Compose.

The reverse proxy configuration can be extended to support additional services.

Maintenance Guidelines

Regularly update base images.

Continuously monitor container logs.

Periodically back up database volumes.

9. Final o conclusion

This project showcases the deployment of a robust and secure multi-tier web application using Docker and Docker Compose. The proposed architecture enforces strict service separation, incorporates essential security practices, and leverages a reverse proxy to efficiently manage traffic. The resulting deployment is reproducible, maintainable, and aligned with real-world professional environments.