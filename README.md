# PulseFit Smart Gym Management System (API & Microservices Project)

A full-stack, enterprise-grade **Smart Gym Management System** architected with clearly separated **Frontend** and **Backend** modules. Built using **Spring Boot 3**, **Spring Data JPA**, **Spring AOP**, **Spring-WS (SOAP)**, **Bean Validation**, **Declarative Transaction Management**, and a responsive **Multi-Role SPA Client**.

---

## 📁 Modular Project Structure

The project is organized into two distinct, decoupled modules:

```
API&MS PROJECT/
├── pom.xml                                    # Parent Multi-Module Aggregator POM
├── mvnw.cmd                                   # Maven Wrapper
├── README.md                                  # Comprehensive System Documentation
├── API_AND_PROJECT_DOCUMENTATION.md           # API & Academic Rubric Guide
│
├── backend/                                   # Backend Module (Spring Boot REST & SOAP Web Services)
│   ├── pom.xml                                # Backend Maven dependencies & plugins
│   └── src/
│       ├── main/
│       │   ├── java/com/gym/smartgym/
│       │   │   ├── aspect/                    # Spring AOP Cross-Cutting Logging & Metrics
│       │   │   ├── config/                    # CORS, OpenAPI Swagger, SOAP, WebMVC Config & Data Seeder
│       │   │   ├── controller/                # REST Controllers (Member, Trainer, Workout, Plan, Payments)
│       │   │   ├── dto/                       # Data Transfer Objects & Validation rules
│       │   │   ├── entity/                    # JPA Entities (Member, Trainer, WorkoutPlan, Plan, Payment)
│       │   │   ├── exception/                 # Centralized Exception Handling (@RestControllerAdvice)
│       │   │   ├── repository/                # Spring Data JPA Repositories
│       │   │   ├── service/                   # Business Logic & @Transactional Services
│       │   │   └── soap/                      # SOAP Web Service Endpoint & JAXB Report Models
│       │   └── resources/
│       │       ├── application.properties     # H2 DB, JPA, Server & SOAP configuration
│       │       ├── xsd/gym-reports.xsd        # XML Schema Definition for SOAP Reports
│       │       └── static/                    # Synchronized static web assets for single-JAR execution
│       └── test/
│
└── frontend/                                  # Frontend Module (Single Page Application)
    ├── package.json                           # Frontend dependencies & dev scripts
    ├── README.md                              # Frontend module guide
    ├── index.html                             # Multi-role landing page, portals, and modals
    ├── css/
    │   └── styles.css                         # CSS design system (tokens, cards, tables, badges)
    ├── js/
    │   └── app.js                             # Client-side routing, auth state, and dynamic API connector
    └── images/
        └── logo.jpg                           # PulseFit brand logo
```

---

## 🌟 Core System Highlights

### 1. Distinct Role Portals & Authentication
- **👤 Member Portal**: Log in with member credentials to view personal membership validity, attendance logs, payment invoices, and assigned workout programs.
- **🏋️ Coach / Trainer Portal**: Log in with coach credentials to view assigned members, construct day-wise workout splits, and modify routines.
- **🔑 Admin Management Portal**: Complete executive control over membership plans, members, coaches, check-ins, financial receipts, transactional rollback tests, and SOAP/AOP inspector labs.

### 2. Member Self-Registration (Restricted Trainer/Admin Sign-up)
- Public landing page allows **Member Sign-up** with INR (₹) plan selection, mobile verification (+91), and address details.
- **Trainer and Admin accounts cannot register publicly**; they are strictly provisioned and managed by gym management.

### 3. Coach Selection in Member Dashboard
- Members can browse all certified coaches, review their specialties (Strength & Conditioning, HIIT, Hypertrophy, Yoga), and choose or switch their personal trainer directly from their dashboard.
- Immediate assignment persistence via `POST /api/members/{memberId}/assign-trainer/{trainerId}`.

