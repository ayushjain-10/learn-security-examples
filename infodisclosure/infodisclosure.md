# Tampering

This example demonstrates information disclosure by injecting malicious query objects to a NoSQL database.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

    `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called __infodisclosure__. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

    ```
        http://localhost:3000/userinfo?username[$ne]=
    ```

4. Do you see user information being displayed despite the malicious request not having a valid username in the request?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**
2. Briefly explain how a malicious attacker can exploit them.
3. Briefly explain the defensive techniques used in **secure.ts** to prevent the information disclosure vulnerability?

Answer:

1. Vulnerabilities in insecure.ts:
The insecure.ts file contains a NoSQL injection vulnerability in the /userinfo endpoint. The vulnerability occurs because the application directly uses user-provided input (the username query parameter) in the MongoDB query without any sanitization or validation. In the line `const user = await User.findOne({ username: username as string }).exec();`, the query parameter is passed directly to the database query without checking the type or format, allowing for manipulation of the query structure.

2. How a malicious attacker can exploit them:
An attacker can exploit this vulnerability by manipulating the query parameters to inject special MongoDB operators. For example, by using a URL like `http://localhost:3000/userinfo?username[$ne]=`, the attacker can create a query that translates to "find a user where username is not equal to an empty string" - which will match any user in the database. The $ne operator is just one example; attackers could use other MongoDB operators like $gt, $lt, $regex, etc. to craft queries that bypass authentication or extract information.

This type of attack can result in:
- Unauthorized access to user data
- Bypassing authentication mechanisms
- Information disclosure about the database structure
- In some cases, it could even lead to data modification or deletion

3. Defensive techniques in secure.ts:
The secure.ts file implements several defensive techniques to prevent NoSQL injection:

a) Type Checking: It verifies that the username parameter is actually a string with the check `if (typeof username !== 'string')`, returning an error response for non-string inputs.

b) Input Sanitization: It uses regex to remove any non-alphanumeric characters from the username with `const sanitizedUsername = username.replace(/[^\w\s]/gi, '')`, which prevents special characters that could be used for NoSQL operators (like $, [, ], etc.).

c) Error Handling: It implements proper error handling with try/catch blocks to prevent detailed error messages from being sent to the client, which could reveal sensitive information about the database structure.

d) Input Validation: By enforcing that the username can only contain alphanumeric characters, it ensures that the query maintains its intended structure and cannot be manipulated to change the query's behavior.

These defensive techniques follow the principle of input validation and sanitization, which is a fundamental security practice to prevent injection attacks of all kinds, including NoSQL injection.
