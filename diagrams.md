# DIAGRAMS & ARCHITECTURE DOCUMENTATION

## NGO Platform - Visual System Design

This document contains comprehensive Mermaid diagram code for the Chitkaar Welfare Society NGO Web Management Platform. All diagrams can be rendered in Markdown-compatible viewers (GitHub, GitLab, Notion, etc.).

---

## 1. SYSTEM ARCHITECTURE DIAGRAM

```mermaid
graph TB
    subgraph "Client Layer"
        WEB["🌐 Web Browser<br/>React/Next.js<br/>TypeScript + Tailwind CSS"]
        MOBILE["📱 Mobile App<br/>React Native/<br/>Expo"]
        ADMIN["👨‍💼 Admin Dashboard<br/>React<br/>TypeScript"]
    end

    subgraph "API Gateway & Load Balancer"
        LB["⚖️ Load Balancer<br/>Nginx/Cloudflare"]
        CORS["🔐 CORS<br/>Rate Limiting<br/>Authentication"]
    end

    subgraph "Application Layer"
        AUTH["🔑 Auth Service<br/>JWT + 2FA<br/>bcrypt"]
        VOL["👥 Volunteer Service<br/>Profile Management<br/>Hours Tracking"]
        EVENT["📅 Event Service<br/>Task Management<br/>QR Code"]
        REPORT["📊 Reporting Service<br/>Analytics<br/>Data Export"]
        CMS["📝 CMS Service<br/>Contentful Integration<br/>Content Management"]
    end

    subgraph "Business Logic Layer"
        BADGE["🏆 Badge Service<br/>Achievement System<br/>Recognition"]
        NOTIFY["📧 Notification Service<br/>Email/SMS/Push<br/>Resend/Twilio"]
        SCHED["📍 Scheduling Service<br/>Conflict Detection<br/>Calendar"]
    end

    subgraph "Data Layer"
        FIRESTORE["🔥 Firestore<br/>NoSQL Database<br/>Real-time Sync"]
        STORAGE["💾 Cloud Storage<br/>Files & Documents<br/>Images"]
        CACHE["⚡ Redis Cache<br/>Session Management<br/>Performance"]
    end

    subgraph "External Services"
        EMAIL["✉️ Resend<br/>Email Service"]
        SMS["📱 Twilio<br/>SMS Service"]
        PAY["💳 Payment Gateway<br/>Stripe/Razorpay"]
        MAPS["🗺️ Google Maps<br/>Location Services"]
        CMS_EXT["📰 Contentful<br/>Headless CMS"]
    end

    subgraph "Infrastructure"
        DEPLOY["🚀 Vercel<br/>Frontend Hosting<br/>Serverless"]
        CLOUD["☁️ Firebase<br/>Backend-as-a-Service<br/>Cloud Functions"]
        MONITOR["📈 Monitoring<br/>Sentry/LogRocket<br/>Analytics"]
    end

    WEB --> LB
    MOBILE --> LB
    ADMIN --> LB
    
    LB --> CORS
    
    CORS --> AUTH
    CORS --> VOL
    CORS --> EVENT
    CORS --> REPORT
    CORS --> CMS
    
    AUTH --> BADGE
    VOL --> BADGE
    VOL --> SCHED
    EVENT --> SCHED
    BADGE --> NOTIFY
    VOL --> NOTIFY
    EVENT --> NOTIFY
    
    AUTH --> FIRESTORE
    VOL --> FIRESTORE
    EVENT --> FIRESTORE
    REPORT --> FIRESTORE
    CMS --> FIRESTORE
    FIRESTORE --> CACHE
    
    BADGE --> STORAGE
    VOL --> STORAGE
    
    NOTIFY --> EMAIL
    NOTIFY --> SMS
    EVENT --> PAY
    EVENT --> MAPS
    CMS --> CMS_EXT
    
    VOL --> CLOUD
    EVENT --> CLOUD
    NOTIFY --> CLOUD
    
    WEB --> DEPLOY
    MOBILE --> CLOUD
    
    DEPLOY --> MONITOR
    CLOUD --> MONITOR

    style WEB fill:#e1f5ff
    style MOBILE fill:#e1f5ff
    style ADMIN fill:#e1f5ff
    style LB fill:#fff3e0
    style CORS fill:#fff3e0
    style AUTH fill:#e8f5e9
    style VOL fill:#e8f5e9
    style EVENT fill:#e8f5e9
    style BADGE fill:#f3e5f5
    style NOTIFY fill:#f3e5f5
    style SCHED fill:#f3e5f5
    style FIRESTORE fill:#fce4ec
    style STORAGE fill:#fce4ec
    style CACHE fill:#fce4ec
    style EMAIL fill:#ffe0b2
    style SMS fill:#ffe0b2
    style PAY fill:#ffe0b2
    style MAPS fill:#ffe0b2
    style DEPLOY fill:#c8e6c9
    style CLOUD fill:#c8e6c9
    style MONITOR fill:#ffccbc
```

---

## 2. USE CASE DIAGRAM

