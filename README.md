# 🚀 Spring Boot Microservice App

This repository contains a **Spring Boot microservices** application that demonstrates an end‑to‑end e‑commerce–style workflow using synchronous REST calls and asynchronous events. The system is built around independent services for products, orders, inventory management and notifications.

------------------------------------------------------------------------

## 🏗 Architecture

![Architecture Diagram](architecture.png)

The system is composed of several Spring Boot services and supporting infrastructure (databases, Kafka, observability stack), all containerized with Docker.

High‑level building blocks:

* **🌐 API Gateway**

    * Single entry point for all external clients.
    * Routes traffic to downstream microservices using service discovery.
    * Integrates **Resilience4j** for circuit‑breaker, retry and timeout patterns.

* **🔒 Auth Server**

    * Central authorization server securing APIs with OAuth2/JWT.
    * Sits in front of the gateway to protect backend services.

* **🛒 Product Service**

    * Manages product catalog data backed by **MongoDB**.
    * Provides CRUD APIs for products.

* **📦 Order Service**

    * Manages order creation, persistence and business logic using **MySQL**.
    * Calls Inventory Service synchronously to validate and reserve stock.
    * Publishes order events to **Kafka** for asynchronous processing by other services.

* **📦 Inventory Service**

    * Maintains product stock in its own **MySQL** database.
    * Exposes APIs for stock check and update.

* **📣 Notification Service**

    * Consumes order‑related events from **Kafka**.
    * Sends notification email to customers.

* **☸️ Kubernetes Built-in Service Discovery**

    * When deployed to Kubernetes, each microservice is exposed via a **Kubernetes Service** and services communicate using internal DNS.
    * Kubernetes automatically handles:
        -   Service registration
        -   DNS resolution
        -   Internal load balancing

* **📊 Observability Stack**

    * **OpenTelemetry** for instrumentation, **Prometheus** for metrics, **Grafana Loki** for logs, **Grafana Tempo** for traces, visualized through **Grafana** dashboards.

* **🐳 Containerization**

    * Docker Compose configuration for local development.
    * Each service is fully containerized using Docker.

* **☸️ Kubernetes Deployment**

    * Production-style container orchestration using Kubernetes.
    * Separate manifests for infrastructure and application workloads.
    * Supports deployment on Docker Desktop Kubernetes and cloud clusters.
    * Enables scalability, rolling updates, and self-healing capabilities.

------------------------------------------------------------------------

## 📁 Project Structure

springboot-microservice-app/
    ├── api-gateway/
    ├── inventory-service/
    ├── notification-service/
    ├── order-service/
    ├── product-service/
    ├── docker/
    ├── k8s/
    │   └── manifests/
    │       ├── applications/
    │       └── infrastructure/
    ├── docker-compose.yml
    ├── pom.xml
    └── README.md

------------------------------------------------------------------------

## ✅ Features

-   🌐 API Gateway with routing & resilience
-   🧩 Independent microservices (database per service)
-   🔄 Synchronous REST communication (Order → Inventory)
-   ⚡ Event-driven architecture with Kafka
-   ☸️ Kubernetes-native service discovery
-   🔒 OAuth2/JWT Security
-   📊 Production-grade observability stack
-   🐳 Dockerized services
-   🏗 Multi-module Maven parent project

------------------------------------------------------------------------

## 🛠 Prerequisites

   ### For Local (Docker Compose)

   -   Java 21
   -   Maven
   -   Docker & Docker Compose

   ### For Kubernetes

   -   Docker
   -   kubectl
   -   Kubernetes cluster (Minikube, Kind, Docker Desktop Kubernetes, or
       cloud cluster)

------------------------------------------------------------------------

## 🏃‍♂️ Getting Started (Local with Docker Compose)

### 🐳 Option 1: Run Locally with Docker Compose

#### 1️⃣ Clone Repository

``` bash
git clone https://github.com/alfredjose17/springboot-microservice-app.git
cd springboot-microservice-app
```

#### 2️⃣ Build All Services

``` bash
mvn clean install
```

#### 3️⃣ Start Services

``` bash
docker compose up -d
```

This starts MySQL, MongoDB, Kafka, microservices, and observability
stack.

------------------------------------------------------------------------

### ☸️ Option 2: Run on Kubernetes

#### 1️⃣ Enable Kubernetes in Docker Desktop

     1. Open **Docker Desktop**
     2. Go to **Settings**
     3. Navigate to **Kubernetes**
     4. Check ✅ **Enable Kubernetes**
     5. Click **Apply & Restart**

     Wait until Kubernetes status shows **Running**.

## 2️⃣ Build Docker Images (from root directory)

``` bash
mvn spring-boot:build-image -DskipTests -DdockerPassword=<docker-password>
```

## 3️⃣ Deploy Infrastructure

``` bash
kubectl apply -f k8s/manifests/infrastructure/
```

## 4️⃣ Deploy Applications

``` bash
kubectl apply -f k8s/manifests/applications/
```

## 5️⃣ Verify

``` bash
kubectl get all
```

## 6️⃣ Access Gateway

``` bash
kubectl port-forward service/api-gateway 9000:9000
```

Access: http://localhost:9000

------------------------------------------------------------------------

## 🔄 Sample Flow

1. 🛒 Create products via Product Service (through the gateway).
2. 📦 Place an order; the Order Service checks the Inventory Service synchronously and reserves stock.
3. 📣 An order event is emitted to Kafka, consumed by Notification Service, which triggers a notification to the customer.
4. 📊 Inspect traces, metrics and logs in Grafana/Prometheus/Loki/Tempo to see the full request path across services.

------------------------------------------------------------------------

## 🧰 Technologies Used

  Area               Stack
  ------------------ ---------------------------------------------------------------
  Core               Spring Boot, Spring Web, Spring Data JPA, Spring Data MongoDB
  Cloud              Spring Cloud Gateway, Resilience4j
  Messaging          Apache Kafka
  Databases          MySQL, MongoDB
  Security           Spring Security, OAuth2/JWT
  Containerization   Docker
  Orchestration      Kubernetes
  Observability      OpenTelemetry, Prometheus, Grafana, Loki, Tempo                                      |

------------------------------------------------------------------------

## 🌟 Future Scope

* [X]🏗 Parent Maven project (`pom.xml`) for multi-module management.
* [X]🐳 Kubernetes deployment manifests and full cluster setup.
* [ ]⚙️ CI/CD pipelines using GitHub Actions to automatically build and deploy images.
* [ ]🔒 Harden security and integrate Auth Server fully across services.
* [ ]📊 Extend observability dashboards with business‑level metrics (orders created, failed payments, etc.).
