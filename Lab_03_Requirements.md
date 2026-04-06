# Lab 3: Requirements Specification (SRS) - Chitkaar Welfare Society Platform

## 1. Introduction

### 1.1 Purpose
The purpose of this Software Requirements Specification (SRS) document is to provide a comprehensive and detailed description of the Chitkaar Welfare Society platform. This document outlines the functional and non-functional requirements, system constraints, and external interfaces necessary for the development of a robust web-based application. It serves as the primary reference for developers, stakeholders, and testers to ensure the final product meets the organizational goals of facilitating NGO activities.

### 1.2 Scope
The Chitkaar Welfare Society platform is a dedicated web application designed to digitize and streamline the NGO’s operations. The software will focus on:
*   **Event Management:** Enabling the creation, display, and management of charitable events.
*   **Volunteer Engagement:** Facilitating seamless volunteer registration and data collection.
*   **Digital Presence:** Showcasing the NGO’s impact through a dynamic media gallery.
*   **Administrative Control:** Providing a secure dashboard for content and data management.

**Note:** The system explicitly excludes payment gateway integration at this stage; all donations are processed offline or via static UPI QR codes.

### 1.3 Definitions, Acronyms, and Abbreviations
*   **SRS:** Software Requirements Specification
*   **NGO:** Non-Governmental Organization
*   **CMS:** Content Management System (Contentful)
*   **CRUD:** Create, Read, Update, Delete
*   **NFR:** Non-Functional Requirement
*   **UI/UX:** User Interface / User Experience

## 2. Overall Description

### 2.1 Product Perspective
The product is a standalone web application that operates within a serverless architecture. It interfaces with external services for specific functionalities:
*   **Contentful:** Headless CMS for managing dynamic content (events, gallery images).
*   **Firebase/Firestore:** NoSQL database for storing volunteer user data and form submissions.
*   **Resend:** API service for transactional email communications.
*   **Vercel:** Hosting and edge network provider.

### 2.2 Product Functions
The major functions of the system include:
1.  **Public Viewing:** Users can browse detailed information about the NGO, its mission, and past/upcoming events.
2.  **Volunteer Registration:** Users can sign up for events using a validated digital form.
3.  **Content Management:** Admins can add, update, or remove events and gallery images without touching code.
4.  **Automated Communication:** The system automatically sends confirmation emails upon successful registration.

### 2.3 User Classes and Characteristics
*   **Guest User (Volunteer/Donor):** Generic web users with basic computer literacy. They interact with the public-facing pages to view content and register for events.
*   **Administrator:** Technical or semi-technical users with privileged access. They manage the platform's content via the CMS and view registration data via the dashboard.

### 2.4 Prerequisites & Dependencies
*   **Version Control:** Git must be installed and configured.
*   **Runtime Environment:** Node.js (v18.0.0 or later) and npm/pnpm/yarn package manager.
*   **Service Accounts:** Active accounts and API keys for Contentful, Firebase, Resend, and Vercel.

## 3. System Features (Functional Requirements)

### 3.1 Use Case Diagram
The following diagram illustrates the primary actors and their interactions with the system.

```mermaid
usecaseDiagram
    actor "Volunteer" as V
    actor "Administrator" as A
    
    package "Chitkaar System" {
        usecase "View Event Listings" as UC1
        usecase "Register for Event" as UC2
        usecase "View Gallery" as UC3
        usecase "Admin Login" as UC4
        usecase "Manage Events (CRUD)" as UC5
        usecase "View Registrations" as UC6
        usecase "Export Data" as UC7
    }

    V --> UC1
    V --> UC2
    V --> UC3
    
    A --> UC4
    A --> UC5
    A --> UC6
    A --> UC7
    
    UC2 .> UC1 : extends
```

