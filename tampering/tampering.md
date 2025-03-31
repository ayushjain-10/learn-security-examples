# Tampering

This example demonstrates tampering through script injection.

## Steps to reproduce

1. Install all dependencies

    `npm install`

2. Start the **insecure.ts** server

    `npx ts-node insecure.ts`

3. In the browser, type a potentially malicious script in the name field of the form

    ```
        <script> document.body.innerHTML = "<a href='https://google.com'> Gotcha </a>"</script>
    ```

4. Do you see the potentially malicious hyperlink being injected into the form?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**
2. Briefly explain how a malicious attacker can exploit them.
3. Briefly explain why **secure.ts** does not have the same vulnerabilties?

Answer:
1. Potential vulnerabilities in insecure.ts:
The insecure.ts file has a Cross-Site Scripting (XSS) vulnerability. In the "/register" route, the user input from the name field (req.body.name) is only trimmed but not sanitized before being stored in the session and then directly inserted into the HTML response on the homepage using template literals (`<h1>Welcome, ${name}</h1>`).

2. How a malicious attacker can exploit this vulnerability:
An attacker can submit malicious JavaScript code in the name field, such as:
`<script> document.body.innerHTML = "<a href='https://google.com'> Gotcha </a>"</script>`

When this script is rendered in the welcome message, the browser will execute it as valid JavaScript, allowing the attacker to:
- Replace page content (as shown in the example)
- Steal cookies or session information
- Redirect users to phishing sites
- Execute arbitrary code in the context of the user's browser session
- Make requests on behalf of the user

3. Why secure.ts does not have the same vulnerabilities:
The secure.ts version implements proper input sanitization using the escapeHTML function. This function escapes HTML special characters by replacing them with their HTML entity equivalents:
- < becomes &lt;
- > becomes &gt;
- " becomes &quot;
- ' becomes &#39;
- & becomes &amp;

This ensures that any HTML or JavaScript code submitted in the name field is treated as plain text rather than executable code when rendered in the browser. The sanitization happens in the "/register" route where it calls `const sanitizedName = escapeHTML(req.body.name.trim())` before storing the value in the session.
