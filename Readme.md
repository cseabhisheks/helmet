## 1. What is an HTTP Header?

An HTTP header is additional information sent with an HTTP request or response.

Examples: `Content-Type`, `Authorization`, `X-Frame-Options`.

**Request header example:**
```http
GET /users HTTP/1.1
Host: example.com
Authorization: Bearer token123
Accept: application/json
```

**Response header example:**
```http
HTTP/1.1 200 OK
Content-Type: application/json
X-Content-Type-Options: nosniff
```

- Request headers: Client → Server
- Response headers: Server → Client

## 2. What is Helmet?

Helmet is an Express.js security middleware library that sets HTTP response headers to help protect against certain web vulnerabilities.

**Installation:**
```bash
npm install helmet
```

```js
const helmet = require("helmet");

app.use(helmet()); // Apply default security configuration
```

## 3. Important Helmet Headers Enabled by Default

`app.use(helmet())` applies Helmet's default configuration. Exact headers may depend on the version and configuration.

| Header | Purpose |
|---|---|
| `Content-Security-Policy` | Restricts permitted content sources to help prevent XSS. |
| `Strict-Transport-Security` | Tells browsers to use HTTPS for future requests. |
| `X-Content-Type-Options` | Prevents MIME-type sniffing. |
| `X-Frame-Options` | Helps protect against clickjacking. |
| `Referrer-Policy` | Controls referrer information sent by browsers. |
| `Cross-Origin-Resource-Policy` | Controls which origins can load resources. |

**Note:** HSTS is intended for HTTPS deployments.

## 4. How to Disable Helmet Headers

Set the required option to `false`.

```js
app.use(helmet({
    contentSecurityPolicy: false,
    crossOriginResourcePolicy: false,
    xFrameOptions: false
}));
```

- `app.use(helmet())` applies default protections.
- `option: false` disables the corresponding protection.

## 5. How to Set Security Headers Manually Without Helmet

Use Express's `res.set()` method.

**Example: `index.js`**
```js
const express = require("express");
const app = express();

// Set security headers manually
app.use((req, res, next) => {
    res.set("X-Content-Type-Options", "nosniff");
    res.set("X-Frame-Options", "SAMEORIGIN");
    res.set("Referrer-Policy", "no-referrer");
    next();
});

app.get("/", (req, res) => {
    res.send("Hello World");
});

app.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

**How it works:**
- `res.set()` sets response headers.
- `next()` passes control to the next middleware.
- Headers are sent from the server to the client.

This example sets only three headers, not all Helmet protections. CSP and HSTS require suitable configuration.

## 6. Helmet Limitations and Scope

- Helps protect applications through HTTP response headers.
- Does not provide authentication or authorization.
- Does not replace input validation, rate limiting, or secure environment variables.
- Does not prevent every security vulnerability.
- HTTPS must be configured separately; HSTS does not create an HTTPS connection.

**Remember:** `app.use(helmet())` is a good starting point for most Express applications.

## 7. How to Send Headers from Client to Server

The frontend can send request headers using `fetch()` or Axios. The Express backend reads them using `req.headers`.

**Frontend (React):**
```js
fetch("http://localhost:3000/users", {
    headers: {
        Authorization: "Bearer token123",
        Accept: "application/json"
    }
})
.then(res => res.json())
.then(data => console.log(data));
```

**Backend (`index.js`):**
```js
app.get("/users", (req, res) => {
    const token = req.headers.authorization;

    console.log(token); // Bearer token123

    res.json({ message: "Success" });
});
```

**Remember:**
- Frontend sends request headers using `headers`.
- Backend reads request headers using `req.headers`.
- Helmet and `res.set()` configure server response headers.
- Sending an authorization token does not automatically authenticate the user; the backend must validate it.# helmet
