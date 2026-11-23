# THIKANA – COMPLETE PROJECT WORK & IMPLEMENTATION REPORT
### Comprehensive Full-Stack Development Log & System Documentation
**Project**: THIKANA – Find Your Perfect Place to Call Home  
**Course**: SE 206 – Software Engineering Lab / Project  
**Institution**: University of Frontier Technology, Bangladesh (UFTB)  
**Supervisor**: Md. Moshiur Rahman  
**Developers & Authors**:
- **Raisa Tasmiha Anisa** – Student ID: `2403029`
- **Irin Jahan Meem** – Student ID: `2403026`

---

## 1. Project Overview & Objectives

**Thikana** is a full-featured, database-driven house rental and finder platform built to commercial standards for the Bangladeshi rental housing market. It unites three distinct stakeholder groups:
1. **Tenants / House Seekers**: Search verified rental homes with rich multi-criteria filters, schedule physical viewings, send rental inquiries, bookmark favorite listings, and purchase temporary access passes.
2. **Landlords / Property Owners**: Purchase monthly subscription packages (Starter, Professional, Business), list rental properties with multi-image upload subject to admin verification, enforce listing quotas, and accept/reject tenant viewing applications with replies.
3. **Super Administrators**: Central platform moderation, verifying new property submissions, approving/rejecting manual Bangladeshi mobile financial service transactions (**bKash**, **Nagad**, **Bank Transfer**), managing user status (suspend/activate), updating subscription plan fees and durations, resolving reports, and inspecting security audit logs.

---

## 2. Strict Technology Stack Adherence

The project strictly complies with all core architectural and library restrictions:

| Layer | Technology | Version | Restrictions Respected |
| :--- | :--- | :--- | :--- |
| **Frontend Markup** | **HTML5** | Modern standard | Pure custom semantic markup; **NO Bootstrap** |
| **Frontend Styles** | **CSS3** | Native custom | Custom design system (`#142B4A`, `#159B79`, `#F7F9FC`); **NO external UI frameworks** |
| **Client Scripting** | **Vanilla JavaScript** | ES6+ | Zero React, Vue, Angular, or Node.js runtime |
| **Backend Language** | **Java** | **JDK 21 LTS** | Modern Java virtual machine |
| **Servlet Standard** | **Jakarta Servlet API** | **6.0.0** | Compatible with Apache Tomcat 10.1 & Jetty 12 EE10 |
| **Server Views** | **JSP + JSTL** | **JSTL 3.0.0** | Server-rendered dynamic pages without scriptlets |
| **Database** | **MySQL / MariaDB** | **10.4.x / 8.0+** | XAMPP local relational database (`thikana_db`) |
| **Database Driver** | **MySQL Connector/J** | **8.3.0** | Type-4 pure JDBC; `PreparedStatement` & transactions |
| **Password & OTP** | **jBCrypt** | **0.4** | 12 salt rounds for Blowfish password & OTP hashing |
| **Email Subsystem** | **Jakarta Mail** | **2.0.1** | Transactional SMTP with dev-mode fallback |
| **Build & Packaging** | **Apache Maven** | **3.9.x** | Enterprise `.war` packaging (`target/Thikana.war`) |

---

## 3. Complete File & Package Inventory

### A. Configuration & Models (`com.thikana.config`, `com.thikana.model`)
- `DBConnection.java`: Configurable JDBC connection pool manager connecting to `localhost:3306/thikana_db`.
- `User.java`: Complete JavaBean for users with role and status helpers (`isAdmin()`, `isLandlord()`, `isTenant()`).
- `Property.java`: Model matching exact MariaDB schema (`monthlyRent`, `sizeSqft`, `contactNumber`, `availableFrom`, `isFeatured`, etc.).
- `PropertyImage.java`: Photo gallery attachments with primary image flags.
- `SubscriptionPlan.java`: Master tier parameters (target role, price, listing limit, duration in days, features).
- `Subscription.java`: User active subscriptions with date calculations and expiry flags.
- `Payment.java`: Manual Bangladeshi payment transactions with sender numbers, TrxIDs, and receipt proofs.
- `RentalRequest.java`: Tenant viewing requests and inquiries with landlord responses.
- `Favorite.java`: Saved properties bookmark bean.
- `Notification.java`: In-app status alert messages.
- `AdminActivityLog.java`: Tamper-evident admin action audit trail.
- `Report.java`: Community moderation complaint model.

