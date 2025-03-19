# Microservices with Spring Cloud
## Overview
This project consists of multiple microservices, each serving a distinct function:
- Limits Service – Provides minimum and maximum limit values.
- Currency Exchange Service – Manages exchange rate data for various currencies.
- Currency Conversion Service – Calculates the total converted amount using exchange rates.
- Spring Cloud Config Server – Externalizes configuration, sourcing properties from a local Git-backed folder.
- Eureka Naming Server – The service registry enables dynamic microservices discovery.
- API Gateway – Routes external requests to the appropriate microservice, providing logging and path rewriting.
---
## Architecture Diagram
<img width="719" alt="Screenshot 2025-03-19 at 1 35 54 PM" src="https://github.com/user-attachments/assets/afb0c97a-b256-434c-b5c8-ad9d471bc768" />

## Setup Instructions
### 1. Clone the Repository
Clone the project repository to your local machine:
   ```xml
   git clone https://github.com/Xiangran-Zhou/microservices-spring-cloud.git
cd microservices-spring-cloud
   ```
---
### 2. Build the Project
Clone the project repository to your local machine:
   ```xml
   mvn clean install
   ```
---
### 3. Configure the Spring Cloud Config Server
Open spring-cloud-config-server’s application.properties and ensure the Git URI is set to:
   ```xml
   spring.cloud.config.server.git.uri=file:./git-localconfig-repo
   ```
This allows the Config Server to read configuration files from the local git-localconfig-repo folder.

---
### 4. Run the Services
Start each module in the following order (each in its own terminal or IDE run configuration):
Take Spring Cloud Config Server (port 8888) as an example
   ```xml
   cd spring-cloud-config-server
mvn spring-boot:run
   ```
Naming Server (Eureka) (port 8761)
Limits Service (ports 8080, 8081, etc.)
Currency Exchange Service (ports 8000, 8001, etc.)
Currency Conversion Service (ports 8100, 8101, etc.)
API Gateway (port 8765)

---
### 5. Usage & Testing
Check Eureka Dashboard

Go to http://localhost:8761 to see all registered services.
Test Limits Service

http://localhost:8080/limits
Test Currency Exchange

http://localhost:8000/currency-exchange/from/USD/to/INR
Test Currency Conversion

RestTemplate:
http://localhost:8100/currency-conversion/from/USD/to/INR/quantity/10
Feign:
http://localhost:8100/currency-conversion-feign/from/USD/to/INR/quantity/10
Test via API Gateway

http://localhost:8765/currency-exchange/from/USD/to/INR
http://localhost:8765/currency-conversion-feign/from/USD/to/INR/quantity/10

---
### 6. Resilience Patterns
Currency Exchange Service demonstrates Resilience4j:

Bulkhead: Limits concurrent calls to an endpoint.
Rate Limiter: Restricts the number of calls within a specified period.
Retry: Automatically retries failed requests for transient errors.
Configure these patterns in application.properties within currency-exchange-service:
   ```xml
   resilience4j.retry.instances.sample-api.maxRetryAttempts=5
resilience4j.retry.instances.sample-api.waitDuration=1s
resilience4j.ratelimiter.instances.default.limitForPeriod=2
resilience4j.ratelimiter.instances.default.limitRefreshPeriod=10s
resilience4j.bulkhead.instances.sample-api.maxConcurrentCalls=10
   ```
---


