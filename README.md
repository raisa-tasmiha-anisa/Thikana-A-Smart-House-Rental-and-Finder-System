# THIKANA – Find Your Perfect Place to Call Home
### Smart House Rental & Finder Platform
**Course Project**: SE 205 – Software Requirement Analysis and Specifications 
**Institution**: University of Frontier Technology, Bangladesh (UFTB)  
**Academic Supervisor**: Md. Moshiur Rahman  
**Authors & Presenting Students**:
- **Raisa Tasmiha Anisa** – Student ID: `2403029`

---

## 1. Executive Summary

**Thikana** is a full-stack, enterprise-grade real estate rental marketplace designed and developed specifically for the housing landscape of Bangladesh. It connects three primary stakeholders:

1. **Tenants / House Seekers**: Search verified rental homes, apartments, and bachelor sublets with multi-criteria filters, schedule physical property viewings, save favorites, and activate temporary access passes for priority support.
2. **Landlords / Property Owners**: Purchase tiered monthly subscription packages (Starter, Professional, Business), publish multi-image property advertisements subject to admin verification, review tenant viewing applications, and accept/reject booking inquiries.
3. **System Administrators**: Centrally moderate listings, audit user accounts, verify manual Bangladeshi mobile financial service transactions (**bKash**, **Nagad**, and **Bank Transfer**), adjust subscription plan pricing/duration, resolve listing reports, and inspect security audit logs.

The platform has been engineered in strict adherence to **pure Java Enterprise technologies**—utilizing **Jakarta Servlets 6.0**, **Jakarta Server Pages (JSP)**, and **raw JDBC** over **MySQL/MariaDB**, backed by a 100% custom-crafted **CSS3 design system** and **Vanilla JavaScript** (without Bootstrap, Node.js, Spring Boot, or frontend framework dependencies).

---

## 2. Technology Stack & Specifications

| Layer / Component | Technology | Exact Version | Purpose & Compliance Note |
| :--- | :--- | :--- | :--- |
| **Runtime Environment** | Java SE Development Kit (JDK) | **JDK 21 LTS** | Modern Java virtual machine runtime |
| **Application Server** | Apache Tomcat | **Tomcat 10.1.x** | Jakarta EE 10 Servlet Container |
| **Servlet Specification** | Jakarta Servlet API | **6.0.0** | `jakarta.servlet.*` namespace standard |
| **Server Pages** | Jakarta Standard Tag Library (JSTL) | **3.0.0** | Server-side templating without scriptlets |
| **Relational Database** | MariaDB / MySQL (XAMPP) | **10.4.x / 8.0+** | ACID-compliant relational persistence (`thikana_db`) |
| **Database Connectivity** | MySQL Connector/J | **8.3.0** | Type-4 JDBC pure Java driver |
| **Password & OTP Security** | jBCrypt | **0.4** | Blowfish crypt hashing with 12 salt rounds |
| **Email Subsystem** | Jakarta Mail API | **2.0.1** | RFC 822 SMTP transactional mailer |
| **Frontend Layout** | Custom HTML5 & CSS3 | **Native CSS3** | Custom palette: Navy `#142B4A`, Emerald `#159B79` |
| **Client Scripting** | Vanilla JavaScript | **ES6+** | Zero external frameworks or libraries |
| **Build Automation** | Apache Maven | **3.9.x** | Standard enterprise build & packaging tool (`.war`) |

---

## 3. High-Level System Architecture

Thikana follows an enterprise **3-Tier Model-View-Controller (MVC)** architectural pattern with robust cross-cutting security filters:

```
+---------------------------------------------------------------------------------------+
|                                    CLIENT BROWSER                                     |
|                       (HTML5, CSS3 Custom Design System, Vanilla JS)                  |
+---------------------------------------------------------------------------------------+
                                           | HTTP Requests (GET / POST / Multipart)
                                           v
+---------------------------------------------------------------------------------------+
|                                 SECURITY & FILTER LAYER                               |
|   +------------------------------------+    +-------------------------------------+   |
|   | CsrfFilter: Anti-Forgery Token     |    | AuthFilter: Role-Based Access       |   |
|   | Validation across all POST actions |    | (/tenant/*, /landlord/*, /admin/*)  |   |
|   +------------------------------------+    +-------------------------------------+   |
+---------------------------------------------------------------------------------------+
                                           | Filtered & Authorized Requests
                                           v
+---------------------------------------------------------------------------------------+
|                               CONTROLLER LAYER (SERVLETS)                             |
|  * HomeServlet             * BrowsePropertiesServlet    * PropertyDetailsServlet      |
|  * LoginServlet            * RegisterServlet            * LogoutServlet               |
|  * ForgotPasswordServlet   * VerifyOtpServlet           * ResetPasswordServlet        |
|  * TenantDashboardServlet  * LandlordDashboardServlet   * AdminDashboardServlet       |
|  * AddPropertyServlet      * EditPropertyServlet        * DeletePropertyServlet       |
|  * SubscriptionServlet     * PaymentServlet             * RentalRequestServlet        |
|  * FavoriteServlet         * ReportServlet                                            |
+---------------------------------------------------------------------------------------+
                                           | Data Transfer / Business Logic
                                           v
+---------------------------------------------------------------------------------------+
|                             DATA ACCESS LAYER (JDBC DAOs)                             |
|  * UserDAO                 * PropertyDAO                * SubscriptionDAO             |
|  * PaymentDAO              * RentalRequestDAO           * FavoriteDAO                 |
|  * NotificationDAO         * AdminActivityLogDAO        * ReportDAO                   |
+---------------------------------------------------------------------------------------+
                                           | Parameterized SQL Queries & Transactions
                                           v
+---------------------------------------------------------------------------------------+
|                                  DATABASE ENGINE                                      |
|                       MySQL / MariaDB Database (`thikana_db`)                          |
|                       12 Normalized Relational Tables (InnoDB)                        |
+---------------------------------------------------------------------------------------+
```

---

## 4. Database Design & Relational Schema

The database is built on **MySQL/MariaDB (`thikana_db`)** using the **InnoDB storage engine** with full foreign key constraints and UTF-8 multi-byte encoding (`utf8mb4`).

### Schema Overview: 12 Tables

1. **`users`**: Master user credentials, roles (`TENANT`, `LANDLORD`, `ADMIN`), account status (`ACTIVE`, `INACTIVE`, `BANNED`), phone numbers, and profile avatars.
2. **`properties`**: Property advertisements including title, description, address, city, area, rent amount, property type (`APARTMENT`, `FAMILY_HOUSE`, `SUBLET_ROOM`, `STUDENT_MESS`, `HOSTEL`), bedrooms, bathrooms, size, status (`PENDING_APPROVAL`, `APPROVED`, `REJECTED`, `EXPIRED`, `RENTED`), and verification flags.
3. **`property_images`**: High-resolution property photos associated with listings (`image_path`, `is_primary` flag).
4. **`subscription_plans`**: Master subscription tier definitions (`STARTER`, `PROFESSIONAL`, `BUSINESS`, `TENANT_PASS`), user role targets, monthly/daily fees, listing quotas, duration in days, and feature matrices.
5. **`subscriptions`**: Active and historical subscriptions linked to users and plans, storing `start_date`, `end_date`, and status (`ACTIVE`, `EXPIRED`, `PENDING_PAYMENT`, `CANCELLED`).
6. **`payments`**: Manual payment verification records for Bangladeshi payment gateways (bKash, Nagad, Bank Transfer) containing sender phone number, Transaction ID (`trx_id`), receipt screenshots, approval state, and timestamps.
7. **`rental_requests`**: Tenant viewing requests and booking inquiries sent to landlords with proposed move-in dates, personal messages, and status (`PENDING`, `APPROVED`, `REJECTED`, `CANCELLED`).
8. **`favorites`**: Saved property bookmarks for prospective tenants.
9. **`password_reset_otps`**: Time-limited 6-digit OTP security tokens stored with BCrypt hashes, attempt tracking, and expiry timestamps.
10. **`notifications`**: Real-time in-app alerts dispatched to users on status transitions (e.g., listing approval, payment approval, request replies).
11. **`reports`**: User-submitted moderation reports regarding fraudulent properties or inappropriate listings.
12. **`admin_activity_logs`**: Tamper-evident audit trail capturing administrative interventions, IP addresses, and affected entities.

---

## 5. Default User Accounts for Evaluation

All seeded passwords have been secured with **BCrypt (12 rounds)**. The following accounts are pre-seeded in `database/thikana_db.sql`:

| Role | Name | Registered Email | Password | Pre-configured State / Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Administrator** | Thikana Super Admin | `admin@thikana.com` | `admin123` | Full administrative control & platform moderation |
| **Landlord 1** | Raisa Tasmiha Anisa | `raisa.owner@thikana.com` | `landlord123` | Active Business Plan; 4 listed properties in Dhaka |
| **Landlord 2** | Irin Jahan Meem | `meem.owner@thikana.com` | `landlord123` | Active Professional Plan; 3 listed properties |
| **Tenant 1** | Tanvir Ahmed | `tanvir.tenant@thikana.com` | `tenant123` | Active 30-Day Tenant Pass; saved favorites & inquiries |
| **Tenant 2** | anisa Tabassum | `anisa.tenant@thikana.com` | `tenant123` | Free Standard Tier; active rental inquiries |

---

## 6. Setup & Installation Guide

### Prerequisites
1. **JDK 21 LTS** installed and configured on your system PATH (`java -version`).
2. **XAMPP** (or standalone MariaDB / MySQL 8.x) running on `localhost:3306`.
3. **Apache Tomcat 10.1.x** (or an IDE with Tomcat 10.1 integration such as IntelliJ IDEA, NetBeans 19+, or VS Code).
4. **Apache Maven 3.9+** (optional if using IDE bundled Maven).

### Step 1: Database Setup
1. Launch the **XAMPP Control Panel** and start **MySQL** (and **Apache** if using phpMyAdmin).
2. Open your terminal or phpMyAdmin (`http://localhost/phpmyadmin`).
3. Import the database schema and seed data located at `database/thikana_db.sql`:
   ```powershell
   # Using MySQL Command Line (Windows PowerShell):
   Get-Content "D:\Projects\Thikana\database\thikana_db.sql" | & "C:\xampp\mysql\bin\mysql.exe" -u root
   ```
   *Note: If you have a password for your local root MySQL user, append `-p yourpassword`.*

### Step 2: Database Configuration
The database connection pool is managed by `com.thikana.config.DBConnection`. If your MySQL credentials differ from standard XAMPP defaults (`root` with blank password on port 3306), edit `src/main/java/com/thikana/config/DBConnection.java`:
```java
private static final String URL = "jdbc:mysql://localhost:3306/thikana_db?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true";
private static final String USERNAME = "root";
private static final String PASSWORD = "";
```

### Step 3: Compiling and Packaging the Application
To build the ready-to-deploy `.war` file, open PowerShell in the project directory:
```powershell
cd D:\Projects\Thikana\Thikana
mvn clean compile war:war
```
This produces `target/Thikana.war` (approximately 8.45 MB, bundling Jakarta Taglibs and MySQL Connector/J).

### Step 4: Deploying to Apache Tomcat 10.1
#### Option A: Standalone Tomcat
1. Copy `D:\Projects\Thikana\Thikana\target\Thikana.war` to your Tomcat `webapps/` directory:
   ```powershell
   Copy-Item "target\Thikana.war" "C:\apache-tomcat-10.1.x\webapps\"
   ```
2. Start Tomcat (`bin/startup.bat`).
3. Access the application in your browser:
   ```
   http://localhost:8081/Thikana/
   ```

#### Option B: IntelliJ IDEA
1. Open `D:\Projects\Thikana\Thikana` as a Maven Web Application project.
2. In Project Properties -> Run, configure your local **Apache Tomcat 10.1** instance.
3. Click **Run** or **Debug**. The IDE will auto-deploy and open `http://localhost:8080/Thikana/`.

---

## 7. Feature Breakdown & Module Highlights

### 1. Landing Page & Public Catalog
- **Hero Filter Bar**: Quick search by location (Dhaka, Chattogram, Sylhet), property type (Apartment, House, Sublet Room), minimum/maximum budget, and bedroom count.
- **Featured Listings**: Dynamic cards displaying monthly rent in Bangladeshi Taka (৳), location, specs, verified badge, and primary photo.
- **How It Works & Value Proposition**: Visual walkthrough for prospective tenants and property owners.
- **Responsive Navigation**: Includes mobile drawer menu, role indicator, and quick links.

### 2. Property Search, Advanced Filtering & Pagination
- **Multi-faceted Filtering**: Filter by City, Neighborhood, Property Type, Rent Range, Bedrooms, Bathrooms, and Furnishing status.
- **Dynamic Sorting**: Sort by Newest, Price (Low to High), and Price (High to Low).
- **Listing Gallery & Details**: Full image carousel, property specifications, verified landlord contact card, neighborhood highlights, and Google Maps location preview.
- **Viewing Request Modal**: Tenants can select preferred tour dates and submit direct inquiries.
- **Reporting System**: Allows users to report suspicious or duplicate properties directly to admins.