### B. Data Access Layer (`com.thikana.dao`)
- `UserDAO.java`: Registration, BCrypt login, email uniqueness check, profile updater, password hasher, admin user management.
- `PropertyDAO.java`: Dynamic multi-filter search, pagination, sorting, transaction-based property + multi-image creation, landlord properties CRUD, admin approvals, listing quota checks.
- `SubscriptionDAO.java`: Master plan retrieval, active user subscription check, atomic subscription activation, server-side auto-expiry scanner.
- `PaymentDAO.java`: Payment submission, atomic verification transaction (payment update + subscription activation + notification + audit log).
- `RentalRequestDAO.java`: Inquiry creation, landlord accept/reject with reply note, tenant cancellation.
- `FavoriteDAO.java`: Add/remove favorites, user saved list, quota checking.
- `NotificationDAO.java`: User notifications dispatch and retrieval.
- `AdminActivityLogDAO.java`: Admin audit trail logger.
- `ReportDAO.java`: User report submission and resolution.

### C. Security & Utilities (`com.thikana.filter`, `com.thikana.util`)
- `AuthFilter.java`: Role-based URL access control for `/tenant/*`, `/landlord/*`, `/admin/*`, `/subscription/*`.
- `CsrfFilter.java`: Cryptographic CSRF token generation and validation on state-changing POST requests.
- `PasswordUtil.java`: BCrypt hashing (12 rounds), verification, secure 6-digit random OTP generation.
- `FileUploadUtil.java`: Validates MIME types, max file sizes (<5MB), file extensions (`.jpg`, `.jpeg`, `.png`, `.webp`), saves with UUIDs to avoid directory traversal.
- `EmailUtil.java`: Jakarta Mail SMTP dispatcher with built-in development fallback.

### D. Controllers & Servlets (`com.thikana.controller`)
1. `HomeServlet.java` (`/home`, `""`): Landing page with featured properties, search counters, and statistics.
2. `BrowsePropertiesServlet.java` (`/properties`): Multi-filter catalog, sorting, and pagination.
3. `PropertyDetailsServlet.java` (`/property-details`): Detailed specs, high-res image carousel, landlord card, viewing modal.
4. `LoginServlet.java` (`/login`): Authentication with remember-me and role redirect.
5. `RegisterServlet.java` (`/register`): User account creation (Tenant or Landlord).
6. `LogoutServlet.java` (`/logout`): Session invalidation and cookie cleanup.
7. `ForgotPasswordServlet.java` (`/forgot-password`): Step 1 email submission and OTP generation.
8. `VerifyOtpServlet.java` (`/verify-otp`): Step 2 6-digit OTP verification with attempt counters.
9. `ResetPasswordServlet.java` (`/reset-password`): Step 3 secure password reset.
10. `TenantDashboardServlet.java` (`/tenant/dashboard`): Tenant portal (Overview, inquiries, favorites, pass, profile).
11. `LandlordDashboardServlet.java` (`/landlord/dashboard`): Landlord portal (KPIs, listings table, inquiries, billing, profile).
12. `AddPropertyServlet.java` (`/landlord/add-property`): Multi-image property submission with quota checking.
13. `EditPropertyServlet.java` (`/landlord/edit-property`): Property modification form.
14. `DeletePropertyServlet.java` (`/landlord/delete-property`): Soft/hard listing removal.
15. `SubscriptionServlet.java` (`/subscription`): Pricing matrix and payment modal.
16. `PaymentServlet.java` (`/payment/submit`): Bangladeshi payment transaction submission (bKash/Nagad/Bank).
17. `RentalRequestServlet.java` (`/rental-request`): Viewing requests management.
18. `FavoriteServlet.java` (`/favorite`): Bookmark toggle controller.
19. `ReportServlet.java` (`/report-property`): Listing abuse reporting.
20. `AdminDashboardServlet.java` (`/admin/dashboard`): Super-admin control center.

