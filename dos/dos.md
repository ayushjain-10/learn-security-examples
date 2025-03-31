# Denial-of-Service (DoS)

This example demonstrates DoS vulnerabilities and how they can be exploited.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Ignore if you have already done this once. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

    `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called __infodisclosure__. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

    ```
        http://localhost:3000/userinfo?id[$ne]=
    ```

4. Do you see the server crashing?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts** that can lead to a DoS attack.
2. Briefly explain how a malicious attacker can exploit them.
3. Briefly explain the defensive techniques used in **secure.ts** to prevent the DoS vulnerability?

## Answers

1. Potential vulnerabilities in insecure.ts that can lead to a DoS attack:
   - No input validation for the 'id' query parameter, allowing any type of input including MongoDB operators
   - Lack of error handling, causing unhandled exceptions when invalid queries are processed
   - No request rate limiting, allowing attackers to send unlimited requests in rapid succession
   - Absence of query timeout settings, allowing long-running database operations


2. How a malicious attacker can exploit these vulnerabilities:
   - By using NoSQL injection with MongoDB operators like [$ne] (not equal) to create computationally expensive queries that scan the entire database
   - Sending malformed IDs that cause the server to throw unhandled exceptions and crash
   - Flooding the server with a high volume of requests in a short time period
   - Chaining multiple attacks simultaneously to amplify the effect and make the server completely unresponsive to legitimate users

3. Defensive techniques used in secure.ts to prevent DoS vulnerability:
   - Implementation of rate limiting with express-rate-limit, restricting each IP to 1 request per 5 seconds
   - Proper error handling with try/catch blocks to prevent unhandled exceptions from crashing the server
   - Timeouts are implied through the error handling mechanism, preventing long-running queries from consuming resources indefinitely