```mermaid
graph TB
    subgraph "NGO Platform"
        UC1["👤 Register Account"]
        UC2["🔑 Login with 2FA"]
        UC3["📝 Create Profile"]
        UC4["🔍 Search Events"]
        UC5["📋 Apply for Event"]
        UC6["⏱️ Check-In/Check-Out"]
        UC7["📊 View Dashboard"]
        UC8["🏆 Track Badges"]
        UC9["💬 View Notifications"]
        
        UC10["📅 Create Event"]
        UC11["👥 Assign Volunteers"]
        UC12["✅ Approve Hours"]
        UC13["📈 View Analytics"]
        UC14["🎉 Recognize Volunteer"]
        
        UC15["⚙️ Manage Users"]
        UC16["🔐 Manage Permissions"]
        UC17["📊 Financial Reports"]
        UC18["📝 Content Management"]
    end

    subgraph "Actors"
        VOLUNTEER["👨‍🤝‍👨 Volunteer"]
        COORDINATOR["📋 Coordinator"]
        ADMIN["🔐 Administrator"]
        DONOR["💰 Donor"]
        SYSTEM["🤖 System"]
    end

    VOLUNTEER --> UC1
    VOLUNTEER --> UC2
    VOLUNTEER --> UC3
    VOLUNTEER --> UC4
    VOLUNTEER --> UC5
    VOLUNTEER --> UC6
    VOLUNTEER --> UC7
    VOLUNTEER --> UC8
    VOLUNTEER --> UC9

    COORDINATOR --> UC2
    COORDINATOR --> UC10
    COORDINATOR --> UC11
    COORDINATOR --> UC12
    COORDINATOR --> UC13
    COORDINATOR --> UC14

    ADMIN --> UC2
    ADMIN --> UC15
    ADMIN --> UC16
    ADMIN --> UC17
    ADMIN --> UC18

    DONOR --> UC2
    DONOR --> UC7
    DONOR --> UC9

    SYSTEM --> UC9
    SYSTEM --> UC14
    SYSTEM --> UC8

    UC5 -.->|includes| UC6
    UC6 -.->|includes| UC12
    UC12 -.->|triggers| UC8
    UC8 -.->|triggers| UC14
    UC10 -.->|includes| UC11
    UC11 -.->|includes| UC12

    style VOLUNTEER fill:#bbdefb
    style COORDINATOR fill:#c8e6c9
    style ADMIN fill:#f8bbd0
    style DONOR fill:#ffe0b2
    style SYSTEM fill:#d1c4e9
```

---

## 3. ENTITY-RELATIONSHIP DIAGRAM (ER Diagram)

```mermaid
erDiagram
    VOLUNTEER ||--o{ VOLUNTEER_PROFILE : has
    VOLUNTEER ||--o{ TASK_ASSIGNMENT : assigned_to
    VOLUNTEER ||--o{ HOURS_LOG : logs
    VOLUNTEER ||--o{ FEEDBACK : provides
    VOLUNTEER ||--o{ BADGE_ACHIEVEMENT : earns
    VOLUNTEER ||--o{ NOTIFICATION : receives

    EVENT ||--o{ TASK : contains
    EVENT ||--o{ EVENT_ATTENDEE : has
    EVENT ||--o{ QR_CODE : generates

    TASK ||--o{ TASK_ASSIGNMENT : assigned_via
    TASK ||--o{ HOURS_LOG : tracks
    TASK ||--o{ FEEDBACK : receives

    COORDINATOR ||--o{ EVENT : manages
    COORDINATOR ||--o{ TASK_ASSIGNMENT : approves
    COORDINATOR ||--o{ HOURS_LOG : reviews

    ADMIN ||--o{ VOLUNTEER : manages
    ADMIN ||--o{ COORDINATOR : supervises
    ADMIN ||--o{ EVENT : oversees
    ADMIN ||--o{ REPORT : generates

    BADGE ||--o{ BADGE_ACHIEVEMENT : awarded_in

    VOLUNTEER_PROFILE {
        string volunteerId PK
        string firstName
        string lastName
        string email UK
        string phoneNumber
        string address
        string[] skills
        string[] languages
        string[] availableDays
        int totalHours
        int tasksCompleted
        string status
        boolean emailVerified
        string backgroundCheckStatus
        datetime createdAt
        datetime updatedAt
    }

    VOLUNTEER {
        string volunteerId PK
        string email UK
        string password
        string firstName
        string lastName
        datetime lastLogin
    }

    EVENT {
        string eventId PK
        string title
        string description
        datetime startDateTime
        datetime endDateTime
        string location
        float latitude
        float longitude
        string eventType
        string status
        int maxAttendees
        int attendeesCount
        datetime createdAt
    }

    TASK {
        string taskId PK
        string eventId FK
        string title
        string description
        string[] requiredSkills
        string difficultyLevel
        int maxVolunteers
        int volunteersAssigned
        datetime createdAt
    }

    TASK_ASSIGNMENT {
        string assignmentId PK
        string volunteerId FK
        string taskId FK
        datetime assignedDate
        string status
        datetime acceptedAt
        datetime rejectedAt
    }

    HOURS_LOG {
        string hoursId PK
        string volunteerId FK
        string taskId FK
        datetime checkInTime
        datetime checkOutTime
        float hoursLogged
        string approvalStatus
        string approvedBy FK
        string approvalNotes
        datetime createdAt
    }

    BADGE {
        string badgeId PK
        string name
        string description
        int requiredHours
        string icon
    }

    BADGE_ACHIEVEMENT {
        string achievementId PK
        string volunteerId FK
        string badgeId FK
        datetime awardedDate
        string reason
    }

    NOTIFICATION {
        string notificationId PK
        string volunteerId FK
        string message
        string type
        boolean read
        datetime sentAt
        datetime readAt
    }

    FEEDBACK {
        string feedbackId PK
        string volunteerId FK
        string taskId FK
        int rating
        string comment
        datetime createdAt
    }

    COORDINATOR {
        string coordinatorId PK
        string email UK
        string name
        string department
        datetime joinDate
    }

    ADMIN {
        string adminId PK
        string email UK
        string name
        string role
        string[] permissions
    }

    REPORT {
        string reportId PK
        string adminId FK
        string title
        string type
        datetime generatedAt
        string filePath
    }

    QR_CODE {
        string qrCodeId PK
        string eventId FK
        string taskId FK
        string code
        datetime generatedAt
        datetime expiresAt
    }

    EVENT_ATTENDEE {
        string attendeeId PK
        string eventId FK
        string volunteerId FK
        datetime registeredAt
        string status
    }
```