### E. Frontend Views & Custom UI (`src/main/webapp`)
- `assets/css/style.css`: 100% custom CSS design system using CSS variables, flexbox, CSS grid, modals, badges, cards, and responsive media queries.
- `assets/js/main.js`: Mobile drawer, tab navigation, modals, thumbnail image switcher, auto-dismiss alerts, confirmation dialogs.
- `index.jsp`: Commercial homepage featuring 9 full sections.
- `properties.jsp`: Search and filter catalog with sidebar and sorting.
- `property-details.jsp`: High-res gallery, amenities checklist, viewing modal, report modal.
- `login.jsp` & `register.jsp`: Authentication views with CSRF tokens.
- `forgot-password.jsp`, `verify-otp.jsp`, `reset-password.jsp`: 3-step OTP recovery flow.
- `subscription.jsp`: Tabbed plans for Landlords & Tenants with bKash/Nagad instructions.
- `tenant/dashboard.jsp`: Multi-tab tenant dashboard.
- `landlord/dashboard.jsp`: Multi-tab landlord dashboard.
- `landlord/add-property.jsp`: Comprehensive property listing submission form.
- `landlord/edit-property.jsp`: Property editing view.
- `admin/dashboard.jsp`: Comprehensive administrative control center.

---

## 4. Complete Database Design (12 Tables)

Database schema file: `database/thikana_db.sql`

1. **`users`**: `user_id` (PK), `full_name`, `email`, `phone`, `password_hash`, `role` (TENANT, LANDLORD, ADMIN), `status` (ACTIVE, SUSPENDED, BANNED), `profile_image`, `created_at`, `updated_at`.
2. **`properties`**: `property_id` (PK), `landlord_id` (FK), `title`, `description`, `address`, `city`, `area`, `monthly_rent`, `property_type`, `bedrooms`, `bathrooms`, `balconies`, `floor_no`, `size_sqft`, `available_from`, `furnishing_status`, `amenities`, `contact_number`, `approval_status`, `listing_status`, `rejection_reason`, `is_featured`, `created_at`, `updated_at`.
3. **`property_images`**: `image_id` (PK), `property_id` (FK), `image_path`, `is_primary`, `created_at`.
4. **`subscription_plans`**: `plan_id` (PK), `plan_name`, `target_role`, `price`, `duration_days`, `listing_limit`, `features`, `is_active`, `created_at`.
5. **`subscriptions`**: `subscription_id` (PK), `user_id` (FK), `plan_id` (FK), `start_date`, `end_date`, `status`, `payment_id` (FK), `created_at`.
6. **`payments`**: `payment_id` (PK), `user_id` (FK), `plan_id` (FK), `payment_method` (bKash, Nagad, Bank), `sender_phone`, `trx_id`, `amount`, `receipt_image`, `payment_status` (PENDING, VERIFIED, REJECTED), `admin_notes`, `verified_by` (FK), `verified_at`, `created_at`.
7. **`rental_requests`**: `request_id` (PK), `property_id` (FK), `tenant_id` (FK), `request_type`, `preferred_date`, `message`, `status` (PENDING, APPROVED, REJECTED, CANCELLED), `landlord_reply`, `created_at`, `updated_at`.
8. **`favorites`**: `favorite_id` (PK), `tenant_id` (FK), `property_id` (FK), `created_at`.
9. **`password_reset_otps`**: `otp_id` (PK), `email`, `otp_hash`, `attempts`, `expires_at`, `is_used`, `created_at`.
10. **`notifications`**: `notification_id` (PK), `user_id` (FK), `title`, `message`, `link`, `is_read`, `created_at`.
11. **`reports`**: `report_id` (PK), `property_id` (FK), `reporter_id` (FK), `reason`, `details`, `status`, `created_at`.
12. **`admin_activity_logs`**: `log_id` (PK), `admin_id` (FK), `action_type`, `target_entity`, `target_id`, `details`, `created_at`.