### 3.2 Feature Description: Event Management (`FR-EVENT`)
*   **Description:** The core module for handling all event-related data.
*   **FR-EVENT-01:** The system shall fetch and display a list of upcoming and past events from Contentful.
*   **FR-EVENT-02:** Users shall be able to view detailed descriptions, dates, and locations for each event.
*   **FR-EVENT-03:** Users shall be able to filter events by category (e.g., Education, Healthcare, Food Drives).
*   **FR-EVENT-04:** Admins shall have the ability to CRUD (Create, Read, Update, Delete) event records via the CMS.
    *   **Input:** Event Title, Date, Description, Image.
    *   **Processing:** Validation of dates (must be future date for new events), Image optimization.
    *   **Output:** Updated Event List on the frontend.

### 3.3 Feature Description: Volunteer Registration (`FR-REG`)
*   **Description:** The process enabling users to sign up for specific events.
*   **FR-REG-01:** The system shall verify if an event has open slots before allowing registration.
*   **FR-REG-02:** The system shall provide a form collecting Name, Email, and Phone Number.
*   **FR-REG-03:** The system must validate input fields (e.g., valid email format, 10-digit phone number) on the client side before submission.
*   **FR-REG-04:** Upon successful submission, the data must be persisted to the Firebase Firestore database.
*   **FR-REG-05:** The system shall prevent duplicate registrations for the same event by the same email/phone.

### 3.4 Feature Description: Communications (`FR-COMM`)
*   **Description:** Automated email handling.
*   **FR-COMM-01:** The system must trigger an automated email via the Resend API immediately after a successful registration.
*   **FR-COMM-02:** The email must contain event details (Time, Venue) and a unique registration reference.

### 3.5 Feature Description: Media Gallery (`FR-GALLERY`)
*   **Description:** A visual showcase of past events.
*   **FR-GALLERY-01:** The system shall display a responsive masonry or grid layout of images.
*   **FR-GALLERY-02:** Images must be optimized (lazy-loaded) to ensure performance.
*   **FR-GALLERY-03:** Users shall be able to click an image to view it in a lightbox/modal overlay.

## 4. Non-Functional Requirements (NFR)

### 4.1 Performance
*   **NFR-PERF-01:** The landing page First Contentful Paint (FCP) must occur within 1.5 seconds on 4G networks.
*   **NFR-PERF-02:** The Time to Interactive (TTI) must not exceed 3.0 seconds.
*   **NFR-PERF-03:** Images must be served in next-gen formats (WebP/AVIF) to minimize payload size.

### 4.2 Reliability & Availability
*   **NFR-REL-01:** The system shall aim for 99.9% uptime, leveraging the distributed nature of the Vercel Edge Network.
*   **NFR-REL-02:** In case of API failure (e.g., Contentful is down), the system should fail gracefully, displaying cached content or a user-friendly error message.

### 4.3 Scalability
*   **NFR-SCALE-01:** The database architecture must support up to 10,000 concurrent user records without performance degradation.
*   **NFR-SCALE-02:** The system must handle traffic spikes during event announcements using serverless auto-scaling.

### 4.4 Usability
*   **NFR-USE-01:** The interface must be fully responsive, adapting verification to mobile (360px+), tablet, and desktop viewports.
*   **NFR-USE-02:** The system shall adhere to WCAG 2.1 AA accessibility standards (e.g., proper contrast, ARIA labels).

## 5. External Interface Requirements

### 5.1 User Interfaces
*   **Front-end:** Developed using React components with Tailwind CSS for styling. The design focuses on a "Premium, Calm, Trust-Driven" aesthetic.
*   **Admin Dashboard:** Utilizes the Contentful Web App interface for content entry, custom-configured for the NGO's data models.

### 5.2 Software Interfaces
*   **Contentful API:** REST/GraphQL API used for retrieving structured content (JSON).
*   **Resend API:** REST API used for dispatching transactional emails.
*   **Firebase SDK:** JavaScript SDK used for direct interaction with the Firestore NoSQL database.