---

## 4. CLASS DIAGRAM

```mermaid
classDiagram
    class User {
        -userId: String
        -email: String
        -password: String
        -firstName: String
        -lastName: String
        +register()
        +login()
        +logout()
        +updateProfile()
        +changePassword()
    }

    class Volunteer {
        -volunteerId: String
        -skills: String[]
        -languages: String[]
        -availableDays: String[]
        -totalHours: int
        -tasksCompleted: int
        -badges: Badge[]
        +applyForTask()
        +checkIn()
        +checkOut()
        +viewDashboard()
        +submitFeedback()
    }

    class Coordinator {
        -coordinatorId: String
        -department: String
        -managedEvents: Event[]
        +createEvent()
        +assignVolunteer()
        +approveHours()
        +generateReport()
        +recognizeVolunteer()
    }

    class Administrator {
        -adminId: String
        -role: String
        -permissions: String[]
        +manageUsers()
        +manageEvents()
        +viewAnalytics()
        +configureSystem()
        +generateFinancialReports()
    }

    class Event {
        -eventId: String
        -title: String
        -description: String
        -startDateTime: DateTime
        -endDateTime: DateTime
        -location: String
        -maxAttendees: int
        -tasks: Task[]
        +createEvent()
        +addTask()
        +removeTask()
        +updateEvent()
        +publishEvent()
        +cancelEvent()
    }

    class Task {
        -taskId: String
        -eventId: String
        -title: String
        -requiredSkills: String[]
        -difficultyLevel: String
        -maxVolunteers: int
        -volunteersAssigned: Volunteer[]
        +assignVolunteer()
        +removeVolunteer()
        +calculateConflicts()
        +notifyVolunteers()
    }

    class TaskAssignment {
        -assignmentId: String
        -volunteerId: String
        -taskId: String
        -assignedDate: DateTime
        -status: String
        +accept()
        +reject()
        +complete()
        +cancel()
    }

    class HoursLog {
        -hoursId: String
        -volunteerId: String
        -taskId: String
        -checkInTime: DateTime
        -checkOutTime: DateTime
        -hoursLogged: float
        -approvalStatus: String
        +recordCheckIn()
        +recordCheckOut()
        +calculateHours()
        +submitForApproval()
        +approveHours()
    }

    class Badge {
        -badgeId: String
        -name: String
        -description: String
        -requiredHours: int
        -icon: String
        -awardedVolunteers: Volunteer[]
        +awardBadge()
        +removeBadge()
    }

    class Notification {
        -notificationId: String
        -recipientId: String
        -message: String
        -type: String
        -read: boolean
        -sentAt: DateTime
        +send()
        +markAsRead()
        +delete()
    }

    class Dashboard {
        -dashboardId: String
        -userId: String
        -widgets: Widget[]
        +loadDashboard()
        +refreshData()
        +addWidget()
        +removeWidget()
    }

    class AuthenticationService {
        +registerUser()
        +authenticateUser()
        +validateToken()
        +refreshToken()
        +logout()
        +enableTwoFA()
        +verifyTwoFA()
    }

    class VolunteerService {
        +createVolunteer()
        +getVolunteerProfile()
        +updateVolunteerProfile()
        +searchVolunteers()
        +getVolunteersBySkills()
        +getVolunteersByAvailability()
        +addVolunteerHours()
        +awardBadge()
    }

    class TaskService {
        +createTask()
        +assignVolunteer()
        +detectConflicts()
        +getTasksForVolunteer()
        +getTasksForEvent()
        +updateTaskStatus()
        +notifyVolunteerAssignment()
    }

    class NotificationService {
        +sendEmail()
        +sendSMS()
        +sendPushNotification()
        +sendInAppNotification()
        +bulkNotify()
    }

    User <|-- Volunteer
    User <|-- Coordinator
    User <|-- Administrator
    
    Event "1" --o "many" Task
    Task "1" --o "many" TaskAssignment
    TaskAssignment "many" --> "1" Volunteer
    TaskAssignment "many" --> "1" Task
    
    Volunteer "many" --> "many" Badge
    
    HoursLog "many" --> "1" Volunteer
    HoursLog "many" --> "1" Task
    
    Notification "many" --> "1" User
    
    Dashboard "1" --> "1" User
    
    AuthenticationService --> User
    VolunteerService --> Volunteer
    TaskService --> Task
    NotificationService --> Notification
```

