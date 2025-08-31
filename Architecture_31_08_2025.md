## ARCHITECTURE 31.08.2025

### Graph

<img width="3840" height="2242" alt="Architektur_31_08_2025" src="https://github.com/user-attachments/assets/0386574f-c540-4b64-b2cb-cc697aef011c" />

### Code

graph TD
    subgraph "User's Device"
        Client[Browser / Mobile App]
    end

    subgraph "Backend System"
        API_Gateway[API Gateway]
        Auth_Service[Auth Service]
        Poll_Service[Poll Management Service]
        Broadcasting_Service[Poll Broadcasting Service]
        DB[(PostgreSQL Database)]
        Message_Queue([Message Queue])
    end


    %% Authentication Flow
    Client -- "1. Sends credentials (user/pass)" --> API_Gateway
    API_Gateway -- "2. Forwards login request" --> Auth_Service
    Auth_Service -- "3. Verifies against user data" --> DB
    Auth_Service -- "4. Issues signed JWT" --> API_Gateway
    API_Gateway -- "5. Returns JWT to client" --> Client

    %% Authenticated API Call (e.g., Casting a Vote)
    Client -- "6. API Call with JWT (e.g., POST /vote)" --> API_Gateway
    
    %% Gateway forwards to the appropriate service
    API_Gateway -- "7. Forwards request" --> Poll_Service
    
    %% Poll Service handles business logic
    Poll_Service -- "8. Validates & persists vote" --> DB
    
    %% Asynchronous flow for real-time updates
    Poll_Service -- "9. Publishes 'VoteCast' event" --> Message_Queue
    Message_Queue -- "10. Event is consumed" --> Broadcasting_Service
    Broadcasting_Service -- "11. Pushes update via WebSocket" --> Client
