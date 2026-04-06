# Lab 7: Data Modeling & Class Design

## 1. Database Design (Cloud Firestore)

### 1.1 Schema Design (NoSQL)
Unlike traditional relational databases, Firestore uses a document-oriented structure. However, for the purpose of conceptual understanding, we define the schemas as Collections and Documents.

#### Collection: `users`
| Field | Type | Description |
| :--- | :--- | :--- |
| `uid` | String (PK) | Unique User ID from Firebase Auth |
| `displayName` | String | Full Name of the user |
| `email` | String | Email Address (indexed) |
| `role` | String | 'admin' or 'volunteer' |
| `createdAt` | Timestamp | Account creation date |

#### Collection: `events` (Synced from Contentful)
| Field | Type | Description |
| :--- | :--- | :--- |
| `id` | String (PK) | Contentful Entry ID |
| `title` | String | Event Title |
| `slug` | String | URL-friendly slug |
| `date` | ISO String | Date and Time of event |
| `location` | GeoPoint | Latitude/Longitude or Address String |
| `capacity` | Number | Maximum allowed participants |

#### Collection: `registrations`
| Field | Type | Description |
| :--- | :--- | :--- |
| `id` | String (PK) | Auto-generated ID |
| `eventId` | String (FK) | Reference to `events` collection |
| `userId` | String (FK) | Reference to `users` collection |
| `status` | String | 'confirmed', 'waitlisted', 'cancelled' |
| `qrCode` | String | URL to generated QR code image |

### 1.2 Entity-Relationship Diagram (ERD)

The following ERD represents the logical relationships between the entities in our system. Note that in a NoSQL environment, these relationships are often denormalized for performance.

```mermaid
erDiagram
    USER ||--o{ REGISTRATION : "makes"
    EVENT ||--o{ REGISTRATION : "receives"
    ADMIN ||--o{ EVENT : "manages"
    GALLERY ||--|{ IMAGE : "contains"
    
    USER {
        string uid PK
        string email
        string phone
        string role
    }
    
    EVENT {
        string id PK
        string title
        datetime date
        int capacity
        string category
    }
    
    REGISTRATION {
        string id PK
        string status
        timestamp timestamp
    }
    
    ADMIN {
        string uid PK
        string permissions
    }
```

## 2. Object-Oriented Design

### 2.1 Class Diagram
The Class Diagram describes the static structure of the system, identifying the classes, their attributes, and operations.

```mermaid
classDiagram
    class Person {
        <<abstract>>
        +String name
        +String email
        +String phone
        +login()
        +logout()
    }

    class Volunteer {
        +String volunteerId
        +List<String> skills
        +registerForEvent(Event e)
        +viewHistory()
        +downloadCertificate()
    }

    class Admin {
        +String adminLevel
        +createEvent()
        +updateEvent()
        +deleteEvent()
        +viewDashboard()
        +exportData()
    }

    class Event {
        +String eventId
        +String title
        +Date date
        +String location
        +getDetails()
        +checkAvailability()
        +addParticipant(Volunteer v)
    }

    class Registration {
        +String regId
        +Date timestamp
        +String status
        +generateQRCode()
        +sendConfirmationEmail()
    }

    Person <|-- Volunteer : Inheritance
    Person <|-- Admin : Inheritance
    Volunteer "1" --> "*" Registration : Creates
    Event "1" --> "*" Registration : Contains
    Admin "1" ..> "*" Event : Manages
```

## 3. Data Dictionary
| Data Element | Type | Length | Format | Description |
| :--- | :--- | :--- | :--- | :--- |
| `User_Email` | String | 100 | RFC 5322 | Valid email address for login and notifications. |
| `Event_Date` | Date | - | ISO 8601 | Future date validation required. |
| `Phone_Num` | String | 10 | RegEx `^[0-9]{10}$` | Indian mobile number validation. |