---

## 5. COLLABORATION DIAGRAM

```mermaid
graph TB
    subgraph "Volunteer Collaboration"
        V["👤 Volunteer"]
        V_UI["📱 Volunteer UI"]
        AUTH["🔑 Auth Service"]
        VOL_SVC["👥 Volunteer Service"]
        FIRESTORE["🔥 Firestore"]
        NOTIFY["📧 Notification Service"]
    end

    subgraph "Coordinator Collaboration"
        C["📋 Coordinator"]
        C_UI["📊 Coordinator Dashboard"]
        EVENT_SVC["📅 Event Service"]
        TASK_SVC["📝 Task Service"]
        BADGE_SVC["🏆 Badge Service"]
    end

    V -->|1: Register/Login| V_UI
    V_UI -->|2: Send Credentials| AUTH
    AUTH -->|3: Validate Token| FIRESTORE
    FIRESTORE -->|4: Return User Data| AUTH
    AUTH -->|5: Create JWT| V_UI
    
    V_UI -->|6: Request Profile| VOL_SVC
    VOL_SVC -->|7: Query| FIRESTORE
    FIRESTORE -->|8: Return Profile| VOL_SVC
    VOL_SVC -->|9: Display Profile| V_UI
    
    V_UI -->|10: Apply for Task| VOL_SVC
    VOL_SVC -->|11: Update Assignment| FIRESTORE
    FIRESTORE -->|12: Confirmation| VOL_SVC
    VOL_SVC -->|13: Notify| NOTIFY
    NOTIFY -->|14: Send Email/SMS| V

    C -->|1: Create Event| C_UI
    C_UI -->|2: Submit Event Data| EVENT_SVC
    EVENT_SVC -->|3: Store Event| FIRESTORE
    FIRESTORE -->|4: Event ID| EVENT_SVC
    EVENT_SVC -->|5: Display Confirmation| C_UI

    C_UI -->|6: Search Volunteers| TASK_SVC
    TASK_SVC -->|7: Query| FIRESTORE
    FIRESTORE -->|8: Return Volunteers| TASK_SVC
    TASK_SVC -->|9: Display List| C_UI

    C_UI -->|10: Assign Volunteer| TASK_SVC
    TASK_SVC -->|11: Check Conflicts| FIRESTORE
    FIRESTORE -->|12: Return Schedule| TASK_SVC
    TASK_SVC -->|13: Save Assignment| FIRESTORE
    FIRESTORE -->|14: Confirmation| TASK_SVC

    C_UI -->|15: Approve Hours| BADGE_SVC
    BADGE_SVC -->|16: Calculate Hours| FIRESTORE
    FIRESTORE -->|17: Update Total Hours| FIRESTORE
    FIRESTORE -->|18: Check Badge Threshold| FIRESTORE
    BADGE_SVC -->|19: Award Badge if Earned| BADGE_SVC
    BADGE_SVC -->|20: Send Recognition| NOTIFY

    style V fill:#bbdefb
    style C fill:#c8e6c9
    style V_UI fill:#e3f2fd
    style C_UI fill:#e8f5e9
    style AUTH fill:#f3e5f5
    style VOL_SVC fill:#f3e5f5
    style EVENT_SVC fill:#f3e5f5
    style TASK_SVC fill:#f3e5f5
    style BADGE_SVC fill:#f3e5f5
    style FIRESTORE fill:#fce4ec
    style NOTIFY fill:#ffe0b2
```

---

## 6. STATE DIAGRAM

```mermaid
stateDiagram-v2
    [*] --> VolunteerRegistration

    VolunteerRegistration --> EmailVerification : Submit Registration
    EmailVerification --> VerificationPending : Verification Email Sent
    VerificationPending --> ProfileIncomplete : Email Verified
    VerificationPending --> VolunteerRegistration : Verification Failed

    ProfileIncomplete --> ReadyToApply : Complete Profile
    ReadyToApply --> SearchingTasks : Browse Tasks

    SearchingTasks --> TaskFound : Find Task
    TaskFound --> AppliedForTask : Apply for Task
    
    AppliedForTask --> AwaitingAssignment : Awaiting Assignment Review
    AwaitingAssignment --> AssignmentRejected : Application Rejected
    AssignmentRejected --> ReadyToApply : Browse Again
    AwaitingAssignment --> AssignmentAccepted : Application Accepted

    AssignmentAccepted --> AwaitingEventDate : Task Assigned

    AwaitingEventDate --> TaskInProgress : Event Date Arrived
    TaskInProgress --> CheckedIn : Volunteer Checked In
    CheckedIn --> Working : Volunteer Working on Task
    Working --> CheckedOut : Volunteer Checked Out

    CheckedOut --> HoursAwaitingApproval : Hours Logged
    HoursAwaitingApproval --> HoursApproved : Hours Approved
    HoursApprovalRejected --> HoursAwaitingApproval : Resubmit Hours

    HoursApproved --> EvaluateBadge : Check Badge Eligibility
    EvaluateBadge --> BadgeEarned : Badge Threshold Reached
    EvaluateBadge --> NoBadge : Badge Not Earned
    BadgeEarned --> RecognitionReceived : Badge Awarded & Notification Sent
    NoBadge --> TaskCompleted : Task Completed

    RecognitionReceived --> TaskCompleted
    TaskCompleted --> DashboardUpdated : Dashboard Refreshed
    DashboardUpdated --> SearchingTasks : Browse More Tasks

    SearchingTasks --> Inactive : No Activity for 30 Days
    Inactive --> [*]

    note right of VolunteerRegistration
        User creates account
        with email and password
    end note

    note right of EmailVerification
        Email link sent
        for verification
    end note

    note right of AppliedForTask
        Volunteer interest
        registered in system
    end note

    note right of CheckedIn
        QR code scanned
        at task location
    end note

    note right of HoursApproved
        Administrator verifies
        and approves hours
    end note

    note right of BadgeEarned
        Automatic badge
        awarded at milestone
    end note
```