### 4. Workout Schedule Management (Cardio, Strength, HIIT, Yoga)
- Trainers assign customized day-wise workout programs with sets, reps, difficulty levels, and nutrition guidelines.
- **Modify Workout Support**: Trainers can modify/update existing routines anytime via `PUT /api/workout-plans/{id}` with instant live reflection across member dashboards.

---

## 🏛️ Course Concept & Architecture Mapping

| Syllabus Concept | Implementation in this Project | Code Location |
| :--- | :--- | :--- |
| **Spring Framework & Dependency Injection** | Demonstrates **Constructor Injection** (mandatory dependencies), **Setter Injection** (optional/mutable dependencies), and **Field Injection** via `@Autowired`. | [`DependencyInjectionDemoService.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/service/DependencyInjectionDemoService.java) |
| **Spring Boot & Auto-Configuration** | Embedded Tomcat, Starters, automatic DataSource configuration, OpenApi Swagger docs, and DevTools. | [`SmartGymApplication.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/SmartGymApplication.java) |
| **Spring Data JPA & Repositories** | Dynamic query methods, JPQL `@Query`, pagination, sorting, entity relationships (`@ManyToOne`, `@OneToMany`). | [`MemberRepository.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/repository/MemberRepository.java) |
| **Spring AOP (Aspect-Oriented Programming)** | Cross-cutting concerns: `@Before`, `@AfterReturning`, `@AfterThrowing`, `@Around`, performance duration monitoring, and persistent audit logging. | [`LoggingAspect.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/aspect/LoggingAspect.java) |
| **Transaction Management (`@Transactional`)** | Atomic multi-step operations (member renewal + payment processing) with automatic rollback on exception or simulated gateway timeout. | [`MembershipRenewalService.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/service/MembershipRenewalService.java) |
| **RESTful Web Services** | Clean REST endpoints with `@RequestBody`, `ResponseEntity`, `@PathVariable`, `@RequestParam`, and full CRUD operations. | [`MemberController.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/controller/MemberController.java), [`WorkoutPlanController.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/controller/WorkoutPlanController.java) |
| **SOAP Web Services & WSDL** | Spring-WS contract-first SOAP web service with XSD schema and dynamic WSDL generation at `/ws/gym-reports.wsdl`. | [`ReportSoapEndpoint.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/soap/ReportSoapEndpoint.java), [`gym-reports.xsd`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/resources/xsd/gym-reports.xsd) |
| **Validation & Centralized Exceptions** | Input validation via `@Valid`, Bean Validation constraints (`@NotBlank`, `@Min`, `@Email`), and `@RestControllerAdvice` error responses. | [`GlobalExceptionHandler.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/backend/src/main/java/com/gym/smartgym/exception/GlobalExceptionHandler.java) |

---

## 🚀 Running the Project

### 1. Build and Run the Backend Module
From the project root:
```powershell
.\mvnw.cmd package -DskipTests
java -jar backend/target/smart-gym-management-system-1.0.0.jar
```
Or directly using Maven:
```powershell
.\mvnw.cmd spring-boot:run -pl backend
```

### 2. Run the Frontend Module Separately (Optional)
Run from the `frontend` folder using any local server:
```powershell
cd frontend
npx serve -l 3000 .
```
Or open [http://localhost:8080/](http://localhost:8080/) directly (served by backend).

### 3. Key Endpoints & Documentation
- **Web App**: [http://localhost:8080/](http://localhost:8080/)
- **Swagger / OpenAPI Documentation**: [http://localhost:8080/swagger-ui/index.html](http://localhost:8080/swagger-ui/index.html)
- **SOAP WSDL Contract**: [http://localhost:8080/ws/gym-reports.wsdl](http://localhost:8080/ws/gym-reports.wsdl)
- **H2 In-Memory Database Console**: [http://localhost:8080/h2-console](http://localhost:8080/h2-console)
  - *JDBC URL*: `jdbc:h2:mem:gymdb`
  - *Username*: `sa`
  - *Password*: `password`
