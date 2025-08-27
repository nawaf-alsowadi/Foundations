Web Application Firewall (WAF) is a server that sits between the client request (see [[HTTPS#Requests & Response]]) and the web server.

It **analyses the web requests** for common attack techniques, whether the request is from a real browser rather than a bot.

It also checks if an **excessive amount of web requests** are being sent by utilising something called **rate limiting**, which will only allow a certain amount of requests from an IP per second. If a request is deemed a potential attack, it will be dropped and never sent to the webserver.

![[24cb6468b4e51e8d8bbe7872e96a22b3.svg]]

WAF developer use Open Web Application Security Project (OWASP) top 10 which is a standard awareness document. OWASP lists the **top 10 most critical web application security risks** used as a foundation for securing web applications.

OWASP top 10 (updated every 3-4 years):
1. **Broken Access Control**
    
    - Failures in enforcing user access restrictions (e.g., users accessing data or functions they shouldn’t).
        
    - Example: A normal user accessing admin pages via direct URL.
        
2. **Cryptographic Failures** (formerly _Sensitive Data Exposure_)
    
    - Issues with protecting sensitive data in storage or transit due to weak or no encryption.
        
    - Example: Storing passwords in plain text.
        
3. **Injection**
    
    - When untrusted input is sent to an interpreter, leading to execution of unintended commands or queries.
        
    - Example: SQL injection, NoSQL injection, OS command injection.
        
4. **Insecure Design**
    
    - Flaws in application design, such as lack of threat modeling or secure design patterns.
        
    - Example: An e-commerce site that allows unlimited failed login attempts.
        
5. **Security Misconfiguration**
    
    - Insecure default configurations, incomplete configurations, or exposed system details.
        
    - Example: Running an application with default admin credentials or directory listing enabled.
        
6. **Vulnerable and Outdated Components**
    
    - Using libraries, frameworks, or software that are outdated or contain known vulnerabilities.
        
    - Example: Using an old version of Apache Struts with a known RCE vulnerability.
        
7. **Identification and Authentication Failures** (formerly _Broken Authentication_)
    
    - Weak authentication or session management mechanisms.
        
    - Example: Session IDs not expiring after logout, allowing hijacking.
        
8. **Software and Data Integrity Failures**
    
    - Issues where code or infrastructure doesn’t protect against integrity violations.
        
    - Example: Applications relying on plugins/modules from untrusted sources without verification.
        
9. **Security Logging and Monitoring Failures**
    
    - Lack of logging, monitoring, or alerting, which prevents timely detection of breaches.
        
    - Example: No alerts when multiple failed login attempts occur.
        
10. **Server-Side Request Forgery (SSRF)**
    

- When a server fetches a remote resource without validating the user request URL, allowing attackers to access internal systems.
    
- Example: An attacker making the server request some data in the internal cloud metadata services.
