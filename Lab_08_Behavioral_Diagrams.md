# Lab 8: Behavioral Modeling

## 1. Sequence Diagrams

### 1.1 Sequence Diagram: Volunteer Registration
This diagram captures the dynamic interaction between objects during the registration process.

```mermaid
sequenceDiagram
    actor Volunteer
    participant Browser as "Web Interface"
    participant API as "API (Next.js)"
    participant DB as "Firestore"
    participant Email as "Resend Service"

    Volunteer->>Browser: Selects Event & Clicks "Join"
    Browser->>API: GET /api/event/{id}/status
    API->>DB: Query Capacity
    DB-->>API: Returns {slots: 5}
    API-->>Browser: Returns "Open"
    
    Browser->>Volunteer: Displays Registration Form
    Volunteer->>Browser: Enters Details & Submits
    Browser->>Browser: Validates Input (Client-Side)
    
    Browser->>API: POST /api/register (JSON)
    API->>DB: Transaction: Decrement Slot & Save User
    
    alt successful transaction
        DB-->>API: Registration ID
        API->>Email: Send Confirmation (RegID)
        Email-->>API: Queued
        API-->>Browser: 200 OK + RegID
        Browser->>Volunteer: Shows Success Modal
    else transaction failed
        DB-->>API: Error (Race Condition)
        API-->>Browser: 409 Conflict
        Browser->>Volunteer: Shows "Slots Full" Error
    end
```

### 1.2 Sequence Diagram: Admin Event Creation

```mermaid
sequenceDiagram
    actor Admin
    participant CMS as "Contentful Web App"
    participant Webhook as "Next.js Revalidate"
    participant CDN as "Edge Cache"

    Admin->>CMS: Log In & Create "Event" Entry
    Admin->>CMS: Uploads Image & Publishes
    
    CMS->>Webhook: POST /api/revalidate (Webhooks)
    Webhook->>CDN: Purge Cache for /events page
    CDN-->>Webhook: Cache Cleared
    
    Admin->>Browser: Visits Website
    Browser->>CDN: GET /events
    CDN->>Browser: Returns New Event Content
```

## 2. Activity Diagrams

### 2.1 Activity Diagram: User Registration Flow
Models the procedural flow of logic for a user trying to register.

```mermaid
graph TD
    Start((Start)) --> ViewList[View Event List]
    ViewList --> SelectEvent[Select Event]
    SelectEvent --> CheckSlots{Slots Available?}
    
    CheckSlots -- No --> ShowFull[Display "Event Full"]
    ShowFull --> ViewList
    
    CheckSlots -- Yes --> ClickJoin[Click "Join Now"]
    ClickJoin --> FillForm[Fill Registration Form]
    FillForm --> Validate{Valid Input?}
    
    Validate -- No --> ShowError[Show Validation Error]
    ShowError --> FillForm
    
    Validate -- Yes --> SubmitAPI[Submit to API]
    SubmitAPI --> DBSave{Database Write?}
    
    DBSave -- Success --> SendEmail[Trigger Email]
    SendEmail --> ShowSuccess[Display Ticket]
    ShowSuccess --> End((End))
    
    DBSave -- Fail --> APIError[Show System Error]
    APIError --> End
```

## 3. State Chart Diagrams

### 3.1 State Diagram: Event Lifecycle
Describes the various states an event can be in during its lifecycle.

```mermaid
stateDiagram-v2
    [*] --> Draft : Created in CMS
    
    Draft --> Published : Admin Publishes
    Draft --> Archived : Admin Deletes
    
    Published --> RegistrationOpen : Date > Today
    
    state RegistrationOpen {
        [*] --> HasSlots
        HasSlots --> Waitlisted : Slots = 0
        Waitlisted --> HasSlots : Slots Increased
    }
    
    RegistrationOpen --> Completed : Event Date Passed
    Completed --> [*]
```

### 3.2 State Diagram: Registration Status
Describes the status of a volunteer's application.

```mermaid
stateDiagram-v2
    [*] --> Pending : Form Submitted
    Pending --> Confirmed : Auto-Approved (Default)
    Pending --> Rejected : Admin Rejects (Manual)
    Confirmed --> Attended : Marked Present
    Confirmed --> Absent : Marked Absent
    Confirmed --> Cancelled : User Cancels
    
    Attended --> [*]
    Absent --> [*]
    Rejected --> [*]
    Cancelled --> [*]
```
