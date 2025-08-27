![[Pasted image 20250818174723.png]]

There are two major components that make up a website:
1. Front End (Client-Side) - the way your browser display a website.
2. Back End (Server-Side) - a server that processes your request and returns a response.
In short, we make a request to a web server, and it return respond. A response can be HyperText Markup Language (HTML) which contain elements (AKA tags).

### Elements/Tags
HTML describe the structure of the website.
![[Pasted image 20250818175335.png]]

- The `<!DOCTYPE html>` defines that the page is a HTML5 document. This helps with standardization across different browsers and tells the browser to use HTML5 to interpret the page.
- The `<html>` element is the root element of the HTML page - all other elements come inside this element.
- The `<head>` element contains information about the page (such as the page title)
- The `<body>` element defines the HTML document's body; only content inside of the body is shown in the browser.
- The `<h1>` element defines a large heading
- The `<p>` element defines a paragraph
- There are many other elements (tags) used for different purposes. For example, there are tags for buttons (`<button>`), images (`<img>`), lists, and much more. [See All Tags](https://www.w3schools.com/tags/).

### Attributes
Used to identify the [[#Elements/Tags]] that we will apply some options on (styling, some functionality). It gives many options, such as:
- `<p class="bold-text">` make the tag a in bold, class used for group of elements/tags 
- `<img src="img/cat.jpg">` location of the image
- `<p id="example">` unique id of the element, cannot be assigned to another element.
- And much more! [See All Attributes](https://www.w3schools.com/tags/ref_attributes.asp)

### Document Object Model (DOM)
The HTML DOM is a standard for how to get, change, add, or delete HTML [[#Elements/Tags]]. DOM define [[#Elements/Tags]] the as **objects**. It also define [[#Elements/Tags]] as:
- The **properties** of all HTML [[#Elements/Tags]] to **set values** on them or **change** the current properties.
- The **methods** to access all HTML elements [[#Elements/Tags]] and **perform actions** on them
- The **events** for all HTML elements [[#Elements/Tags]]
DOM can be accessed by using JS:
```js
<html>  
<body>  
  
<p id="demo"></p>  
  
<script>  
document.getElementById("demo").innerHTML = "Hello World!";  
</script>  
  
</body>  
</html>
```
Where `getElementById` is a **method**, while `innerHTML` is a **property**.

If we want to **access** any element in an HTML page, you **always start** with accessing the `document` object.

For example: 
## Finding HTML Elements

|Method|Description|
|---|---|
|document.getElementById(_id_)|Find an element by element id|
|document.getElementsByTagName(_name_)|Find elements by tag name|
|document.getElementsByClassName(_name_)|Find elements by class name|
## Changing HTML Elements

|Property|Description|
|---|---|
|_element_.innerHTML =  _new html content_|Change the inner HTML of an element|
|_element_._attribute = new value_|Change the attribute value of an HTML element|
|_element_.style._property = new style_|Change the style of an HTML element|
|Method|Description|
|_element_.setAttribute_(attribute, value)_|Change the attribute value of an HTML element|
## Adding and Deleting Elements

|Method|Description|
|---|---|
|document.createElement(_element_)|Create an HTML element|
|document.removeChild(_element_)|Remove an HTML element|
|document.appendChild(_element_)|Add an HTML element|
|document.replaceChild(_new, old_)|Replace an HTML element|
|document.write(_text_)|Write into the HTML output stream|

## Adding Events Handlers

|Method|Description|
|---|---|
|document.getElementById(_id_).onclick = function(){_code_}|Adding event handler code to an onclick event|

DOM  represent the current content in tree of [[#Elements/Tags]] to facilitate the finding of the [[#Elements/Tags]] and to manipulate them. For example:

![[pic_htmltree.gif]]