---

## 7. SEQUENCE DIAGRAM

```mermaid
sequenceDiagram
    actor V as Volunteer
    participant WEB as Web App
    participant API as API Server
    participant AUTH as Auth Service
    participant FS as Firestore
    participant NOTIFY as Notification Service
    participant EMAIL as Email Service

    rect rgb(200, 220, 255)
        note over V,EMAIL: Volunteer Registration & Onboarding Flow
        V->>WEB: Navigate to Registration
        WEB->>WEB: Display Registration Form
        V->>WEB: Enter Details (Name, Email, Skills, Availability)
        WEB->>API: POST /auth/register
        API->>AUTH: Validate Email & Password
        AUTH->>AUTH: Hash Password with bcrypt
        AUTH->>FS: Check if Email Exists
        FS-->>AUTH: Email Available
        API->>FS: Create Volunteer Document
        FS-->>API: Document Created (volunteerId)
        API->>NOTIFY: Queue Verification Email
        NOTIFY->>EMAIL: Send Email
        EMAIL-->>V: Verification Link Email
        API-->>WEB: Return Success Message
        WEB-->>V: Show Success & Redirect to Login
    end

    rect rgb(200, 255, 200)
        note over V,EMAIL: Email Verification
        V->>EMAIL: Click Verification Link
        EMAIL->>API: GET /auth/verify?token=XXX
        API->>AUTH: Validate Token
        AUTH->>FS: Update emailVerified = true
        FS-->>AUTH: Updated
        API-->>V: Redirect to Login
        WEB->>WEB: Show Login Page
    end

    rect rgb(255, 220, 200)
        note over V,EMAIL: Login with 2FA
        V->>WEB: Enter Email & Password
        WEB->>API: POST /auth/login
        API->>AUTH: Authenticate Credentials
        AUTH->>FS: Query User Record
        FS-->>AUTH: User Found
        AUTH->>AUTH: Verify Password Hash
        AUTH->>NOTIFY: Generate & Queue 2FA Code
        NOTIFY->>EMAIL: Send 2FA Code via SMS/Email
        EMAIL-->>V: 2FA Code Received
        API-->>WEB: Prompt for 2FA Code
        WEB-->>V: Show 2FA Input Field
        V->>WEB: Enter 2FA Code
        WEB->>API: POST /auth/verify-2fa
        API->>AUTH: Validate 2FA Code
        AUTH->>AUTH: Generate JWT Token
        API->>FS: Create Session Record
        FS-->>API: Session Created
        API-->>WEB: Return JWT & Refresh Token
        WEB->>WEB: Store Tokens in localStorage
        WEB-->>V: Redirect to Dashboard
    end

    rect rgb(220, 200, 255)
        note over V,EMAIL: Task Application Flow
        V->>WEB: Browse Available Tasks
        WEB->>API: GET /tasks?skills=teaching,counseling
        API->>FS: Query Tasks by Skill
        FS-->>API: Return Matching Tasks
        API-->>WEB: Display Task List
        WEB-->>V: Show Task Details
        V->>WEB: Click "Apply for Task"
        WEB->>API: POST /tasks/apply
        API->>FS: Check Volunteer Availability
        FS-->>API: Check Schedule Conflicts
        API->>API: Validate No Conflicts
        API->>FS: Create Task Assignment
        FS-->>API: Assignment Created
        API->>NOTIFY: Queue Assignment Confirmation
        NOTIFY->>EMAIL: Send Confirmation Email
        API-->>WEB: Return Success
        WEB-->>V: Show "Application Submitted"
    end

    rect rgb(255, 255, 200)
        note over V,EMAIL: Check-In at Event
        V->>WEB: At Event Location - Click Check-In
        WEB->>WEB: Activate Camera for QR Scan
        V->>WEB: Scan Event QR Code
        WEB->>WEB: Parse QR Code (eventId)
        WEB->>API: POST /hours/check-in
        API->>FS: Create Check-In Record
        FS-->>API: Record Created with timestamp
        API->>NOTIFY: Send Check-In Confirmation
        NOTIFY->>EMAIL: Send SMS/Push Notification
        API-->>WEB: Show Check-In Success
        WEB-->>V: Display "Checked In at 10:05 AM"
    end

    rect rgb(200, 255, 255)
        note over V,EMAIL: Check-Out & Hours Logging
        V->>WEB: After Task Completion - Click Check-Out
        WEB->>API: POST /hours/check-out
        API->>API: Calculate Hours (checkout - checkin)
        API->>FS: Create Hours Log Record
        API->>FS: Update Volunteer Total Hours
        FS-->>API: Hours Record Created
        API->>AUTH: Check Badge Thresholds
        AUTH->>AUTH: Calculate if Badge Earned (e.g., 150 hours = Silver)
        API->>FS: Award Badge if Threshold Met
        API->>NOTIFY: Queue Recognition Email & Badge Award
        NOTIFY->>EMAIL: Send Badge Achievement Email
        API-->>WEB: Show Hours Logged Successfully
        WEB-->>V: Display "Pending Admin Approval"
    end
```

