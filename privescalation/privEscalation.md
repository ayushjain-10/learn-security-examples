# Privilege Escalation

The example demonstrates a privilege escalation vulnerability and how to exploit it.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, send a GET request

    ```
        http://localhost:3000/send-form
    ```

4. Try different UserIds and see which one gives you authorized access to change the role of that user.

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**
2. Briefly explain how a malicious attacker can exploit them.
3. Briefly explain the defensive techniques used in **secure.ts** to prevent the privilege escalation vulnerability?

Answers:

1. Vulnerabilities in insecure.ts:
The insecure.ts file has a critical privilege escalation vulnerability in the /update-role endpoint. The main issue is in the authentication and authorization mechanism. The code only checks if the user with the specified userId exists and has the 'admin' role, but it doesn't verify that the requester is actually that admin user. This means that anyone can simply provide the admin's userId (1) in the request and gain the ability to update any user's role, essentially impersonating the admin without proper authentication.

There's no session management or proper authentication to ensure that the person making the request is truly the admin user.

2. How a malicious attacker can exploit them:
An attacker can exploit this vulnerability by:
- Accessing the /send-form endpoint to get the HTML form
- Submitting the form with userId=1 (which is the admin's ID in the system)
- Setting any newRole value they want for any user
- The server will check if user ID 1 exists (it does) and if they have the admin role (they do)
- The server then performs the requested role change without verifying if the requester is actually the admin

This allows an unauthenticated attacker to escalate privileges by:
1. Making themselves an admin
2. Demoting legitimate admins to regular users
3. Manipulating the roles of any user in the system

3. Defensive techniques in secure.ts:
The secure.ts file implements several measures to prevent privilege escalation:

a) Session-based authentication:
- Uses express-session middleware to establish and maintain user sessions
- Stores the authenticated user's ID in the session (req.session.userId)
- Checks for this session variable to verify the user is logged in before processing any requests

b) Proper authorization flow:
- Instead of trusting the userId from the request body, it uses the userId from the session
- Verifies that the logged-in user (from the session) has admin privileges
- Only then allows the role change operation

c) Enhanced security configurations:
- Uses httpOnly cookies to prevent JavaScript access to session cookies
- Implements SameSite 'strict' policy to prevent CSRF attacks
- Uses secure session management with proper secret key handling

d) Explicit permission checks:
- Checks both authentication (if user is logged in) and authorization (if user has admin role)
- Uses proper HTTP status codes (401 for unauthenticated, 403 for unauthorized)

The key improvement is that secure.ts verifies the identity of the requester through the session before allowing privileged operations, rather than simply trusting the userId provided in the request.
