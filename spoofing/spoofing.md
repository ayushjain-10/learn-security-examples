# Spoofing

This example demonstrates spoofind through two ways -- Stealing cookies programmatically and cross site request forgery (CSRF).

## Steps to reproduce the vulnerability

1. Install dependencies

    `$ npx install`

2. Start the **insecure.ts** server

    `npx ts-node insecure.ts`

3. Start the malicious server **mal.ts**

    `npx ts-node mal.ts`

4. Open __http://localhost:8000__ in a browser, type a name and Submit.

5. Open the __Application__ tab in the Browser's inspect pane. Find the __Cookies__ under __Storage__. You should see a __connect.sid__ cookie being set.

6. Open the HTML file __mal-steal-cookie.html__ file in the same browser (different tab). Open inspect and view the console.

7. Click the link in the HTML file. Do you see the cookie being stolen in the console?

8. Open the HTML file __mal-csrf.html__ file in the same browser (different tab). What do you see if the user has not logged out of **insecure.ts**? What do you see if the user has logged out? 


## For you to answer

1. Briefly explain the spoofing vulnerability in **insecure.ts**.
Insecure cookie and session management is the main spoofing risk in insecure.ts.  The httpOnly: false option exposes the session cookie to cross-site scripting (XSS) attacks by granting client-side JavaScript access to it.  Furthermore, cookies do not have the sameSite attribute set.  Instead of being a strong, one-of-a-kind value supplied as an environment variable or command-line parameter, the session secret ("SOMESECRET") is hardcoded.

2. Briefly explain different ways in which vulnerability can be exploited.
The following methods may be used to exploit the vulnerability:• Cookie Theft: A hacker may insert JavaScript into a malicious website or user-visited page using cookies. This script allows the attacker to assume the identity of the user and get unauthorized access by reading the connect.sid session cookie and sending it to a server under their control.• Cross-Site Request Forgery (CSRF): When a user is logged into insecure.ts and visits a malicious website that sends unexpected requests to the insecure.ts server (for example, sending a POST request to /sensitive), the server will accept the request because it includes the user's session cookie (connect.sid), which is sent automatically with the request. The attacker would then be able to initiate sensitive actions, such as giving himself access to supersecret information.
3. Briefly explain why **secure.ts** does not have the spoofing vulnerability in **insecure.ts**.
To ensure confidentiality, the secret used to hash the session is no longer hard-coded but rather is extracted from the configuration file or command line.In order to prevent JavaScript codes from accessing cookies, the cookie is set to httpOnly: true and sameSite: true. Additionally, the client-side cookie will only be sent to the server if the client is on the same domain as the server, thereby preventing cross-site request forgery.