---

## 8. DEPLOYMENT DIAGRAM

```mermaid
graph TB
    subgraph "Client Devices"
        BROWSER["🌐 Web Browser<br/>Desktop/Laptop<br/>Chrome, Firefox, Safari"]
        PHONE["📱 Mobile Phone<br/>iOS/Android<br/>Native App"]
        TABLET["📱 Tablet<br/>iPad/Android<br/>Responsive Web"]
    end

    subgraph "CDN & Edge Network"
        CF["☁️ Cloudflare<br/>Global CDN<br/>DDoS Protection<br/>SSL/TLS"]
    end

    subgraph "Frontend Hosting"
        VERCEL["🚀 Vercel<br/>Next.js Deployment<br/>Auto-scaling<br/>Edge Functions"]
    end

    subgraph "API Gateway & Load Balancing"
        LB["⚖️ Load Balancer<br/>Nginx/Cloudflare<br/>Rate Limiting<br/>API Routing"]
    end

    subgraph "Backend Services (AWS/Google Cloud)"
        API1["🔧 API Server 1<br/>Node.js/Express<br/>Container"]
        API2["🔧 API Server 2<br/>Node.js/Express<br/>Container"]
        API3["🔧 API Server 3<br/>Node.js/Express<br/>Container"]
        
        AUTH_SVC["🔑 Auth Service<br/>Isolated Container<br/>JWT Generation"]
        WORKER["⚙️ Background Worker<br/>Job Processing<br/>Email Queues"]
    end

    subgraph "Kubernetes Orchestration"
        K8S["🐳 Kubernetes Cluster<br/>Container Orchestration<br/>Auto-healing<br/>Scaling"]
    end

    subgraph "Data Layer"
        FS["🔥 Firestore<br/>NoSQL Database<br/>Multi-region replication"]
        STORAGE["💾 Cloud Storage<br/>Google Cloud Storage<br/>CDN Enabled"]
        REDIS["⚡ Redis<br/>Session Cache<br/>Real-time Data"]
    end

    subgraph "External Services"
        EMAIL_SVC["✉️ Resend<br/>Email Service Provider"]
        SMS_SVC["📱 Twilio<br/>SMS Provider"]
        MAP_SVC["🗺️ Google Maps<br/>Location Services"]
        PAYMENT["💳 Stripe/Razorpay<br/>Payment Gateway"]
    end

    subgraph "Monitoring & Logging"
        SENTRY["🐛 Sentry<br/>Error Tracking<br/>Performance Monitoring"]
        LOGS["📊 Cloud Logging<br/>Log Aggregation<br/>Analysis"]
        METRICS["📈 Prometheus<br/>Metrics Collection<br/>Dashboards"]
        GRAFANA["📉 Grafana<br/>Visualization<br/>Alerts"]
    end

    subgraph "CI/CD Pipeline"
        GITHUB["🔗 GitHub<br/>Source Control<br/>Repository"]
        CI["🔄 GitHub Actions<br/>Build & Test"]
        REGISTRY["📦 Container Registry<br/>Docker Images"]
    end

    BROWSER -->|HTTPS| CF
    PHONE -->|HTTPS| CF
    TABLET -->|HTTPS| CF
    
    CF -->|Cached Content| VERCEL
    CF -->|API Requests| LB
    
    VERCEL -->|Static Assets| CF
    
    LB -->|Routes| API1
    LB -->|Routes| API2
    LB -->|Routes| API3
    LB -->|Auth Requests| AUTH_SVC
    
    API1 -->|Container| K8S
    API2 -->|Container| K8S
    API3 -->|Container| K8S
    AUTH_SVC -->|Container| K8S
    WORKER -->|Container| K8S
    
    API1 -->|Query/Write| FS
    API2 -->|Query/Write| FS
    API3 -->|Query/Write| FS
    API1 -->|Upload| STORAGE
    API2 -->|Upload| STORAGE
    
    AUTH_SVC -->|Cache Sessions| REDIS
    
    WORKER -->|Send| EMAIL_SVC
    WORKER -->|Send| SMS_SVC
    API1 -->|Request| MAP_SVC
    API2 -->|Request| PAYMENT
    
    API1 -->|Error Tracking| SENTRY
    API2 -->|Error Tracking| SENTRY
    VERCEL -->|Error Tracking| SENTRY
    
    K8S -->|Logs| LOGS
    K8S -->|Metrics| METRICS
    METRICS -->|Display| GRAFANA
    LOGS -->|Query| LOGS
    
    GITHUB -->|Webhook| CI
    CI -->|Build| REGISTRY
    CI -->|Deploy| K8S
    CI -->|Deploy| VERCEL

    style BROWSER fill:#e3f2fd
    style PHONE fill:#e3f2fd
    style TABLET fill:#e3f2fd
    style CF fill:#fff3e0
    style VERCEL fill:#e8f5e9
    style LB fill:#fff3e0
    style K8S fill:#f3e5f5
    style FS fill:#fce4ec
    style STORAGE fill:#fce4ec
    style REDIS fill:#fce4ec
    style SENTRY fill:#ffccbc
    style LOGS fill:#ffccbc
```

