# Research Lab Management — Spring Cloud Microservices 🧪

School project (ENIS, 2023–2024): the back end of a web application for managing a research laboratory — its publications and tools — split into independent Spring Boot services behind an API gateway, with service discovery and centralised configuration.

![Java](https://img.shields.io/badge/Java%2017-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot%203.1-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-6DB33F?style=flat-square&logo=spring&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-C71A36?style=flat-square&logo=apachemaven&logoColor=white)

## Architecture

```
                       ┌──────────────────────┐
   Client ───────────► │ Gateway  :9000       │  Spring Cloud Gateway
                       └──────────┬───────────┘
                                  │ routes by service name
            ┌─────────────────────┼─────────────────────┐
            ▼                                           ▼
 ┌──────────────────────┐                   ┌──────────────────────┐
 │ publication-service  │                   │ outil-service  :9999 │
 └──────────────────────┘                   └──────────────────────┘
            │   register / discover                     │
            └──────────────► Registry (Eureka) ◄────────┘
                             Config Server :8888 ◄── all services load their config
```

| Service | Role | Stack |
|---|---|---|
| `ConfigServer` | Centralised configuration (port 8888) | Spring Cloud Config Server |
| `RegistryService` | Service registry | Netflix Eureka Server |
| `Gateway` | Single entry point, routing (port 9000) | Spring Cloud Gateway |
| `Publication` | Lab publications: type, title, link, date, PDF | Spring Web · Spring Data JPA · OpenFeign |
| `Outil` | Lab tools: date, source — full CRUD (port 9999) | Spring Web · Spring Data JPA · OpenFeign |

## Main endpoints

| Service | Method | Path |
|---|---|---|
| Publication | GET | `/publications`, `/publications/{id}` |
| Publication | GET | `/publications/search/titre`, `/search/type`, `/search/lien`, `/search/sourcepdf` |
| Outil | GET | `/outils` |
| Outil | POST | `/outils/save` |
| Outil | PUT | `/outils/update/{id}` |
| Outil | DELETE | `/outils/{id}` |

## Run locally

Requirements: JDK 17 (each service ships its own Maven wrapper).

1. **Config repository.** The Config Server reads a local Git repository at
   `ConfigServer/src/main/resources/myconfig` (not included). Create it with one
   `<service-name>.properties` file per service (e.g. `publication-service.properties`
   holding the port, datasource and Eureka URL), then `git init && git commit` inside it.
2. **Start the services in this order**, each in its own terminal:

```bash
cd ConfigServer    && ./mvnw spring-boot:run
cd RegistryService && ./mvnw spring-boot:run
cd Publication     && ./mvnw spring-boot:run
cd Outil           && ./mvnw spring-boot:run
cd Gateway         && ./mvnw spring-boot:run
```

## Scope of this repository

This repository holds the Spring Cloud back end. The member and event services and the Angular front end
of the original school project are not included.

## Author

**Hadil Ben Rhouma** — [Portfolio](https://portfilio-gules-three.vercel.app/?utm_source=github) · [LinkedIn](https://www.linkedin.com/in/hadil-benrhouma/)