### 3. Authentication & Security Layer
- **BCrypt Password Security**: All passwords salted and hashed using 12 rounds of Blowfish encryption.
- **CSRF Token Validation**: `CsrfFilter` generates and verifies unique cryptographic tokens for every state-changing POST request.
- **Role-Based Access Control (`AuthFilter`)**: Strict URL pattern enforcement (`/admin/*`, `/landlord/*`, `/tenant/*`, `/subscription/*`). Unauthorized attempts are redirected to login with flash warnings.
- **File Upload Protection**: `FileUploadUtil` enforces strict MIME-type sniffing, maximum file size (5MB per image), allowed extensions (`.jpg`, `.jpeg`, `.png`, `.webp`), and stores uploads with randomized UUID filenames to prevent remote code execution and directory traversal.

### 4. 3-Step Password Recovery with Secure OTP
- **Step 1 (`forgot-password.jsp`)**: User enters registered email.
- **Step 2 (`verify-otp.jsp`)**: System generates a secure 6-digit OTP, hashes it in the database with a 15-minute expiration timestamp, and limits incorrect attempts.
- **Step 3 (`reset-password.jsp`)**: Upon valid OTP verification, the user securely resets their password.
- **Academic / Dev-Mode Testing Fallback**: If an external SMTP relay is not configured in local development, `ForgotPasswordServlet` automatically surfaces the generated OTP in a clear, secure dev-mode alert box directly on the screen for effortless academic evaluation.

### 5. Tenant Control Panel (`/tenant/dashboard`)
- **Dashboard Overview**: Welcome banner, active pass status, total saved properties, active inquiries, approved bookings.
- **My Rental Inquiries**: Real-time status tracker (`PENDING`, `APPROVED`, `REJECTED`, `CANCELLED`) with landlord replies and viewing dates.
- **Saved Favorites**: One-click bookmarking of properties with quota management.
- **Tenant Temporary Pass**: Subscription status tracker and renewal portal.
- **Account Settings**: Profile updater and secure password changer.

### 6. Landlord Control Panel (`/landlord/dashboard`)
- **Performance KPIs**: Total properties, active listings, pending approvals, total tenant inquiries, and subscription validity.
- **Property Management (CRUD)**:
  - Add Property (`/landlord/add-property`) with multi-image drag-and-drop file upload.
  - Quota Enforcement: System automatically blocks new property submissions if the landlord has exceeded their plan's listing allowance.
  - Edit Property (`/landlord/edit-property`) with pre-filled inputs.
  - Delete Property (`/landlord/delete-property`) with safety confirmation.
- **Tenant Inquiries Moderation**: View tenant profiles, contact phone numbers, proposed viewing dates, and approve or reject with custom landlord reply notes.
- **Billing & Subscription History**: Active plan badge, days remaining, and payment logs.

### 7. Subscription System & Bangladesh Payment Verification
- **Dual Plan Architecture**:
  - **Landlord Plans**: Starter (3 listings, ৳499/mo), Professional (8 listings, ৳999/mo, Featured badge), Business (25 listings, ৳1899/mo, Top priority).
  - **Tenant Temporary Pass**: 15-Day Pass (৳149) or 30-Day Pass (৳249) unlocking priority inquiries and unlimited favorites.
- **Bangladeshi Mobile Financial Services Support**:
  - **bKash** (Merchant / Personal Number: `01712-345678`)
  - **Nagad** (Merchant / Personal Number: `01812-345678`)
  - **Bank Transfer** (Brac Bank / Dutch-Bangla Bank)
- **Manual Payment Submission & Admin Audit**:
  - Landlord or Tenant selects plan and submits sender phone number, Transaction ID (e.g., `TRX-BK784912`), and payment receipt screenshot.
  - Payment enters `PENDING` state.
  - Admin verifies the transaction in the Admin Dashboard.
  - Upon approval, an atomic database transaction activates the subscription, computes the expiration timestamp, creates a notification, and logs the action in the audit trail.

### 8. Server-Side Automatic Subscription Expiry Handling
- `SubscriptionDAO.checkAndExpireSubscriptions()` executes automatically during daily requests or authentication events.
- Once a subscription passes its `end_date`, its status is updated to `EXPIRED`.
- For landlords, properties exceeding active subscription coverage are automatically downgraded from public search until subscription renewal.