---

## 9. SAMPLE FRONTEND DESIGN

```mermaid
graph TB
    subgraph "App Navigation Structure"
        APP["🚀 NGO Platform"]
        
        subgraph "Volunteer Portal"
            V_NAV["📱 Volunteer Navigation"]
            V_DASHBOARD["📊 Dashboard<br/>Hours, Badges, Stats"]
            V_TASKS["📋 Browse Tasks<br/>Search & Filter"]
            V_PROFILE["👤 My Profile<br/>Skills & Availability"]
            V_HISTORY["📜 My History<br/>Completed Tasks"]
            V_NOTIFICATIONS["🔔 Notifications<br/>Messages & Updates"]
            V_SETTINGS["⚙️ Settings<br/>Preferences & Account"]
        end

        subgraph "Coordinator Portal"
            C_NAV["📊 Coordinator Navigation"]
            C_EVENTS["📅 Events<br/>Create & Manage"]
            C_VOLUNTEERS["👥 Volunteers<br/>Search & Assign"]
            C_APPROVAL["✅ Approvals<br/>Hours & Applications"]
            C_REPORTS["📊 Reports<br/>Analytics & Data"]
            C_RECOGNITION["🏆 Recognition<br/>Badges & Awards"]
        end

        subgraph "Admin Portal"
            A_NAV["🔐 Admin Navigation"]
            A_USERS["👨‍💼 User Management<br/>Roles & Permissions"]
            A_SYSTEM["⚙️ System Config<br/>Settings & Rules"]
            A_FINANCIAL["💰 Financial<br/>Budgets & Reports"]
            A_CONTENT["📝 Content<br/>CMS Integration"]
            A_ANALYTICS["📈 Analytics<br/>KPIs & Dashboards"]
        end
    end

    subgraph "Volunteer Dashboard Layout"
        VD["📊 Volunteer Dashboard"]
        VD_HEADER["🎯 Header: Profile & Welcome"]
        VD_STATS["📈 Stat Cards<br/>Total Hours | Tasks Completed<br/>Current Badge | Next Badge %"]
        VD_BADGES["🏆 Badge Progress<br/>Visual Badge Display<br/>Milestone Progress"]
        VD_CHART["📊 Hours Trend Chart<br/>Last 12 Months"]
        VD_UPCOMING["📅 Upcoming Tasks<br/>Next 7 Days"]
        VD_RECOGNITION["🎉 Recent Recognition<br/>Latest Achievements"]
    end

    subgraph "Event Management UI"
        EM["📅 Event Management"]
        EM_LIST["📋 Event List<br/>Status | Date | Attendees"]
        EM_CREATE["➕ Create Event Form<br/>Title, Date, Location, Type"]
        EM_DETAIL["🔍 Event Details<br/>Full info | Attendee List"]
        EM_VOLUNTEERS["👥 Assign Volunteers<br/>Search & Conflict Check"]
        EM_CALENDAR["📅 Event Calendar<br/>Month/Week View"]
    end

    subgraph "Task Assignment UI"
        TA["📝 Task Assignment"]
        TA_SEARCH["🔍 Search Volunteers<br/>By Skills, Availability"]
        TA_CONFLICT["⚠️ Conflict Detection<br/>Visual Warnings"]
        TA_CALENDAR["📆 Volunteer Calendar<br/>Schedule View"]
        TA_ASSIGN["✅ Assign Confirmation<br/>Send Notifications"]
        TA_BULK["📦 Bulk Assignment<br/>Multiple Volunteers"]
    end

    subgraph "Hours Approval UI"
        HA["✅ Hours Approval"]
        HA_LIST["📋 Pending Hours List<br/>Volunteer | Hours | Status"]
        HA_DETAIL["🔍 Hours Detail<br/>Check-in/out Times | Duration"]
        HA_APPROVE["✅ Approve/Reject<br/>Add Notes"]
        HA_BADGE["🏆 Auto-Badge Award<br/>If Threshold Met"]
        HA_RECOGNITION["📧 Send Recognition<br/>Email & Notifications"]
    end

    subgraph "Mobile Experience"
        MOBILE["📱 Mobile App UI"]
        M_NAVBAR["📱 Bottom Navigation<br/>Tasks | Dashboard | Profile"]
        M_CHECKIN["📍 Check-In Screen<br/>QR Scanner | Camera"]
        M_HOURS["⏱️ Hours Summary<br/>Today's Hours"]
        M_BADGES["🏆 Badge Display<br/>Pop-ups & Celebrations"]
        M_NOTIFY["🔔 Push Notifications<br/>Task Alerts"]
    end

    APP --> V_NAV
    APP --> C_NAV
    APP --> A_NAV
    
    V_NAV --> V_DASHBOARD
    V_NAV --> V_TASKS
    V_NAV --> V_PROFILE
    V_NAV --> V_HISTORY
    V_NAV --> V_NOTIFICATIONS
    V_NAV --> V_SETTINGS
    
    V_DASHBOARD --> VD
    VD --> VD_HEADER
    VD --> VD_STATS
    VD --> VD_BADGES
    VD --> VD_CHART
    VD --> VD_UPCOMING
    VD --> VD_RECOGNITION
    
    C_NAV --> C_EVENTS
    C_NAV --> C_VOLUNTEERS
    C_NAV --> C_APPROVAL
    C_NAV --> C_REPORTS
    C_NAV --> C_RECOGNITION
    
    C_EVENTS --> EM
    EM --> EM_LIST
    EM --> EM_CREATE
    EM --> EM_DETAIL
    EM --> EM_CALENDAR
    
    C_VOLUNTEERS --> TA
    TA --> TA_SEARCH
    TA --> TA_CONFLICT
    TA --> TA_CALENDAR
    TA --> TA_ASSIGN
    TA --> TA_BULK
    
    C_APPROVAL --> HA
    HA --> HA_LIST
    HA --> HA_DETAIL
    HA --> HA_APPROVE
    HA --> HA_BADGE
    HA --> HA_RECOGNITION
    
    A_NAV --> A_USERS
    A_NAV --> A_SYSTEM
    A_NAV --> A_FINANCIAL
    A_NAV --> A_CONTENT
    A_NAV --> A_ANALYTICS
    
    V_DASHBOARD -.->|Mobile| MOBILE
    MOBILE --> M_NAVBAR
    MOBILE --> M_CHECKIN
    MOBILE --> M_HOURS
    MOBILE --> M_BADGES
    MOBILE --> M_NOTIFY

    style VD_HEADER fill:#e3f2fd
    style VD_STATS fill:#e3f2fd
    style VD_BADGES fill:#c8e6c9
    style VD_CHART fill:#fff9c4
    style VD_UPCOMING fill:#f0f4c3
    style VD_RECOGNITION fill:#f8bbd0
    style M_CHECKIN fill:#ffccbc
    style M_BADGES fill:#f8bbd0
    style TA_CONFLICT fill:#ffcdd2
    style HA_BADGE fill:#c8e6c9
```

