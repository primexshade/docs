# Lab 6: System Design & Architecture

## 1. Architectural Design

### 1.1 Architectural Pattern
The Chitkaar Welfare Society ecosystem is built on a **Serverless, Component-Based Architecture**. This modern approach allows us to decouple the frontend from the backend services, ensuring scalability and reducing maintenance overhead.

*   **Frontend:** Built with **Next.js** (React Framework), following the Atomic Design methodology for components.
*   **Backend:** Leverages **Next.js API Routes** (Serverless Functions) acting as the middleware between the client and third-party services.
*   **Database:** **Firebase Firestore** (NoSQL) for user data and **Contentful** (Headless CMS) for content data.

### 1.2 High-Level Architecture Diagram (HLD)

```mermaid
graph TD
    subgraph "Client Layer (Browser)"
        User[End User]
        Admin[Administrator]
        Browser[Web Browser / PWA]
    end

    subgraph "Application Layer (Vercel)"
        NextJS[Next.js Server]
        API[API Gateway / Edge Functions]
        SSR[Server Side Rendering]
    end

    subgraph "Data & Services Layer"
        Firebase[(Firebase Firestore)]
        Contentful[(Contentful CMS)]
        Resend[Resend Email API]
        Cloudinary[Cloudinary Media CDN]
    end

    User --> Browser
    Admin --> Browser
    Browser --> NextJS
    NextJS --> SSR
    SSR --> API
    
    API --> Firebase
    API --> Contentful
    API --> Resend
    API --> Cloudinary
```

## 2. Data Flow Design

### 2.1 Context Diagram (DFD Level 0)
See **Lab 1: Section 4** for the System Context Diagram.

### 2.2 DFD Level 1: Event Registration Process
This diagram details the flow of data when a user registers for an event.

```mermaid
graph LR
    User((User))
    Process1[1.0 Validation]
    Process2[2.0 Check Availability]
    Process3[3.0 Save Registration]
    Process4[4.0 Send Confirmation]
    
    DB[(Firestore)]
    Email[Email Service]
    
    User -- "Submit Form" --> Process1
    Process1 -- "Valid Data" --> Process2
    Process2 -- "Slot Available" --> Process3
    Process3 -- "Store Data" --> DB
    Process3 -- "Trigger Email" --> Process4
    Process4 -- "Send Mail" --> Email
    Email -- "Delivery Status" --> Process4
    Process4 -- "Success Msg" --> User
```

### 2.3 DFD Level 1: Admin Event Management
This diagram details how an admin creates and publishes a new event.

```mermaid
graph LR
    Admin((Admin))
    Process1[1.0 Login / Auth]
    Process2[2.0 Input Event Details]
    Process3[3.0 Upload Image]
    Process4[4.0 Publish Event]
    
    AuthDB[(Firebase Auth)]
    CMS[(Contentful)]
    CDN[Media Storage]
    
    Admin -- "Credentials" --> Process1
    Process1 -- "Verify" --> AuthDB
    AuthDB -- "Token" --> Process1
    Process1 -- "Access Granted" --> Process2
    
    Process2 -- "Event Data" --> Process4
    Process3 -- "Image File" --> CDN
    CDN -- "Image URL" --> Process4
    Process4 -- "Save Entry" --> CMS
```

## 3. Technology Stack Justification

| component | Technology | Justification |
| :--- | :--- | :--- |
| **Frontend Framework** | **Next.js 14** | Best-in-class SEO capabilities (critical for an NGO), vast ecosystem, and hybrid rendering (SSR/SSG). |
| **Styling** | **Tailwind CSS** | Utility-first approach speeds up development and ensures consistent design tokens. |
| **Database** | **Firebase** | Real-time capabilities, generous free tier, and easy integration with Google Auth. |
| **CMS** | **Contentful** | strictly separated content from code, allowing non-technical admins to update the site. |
| **Hosting** | **Vercel** | Native support for Next.js, zero-config deployment, and global Edge Network. |
