# Repudiation

The example demonstrates a vulnerability that can lead to repudiation by malicious users attempting to access the services provided by a server.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Run the server __insecure.ts__.

3. Pretend to be a malicous user and interact with the services by sending requests from the browser.

4. Do you think your actions can be repudiated?

## For you to do

1. Briefly explain the vulnerability.
2. Briefly explain why the vulnerability is addressed in __secure.ts__.
3. Which design pattern is used in the secure version to address the vulnerability? Briefly explain how it works?

Answers:

1. Vulnerability explanation:
The insecure.ts application suffers from a repudiation vulnerability where users can deny their actions because there's no proper logging or audit trail. When users send messages or retrieve data, the application doesn't record who performed these actions, when they occurred, or from what IP address. This lack of accountability means that if a malicious user performs harmful actions, they can later deny responsibility, and there would be no evidence to contradict their claims.

2. Why the vulnerability is addressed in secure.ts:
The secure version addresses the repudiation vulnerability by implementing comprehensive logging throughout the application. It records detailed information about every request including:
- Timestamps with exact date and time
- IP addresses of clients making requests
- HTTP methods and URLs accessed
- User identifiers for actions
- Specific actions performed (e.g., sending messages, retrieving data)
- Error conditions and exceptions

This creates an audit trail that makes it difficult for malicious users to deny their actions since there is documented evidence of what occurred, when it happened, and who was responsible.

3. Design pattern used in the secure version:
The secure version implements the **Decorator Pattern** along with the **Chain of Responsibility Pattern** through middleware:

The Decorator Pattern is used to add logging functionality to the Express application without modifying its core functionality. This is achieved through middleware that "decorates" each request with additional logging behavior.

How it works:
- A middleware function is created that logs details of each request
- This middleware is applied to the application using app.use()
- The middleware executes for every request that passes through the application
- The middleware logs the request details, then passes control to the next handler in the chain
- Specific route handlers also include additional logging related to their specific operations
- All logs are written to a persistent file storage (server.log) using a write stream

The Chain of Responsibility Pattern is evident in how multiple middleware functions handle different aspects of processing the request in sequence, with each having the ability to either pass the request to the next handler or terminate the chain.