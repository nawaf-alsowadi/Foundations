HyperText Transfer Protocol (HTTP) is a protocol that set the rules in order to communicate with web servers in order to get the websites data such as HTML, images, videos, ... etc.

HyperText Transfer Protocol Secure (HTTPs) is HTTP over [[OSI Model Layers#Transport Layer]] to encrypt and authenticate the data sent & received ([[#Requests & Response]]) from and to the web servers.

When you request a website, your computer needs to know the server's IP address it needs to talk to; for this, it uses [[DNS]] .
![[Pasted image 20250819183139.png]]
Your computer then talks to the web server using a special set of commands called the HTTP protocol (see [[#Requests & Response]]); the webserver then returns [[HTML]], [[JavaScript]], CSS, Images, etc., which your browser then uses to correctly format and display the website to you.
![[Pasted image 20250819195143.png]]

### Requests & Response
When accessing (websites) web servers, we requests assets such as HTML, images, videos that exist in the webservers and interpreted by the browser. In order to access the assets, we use Unform Resource Locators (**URLs**).  

URL is used to instruct the browser on how to access the specific resource. All URLs components (we don't use all of them always):
![[Pasted image 20250812181737.png]]
**Scheme:** This instructs on what protocol to use for accessing the resource such as HTTP, HTTPS, FTP (File Transfer Protocol).

**User:** *Some services require authentication* to log in, you can put a username and password into the URL to log in.

**Host:** The domain name (require [[DNS]] to resolve to IP because domain name is easier to memorize and type) or IP address of the server you wish to access.

**Port:** The Port that you are going to connect to, usually 80 for HTTP and 443 for HTTPS, but this *can be hosted on any port between 1 - 65535*.

**Path:** The file name (including folder) or location of the resource you are trying to access.

**Query String:** *Extra bits of information* that can be sent to the requested path. For example, /blog?**id=1** would tell the blog path that you wish to receive the *blog article with the id of 1*.

**Fragment:** This is a *reference to a location on the actual page* requested. This is commonly *used for pages with long content* and can have a certain part of the page directly linked to it, so it is viewable to the user as soon as they access the page.

#### Deep Dive
It's possible to make a request to a web server with just one line **GET / HTTP/1.1**.

Full example request (**HTTP Header**):
```http
GET / HTTP/1.1

Host: tryhackme.com
User-Agent: Mozilla/5.0 Firefox/87.0
Referer: https://tryhackme.com/
```
**Line 1:** This request is sending the GET method, request the home page with / and telling the web server we are using HTTP protocol version 1.1.

**Line 2:** We tell the web server we want the website tryhackme.com from the host (host can have multiple websites)

**Line 3:** We tell the web server we are using the Firefox version 87 Browser

**Line 4:** We are telling the web server that the web page that referred us to this one is [https://tryhackme.com](https://tryhackme.com/)

**Line 5:** HTTP requests always end with a blank line to inform the web server that the request has finished.

## **1. GET – Retrieve Data**

```http
GET /products?category=shoes&page=2 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Cache-Control: no-cache
Pragma: no-cache
Upgrade-Insecure-Requests: 1
Connection: keep-alive
Cookie: session_id=abc123def456; user_preference=dark_mode; tracking_consent=true
```

``` HTTP
HTTP/1.1 200 OK
Date: Tue, 12 Aug 2025 15:12:45 GMT
Content-Type: application/json
Content-Length: 157
Cache-Control: no-cache
Server: Apache/2.4.57
Set-Cookie: sessionId=abc123def456; Expires=Wed, 09 Jun 2026 10:18:14 GMT; Path=/; HttpOnly; Secure; SameSite=Lax  
Set-Cookie: userPreference=darkmode; Max-Age=3600; Path=/; SameSite=Lax

[
  { "id": 101, "name": "Running Shoes", "price": 59.99 },
  { "id": 102, "name": "Trail Shoes", "price": 74.99 }
]
```
200 OK: for successful data retrieval; JSON body contains requested data.
**Explanation:**

- **`GET /products?category=shoes&page=2 HTTP/1.1`** – Method (`GET`), path (`/products`), query parameters (`category` and `page`), and protocol version (`HTTP/1.1`).
    
- **`Host: www.example.com`** – Required in HTTP/1.1; tells the server which domain you want (important for shared hosting). So, if the webserver is hosting multiple websites, it will redirect u to the correct one, otherwise, it will redirect u to the default website.
    
- **`User-Agent: ...`** – Identifies the client software and version (browser or tool).
    
- **`Accept: ...`** – Lists acceptable content types for the response, ranked by preference (`q` value).
    
- **`Accept-Language: en-US,en;q=0.9`** – Preferred languages for the response.
    
- **`Accept-Encoding: gzip, deflate, br`** – Compression formats accepted.
    
- **`Cache-Control: no-cache`** – Tells the server/client to not use cached copies.
    
- **`Pragma: no-cache`** – Legacy HTTP/1.0 cache control header for backward compatibility.
    
- **`Upgrade-Insecure-Requests: 1`** – Asks the server to use HTTPS if possible.
    
- **`Connection: keep-alive`** – Keeps the TCP connection open for multiple requests.
- `Cookie: session_id=abc123def456; user_preference=dark_mode; tracking_consent=true` Data sent to the server to help remember your information (see cookies task for more information).
- `Set-Cookie: sessionId=abc123def456; Expires=Wed, 09 Jun 2026 10:18:14 GMT; Path=/; HttpOnly; Secure; SameSite=Lax` Used to save the cookies in the browser.
	- - `sessionId=abc123def456` and `userPreference=darkmode` are the actual cookie name-value pairs.
	- `Expires` specifies a future date and time when the `sessionId` cookie should expire.
	- `Max-Age` specifies the lifetime of the `userPreference` cookie in seconds (3600 seconds = 1 hour).
	- `Path=/` indicates the cookie is valid for all paths under the domain.
	- `HttpOnly` prevents client-side scripts from accessing the `sessionId` cookie, enhancing security.
	- `Secure` ensures the `sessionId` cookie is only sent over HTTPS connections.
	- `SameSite=Lax` provides a level of protection against Cross-Site Request Forgery (CSRF) by controlling when the cookie is sent with cross-site requests.
## **2. POST – Create New Resource**

```http
POST /users HTTP/1.1
Host: www.example.com
User-Agent: PostmanRuntime/7.35.0
Content-Type: application/json
Content-Length: 67
Accept: application/json
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR...
Cache-Control: no-cache
Connection: keep-alive

{
  "name": "John Doe",
  "email": "john@example.com",
  "role": "admin"
}
```

```HTTP
HTTP/1.1 201 Created
Date: Tue, 12 Aug 2025 15:15:22 GMT
Content-Type: application/json
Content-Length: 85
Location: /users/123
Server: nginx/1.25.2

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "admin"
}
```
201 Created: indicates new resource; `Location` header shows where it is.
**Explanation:**

- **`POST /users HTTP/1.1`** – `POST` method creates a resource at `/users`.
    
- **`Host: www.example.com`** – Target server.
    
- **`User-Agent: PostmanRuntime/...`** – Identifies Postman tool.
    
- **`Content-Type: application/json`** – Body format is JSON.
    
- **`Content-Length: 67`** – Size of request body in bytes.
    
- **`Accept: application/json`** – Client expects JSON back.
    
- **`Accept-Language: ...`** – Language preferences.
    
- **`Accept-Encoding: ...`** – Accepted compression formats.
    
- **`Authorization: Bearer ...`** – Token for authentication.
    
- **`Cache-Control: no-cache`** – No cached results.
    
- **`Connection: keep-alive`** – Persistent connection.
    
- **Body** – The JSON object being sent to the server.
    
## **3. PUT – Replace Existing Resource**

```http
PUT /users/123 HTTP/1.1
Host: www.example.com
User-Agent: curl/8.0.1
Content-Type: application/json
Content-Length: 82
Accept: application/json
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate
Authorization: Basic am9obmRvZTpwYXNzd29yZA==
If-Match: "abc123etagvalue"
Cache-Control: no-cache
Connection: keep-alive

{
  "name": "John Updated",
  "email": "john.updated@example.com",
  "role": "editor"
}
```

```HTTP
HTTP/1.1 200 OK
Date: Tue, 12 Aug 2025 15:18:11 GMT
Content-Type: application/json
Content-Length: 88

{
  "id": 123,
  "name": "John Updated",
  "email": "john.updated@example.com",
  "role": "editor"
}

```
200 OK: means replacement succeeded; updated resource returned.
**Explanation:**

- **`PUT /users/123 HTTP/1.1`** – Replace user with ID `123`.
    
- **`Host`** – Target domain.
    
- **`User-Agent: curl/...`** – Identifies `curl` tool.
    
- **`Content-Type`** – JSON payload.
    
- **`Content-Length`** – Length of body.
    
- **`Accept`** – Client wants JSON back.
    
- **`Authorization: Basic ...`** – HTTP Basic Auth (username/password Base64 encoded).
    
- **`If-Match: "abc123etagvalue"`** – Only update if resource ETag matches (avoids overwriting newer versions).
    
- **`Cache-Control`** – No cache.
    
- **Body** – Full replacement of resource.
## **4. PATCH – Partial Update**

```http
PATCH /users/123 HTTP/1.1
Host: api.example.com
User-Agent: Mozilla/5.0
Content-Type: application/json
Content-Length: 45
Accept: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR...
Accept-Encoding: gzip, deflate
Cache-Control: no-cache
Connection: keep-alive

{
  "email": "patched@example.com"
}
```

```HTTP
HTTP/1.1 200 OK
Date: Tue, 12 Aug 2025 15:20:42 GMT
Content-Type: application/json
Content-Length: 85

{
  "id": 123,
  "name": "John Updated",
  "email": "patched@example.com",
  "role": "editor"
}
```
200 OK: confirms partial update applied.
**Explanation:**
- Same as PUT, but body contains **only the changed fields**.
## **5. DELETE – Remove Resource**

```http
DELETE /users/123 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: application/json
Accept-Language: en-US,en;q=0.9
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR...
Accept-Encoding: gzip, deflate
Cache-Control: no-cache
Connection: keep-alive
```

```HTTP
HTTP/1.1 204 No Content
Date: Tue, 12 Aug 2025 15:22:10 GMT
Server: Apache/2.4.57
```
204 No Content: is common when deletion succeeds without returning a body.
**Explanation:**
- Deletes the resource at `/users/123` (typically no request body).
## **6. HEAD – Headers Only**

```http
HEAD /products HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: application/json
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate
Connection: keep-alive
```

```HTTP
HTTP/1.1 200 OK
Date: Tue, 12 Aug 2025 15:23:55 GMT
Content-Type: application/json
Content-Length: 157
Cache-Control: no-cache
Server: Apache/2.4.57
```

**Explanation:**
- Same as GET, but **server returns only headers**, no body.
## **7. OPTIONS – Check Capabilities**

```http
OPTIONS /users HTTP/1.1
Host: www.example.com
User-Agent: curl/8.0.1
Accept: */*
Access-Control-Request-Method: POST
Access-Control-Request-Headers: content-type,authorization
Connection: keep-alive
```

```HTTP
HTTP/1.1 204 No Content
Date: Tue, 12 Aug 2025 15:25:12 GMT
Allow: GET, POST, PUT, PATCH, DELETE, OPTIONS
Access-Control-Allow-Methods: GET, POST, PUT, PATCH, DELETE, OPTIONS
Access-Control-Allow-Headers: content-type, authorization
Server: nginx/1.25.2
```

204 No Content: with `Allow` header showing permitted methods.
**Explanation:**
- Used by browsers (CORS preflight) or clients to discover supported methods.
## **8. TRACE – Echo Request**

```http
TRACE /test HTTP/1.1
Host: www.example.com
Max-Forwards: 5
User-Agent: Mozilla/5.0
Connection: keep-alive
```

```HTTP
HTTP/1.1 200 OK
Content-Type: message/http

TRACE /test HTTP/1.1
Host: www.example.com
```
200 OK: echoes back the request exactly.
**Explanation:**
- Asks server to **echo back the request** for debugging.
- `Max-Forwards: 5` limits hop count.
## **9. CONNECT – Proxy Tunnel**

```http
CONNECT www.example.com:443 HTTP/1.1
Host: www.example.com:443
Proxy-Authorization: Basic am9obmRvZTpwYXNzd29yZA==
User-Agent: curl/8.0.1
```

```HTTP
HTTP/1.1 200 Connection Established
Proxy-Agent: squid/5.7
```
200 Connection Established: means proxy is ready to tunnel traffic.
**Explanation:**
- Establishes a tunnel through a proxy to an HTTPS server.
- `Proxy-Authorization` authenticates to proxy.

#### Status Codes
![[Pasted image 20250813184208.png]]

![[Pasted image 20250813193035.png]]

### Cookie
Small piece of information **stored in the web browser** that **help the web server** remembering some of the client information. **Usually it is used for user authentication** by using **token** that are not human readable. 

Types of cookies:
- **Session cookies:** for the current *browser session*. Everything should be removed after the client close the browser. 
	- Example: Online shopping carts, login status.
- **Persistent cookies:** can last even after closing the browser for the specified expiration period of the cookie.
	- Example: user preferences like website language, login details for future visit, tracking user behavior across multiple sessions. 
- **Third-Party cookies:** placed by other websites different than the current visited website.
	- Example: used for tracking user behavior across multiple websites to deliver targeted advertising or gather analytics data (major privacy concerns).
![[Pasted image 20250817213128.png]]

Different categorization based on the function of the cookies:
- **First-Party Cookies:** These are cookies set by the website the user is directly visiting. 
- **Strictly Necessary Cookies:** These cookies are essential for the basic functioning of a website and don't require consent. 
- **Performance Cookies:** These cookies monitor website performance and collect anonymous data on how users interact with the site. 
- **Functional Cookies:** These cookies remember user preferences and settings to customize the website experience. 
- **Targeting Cookies:** These cookies are used to track users across different websites for advertising and marketing purposes. 

![[Pasted image 20250817211045.png]]

Check all cookies stored in the browser:
*Settings* -> *Privacy and Security* -> *Third-party cookies* -> *See all site data and permissions*
![[Pasted image 20250817214006.png]]

For detailed cookies for each website:
From the website: *Right click* -> *Inspect* -> *Application* -> *Storage* -> *Cookies*
![[Pasted image 20250817214304.png]]

### HTTP Emulator:

Make a **GET** request to **/room** page
![[Pasted image 20250817214631.png]]

Make a **GET** request to **/blog** page and set the **id** parameter to **1**
![[Pasted image 20250817214717.png]]

Make a **DELETE** request to **/user/1** page
![[Pasted image 20250817215148.png]]

Make a **PUT** request to **/user/2** page with the **username** parameter set to **admin**
Note: Use the gear button on the right to manage body parameters
![[Pasted image 20250817215336.png]]


Make a **POST** request to **/login** page with the **username** of **thm** and a **password** of **letmein**
Note: Use the gear button on the right to manage body parameters
![[Pasted image 20250817215547.png]]