---

## 5. Issues Diagnosed & Resolved

During development and testing, several critical real-world edge cases were diagnosed and permanently resolved:

### 1. JSTL Missing in Jakarta EE / Tomcat 10 (`NoClassDefFoundError: ConditionalTagSupport`)
- **Cause**: Tomcat 10.1 and Jetty 12 implement Jakarta EE 10 specifications and no longer include legacy JSTL libraries by default. The GlassFish implementation alone lacked the `jakarta.servlet.jsp.jstl-api` classes.
- **Resolution**: Added `jakarta.servlet.jsp.jstl-api:3.0.0` with `runtime` scope in `pom.xml`, packaging all 8 required JARs directly inside `WEB-INF/lib`.

### 2. Model & DAO Timestamp Conversion Mismatch
- **Cause**: SQL `java.sql.Timestamp` returned by ResultSet could not be cast directly to Java 21 `LocalDateTime` in `UserDAO.mapRow()`.
- **Resolution**: Updated `UserDAO` to safely convert timestamps using `ca.toLocalDateTime()` and restored the complete `User.java` class structure.

### 3. Stale Class Files in Local Build Cache (`PasswordUtil cannot be resolved`)
- **Cause**: Jetty server was running stale `.class` bytecode generated before `PasswordUtil` was compiled.
- **Resolution**: Executed `mvn clean compile war:war`, clearing target artifacts and re-compiling all 46 Java classes cleanly.

### 4. Admin & Dashboard Actions Failing with `HTTP ERROR 403 Invalid CSRF Token`
- **Cause**: `CsrfFilter` strictly inspects POST requests for a valid `csrfToken`. The forms on `admin/dashboard.jsp`, `landlord/dashboard.jsp`, `tenant/dashboard.jsp`, `property-details.jsp`, `properties.jsp`, and `index.jsp` lacked the hidden token input.
- **Resolution**: Added `<input type="hidden" name="csrfToken" value="${sessionScope.csrfToken}">` to all administrative action forms (property approval, payment verification, user suspension, plan saving, reports resolution) and client forms.

---

## 6. How to Run the Application

### Option A: Embedded Jetty (Quickest — Port 8081)
Open a terminal in `D:\Projects\Thikana\Thikana` and run:
```powershell
& "C:\Program Files\JetBrains\IntelliJ IDEA 2025.3.1\plugins\maven\lib\maven3\bin\mvn.cmd" jetty:run
```
Visit: `http://localhost:8081/Thikana/`

### Option B: Apache Tomcat 10.1 (Production Standard — Port 8080)
1. Ensure the Windows service **Apache Tomcat 10.1 Tomcat10** is started.
2. Deploy the pre-built WAR artifact:
   ```powershell
   Copy-Item "D:\Projects\Thikana\Thikana\target\Thikana.war" "C:\Program Files\Apache Software Foundation\Tomcat 10.1\webapps\"
   ```
3. Visit: `http://localhost:8080/Thikana/`

---

## 7. Seed Accounts for Testing

| Role | Email | Password | Pre-loaded State |
| :--- | :--- | :--- | :--- |
| **Super Admin** | `admin@thikana.com` | `admin123` | Control center, approval queues, revenue KPIs |
| **Landlord 1** | `raisa.owner@thikana.com` | `landlord123` | Active Business Plan, 4 properties in Dhaka |
| **Landlord 2** | `meem.owner@thikana.com` | `landlord123` | Active Professional Plan, 3 properties |
| **Tenant 1** | `tanvir.tenant@thikana.com` | `tenant123` | Active 30-Day Tenant Pass, saved favorites |
| **Tenant 2** | `anisa.tenant@thikana.com` | `tenant123` | Free Standard Tier, viewing requests |

---
© 2026 Thikana Platform. Built with Java 21, Jakarta Servlets 6.0 & MySQL.
