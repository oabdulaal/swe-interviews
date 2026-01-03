# AI Chat Assistant

## Goal

Goal: Design a scalable, real-time conversational (ChatGPT-like) AI platform that handles persistent chat history and multi-modal inputs while ensuring content safety.

## Functional Requirements:

Requirement 1: 
- User can view conversations history
- User can create a new conversation
- User can send a message in the conversation and get responses from the LLM 

Requirement 2:
- User can send text, files and documents alongside messages in the conversation

Requirement 3:
- System needs to have a component that filters illegal documents, phishing, etc.

## Expected Core Components

- Client: Web/Mobile UI.
- Backend: Orchestration layer (API Gateway/App Server).
- Services: Authentication, History Service, Content Filtering Service.
- Data Store: Database for conversations and message persistence.

## Probing Questions

- What would be the API between front end and backend? How would you handle data sent and received?
- What would be the database design for the conversations and messages? 
- How can I stream LLM tokens of the LLM response? - Could be HTTP streaming, Websockets, SSE, gRPC, other
- Scaling the system. What are the bottlenecks?
- How would you observe and monitor the system?
- How would you secure the system?

## Evaluation Criteria

- Architecture HLD
- Databases
- APIs
- Scalability
- Monitoring & Observability
- Security

