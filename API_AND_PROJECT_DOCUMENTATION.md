# Smart Gym Management System — Complete Technical & API Documentation

## 1. Project Overview & Architecture

The **Smart Gym Management System** is a enterprise-ready fitness management solution engineered using **Spring Boot 3**, **Spring Data JPA**, **Spring AOP**, **Spring-WS (SOAP)**, **Bean Validation**, and **Declarative Transaction Management**.

### Architecture Diagram
```
+-----------------------------------------------------------------------------+
|                      Client Layer (SPA Web Dashboard / Postman / SoapUI)    |
+-----------------------------------------------------------------------------+
                                       |
                   +-------------------+-------------------+
                   | (HTTP REST / JSON)                    | (HTTP POST / XML)
                   v                                       v
+------------------------------------+  +-------------------------------------+
|        REST API Controllers        |  |        Spring-WS SOAP Endpoint      |
|  - MemberController                |  |  - ReportSoapEndpoint               |
|  - TrainerController               |  |  - /ws/gym-reports.wsdl             |
|  - MembershipPlanController        |  |  - XSD Validation                   |
|  - AttendanceController            |  +-------------------------------------+
|  - PaymentController               |                     |
|  - AuditLogController              |                     |
+------------------------------------+                     |
                   \                                      /
                    v                                    v
+-----------------------------------------------------------------------------+
|                               Service Layer                                 |
|  - MemberService / MemberServiceImpl                                        |
|  - MembershipRenewalService (@Transactional ACID Multi-Step Renewal)        |
|  - PaymentService / AttendanceService / WorkoutPlanService                  |
|  - DependencyInjectionDemoService (Constructor vs Setter vs Field)          |
+-----------------------------------------------------------------------------+
                   |                                       ^
                   v                                       |
+------------------------------------+                     |
|     Spring AOP Aspect Layer        |                     |
|  - LoggingAspect (@Before, @Around)|                     |
|  - Execution Time Interception     |---------------------+
|  - Auditing & Performance Metrics  |
+------------------------------------+
                   |
                   v
+-----------------------------------------------------------------------------+
|                        Data Access Layer (Spring Data JPA)                  |
|  - MemberRepository, TrainerRepository, PaymentRepository, etc.            |
|  - Custom JPQL Queries, Pagination, and Derived Finders                     |
+-----------------------------------------------------------------------------+
                                       |
                                       v
+-----------------------------------------------------------------------------+
|                           Relational Database                               |
|  - In-Memory H2 (Embedded / Console) or MySQL                               |
+-----------------------------------------------------------------------------+
```

---

## 2. Core Entities & Relationships

1. **Member**: Gym member profile, contact info, join date, validity date, status (`ACTIVE`, `EXPIRED`, `PENDING`, `INACTIVE`), assigned `MembershipPlan`, and assigned `Trainer`.
2. **Trainer**: Fitness coach, specialization, experience, salary, and list of assigned members.
3. **MembershipPlan**: Subscription tier (e.g. Standard, Pro, Elite), duration in months, fee, and max sessions.
4. **WorkoutPlan**: Routine schedule, target fitness goal (`MUSCLE_GAIN`, `WEIGHT_LOSS`, `FLEXIBILITY`), and difficulty level.
5. **Attendance**: Timestamped check-in and check-out logs with status (`PRESENT`, `LATE`, `EXCUSED`).
6. **Payment**: Financial transactions, payment method (`CARD`, `UPI`, `CASH`, `NET_BANKING`), type, transaction reference, and status (`COMPLETED`, `PENDING`, `FAILED`).
7. **AuditLog**: Automated records captured by Spring AOP, recording intercepted method names, duration in milliseconds, action types, and statuses.

---

## 3. RESTful Web Services Reference

### A. Member Management (`/api/members`)
- `POST /api/members`: Register a new member with validation.
- `GET /api/members/{id}`: Retrieve member details.
- `GET /api/members`: Retrieve paginated members with search, sorting, and status filter (`page`, `size`, `sortBy`, `sortDir`, `status`, `search`).
- `PUT /api/members/{id}`: Update member details.
- `DELETE /api/members/{id}`: Deactivate member account.
- `POST /api/members/{id}/assign-trainer/{trainerId}`: Assign coach.
- `POST /api/members/{id}/assign-plan/{planId}`: Assign membership tier.

### B. Trainer Management (`/api/trainers`)
- `POST /api/trainers`: Create trainer profile.
- `GET /api/trainers/{id}`: Retrieve trainer details.
- `GET /api/trainers`: List trainers (optional `activeOnly`, `specialization`).
- `PUT /api/trainers/{id}`: Update trainer.
- `DELETE /api/trainers/{id}`: Deactivate trainer.

### C. Membership Plans (`/api/membership-plans`)
- `POST /api/membership-plans`: Create plan.
- `GET /api/membership-plans`: List all plans.
- `PUT /api/membership-plans/{id}`: Update plan.
- `DELETE /api/membership-plans/{id}`: Deactivate plan.

### D. Attendance Tracking (`/api/attendance`)
- `POST /api/attendance/check-in`: Record check-in.
- `PUT /api/attendance/check-out/{id}`: Record check-out timestamp.
- `GET /api/attendance/today`: List active floor traffic today.
- `GET /api/attendance/member/{memberId}`: Member attendance history.

### E. Billing & Transactional Renewal (`/api/payments`)
- `POST /api/payments`: Record standalone payment.
- `GET /api/payments`: Paginated transaction list.
- `GET /api/payments/stats/revenue`: Revenue overview metrics.
- `POST /api/payments/renew-membership`: **Atomic Composite Transaction** renewing membership validity and recording payment. Supports `simulateFailure: true` to trigger automatic rollback.

---

## 4. SOAP Web Services Reference

- **Service Endpoint**: `http://localhost:8080/ws`
- **WSDL URL**: `http://localhost:8080/ws/gym-reports.wsdl`
- **Target Namespace**: `http://gym.com/smartgym/soap`

### Operations Supported:
1. **`GetMemberReportRequest`**: Returns member dossier, active plan, trainer, total attendance sessions, total revenue paid, and current workout goal.
2. **`GetGymSummaryReportRequest`**: Returns overall gym metrics (total members, active members, trainers, today's check-ins, total revenue).
3. **`GetPaymentReportRequest`**: Returns transaction summaries, completed payments, and total revenue collected.

---

## 5. Dependency Injection Architecture

Located in [`DependencyInjectionDemoService.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/src/main/java/com/gym/smartgym/service/DependencyInjectionDemoService.java):
1. **Constructor Injection**: Enforces mandatory, immutable dependencies at creation time (`memberRepository`).
2. **Setter Injection**: Uses `@Autowired` on setter methods for optional/reconfigurable dependencies (`trainerRepository`).
3. **Field Injection**: Uses `@Autowired` directly on private fields (`paymentRepository`).

---

## 6. Spring AOP Interceptor & Monitoring

Located in [`LoggingAspect.java`](file:///c:/Users/nithi/OneDrive%20-%20Aditya%20Educational%20Institutions/API&MS%20PROJECT/src/main/java/com/gym/smartgym/aspect/LoggingAspect.java):
- **`@Before`**: Logs entry and arguments into service layer methods.
- **`@AfterReturning`**: Logs successful return values.
- **`@AfterThrowing`**: Captures unhandled exceptions with full context.
- **`@Around`**: Intercepts `@GymAuditable` methods, measures execution duration (in ms), and automatically saves entries into the `AuditLog` repository for live monitoring.
