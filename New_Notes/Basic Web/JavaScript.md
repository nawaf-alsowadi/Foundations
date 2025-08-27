JavaScript (JS) is a programming language that is mostly used for **web applications to make them interactive.** Without JavaScript, a page would not have interactive elements and would always be static.

JS can dynamically update the page in real-time, giving functionality to change the style of a button when a particular event on the page occurs (such as when a user clicks a button) or to display moving animations.

JavaScript is added within the page source code and can be either loaded within `<script type="text/javascript">...<script>` tags or can be included remotely with the src attribute: `<script src="/location/of/javascript_file.js"></script>`

The following JavaScript code finds a HTML element on the page with the id of "demo" and **changes** the element's **contents** to "Hack the Planet" :
```js
document.getElementById("demo").innerHTML = "Hack the Planet";
```
![[Pasted image 20250818185757.png]]
*Note: This will replace `Hi there!` with `test the Planet` and when we click on the button, it changes to Button Clicked.*
HTML elements can also have events, such as "onclick" or "onhover" that execute JavaScript when the event occurs. The following code changes the text of the element with the demo ID to Button Clicked: 
```HTML
<button onclick='document.getElementById("demo").innerHTML = "Button Clicked";'>Click Me!</button>
```
onclick events can also be defined inside the JavaScript script tags, and not on elements directly.
[More on JS](https://www.w3schools.com/js/default.asp)

*Note: Whenever you're assessing a web application for security issues, one of the first things you should do is review the page source code to see if you can find any exposed login credentials or hidden links.*
![[Pasted image 20250818190746.png]]
