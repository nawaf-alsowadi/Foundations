As used in [[JavaScript]], the method:
```JS
document.getElementById("demo").innerHTML = 
```
allow the webserver to add [[HTML]] code inside the web page when the [[HTML#Attributes]]  inside the [[HTML#Elements/Tags]] matches the query 
```js
document.getElementBy...("")
```

If a malicious user tries to inject [[HTML]] code instead of normal text, then this can manipulate the website into displaying whatever the attacker wants to display or even deface the website. For example, following website let user enter their name to display by using `innerHTML`:
![[Pasted image 20250818192851.png]]

If attacker inject `<a href="http://hacker.com">Click to view attacker website</a>` to insert URL, the following will be displayed:
![[Pasted image 20250818192733.png]]

### Types
- ## Stored HTML Injection
The affect of the injection happen on the server-side permanently. When any user request the web page, web-server will forward the injected web page.

Imaging if the injection was a malicious code that get sensitive data from users machines and sends it to attacker machine? Bfffff.

- ## Reflected HTML Injection
Unlike stored injections, reflected attacks are not permanently housed on the server. Instead, they trick users into executing malicious code via a URL.

Lure the victim into clicking on a malicious link that is embedded in phishing email.

- ## DOM-based HTML Injection
Manipulate [[HTML#Document Object Model (DOM)]] which represents the page’s structure, to introduce malicious scripts to be executed in users machine.

### Examples
### Example 1: URL Parameter Manipulation

1. Vulnerability Setup: A web page uses JavaScript to directly **include a URL parameter in the HTML** without proper sanitization. For instance, a parameter userInput is directly included in the DOM.
2. User Interaction: The URL ![example1](https://www.imperva.com/learn/wp-content/uploads/sites/13/2016/02/example1.png)
3. Outcome: The script tag `<script>alert('injected')</script>` gets executed as part of the HTML, popping up an alert box with the message ‘Injected!’. This demonstrates how JavaScript code can be injected into the DOM.

### Example 2: InnerHTML Vulnerability

1. (same as first example) Vulnerability Setup: A JavaScript function on a webpage **uses innerHTML to insert** user-provided content into the DOM.
2. User Interaction: The user inputs a string such as ![example2](https://www.imperva.com/learn/wp-content/uploads/sites/13/2016/02/example2.png) into a form field.
3. Outcome: When the input is rendered on the page using innerHTML, the browser tries to load the image, fails, and executes the onerror JavaScript, showing the alert ‘Injected!’.

### Example 3: JavaScript-based Redirection

1. Vulnerability Setup: A webpage uses JavaScript to handle **redirection based on a URL parameter** without proper validation. For example, **window.location is set based on a URL parameter** redirect.
2. User Interaction: A crafted URL like ![example3](https://www.imperva.com/learn/wp-content/uploads/sites/13/2016/02/example3.png)
3. Outcome: The JavaScript in the redirect parameter gets executed, showing the alert ‘Injected!’. This can be used to redirect a user to a malicious site or execute harmful scripts.

### Example 4: Insecure JavaScript Evaluation

1. Vulnerability Setup: A webpage includes a JavaScript eval() function that takes a **string as input** and executes it as **code**.
2. User Interaction: The user navigates to a URL like ![example4](https://www.imperva.com/learn/wp-content/uploads/sites/13/2016/02/example4.png)
3. Outcome: The eval() function executes the code from the code parameter, resulting in an alert displaying ‘Injected!’.