---

## ADDITIONAL DESIGN NOTES

### Color Scheme Reference
```
Primary Colors:
- Blue (#2196F3): Primary actions, links, headers
- Green (#4CAF50): Success, completion, approval
- Orange (#FF9800): Warnings, attention needed
- Red (#F44336): Errors, critical alerts
- Purple (#9C27B0): Features, special actions
- Gray (#757575): Secondary, inactive states
- White (#FFFFFF): Backgrounds, cards

Accessibility:
- Minimum contrast ratio: 4.5:1
- Color not only indicator
- Clear focus states
```

### Typography
```
Headings:
- H1: 32px, Bold (Page titles)
- H2: 24px, Semibold (Section titles)
- H3: 18px, Semibold (Subsection titles)
- H4: 16px, Medium (Card titles)

Body Text:
- Regular: 14px, Regular (Main content)
- Small: 12px, Regular (Helper text)
- Label: 12px, Semibold (Form labels)
```

### Component Specifications

**Buttons:**
- Primary: Blue background, white text, 44px height
- Secondary: Gray background, blue text
- Tertiary: Transparent background, blue text
- Disabled: Gray background, gray text, 50% opacity

**Form Fields:**
- Input height: 40px minimum
- Touch target: 44px minimum
- Padding: 12px horizontal, 8px vertical
- Border: 1px solid gray, 2px solid blue on focus

**Cards:**
- Padding: 16px
- Border radius: 8px
- Shadow: 0 2px 4px rgba(0,0,0,0.1)
- Hover: Slight shadow increase

**Badges:**
- Minimum size: 24x24px
- Background: Status-dependent color
- Animation: Scale on new achievement

---

## DIAGRAM RENDERING INSTRUCTIONS

To render these diagrams:

1. **GitHub/GitLab:** Mermaid diagrams render automatically in markdown
2. **Online:** Visit https://mermaid.live/ and paste diagram code
3. **Local:** Use Mermaid CLI: `npm install -g @mermaid-js/mermaid-cli`
   ```bash
   mmdc -i diagram.mmd -o diagram.svg
   ```
4. **VS Code:** Install "Markdown Preview Mermaid Support" extension
5. **Confluence:** Use Mermaid macro (requires plugin)
6. **Notion:** Embed via external link or use native diagram tools

---

**All diagrams represent the NGO Platform architecture and design as of April 6, 2026.**