### 9. Super-Admin Dashboard (`/admin/dashboard`)
- **System Metrics**: Total users, total active listings, pending property submissions, pending payment verifications, and total verified platform revenue in ৳.
- **Property Approvals**: Review newly submitted landlord listings, inspect uploaded images, and Approve or Reject with feedback.
- **Payment Verification**: Verify bKash / Nagad TrxIDs and receipts, and Approve or Reject payments.
- **User Management**: View all platform users, filter by role, and Toggle Ban/Unban status.
- **Plan Pricing Management**: Update monthly fees, listing limits, and plan durations dynamically without modifying code.
- **Listing Reports Resolution**: Inspect community reports and take moderation actions.
- **Audit Logs**: Comprehensive activity log showing timestamp, admin user, action taken, and target ID.

---

## 8. Step-by-Step Evaluation Walkthrough

Follow this 7-step guide to evaluate every major functionality of the application:

### Scenario 1: Authentication & Role Dashboards
1. Navigate to `http://localhost:8080/Thikana/login`.
2. Login as Administrator (`admin@thikana.com` / `admin123`).
   - Notice the administrative KPI bar, pending property approvals, and revenue overview.
3. Logout and login as Landlord (`raisa.owner@thikana.com` / `landlord123`).
   - Notice the Landlord Dashboard showing 4 properties, active Business plan, and incoming inquiries.
4. Logout and login as Tenant (`tanvir.tenant@thikana.com` / `tenant123`).
   - Notice the Tenant Dashboard showing saved favorites, rental requests, and active Tenant Pass.

### Scenario 2: Testing Forgot Password & OTP Flow
1. Click **Login** and select **Forgot Password?** (or visit `/forgot-password`).
2. Enter `tanvir.tenant@thikana.com` and submit.
3. The system navigates to `/verify-otp`.
   - *Dev-Mode Notice*: The 6-digit OTP is conveniently displayed in the testing alert banner (e.g., `Generated OTP for evaluation: 582194`).
4. Enter the 6-digit OTP and submit.
5. On the Reset Password screen, enter a new password (e.g., `newpass123`), confirm, and submit.
6. The user is redirected to the login page with a success confirmation. You can now login with `newpass123`.

### Scenario 3: Property Search, Filtering & Viewing Inquiries
1. Visit the public **Browse Properties** page (`/properties`).
2. Select City: `Dhaka`, Type: `Apartment`, Max Rent: `40000`, and click **Apply Filters**.
3. Click on a property card to open its detailed page (`/property-details?id=1`).
4. Inspect the image carousel, amenities, and landlord profile card.
5. If logged in as a Tenant, click **Schedule a Viewing**. Choose a date, write an inquiry message, and submit.
6. Log in as the corresponding landlord (`raisa.owner@thikana.com`) and visit the **Inquiries** tab in the Landlord Dashboard to view the inquiry and click **Approve** with a reply message.

### Scenario 4: Landlord Listing Submission & Admin Moderation
1. Log in as Landlord (`meem.owner@thikana.com` / `landlord123`).
2. Click **List Your Property** (`/landlord/add-property`).
3. Fill out the property title, location (e.g., *Green Road, Farmgate, Dhaka*), rent amount (`22000`), bedrooms, bathrooms, and select property photos.
4. Submit the form. Notice the property is saved with `PENDING_APPROVAL` status.
5. Logout and log in as Admin (`admin@thikana.com` / `admin123`).
6. In the **Pending Property Approvals** section, locate the new listing and click **Approve Listing**.
7. Visit `/properties` as a public guest: the property is now live and searchable!

### Scenario 5: Subscription Purchase & Bangladesh Payment
1. Log in as Landlord or Tenant and visit **Subscription Plans** (`/subscription`).
2. Select a desired plan (e.g., **Professional Landlord Plan** or **Tenant 30-Day Pass**).
3. Select payment method: **bKash** or **Nagad**.
4. Enter the sender mobile number (`017XXXXXXXX`), an arbitrary Transaction ID (e.g., `BKASH-998241`), and click **Submit Payment Details**.
5. Log in as Admin (`admin@thikana.com`).
6. Under **Pending Payment Verifications**, click **Verify & Activate Subscription**.
7. The payment is marked as `VERIFIED`, the user's subscription is activated, and expiry date is automatically calculated.

---

## 9. Academic Integrity & Project Details

This software project has been conceived, designed, and implemented from scratch by **Raisa Tasmiha Anisa**  under the academic supervision of **Md. Moshiur Rahman** for course **SE 205 (Software Requirement Analysis and Specifications)** at the **University of Frontier Technology, Bangladesh (UFTB)**.

All code, architectural designs, database schemas, and documentation are strictly developed for academic evaluation and real-world deployment demonstration.

---
© 2026 Thikana Platform. All rights reserved. Built with Java, Jakarta EE & MySQL.
