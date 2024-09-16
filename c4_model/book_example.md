To define the architecture of a "Book Borrowed Management" system using the C4 model, we'll break down each level of the model with your example:

## 1. Context Level
At this level, we describe the high-level architecture of the system and its interaction with external systems and users.

### System: 
Book Borrowed Management System
### Primary Actors:
**Admin Users**: Interact with the Admin Application to manage books, publishers, authors, categories, and notifications.  
**End Users**: Interact with the Library Application to browse, borrow, and return books.  
### External Systems:
**Library Management System**: Integrates with the Book Management domain to manage borrowing and returning of books.  
**3rd Party Notification Services**: Integrates via the Adapter Layer to send notifications through SMS, Email, and WebSocket.

## 2. Container Level
At this level, we detail the main components (containers) within the system and their interactions.

### Containers:
**Admin SPA (Single Page Application)**: A web application used by Admin Users to manage books, publishers, authors, categories, and notifications.  
**Library SPA, Android, iOS**: Applications used by End Users to interact with the library system, borrow, and return books.  
**Book Management Microservice**: Manages the domains for Publisher, Author, and Book, using CQRS with Event Sourcing for handling data.  
**Category Management Microservice**: Manages Category data, following a traditional CRUD with an n-layer architecture.  
**Library Management Microservice**: Integrates with the Book Management domain to manage book borrowing and returning.  
**Adapter Layer**: Notifier service that integrates with 3rd Party Notification Services (SMS, Email, WebSocket) to send notifications.  
**Event Store**: Stores events for the Book Management domain, allowing event sourcing and replay.  
**Database**: Stores the Category data using a traditional relational model.  
## 3. Component Level
This level describes the internal structure of each container, breaking them into components and detailing their roles and interactions.

### Book Management Microservice Components:

Command Handler: Handles commands (e.g., AddBook, UpdateBook) and writes to the Event Store.
Query Handler: Reads from the Event Store and projects the state of Books, Publishers, and Authors.
Event Store Integration: Manages event sourcing, storing, and replaying of events.
### Category Management Microservice Components:

API Layer: Exposes CRUD operations for categories.
Service Layer: Business logic for managing categories.
Repository Layer: Handles database interactions for categories.
### Library Management Microservice Components:

Borrowing Service: Handles the borrowing and returning of books, integrates with the Book Management Microservice.
Notification Service: Interacts with the Adapter Layer to send notifications to users about borrowed books.
### Adapter Layer Components:

Notifier Service: Acts as an adapter to integrate with external notification services (SMS, Email, WebSocket).
Applications (Admin, Library):

UI Components: User interfaces for managing and interacting with the library.
API Client: Handles communication between the front-end and back-end services.
## 4. Code Level
At this level, we describe the implementation details, which might include classes, functions, or other code-level structures.

### Book Management Microservice Code Structure:

Commands: Classes representing actions to be taken (e.g., AddBookCommand, UpdateAuthorCommand).
Events: Immutable objects representing state changes (e.g., BookAddedEvent, AuthorUpdatedEvent).
Aggregates: Domain objects that encapsulate state and behavior (e.g., Book, Publisher, Author).
Event Store: Manages the persistence and retrieval of events.
### Category Management Microservice Code Structure:

Controller: Exposes API endpoints for category management.
Service: Contains business logic for CRUD operations.
Repository: Handles database interactions, mapping between data models and database entities.
### Adapter Layer Code Structure:

Notifier Adapter: Classes that wrap around SMS, Email, and WebSocket services, providing a unified interface for sending notifications.
This C4 model representation provides a comprehensive view of the system architecture, detailing each layer's structure and how they interact with each other.